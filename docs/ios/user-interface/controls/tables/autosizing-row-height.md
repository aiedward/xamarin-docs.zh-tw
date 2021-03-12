---
title: 在 Xamarin 中自動調整大小的資料列高度
description: 本檔說明如何將高度因內容而異的 [iOS 應用程式] 資料表視圖資料列新增至 [Xamarin]。 它會討論 iOS 設計工具中的儲存格版面配置，並啟用自動調整大小的高度。
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: b612800656a7073d0442e450f52c8aacdd12f8cd
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602966"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>在 Xamarin 中自動調整大小的資料列高度
> [!WARNING]
> IOS 設計工具在 Visual Studio 2019 16.8 版和 Visual Studio 2019 for Mac 版本8.8 中已淘汰，並已在 Visual Studio 2019 版本16.9 和 Visual Studio for Mac 版本8.9 中移除。
> 建立 iOS 使用者介面的建議方式是直接在執行 Xcode 介面產生器的 Mac 上。 如需詳細資訊，請參閱 [使用 Xcode 設計使用者介面](~/ios/user-interface/storyboards/index.md)。 

從 iOS 8 開始，Apple 新增了建立資料表視圖的功能 (`UITableView`) ，根據使用自動設定、大小類別和條件約束，根據其內容的大小來自動成長和縮減指定資料列的高度。

iOS 11 已新增資料列自動擴充的功能。 標題、頁尾和資料格現在可以根據其內容自動調整大小。 但是，如果您的資料表是在 iOS 設計工具、介面產生器中建立，或者如果它有固定的資料列高度，您必須手動啟用自行調整資料格，如本指南所述。

## <a name="cell-layout-in-the-ios-designer"></a>IOS 設計工具中的儲存格版面配置

開啟您想要在 iOS 設計工具中自動調整大小之資料列的資料表視圖的分鏡腳本，選取儲存格的 *原型* ，並設計資料格的配置。 例如：

[![儲存格的原型設計](autosizing-row-height-images/table01.png)](autosizing-row-height-images/table01.png#lightbox)

針對原型中的每個專案，加入條件約束以將專案保留在正確的位置，因為資料表視圖會針對旋轉或不同的 iOS 裝置螢幕大小調整大小。 例如，將釘選 `Title` 到儲存格 *內容視圖* 的左上角和右邊：

[![將標題釘選在資料格內容視圖的上方、左方和右邊](autosizing-row-height-images/table02.png)](autosizing-row-height-images/table02.png#lightbox)

在我們的範例資料表中， `Label`) 下的小 (`Title` 是可壓縮和成長以增加或減少資料列高度的欄位。 若要達到此效果，請新增下列條件約束，以釘選標籤的左、右、頂端和底部：

[![這些條件約束可釘選標籤的左邊、右邊、頂端和底部](autosizing-row-height-images/table03.png)](autosizing-row-height-images/table03.png#lightbox)

既然我們已完全限制儲存格中的元素，就必須明確地說明應該延展的元素。 若要這樣做，請 **在 [屬性**] 面板的 [配置] 區段中，視需要設定 **內容 Hugging 優先順序** 和 **內容壓縮阻力**：

[![屬性面板的版面配置區段](autosizing-row-height-images/table03a.png)](autosizing-row-height-images/table03a.png#lightbox)

設定您想要擴充的元素，使其具有 **較低** 的 Hugging 優先權值和 **較低** 的壓縮阻力優先權值。

接下來，我們需要選取資料格原型，並為它提供唯一 **識別碼**：

[![將唯一識別碼提供給資料格原型](autosizing-row-height-images/table04.png)](autosizing-row-height-images/table04.png#lightbox)

在我們的範例中為 `GrowCell` 。 稍後當我們填入資料表時，將會使用此值。

> [!IMPORTANT]
> 如果您的資料表包含一個以上的資料格類型 (**原型**) ，您必須確定每個類型都有自己的唯一， `Identifier` 自動資料列調整大小才能運作。

針對我們的資料格原型的每個元素，指派 **名稱** 以將其公開給 c # 程式碼。 例如：

[![指派名稱以將其公開給 c # 程式碼](autosizing-row-height-images/table05.png)](autosizing-row-height-images/table05.png#lightbox)

接下來，為 `UITableViewController` 、 `UITableView` 和 `UITableCell` (原型) 加入自訂類別。 例如：

[![新增 UITableViewController、Ios uitableview 範例和 UITableCell 的自訂類別](autosizing-row-height-images/table06.png)](autosizing-row-height-images/table06.png#lightbox)

最後，為了確保所有預期的內容都會顯示在標籤中，請將 [ **線條** ] 屬性設定為 `0` ：

[![[線條] 屬性設定為0](autosizing-row-height-images/table06.png)](autosizing-row-height-images/table06a.png#lightbox)

定義 UI 之後，讓我們新增程式碼，以啟用自動資料列高度調整大小。

## <a name="enabling-auto-resizing-height"></a>啟用自動調整大小高度

在資料表視圖的 Datasource (`UITableViewDatasource`) 或來源 () 中 `UITableViewSource` ，當我們將儲存格清除佇列時，我們必須使用在 `Identifier` 設計工具中定義的。 例如：

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

根據預設，資料表視圖會設定為自動調整大小的資料列高度。 為了確保這一點， `RowHeight` 屬性應該設定為 `UITableView.AutomaticDimension` 。 我們也需要 `EstimatedRowHeight` 在中設定屬性 `UITableViewController` 。 例如：

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

這項估計不一定是精確的，只是資料表視圖中每個資料列平均高度的粗略估計。

使用此程式碼時，在執行應用程式時，每個資料列都會根據資料格原型中最後一個標籤的高度來縮減和成長。 例如：

[![執行的範例資料表](autosizing-row-height-images/table07.png)](autosizing-row-height-images/table07.png#lightbox)

## <a name="related-links"></a>相關連結

- [GrowRowTable (範例) ](/samples/xamarin/ios-samples/growrowtable)