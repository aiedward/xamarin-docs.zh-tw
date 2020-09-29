---
title: 自訂 Xamarin 中的資料表外觀
description: 本檔說明如何在 Xamarin 中自訂資料表的外觀。 它會討論資料格樣式、配件、資料格分隔符號和自訂資料格版面配置。
ms.prod: xamarin
ms.assetid: 8A83DE38-0028-CB61-66F9-0FB9DE552286
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: dd79c2059d53702d947a966327bf22b2043d36c5
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429942"
---
# <a name="customizing-a-tables-appearance-in-xamarinios"></a>自訂 Xamarin 中的資料表外觀

變更資料表外觀最簡單的方式，就是使用不同的儲存格樣式。 您可以變更在的方法中建立每個資料格時，所使用的儲存格樣式 `UITableViewSource` `GetCell` 。

## <a name="cell-styles"></a>儲存格樣式

內建的樣式有四種：

- **預設值** -支援 `UIImageView` 。
- **副標題** –支援 `UIImageView` 和副標題。
- **Value1** –靠右對齊的子標題可支援 `UIImageView` 。
- **Value2** -標題靠右對齊，而子標題靠左對齊 (但沒有影像) 。

這些螢幕擷取畫面顯示每種樣式的顯示方式：

 [![這些螢幕擷取畫面顯示每種樣式的顯示方式](customizing-table-appearance-images/image7.png)](customizing-table-appearance-images/image7.png#lightbox)

範例 **CellDefaultTable** 包含產生這些畫面的程式碼。 儲存格樣式是在函式中設定 `UITableViewCell` ，如下所示：

```csharp
cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Subtitle, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value1, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value2, cellIdentifier);
```

接著可以設定資料格樣式的[支援屬性](xref:UIKit.UITableViewCell)：

```csharp
cell.TextLabel.Text = tableItems[indexPath.Row].Heading;
cell.DetailTextLabel.Text = tableItems[indexPath.Row].SubHeading;
cell.ImageView.Image = UIImage.FromFile("Images/" + tableItems[indexPath.Row].ImageName); // don't use for Value2
```

## <a name="accessories"></a>Accessories

儲存格可以在視圖的右邊加入下列配件：

- **核取記號** –可用來指出資料表中的多重選取專案。
- **DetailButton** –會回應獨立于儲存格其餘部分的觸控，讓它能夠執行不同的函式來觸及儲存格本身 (例如，開啟不是堆疊) 一部分的快顯視窗或新視窗 `UINavigationController` 。
- **DisclosureIndicator** –通常用來表示觸及儲存格將會開啟另一個 view。
- **DetailDisclosureButton** –和的組合 `DetailButton` `DisclosureIndicator` 。

如下所示：

 [![範例配件](customizing-table-appearance-images/image8.png)](customizing-table-appearance-images/image8.png#lightbox)

若要顯示其中一個配件，您可以 `Accessory` 在方法中設定 `GetCell` 屬性：

```csharp
cell.Accessory = UITableViewCellAccessory.Checkmark;
//cell.Accessory = UITableViewCellAccessory.DisclosureIndicator;
//cell.Accessory = UITableViewCellAccessory.DetailDisclosureButton; // implement AccessoryButtonTapped
//cell.Accessory = UITableViewCellAccessory.None; // to clear the accessory
```

當 `DetailButton` 或 `DetailDisclosureButton` 顯示時，您也應該覆寫， `AccessoryButtonTapped` 以在觸及時執行某些動作。

```csharp
public override void AccessoryButtonTapped (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("DetailDisclosureButton Touched", tableItems[indexPath.Row].Heading, UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    owner.PresentViewController (okAlertController, true, null);

    tableView.DeselectRow (indexPath, true);
}
```

範例 **CellAccessoryTable** 會顯示使用附屬應用程式的範例。

## <a name="cell-separators"></a>儲存格分隔符號

資料格分隔符號是用來分隔資料表的資料表單元格。 這些屬性是在資料表上設定的。

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

分隔符號也可以有內凹：

```csharp
TableView.SeparatorInset.InsetRect(new CGRect(4, 4, 150, 2));
```

## <a name="creating-custom-cell-layouts"></a>建立自訂資料格版面配置

若要變更資料表的視覺化樣式，您需要提供自訂資料格才能顯示。 自訂資料格可以有不同的色彩和控制項版面配置。

CellCustomTable 範例會執行子 `UITableViewCell` 類別，以定義的自訂配置 `UILabel` 和 `UIImage` 具有不同字型和色彩的。 產生的資料格看起來像這樣：

 [![自訂資料格版面配置](customizing-table-appearance-images/image9.png)](customizing-table-appearance-images/image9.png#lightbox)

自訂資料格類別只包含三種方法：

- 「函式 **」–建立**UI 控制項並設定 (的自訂樣式屬性。 字型、大小和色彩) 。
- **UpdateCell** –  `UITableView.GetCell` 用來設定儲存格屬性的方法。
- **LayoutSubviews** –設定 UI 控制項的位置。 在範例中，每個資料格都有相同的配置，但更複雜的資料格 (特別是具有不同大小的資料格，) 可能需要不同的版面配置位置，視顯示的內容而定。

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

的 `GetCell` 方法 `UITableViewSource` 需要修改以建立自訂資料格：

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

- [WorkingWithTables (範例) ](/samples/xamarin/ios-samples/workingwithtables)