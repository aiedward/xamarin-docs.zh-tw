---
title: "自訂資料表的外觀"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8A83DE38-0028-CB61-66F9-0FB9DE552286
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 12314e6ec5951a5a094ed8b0a5536b450853c5fb
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="customizing-a-tables-appearance"></a>自訂資料表的外觀

變更資料表的外觀的最簡單方式是使用不同的儲存格樣式。 您可以變更建立每個儲存格時使用的儲存格樣式`UITableViewSource`的`GetCell`方法。

## <a name="cell-styles"></a>儲存格樣式

有四個內建樣式：

-  **預設**– 支援`UIImageView`。
-  **副標題**– 支援`UIImageView`和副標題。
-  **Value1** – 右對齊的副標題，支援`UIImageView`。
-  **Value2** – 標題為靠右對齊和子標題為靠左對齊 （但無影像）。


這些螢幕擷取畫面會顯示每個樣式的顯示方式：

 [ ![](customizing-table-appearance-images/image7.png "這些螢幕擷取畫面顯示每個樣式的顯示方式")](customizing-table-appearance-images/image7.png)

此範例**CellDefaultTable**包含程式碼以產生這些畫面。 儲存格樣式中設定`UITableViewCell`建構函式，就像這樣：

```csharp
cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Subtitle, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value1, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value2, cellIdentifier);
```

[支援內容](http://developer.xamarin.com/api/type/UIKit.UITableViewCell/)儲存格樣式可以設定：

```csharp
cell.TextLabel.Text = tableItems[indexPath.Row].Heading;
cell.DetailTextLabel.Text = tableItems[indexPath.Row].SubHeading;
cell.ImageView.Image = UIImage.FromFile("Images/" + tableItems[indexPath.Row].ImageName); // don't use for Value2
```

## <a name="accessories"></a>附屬應用程式

資料格都可以有下列的附屬應用程式檢視的右邊加入：

-   **核取記號**– 可用於指出多重選取資料表中。
-   **DetailButton** – 要碰觸個別資料格，讓它可以執行不同的函式要碰觸本身的儲存格的其餘部分的回應 (例如開啟快顯視窗或新的視窗不屬於`UINavigationController`堆疊)。
-   **DisclosureIndicator** -通常用來指出接觸資料格將會開啟另一個檢視。
-   **DetailDisclosureButton** – 的組合`DetailButton`和`DisclosureIndicator`。


這是什麼樣子：

 [ ![](customizing-table-appearance-images/image8.png "範例 附屬應用程式")](customizing-table-appearance-images/image8.png)

若要顯示這些 附屬應用程式，您可以設定的其中一個`Accessory`屬性`GetCell`方法：

```csharp
cell.Accessory = UITableViewCellAccessory.Checkmark;
//cell.Accessory = UITableViewCellAccessory.DisclosureIndicator;
//cell.Accessory = UITableViewCellAccessory.DetailDisclosureButton; // implement AccessoryButtonTapped
//cell.Accessory = UITableViewCellAccessory.None; // to clear the accessory
```

當`DetailButton`或`DetailDisclosureButton`顯示，您也應該覆寫`AccessoryButtonTapped`它碰觸時執行一些動作。

```csharp
public override void AccessoryButtonTapped (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("DetailDisclosureButton Touched", tableItems[indexPath.Row].Heading, UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    owner.PresentViewController (okAlertController, true, null);

    tableView.DeselectRow (indexPath, true);
}
```

此範例**CellAccessoryTable**顯示使用附屬應用程式的範例。

## <a name="cell-separators"></a>資料格分隔符號

資料格分隔符號是用來分隔資料表的資料表資料格。 在資料表上所設定的屬性。

```csharp
TableView.SeparatorColor = UIColor.Blue;
TableView.SeparatorStyle = UITableViewCellSeparatorStyle.DoubleLineEtched;
```

此外，也可以加入分隔符號柔邊或 vibrancy 效果：

```csharp
// blur effect
TableView.SeparatorEffect =
    UIBlurEffect.FromStyle(UIBlurEffectStyle.Dark);

//vibrancy effect
var effect = UIBlurEffect.FromStyle(UIBlurEffectStyle.Light);
TableView.SeparatorEffect = UIVibrancyEffect.FromBlurEffect(effect);
```

在分隔符號也可以擁有內凹：

```csharp
TableView.SeparatorInset.InsetRect(new CGRect(4, 4, 150, 2));
```

## <a name="creating-custom-cell-layouts"></a>建立自訂儲存格的版面配置

若要變更的資料表，您必須提供自訂的儲存格，以顯示的視覺化樣式。 自訂的儲存格可以具有不同色彩和控制項的版面配置。

CellCustomTable 範例會實作`UITableViewCell`子類別，定義的自訂配置`UILabel`s 和`UIImage`以不同的字型和色彩。 產生的資料格看起來像這樣：

 [ ![](customizing-table-appearance-images/image9.png "自訂儲存格的版面配置")](customizing-table-appearance-images/image9.png)

自訂儲存格類別包含只有三個方法：

-   **建構函式**– 建立 UI 控制項，並設定自訂樣式屬性 （例如。 字體、 大小和色彩）。
-   **UpdateCell** – 方法`UITableView.GetCell`用來設定儲存格的內容。
-   **LayoutSubviews** – 設定 UI 控制項的位置。 在範例中，每個資料格具有相同的配置，但更複雜的資料格 （特別是那些與不同大小） 可能需要根據要顯示的內容不同的版面配置位置。


中的完整範例程式碼**CellCustomTable > CustomVegeCell.cs**遵循：

```csharp
public class CustomVegeCell : UITableViewCell  {
    UILabel headingLabel, subheadingLabel;
    UIImageView imageView;
    public CustomVegeCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
    {
        SelectionStyle = UITableViewCellSelectionStyle.Gray;
        ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);
        imageView = new UIImageView();
        headingLabel = new UILabel () {
            Font = UIFont.FromName("Cochin-BoldItalic", 22f),
            TextColor = UIColor.FromRGB (127, 51, 0),
            BackgroundColor = UIColor.Clear
        };
        subheadingLabel = new UILabel () {
            Font = UIFont.FromName("AmericanTypewriter", 12f),
            TextColor = UIColor.FromRGB (38, 127, 0),
            TextAlignment = UITextAlignment.Center,
            BackgroundColor = UIColor.Clear
        };
        ContentView.AddSubviews(new UIView[] {headingLabel, subheadingLabel, imageView});

    }
    public void UpdateCell (string caption, string subtitle, UIImage image)
    {
        imageView.Image = image;
        headingLabel.Text = caption;
        subheadingLabel.Text = subtitle;
    }
    public override void LayoutSubviews ()
    {
        base.LayoutSubviews ();
        imageView.Frame = new CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
        headingLabel.Frame = new CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
        subheadingLabel.Frame = new CGRect (100, 18, 100, 20);
    }
}
```

`GetCell`方法`UITableViewSource`需要加以修改才能建立自訂儲存格：

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (cellIdentifier) as CustomVegeCell;
    if (cell == null)
        cell = new CustomVegeCell (cellIdentifier);
    cell.UpdateCell (tableItems[indexPath.Row].Heading
            , tableItems[indexPath.Row].SubHeading
            , UIImage.FromFile ("Images/" + tableItems[indexPath.Row].ImageName) );
    return cell;
}
```



## <a name="related-links"></a>相關連結

- [WorkingWithTables （範例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
