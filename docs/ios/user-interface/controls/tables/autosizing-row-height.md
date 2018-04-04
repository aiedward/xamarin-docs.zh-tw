---
title: 自動調整大小的資料列高度
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 73e16c3381b639645463e3e8aaeed35224b67861
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="auto-sizing-row-height"></a>自動調整大小的資料列高度

從 iOS 8 開始，Apple 加入建立資料表的檢視表的能力 (`UITableView`)，可以自動擴增和縮減使用自動配置大小類別，限制其內容的大小為基礎的指定資料列的高度。

iOS 11 已新增的功能會自動擴展的資料列。 頁首、 頁尾和資料格可以現在會自動調整大小根據其內容。 不過，如果模型中 iOS 設計工具中，介面產生器中，建立您的資料表，或者如果它有固定的資料列高度必須以手動方式啟用自動調整儲存格，本指南中所述。

## <a name="cell-layout-in-the-ios-designer"></a>在 iOS 設計工具中的資料格版面配置

開啟分鏡腳本，您想要在 iOS 設計工具中，資料列的自動調整的資料表檢視選取的儲存格*原型*和設計儲存格的版面配置。 例如: 

[![](autosizing-row-height-images/table01.png "儲存格的原型設計")](autosizing-row-height-images/table01.png#lightbox)

每個項目在原型中，加入條件約束，以將項目保存在正確的位置，因為資料表檢視旋轉或不同的 iOS 裝置的螢幕大小重新調整大小。 例如，釘選`Title`儲存格的左上和右側*內容檢視*:

[![](autosizing-row-height-images/table02.png "釘選的標題上方、 左邊與右邊的儲存格的內容檢視")](autosizing-row-height-images/table02.png#lightbox)

如果我們的範例資料表，小型`Label`(下`Title`) 是可以壓縮和成長來增加或減少資料列高度的欄位。 若要達成這個效果，加入左、 右邊、 上方和標籤的底部釘選的下列條件約束：

[![](autosizing-row-height-images/table03.png "若要釘選左、 右邊、 上方和標籤的底部這些條件約束")](autosizing-row-height-images/table03.png#lightbox)

既然我們已經完全，限制在資料格中的項目，我們需要釐清哪些項目自動縮放。 若要這樣做，請設定**內容 Hugging 優先順序**和**內容壓縮抵抗優先順序**視需要在**配置**屬性板區段：

[![](autosizing-row-height-images/table03a.png "之配置區段的內容填補")](autosizing-row-height-images/table03a.png#lightbox)

您想要將展開的元素設定**低**Hugging 優先順序值，和**低**壓縮抵抗優先權值。

接下來，我們必須以選取儲存格原型，並賦予獨特**識別碼**:

[![](autosizing-row-height-images/table04.png "提供資料格原型的唯一識別碼")](autosizing-row-height-images/table04.png#lightbox)

在我們的範例， `GrowCell`。 稍後當我們填入的資料表時，我們將使用此值。

> [!IMPORTANT]
> 如果您的資料表包含一個以上的資料格類型 (**原型**)，您必須確定每個類型都有自己的唯一`Identifier`自動運作的資料列大小。

對於我們的儲存格原型的每個項目，將指派**名稱**將它公開至 C# 程式碼。 例如: 

[![](autosizing-row-height-images/table05.png "指定要將它公開至 C# 程式碼的名稱")](autosizing-row-height-images/table05.png#lightbox)

接下來，新增自訂類別`UITableViewController`、`UITableView`和`UITableCell`(Prototype)。 例如:  

[![](autosizing-row-height-images/table06.png "將自訂類別加入 UITableViewController、 UITableView 和 UITableCell")](autosizing-row-height-images/table06.png#lightbox)

最後，為了確定所有預期的內容會顯示在我們的標籤，請設定**行**屬性`0`:

[![](autosizing-row-height-images/table06.png "行數屬性設定為 0")](autosizing-row-height-images/table06a.png#lightbox)

與定義 UI，讓我們加入的程式碼，以啟用自動的資料列高度調整大小。

## <a name="enabling-auto-resizing-height"></a>啟用自動調整大小的高度

在我們的資料表檢視的資料來源 (`UITableViewDatasource`) 或來源 (`UITableViewSource`)，當我們清除佇列我們要使用的資料格`Identifier`我們在設計工具中定義。 例如: 

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

根據預設，[資料表] 檢視將會針對自動調整大小的資料列高度設定。 若要確保這點，`RowHeight`屬性應該設定為`UITableView.AutomaticDimension`。 我們也要設定`EstimatedRowHeight`屬性中的我們`UITableViewController`。 例如: 

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

此預估值不一定要是精確，只在資料表檢視中的每個資料列的平均高度的概略估計。

此位置的程式碼，執行應用程式時，每個資料列會壓縮及成長根據在資料格的原型中的最後一個標籤的高度。 例如: 

[![](autosizing-row-height-images/table07.png "執行範例資料表")](autosizing-row-height-images/table07.png#lightbox)


## <a name="related-links"></a>相關連結

- [GrowRowTable （範例）](https://developer.xamarin.com/samples/monotouch/GrowRowTable/)
