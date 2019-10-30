---
title: 在 Xamarin 中自訂資料表的外觀
description: 本檔說明如何在 Xamarin 中自訂資料表的外觀。 它討論儲存格樣式、配件、資料格分隔符號和自訂資料格版面配置。
ms.prod: xamarin
ms.assetid: 8A83DE38-0028-CB61-66F9-0FB9DE552286
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 7b4042d9090823fbeff89face7c00e5753666c97
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021915"
---
# <a name="customizing-a-tables-appearance-in-xamarinios"></a>在 Xamarin 中自訂資料表的外觀

變更資料表外觀最簡單的方式，就是使用不同的儲存格樣式。 在 `UITableViewSource`的 `GetCell` 方法中建立每個資料格時，您可以變更所使用的儲存格樣式。

## <a name="cell-styles"></a>儲存格樣式

有四種內建樣式：

- **預設值**-支援 `UIImageView`。
- **副標題**–支援 `UIImageView` 和子標題。
- **Value1** –靠右對齊的副標題，支援 `UIImageView`。
- **Value2** –標題會靠右對齊，而副標題會靠左對齊（但不含影像）。

這些螢幕擷取畫面顯示每個樣式的顯示方式：

 [![](customizing-table-appearance-images/image7.png "These screenshots show how each style appears")](customizing-table-appearance-images/image7.png#lightbox)

範例**CellDefaultTable**包含用來產生這些畫面的程式碼。 儲存格樣式是在 `UITableViewCell` 的函式中設定，如下所示：

```csharp
cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Subtitle, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value1, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value2, cellIdentifier);
```

接著可以設定儲存格樣式的[支援屬性](xref:UIKit.UITableViewCell)：

```csharp
cell.TextLabel.Text = tableItems[indexPath.Row].Heading;
cell.DetailTextLabel.Text = tableItems[indexPath.Row].SubHeading;
cell.ImageView.Image = UIImage.FromFile("Images/" + tableItems[indexPath.Row].ImageName); // don't use for Value2
```

## <a name="accessories"></a>附屬

資料格可以在此視圖的右方加入下列配件：

- **核取記號**–可以用來表示資料表中的多重選取。
- **DetailButton** –回應與儲存格其餘部分無關的觸控，讓它可以執行不同的函式來觸及儲存格本身（例如開啟不屬於 `UINavigationController` 堆疊的快顯或新視窗）。
- **DisclosureIndicator** –通常用來表示觸及資料格會開啟另一個視圖。
- **DetailDisclosureButton** – `DetailButton` 和 `DisclosureIndicator`的組合。

這就是它們的樣子：

 [![](customizing-table-appearance-images/image8.png "Sample Accessories")](customizing-table-appearance-images/image8.png#lightbox)

若要顯示其中一個配件，您可以在 `GetCell` 方法中設定 `Accessory` 屬性：

```csharp
cell.Accessory = UITableViewCellAccessory.Checkmark;
//cell.Accessory = UITableViewCellAccessory.DisclosureIndicator;
//cell.Accessory = UITableViewCellAccessory.DetailDisclosureButton; // implement AccessoryButtonTapped
//cell.Accessory = UITableViewCellAccessory.None; // to clear the accessory
```

當顯示 `DetailButton` 或 `DetailDisclosureButton` 時，您也應該覆寫 `AccessoryButtonTapped`，以在觸及動作時執行某些動作。

```csharp
public override void AccessoryButtonTapped (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("DetailDisclosureButton Touched", tableItems[indexPath.Row].Heading, UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    owner.PresentViewController (okAlertController, true, null);

    tableView.DeselectRow (indexPath, true);
}
```

範例**CellAccessoryTable**會顯示使用附屬應用程式的範例。

## <a name="cell-separators"></a>資料格分隔符號

資料格分隔符號是用來分隔資料表的資料表資料格。 這些屬性是在資料表上設定的。

```csharp
TableView.SeparatorColor = UIColor.Blue;
TableView.SeparatorStyle = UITableViewCellSeparatorStyle.DoubleLineEtched;
```

您也可以將模糊或 vibrancy 效果新增至分隔符號：

```csharp
// blur effect
TableView.SeparatorEffect =
    UIBlurEffect.FromStyle(UIBlurEffectStyle.Dark);

//vibrancy effect
var effect = UIBlurEffect.FromStyle(UIBlurEffectStyle.Light);
TableView.SeparatorEffect = UIVibrancyEffect.FromBlurEffect(effect);
```

分隔符號也可以有內陷：

```csharp
TableView.SeparatorInset.InsetRect(new CGRect(4, 4, 150, 2));
```

## <a name="creating-custom-cell-layouts"></a>建立自訂資料格版面配置

若要變更資料表的視覺化樣式，您必須提供要顯示的自訂資料格。 自訂資料格可以有不同的色彩和控制項版面配置。

CellCustomTable 範例會執行 `UITableViewCell` 子類別，以定義 `UILabel`s 的自訂配置，以及具有不同字型和色彩的 `UIImage`。 產生的資料格看起來像這樣：

 [![](customizing-table-appearance-images/image9.png "Custom Cell Layouts")](customizing-table-appearance-images/image9.png#lightbox)

自訂資料格類別只包含三種方法：

- 「**構造**函式」–建立 UI 控制項並設定自訂樣式屬性（例如 字型、大小和色彩）。
- **UpdateCell** – `UITableView.GetCell` 用來設定儲存格屬性的方法。
- **LayoutSubviews** –設定 UI 控制項的位置。 在範例中，每個資料格都有相同的配置，但較複雜的資料格（特別是具有不同大小的資料格）可能需要不同的版面配置位置，視所顯示的內容而定。

**CellCustomTable > CustomVegeCell.cs**中的完整範例程式碼如下所示：

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

您必須修改 `UITableViewSource` 的 `GetCell` 方法，才能建立自訂資料格：

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

- [WorkingWithTables （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
