---
title: IOS 11 中的視覺化設計更新
description: 本檔描述 iOS 11 中引進的視覺化設計更新。 它討論導覽列、搜尋控制器、邊界、全螢幕內容和表格視圖的變更。
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: e5a61af4cd8a09df3ffddb74658f646aa8edfa1f
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304755"
---
# <a name="visual-design-updates-in-ios-11"></a>IOS 11 中的視覺化設計更新

在 iOS 11 中，Apple 引進了新的視覺效果變更，包括對導覽列、搜尋列和資料表視圖的更新。 此外，也提供了改進功能，讓邊界和全螢幕內容更具彈性。 本指南涵蓋這些變更。 

如需專為 iPhone X 設計的相關資訊，請觀賞 Apple 的[Iphone x Video 設計](https://developer.apple.com/videos/play/fall2017/801/)。

## <a name="uikit"></a>UIKit

UIKit 橫條已在 iOS 11 中進行調整，讓使用者更容易存取。

其中一項變更就是新的抬頭顯示器顯示，會在使用者長按一列專案時出現。 若要啟用此功能，請在 `UIBarItem` 上設定 `largeContentSizeImage` 屬性，並透過[資產目錄](~/ios/app-fundamentals/images-icons/displaying-an-image.md)新增較大的影像：

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>巡覽列
iOS 11 引進了新功能，讓巡覽列標題更容易閱讀。 應用程式可以將 `PrefersLargeTitles` 屬性指派為 true，以顯示這個較大的標題：

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

在您的應用程式中設定較大的標題，會使應用程式中的_所有_導覽列標題變大，如下列螢幕擷取畫面所示：

![大型導覽標題](visual-design-images/image7.png)

若要控制在導覽列上顯示大型標題的時間，請將導覽專案上的 `LargeTitleDisplayMode` 設定為 [`Always`]、[`Never`] 或 [`Automatic`]。

### <a name="search-controller"></a>搜尋控制器

iOS 11 可讓您更輕鬆地將搜尋控制器直接新增至導覽列。 一旦您建立了搜尋控制器，請使用 [`SearchController`] 屬性將它新增至您的導覽列：

```csharp
NavigationItem.SearchController = searchController;
```

[使用搜尋列 ![大型導覽標題](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

視您應用程式的功能而定，您可能會想要在使用者滾動清單時隱藏搜尋列。 您可以使用 `HidesSearchBarWhenScrolling` 屬性來進行調整。

## <a name="margins"></a>邊界

Apple 已建立新的屬性– `directionalLayoutMargins`，可用來設定 views 和子檢視之間的間距。 搭配 `leading` 或 `trailing` 內凹使用 `directionalLayoutMargins`。 無論系統是由左至右或由右至左的語言，您的應用程式中的間距都會由 iOS 適當設定。

在 iOS 10 和之前，所有視圖的最小邊界大小都會對齊。 iOS 11 引進了使用 `ViewRespectsSystemMinimumLayoutMargins`覆寫它的選項。 例如，將此屬性設定為 false，可讓您將 edge 內凹調整為零：

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```

![在 ios 11 中顯示具有零內陷的邊界的影像](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>全螢幕內容

iOS 7[引進](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen)了 `topLayoutGuide` 和 `bottomLayoutGuide` 的方式，以限制您的視圖，使其不會被 UIKit 的橫條隱藏，而且是在螢幕的可見區域中。 這些已在 iOS 11 中被取代為_安全區域_。

[安全] 區域是思考應用程式可見空間的新方法，以及如何在視圖和超級視圖之間加入條件約束。 例如，請考慮下列影像：

[![安全區域與頂端和底部的版面配置指南](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

先前，如果您已新增一個視圖，並想要在上面的綠色區域中看到它，您會將它限制在 `TopLayoutGuide` 的_底部_和 `BottomLayoutGuide`的_頂端_。 在 iOS 11 中，您會改為將它限制在安全區域的_頂端_和_底部_。 範例如下：

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>資料表視圖

UITableView 在 iOS 11 中有幾個小型但重大的變更。

根據預設，標頭、頁尾和資料格現在會依據其內容自動調整大小。 若要退出宣告此自動調整大小行為，請將 `EstimatedRowHeight`、`EstimatedSectionHeaderHeight`或 `EstimatedSectionFooterHeight` 設定為零。

不過，在某些情況下（例如，在 iOS 設計工具中新增 UITableViewController 時，或在 Interface Builder 中使用現有的 Storboards 時），您可能需要手動啟用自行調整大小的儲存格。 若要執行這項操作，請確定您已在資料表上分別針對資料格、標頭和頁尾設定下列屬性：

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

iOS 11 已擴充資料列動作的功能。 引進了 `UISwipeActionsConfiguration`，以定義當使用者在資料表視圖中的資料列上撥動任一方向時，應採取的一組動作。 此行為類似于原生郵件應用程式。 如需詳細資訊，請參閱資料[列動作](~/ios/user-interface/controls/tables/row-action.md)指南。

資料表視圖支援 iOS 11 中的拖放功能。 如需詳細資訊，請參閱[拖放](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview)指南。

## <a name="related-links"></a>相關連結

- [IOS 11 （Apple）的新功能](https://developer.apple.com/ios/)
- [已更新 App Store 產品頁面（Apple）](https://developer.apple.com/app-store/product-page/)
- [針對 iPhone X （Apple）設計（影片）](https://developer.apple.com/videos/play/fall2017/801/)
- [更新 iOS 11 應用程式（WWDC）（影片）](https://developer.apple.com/videos/play/wwdc2017/204/)
