---
title: "視覺化設計的更新"
description: "瀏覽 iOS 11 的新功能"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: d66d8cd722aa9a7b6fe27db3f6128ee24309a1de
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="visual-design-updates"></a>視覺化設計的更新

_瀏覽 iOS 11 的新功能_

在 iOS 11 中，Apple 引進了新的視覺效果變更，包括瀏覽列、 搜尋列和資料表檢視更新。 此外已改良上邊界和全螢幕的內容，以便更大的彈性。 本指南涵蓋這些變更。

## <a name="uikit"></a>UIKit

UIKit 軸已修正在 iOS 11，以使其更容易存取的使用者。

這類的一項變更是新抬頭顯示器顯示時使用者長時間-按列上出現的項目。 若要啟用此功能，設定`largeContentSizeImage`屬性`UIBarItem`新增較大的映像，透過[資產目錄](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>巡覽列
iOS 11 導入了新功能可以讓您更容易讀取導覽列標題。 應用程式可以顯示這個較大的項目，藉由指派`PrefersLargeTitles`屬性設定為 true:

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

可讓您的應用程式中設定大標題_所有_跨應用程式的導覽列標題越大，如下列螢幕擷取畫面所示：

![瀏覽大型標題](visual-design-images/image7.png)

若要控制當大型標題會顯示在導覽列上，設定`LargeTitleDisplayMode`上的導覽項目`Always`， `Never`，或`Automatic`。

### <a name="search-controller"></a>搜尋控制站

iOS 11 變成您更輕鬆地瀏覽列中直接加入搜尋控制站。 當您建立的搜尋控制站之後時，將它加入至您的導覽列與`SearchController`屬性：

```csharp
NavigationItem.SearchController = searchController;
```

[![使用搜尋列中的大型瀏覽標題](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

根據您的應用程式的功能，您可能會或可能不想在使用者捲動清單時，要隱藏的 [搜尋] 列。 您可以調整這個設定使用`HidesSearchBarWhenScrolling`屬性。

## <a name="margins"></a>邊界

Apple 已建立新的屬性 – `directionalLayoutMargins` –，可以用來設定檢視和 subviews 之間的間距。 使用`directionalLayoutMargins`與`leading`或`trailing`內凹。 不論系統是由左到右或從右至左的語言，在您的應用程式中的間距會適當地 ios 設定。

在 iOS 10 且之前，所有檢視都有它們會對齊最小邊界大小。 iOS 11 導入的選項來覆寫該使用`ViewRespectsSystemMinimumLayoutMargins`。 例如，將這個屬性設定為 false 可讓您調整您的邊緣內凹為零：

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```
![具有零內凹 ios 11 中的影像顯示邊界](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>全螢幕的內容

iOS 7[導入](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen)`topLayoutGuide`和`bottomLayoutGuide`這種限制您的檢視，使它們不會隱藏由 UIKit 長條圖，且會在螢幕的可見區域。 這些已被取代之喜好 iOS 11 中_安全區域_。

安全的區域是新的方式來思考應用程式的方式檢視和超級檢視之間加入條件約束可見的空間。 例如，請考慮下列影像：

[![安全區域 vs 頂端和底部的版面配置輔助](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

過去，如果您加入檢視，它會顯示在上方的綠色區域想，您會限制它_底部_的`TopLayoutGuide`和_頂端_的`BottomLayoutGuide`。 在 iOS 11 中，您會改為限制它_頂端_和_底部_的安全區域。 範例如下：

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>資料表檢視

UITableView iOS 11 中已數目很小，但重要的變更。

根據預設，頁首、 頁尾和資料格現在會自動調整大小根據其內容。 若要退出此自動調整大小行為集`EstimatedRowHeight`， `EstimatedSectionHeaderHeight`，或`EstimatedSectionFooterHeight`為零。

不過，在某些情況下 (例如當加入 UITableViewController iOS 設計工具中，或在介面產生器中使用現有 Storboards 時) 您可能需要手動啟用自我調整大小的資料格。 若要這樣做，請確定您已設定下列屬性的資料表檢視的資料格、 頁首和頁尾，分別：

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

iOS 11 擴充功能的資料列的動作。 `UISwipeActionsConfiguration` 已導入定義的一組使用者 swipes 任一方向的表格檢視中的資料列時的動作。 此行為是類似於原生 Mail.app。 如需詳細資訊，請參閱[列動作](~/ios/user-interface/controls/tables/row-action.md)指南。

資料表檢視有支援拖放 iOS 11。 如需詳細資訊，請參閱[拖曳和卸除](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview)指南。


## <a name="related-links"></a>相關連結

- [什麼是 iOS (Apple) 11 中的新功能](https://developer.apple.com/ios/)
- [更新應用程式市集產品網頁 (Apple)](https://developer.apple.com/app-store/product-page/)
- [正在更新您的應用程式適用於 iOS 11 (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/204/)
