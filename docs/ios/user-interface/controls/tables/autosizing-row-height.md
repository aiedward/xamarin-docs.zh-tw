---
title: 在 Xamarin.iOS 中的自動調整大小的資料列高度
description: 本文件說明如何將新增至 Xamarin.iOS 應用程式資料表的檢視資料列的高度會根據的內容而有所不同。 它討論 「 iOS 設計工具中的資料格版面配置] 和 [啟用自動調整大小的高度。
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: e4446abc73817eb0672cd10a69ff6f738de0c1e1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116468"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>在 Xamarin.iOS 中的自動調整大小的資料列高度

從 iOS 8 開始，Apple 新增能力以建立資料表檢視 (`UITableView`)，就可以自動擴增和縮減使用自動版面配置、 大小類別和條件約束及其內容的大小為基礎的指定資料列的高度。

iOS 11 有新增的功能，自動展開的資料列。 標頭、 頁尾和資料格可以現在會自動調整大小根據其內容。 不過，如果您的資料表建立在 iOS 設計工具介面產生器中，或如果它有固定的資料列高度必須以手動方式啟用自我調整大小的儲存格，本指南中所述。

## <a name="cell-layout-in-the-ios-designer"></a>IOS 設計工具中的儲存格配置

開啟分鏡腳本，您想要在 iOS 設計工具中的資料列的自動調整的資料表檢視選取的儲存格*原型*和設計的資料格的版面配置。 例如: 

[![](autosizing-row-height-images/table01.png "儲存格的原型設計")](autosizing-row-height-images/table01.png#lightbox)

每個項目在原型中，新增條件約束，以將項目保留在正確的位置，[資料表] 檢視的旋轉或不同的 iOS 裝置螢幕大小調整大小時。 比方說，釘選`Title`儲存格的上、 左和右*內容檢視*:

[![](autosizing-row-height-images/table02.png "釘選到頂端、 左邊與右邊的儲存格的內容檢視的標題")](autosizing-row-height-images/table02.png#lightbox)

如果我們的範例資料表，小型`Label`(下`Title`) 是可以縮小和成長來增加或減少資料列高度的欄位。 若要達成此效果，請新增下列條件約束，若要釘選左、 右、 頂端和底部的標籤：

[![](autosizing-row-height-images/table03.png "若要釘選左、 右、 nahoře a dole 標籤的這些條件約束")](autosizing-row-height-images/table03.png#lightbox)

既然我們已經完全限制在儲存格中的項目，我們需要釐清哪些項目應自動縮放。 若要這樣做，請設定**內容 Hugging 優先順序**並**內容的壓縮防禦優先順序**視需要在**版面配置**區段的 [屬性] 面板：

[![](autosizing-row-height-images/table03a.png "[屬性] 面板的 [版面配置] 區段")](autosizing-row-height-images/table03a.png#lightbox)

設定您想要將展開的項目**較低**Hugging 優先權值，並**低**壓縮防禦優先順序值。

接下來，我們要選取資料格原型，並為它提供的唯一**識別碼**:

[![](autosizing-row-height-images/table04.png "提供資料格原型的唯一識別碼")](autosizing-row-height-images/table04.png#lightbox)

在我們的範例中， `GrowCell`。 稍後當我們填入資料表時，我們將使用此值。

> [!IMPORTANT]
> 如果您的資料表包含一個以上的資料格類型 (**原型**)，您必須確定每個類型都有自己的唯一`Identifier`的自動調整運作的資料列。

對於我們的資料格原型的每個項目，將指派**名稱**公開至C#程式碼。 例如: 

[![](autosizing-row-height-images/table05.png "指派來公開它的名稱C#程式碼")](autosizing-row-height-images/table05.png#lightbox)

接下來，新增的自訂類別`UITableViewController`，則`UITableView`而`UITableCell`(Prototype)。 例如:  

[![](autosizing-row-height-images/table06.png "將自訂類別加入 UITableViewController、 UITableView 和 UITableCell")](autosizing-row-height-images/table06.png#lightbox)

最後，為了確保所有預期的內容會顯示在我們的標籤，設定**線條**屬性設`0`:

[![](autosizing-row-height-images/table06.png "線條屬性設定為 0")](autosizing-row-height-images/table06a.png#lightbox)

定義 ui，讓我們新增的程式碼，以啟用自動的資料列高度調整大小。

## <a name="enabling-auto-resizing-height"></a>啟用自動調整大小的高度

在我們的表格檢視的資料來源 (`UITableViewDatasource`) 或來源 (`UITableViewSource`)，當我們從佇列中清除我們要使用的儲存格`Identifier`我們在設計工具中定義。 例如: 

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

根據預設，[資料表] 檢視會為自動調整大小的資料列高度。 為了確保功效，`RowHeight`屬性應設為`UITableView.AutomaticDimension`。 我們也需要設定`EstimatedRowHeight`屬性中的我們`UITableViewController`。 例如: 

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

這項估計不一定要完全相同，只是平均的資料表檢視中的每個資料列高度的概略估計。

與此程式碼就緒之後，應用程式執行時，每個資料列會壓縮，而且成長根據在資料格的原型中的最後一個標籤的高度。 例如: 

[![](autosizing-row-height-images/table07.png "執行範例資料表")](autosizing-row-height-images/table07.png#lightbox)


## <a name="related-links"></a>相關連結

- [GrowRowTable （範例）](https://developer.xamarin.com/samples/monotouch/GrowRowTable/)
