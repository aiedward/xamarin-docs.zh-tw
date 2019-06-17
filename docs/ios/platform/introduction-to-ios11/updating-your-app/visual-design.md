---
title: IOS 11 中的視覺效果設計更新
description: 本文件說明於 iOS 11 推出更新的視覺化設計。 它討論變更導覽、 搜尋控制站、 邊界、 全螢幕的內容，以及資料表檢視。
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: c6351f2c25f8e31181c761aea1b471315a8a05e8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400176"
---
# <a name="visual-design-updates-in-ios-11"></a>IOS 11 中的視覺效果設計更新

在 iOS 11 中，Apple 已引進新的視覺變更，包括更新的導覽列、 搜尋列和資料表檢視。 除了進行了改進以提供更大的彈性，邊界和全螢幕的內容。 本指南會說明這些變更。 

特別是有關適用於 iPhone X 的設計資訊，觀看 Apple[設計適用於 iPhone X](https://developer.apple.com/videos/play/fall2017/801/)視訊。

## <a name="uikit"></a>UIKit

UIKit 列已修正在 iOS 11，使其更容易存取的使用者。

這一項改變是使用者長時間-動作列上時，會出現新抬頭顯示器顯示項目。 若要啟用此功能，將`largeContentSizeImage`上的屬性`UIBarItem`並加入較大的映像，透過[資產目錄](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>巡覽列
iOS 11 導入了新的功能，以便於讀取導覽列標題。 應用程式可以顯示這個較大的項目，藉由指派`PrefersLargeTitles`屬性設為 true:

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

您的應用程式中設定較大的項目可讓_所有_跨您的應用程式的導覽列標題出現較大，如下列螢幕擷取畫面所示：

![大型的瀏覽標題](visual-design-images/image7.png)

若要控制的大型標題顯示在瀏覽列上時，設定`LargeTitleDisplayMode`上的導覽項目`Always`， `Never`，或`Automatic`。

### <a name="search-controller"></a>搜尋控制站

iOS 11 有更輕鬆地導覽列中直接加入的搜尋控制站。 當您建立的搜尋控制站之後時，將它新增至您的導覽列與`SearchController`屬性：

```csharp
NavigationItem.SearchController = searchController;
```

[![使用搜尋列中的大型的瀏覽標題](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

根據您的應用程式的功能，您可能會或可能不想使用者捲動清單時，要隱藏的 [搜尋] 列。 您可以使用來調整這`HidesSearchBarWhenScrolling`屬性。

## <a name="margins"></a>邊界

Apple 建立的新屬性 – `directionalLayoutMargins` –，可以用來設定檢視和子檢視之間的間距。 使用`directionalLayoutMargins`具有`leading`或`trailing`內凹。 不論系統是由左到右或從右至左的語言，在您的應用程式中的間距會適當地依 iOS 設定。

在 iOS 10，且之前，所有的檢視必須予以會對齊最小邊界大小。 iOS 11 導入的選項來覆寫的方法使用`ViewRespectsSystemMinimumLayoutMargins`。 比方說，此屬性設定為 false 可讓您調整您的 edge 內凹，為零：

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```
![使用零內凹 ios 11 中的影像顯示邊界](visual-design-images/image9.png)

<a name="fullscreen" />

## <a name="full-screen-content"></a>全螢幕的內容

iOS 7[引進](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen)`topLayoutGuide`和`bottomLayoutGuide`這種限制您的檢視，使他們並未隱藏 UIKit 橫條和位於螢幕的可見區域。 這些已被取代的益處的 iOS 11 中_安全區域_。

安全區域是思考的可見的空間，您的應用程式，以及如何檢視和進階的檢視之間加入條件約束的新方式。 例如，請考慮下列映像：

[![安全區域與頂端和底端版面配置輔助線](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

先前，如果您已新增檢視，希望它會顯示在上方的綠色區域，您會限制它_底部_的`TopLayoutGuide`並_頂端_的`BottomLayoutGuide`。 在 iOS 11 中，您會改為限制它_頂端_而_底部_的安全區域。 範例如下：

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>資料表檢視

UITableView iOS 11 中有一些小，卻很重要，變更。

根據預設，標頭、 頁尾和資料格現在會自動調整大小根據其內容。 若要退出此自動調整大小行為組`EstimatedRowHeight`， `EstimatedSectionHeaderHeight`，或`EstimatedSectionFooterHeight`為零。

不過，在某些情況下 (例如當新增 UITableViewController，iOS 設計工具中，或使用現有的 Storboards Interface Builder 中時) 您可能需要以手動方式啟用自我調整大小的資料格。 若要這樣做，請確定您已設定下列屬性的資料表檢視儲存格、 標題和頁尾，分別：

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

iOS 11 擴充功能的資料列的動作。 `UISwipeActionsConfiguration` 引進了以定義一組使用者 swipes 任一方向中的表格檢視的資料列時，應該會發生的動作。 此行為是類似於原生 Mail.app。 如需詳細資訊，請參閱 <<c0> [ 資料列動作](~/ios/user-interface/controls/tables/row-action.md)指南。

資料表檢視都支援拖放 iOS 11。 如需詳細資訊，請參閱 <<c0> [ 拖放](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview)指南。


## <a name="related-links"></a>相關連結

- [新功能 iOS 11 (Apple)](https://developer.apple.com/ios/)
- [更新應用程式市集產品頁面 (Apple)](https://developer.apple.com/app-store/product-page/)
- [設計適用於 iPhone X (Apple) （影片）](https://developer.apple.com/videos/play/fall2017/801/)
- [更新您的應用程式，適用於 iOS 11 (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/204/)

