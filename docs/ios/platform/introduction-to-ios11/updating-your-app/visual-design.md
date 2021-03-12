---
title: IOS 11 中的視覺設計更新
description: 本檔說明 iOS 11 中引進的視覺設計更新。 其中討論巡覽列、搜尋控制器、邊界、全螢幕內容和資料表視圖的變更。
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 06a322a92158cf413e7b33109806010c068cf733
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602953"
---
# <a name="visual-design-updates-in-ios-11"></a>IOS 11 中的視覺設計更新

在 iOS 11 中，Apple 引進了新的視覺變更，包括對巡覽列、搜尋列和資料表視圖的更新。 此外，還提供更多的彈性，讓您可以更靈活地使用邊界和全螢幕內容。 本指南涵蓋這些變更。 

如需專為 iPhone X 設計的詳細資訊，請觀賞 Apple 的 [Iphone x 影片設計](https://developer.apple.com/videos/play/fall2017/801/) 。

## <a name="uikit"></a>UIKit

UIKit 橫條已在 iOS 11 中調整，讓使用者更容易存取。

其中一項變更就是當使用者長按橫條圖專案時，所顯示的新抬頭顯示器顯示。 若要啟用此功能，請 `largeContentSizeImage` 在上設定屬性， `UIBarItem` 並透過 [資產目錄](~/ios/app-fundamentals/images-icons/displaying-an-image.md)新增較大的影像：

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>巡覽列
iOS 11 引進了新功能，可讓您更容易閱讀巡覽列標題。 應用程式可以藉由將屬性指派給 true 來顯示此較大的標題 `PrefersLargeTitles` ：

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

在您的應用程式中設定較大的標題，會讓應用程式中的 _所有_ 導覽列標題變大，如下列螢幕擷取畫面所示：

![大型導覽標題](visual-design-images/image7.png)

若要控制在導覽列上顯示大型標題的時間，請將 `LargeTitleDisplayMode` 導覽專案上的設 `Always` 為、 `Never` 或 `Automatic` 。

### <a name="search-controller"></a>搜尋控制器

iOS 11 可讓您更輕鬆地將搜尋控制器直接新增至導覽列。 一旦您建立了搜尋控制器，請使用屬性將它新增至您的巡覽列 `SearchController` ：

```csharp
NavigationItem.SearchController = searchController;
```

[![使用搜尋列的大型導覽標題](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

根據您應用程式的功能，您可能會想要在使用者滾動清單時隱藏搜尋列。 您可以使用屬性來進行調整 `HidesSearchBarWhenScrolling` 。

## <a name="margins"></a>邊界

Apple 已建立新的屬性– `directionalLayoutMargins` -可用來設定 views 與子檢視之間的空間。 使用 `directionalLayoutMargins` with `leading` 或內凹 `trailing` 。 無論系統是由左至右還是由右至左的語言，您應用程式中的間距都是由 iOS 適當設定。

在 iOS 10 和之前的範圍中，所有的 views 都有與其對齊的最小邊界大小。 iOS 11 引進了使用覆寫該選項的選項 `ViewRespectsSystemMinimumLayoutMargins` 。 例如，將此屬性設定為 false，可讓您將邊緣內凹調整為零：

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```

![顯示 ios 11 中有零個內凹邊界的影像](visual-design-images/image9.png)

<a name="fullscreen"></a>

## <a name="full-screen-content"></a>全螢幕內容

iOS 7 [引進](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen) `topLayoutGuide` `bottomLayoutGuide` 了一種限制您的視圖，使其不會被 UIKit 橫條隱藏，而且會出現在螢幕的可見區域中。 這些在 iOS 11 中已被取代為 _安全區域_。

安全區域是一種新的方式，可考慮應用程式的可見空間，以及如何在視圖與超級視圖之間加入條件約束。 例如，假設有下列影像：

[![安全區域 vs 頂端和底部版面配置指南](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

先前，如果您已加入一個視圖，而且想要在上面的綠色區域中看到它，則會將它限制在 `TopLayoutGuide` 和 _頂端_ 的底部 `BottomLayoutGuide` 。 在 iOS 11 中，您會改為將它限制在安全區域的 _頂端_ 和 _底部_ 。 範例如下：

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>資料表檢視

Ios uitableview 範例在 iOS 11 中有幾個小型但重大的變更。

根據預設，頁首、頁尾和資料格現在會根據其內容自動調整大小。 若要退出這個自動調整大小行為 `EstimatedRowHeight` ，請將、 `EstimatedSectionHeaderHeight` 或設定 `EstimatedSectionFooterHeight` 為零。

不過，在某些情況下 (例如在使用介面產生器中的現有分鏡腳本時新增 UITableViewController 時) 您可能需要手動啟用自動調整大小的儲存格。 若要這樣做，請確定您已在資料表視圖上分別針對資料格、標頭和頁尾設定下列屬性：

```csharp
// Cells
TableView.RowHeight = UITableView.AutomaticDimension;
TableView.EstimatedRowHeight = UITableView.AutomaticDimension;

// Header
TableView.SectionHeaderHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionHeaderHeight = 40f;

//Footer
TableView.SectionFooterHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionFooterHeight = 40f;

```

iOS 11 已擴充資料列動作的功能。 `UISwipeActionsConfiguration` 已導入來定義一組當使用者在資料表視圖中的資料列上撥動任何方向時應採取的動作。 此行為類似于原生的郵件應用程式。 如需詳細資訊，請參閱資料 [列動作](~/ios/user-interface/controls/tables/row-action.md) 指南。

資料表視圖支援 iOS 11 中的拖放功能。 如需詳細資訊，請參閱 [拖放](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview) 指南。

## <a name="related-links"></a>相關連結

- [IOS 11 (Apple) 的新功能 ](https://developer.apple.com/ios/)
- [已更新 (Apple) 的 App Store 產品頁面 ](https://developer.apple.com/app-store/product-page/)
- [設計 iPhone X (Apple)  (影片) ](https://developer.apple.com/videos/play/fall2017/801/)
- [更新 iOS 11 的應用程式 (WWDC)  (影片) ](https://developer.apple.com/videos/play/wwdc2017/204/)
