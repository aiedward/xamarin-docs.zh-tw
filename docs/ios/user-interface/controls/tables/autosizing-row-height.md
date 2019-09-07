---
title: 在 Xamarin 中自動調整資料列高度大小
description: 本檔說明如何新增至 Xamarin iOS 應用程式資料表。其高度根據內容而有所不同的資料列。 它討論 iOS 設計工具中的儲存格版面配置，以及啟用自動調整大小的高度。
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 03d79c93821737afb1f8e1946ebb3b6380d7a527
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768441"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>在 Xamarin 中自動調整資料列高度大小

從 iOS 8 開始，Apple 新增了建立資料表視圖（`UITableView`）的功能，可以根據其內容的大小，使用自動設定、大小類別和條件約束，自動擴大和縮減指定資料列的高度。

iOS 11 已加入資料列自動擴充的功能。 標頭、頁尾和資料格現在可以根據其內容自動調整大小。 不過，如果您的資料表是在 iOS 設計工具中建立，請 Interface Builder，或者，如果它有固定的資料列高度，您必須手動啟用自行調整資料格的大小，如本指南中所述。

## <a name="cell-layout-in-the-ios-designer"></a>IOS 設計工具中的儲存格版面配置

在 iOS 設計工具中，開啟您想要讓資料列自動調整大小的資料表視圖的分鏡腳本，選取資料格的*原型*，並設計資料格的版面配置。 例如：

[![](autosizing-row-height-images/table01.png "資料格的原型設計")](autosizing-row-height-images/table01.png#lightbox)

針對原型中的每個專案，加入條件約束，以將元素保留在正確的位置，因為資料表視圖會針對旋轉或不同的 iOS 裝置螢幕大小調整大小。 例如，將釘`Title`選到儲存格*內容視圖*的頂端、左側和右側：

[![](autosizing-row-height-images/table02.png "將標題釘選到資料格內容視圖的左上方和右邊")](autosizing-row-height-images/table02.png#lightbox)

在範例資料表的案例中，小`Label` （ `Title`在下）是可以縮小和擴大以增加或減少資料列高度的欄位。 若要達到此效果，請新增下列條件約束，以釘選標籤的左邊、右邊、上方和底部：

[![](autosizing-row-height-images/table03.png "用來釘選標籤左邊、右邊、上方和底部的條件約束")](autosizing-row-height-images/table03.png#lightbox)

既然我們已完全限制儲存格中的元素，我們需要澄清應該延展的元素。 若要這樣做，請在 Properties Pad 的**配置區段中，視**需要設定**內容 Hugging 優先順序**和**內容壓縮抵抗優先權**：

[![](autosizing-row-height-images/table03a.png "Properties Pad 的版面配置區段")](autosizing-row-height-images/table03a.png#lightbox)

設定您想要展開的專案，使其具有**較低**的 Hugging 優先順序值，以及**較低**的壓縮抵抗優先順序值。

接下來，我們需要選取資料格原型，並為它提供唯一**識別碼**：

[![](autosizing-row-height-images/table04.png "為數據格原型提供唯一識別碼")](autosizing-row-height-images/table04.png#lightbox)

在我們的範例`GrowCell`中為。 我們稍後會在填入資料表時使用此值。

> [!IMPORTANT]
> 如果您的資料表包含一個以上的資料格類型（**原型**），您必須確保每個類型都有`Identifier`自己的唯一，以自動調整資料列大小來進行工作。

針對我們的資料格原型的每個元素，指派**名稱**以將C#它公開給程式碼。 例如：

[![](autosizing-row-height-images/table05.png "指派名稱以將其公開至C#程式碼")](autosizing-row-height-images/table05.png#lightbox)

接下來，為`UITableViewController` `UITableView` 、和`UITableCell` （原型）新增自訂類別。 例如： 

[![](autosizing-row-height-images/table06.png "新增 UITableViewController、UITableView 和 UITableCell 的自訂類別")](autosizing-row-height-images/table06.png#lightbox)

最後，為了確保所有預期的內容都會顯示在標籤中，請將 [程式列`0`] 屬性設定為：

[![](autosizing-row-height-images/table06.png "Lines 屬性設定為0")](autosizing-row-height-images/table06a.png#lightbox)

定義 UI 之後，讓我們新增程式碼，以啟用自動資料列高度調整大小。

## <a name="enabling-auto-resizing-height"></a>啟用自動調整大小高度

在資料表視圖的資料來源（`UITableViewDatasource`）或來源（`UITableViewSource`）中，當我們將儲存格清除佇列時，我們`Identifier`必須使用我們在設計工具中定義的。 例如：

```csharp
public string CellID {
    get { return "GrowCell"; }
}
...

public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (CellID, indexPath) as GrowRowTableCell;
    var item = Items [indexPath.Row];

    // Setup
    cell.Image = UIImage.FromFile(item.ImageName);
    cell.Title = item.Title;
    cell.Description = item.Description;

    return cell;
}
```

根據預設，資料表視圖會針對自動調整資料列高度進行設定。 若要確保這一點`RowHeight` ，屬性應該設定為`UITableView.AutomaticDimension`。 我們也需要`UITableViewController`在中設定`EstimatedRowHeight`屬性。 例如：

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Initialize table
    TableView.DataSource = new GrowRowTableDataSource(this);
    TableView.Delegate = new GrowRowTableDelegate (this);
    TableView.RowHeight = UITableView.AutomaticDimension;
    TableView.EstimatedRowHeight = 40f;
    TableView.ReloadData ();
}
```

這項估計不一定要精確，只是資料表視圖中每個資料列的平均高度粗略估計。

當此程式碼準備好時，當應用程式執行時，每個資料列都會根據資料格原型中最後一個標籤的高度來縮小和成長。 例如：

[![](autosizing-row-height-images/table07.png "範例資料表執行")](autosizing-row-height-images/table07.png#lightbox)

## <a name="related-links"></a>相關連結

- [GrowRowTable （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/growrowtable)
