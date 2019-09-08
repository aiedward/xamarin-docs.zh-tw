---
title: Xamarin 中的整合分鏡腳本
description: 本檔說明 Xamarin 中的整合分鏡腳本。 統一的分鏡腳本可讓開發人員以單一介面定義支援多個螢幕大小。
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/20/2017
ms.openlocfilehash: 07a61eb9d0c16f82d6c367cefc9e3050ca8dfc25
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768824"
---
# <a name="unified-storyboards-in-xamarinios"></a>Xamarin 中的整合分鏡腳本

iOS 8 包含新的、更簡單易用的機制，可建立使用者介面，也就是整合的分鏡腳本。 透過單一分鏡腳本來涵蓋所有不同的硬體螢幕大小，您可以在「設計一次，使用-多」的樣式中建立快速且回應迅速的觀點。

由於開發人員不再需要為 iPhone 和 iPad 裝置建立個別的特定分鏡腳本，因此他們可以彈性地使用通用介面來設計應用程式，然後針對不同大小的類別自訂該介面。 如此一來，應用程式可以調整為每個外型規格的優勢，而且每個使用者介面都可以微調以提供最佳的體驗。

<a name="size-classes" />

## <a name="size-classes"></a>大小類別

在 iOS 8 之前，開發人員使用`UIInterfaceOrientation`和`UIInterfaceIdiom`來區分直向和橫向模式，以及 iPhone 與 iPad 裝置之間的區別。 在 iOS8 中，會使用*大小類別*來決定方向和裝置。

裝置是由大小類別以垂直和水準軸定義，而 iOS 8 中有兩種大小類別：

- **一般**–這適用于大型螢幕大小（例如 iPad）或小工具，可提供大大小的印象（例如`UIScrollView`
- **Compact** –這適用于較小的裝置（例如 iPhone）。 此大小會考慮裝置的方向。

如果同時使用這兩個概念，結果會是 2 x 2 方格，定義可用於不同方向的各種可能大小，如下圖所示：

 [![](unified-storyboards-images/sizeclassgrid.png "2 x 2 方格，定義可用於一般和精簡方向的各種可能大小")](unified-storyboards-images/sizeclassgrid.png#lightbox)

開發人員可以建立一個使用可能會導致不同版面配置（如上圖所示）之四種可能性的視圖控制器。

### <a name="ipad-size-classes"></a>iPad 大小類別

由於大小的緣故，iPad 具有這兩個方向的**一般**類別大小。

 [![](unified-storyboards-images/image1.png "iPad 大小類別")](unified-storyboards-images/image1.png#lightbox)

### <a name="iphone-size-classes"></a>iPhone 大小類別

IPhone 根據裝置的方向而有不同大小的類別：

 [![](unified-storyboards-images/iphonesizeclasses.png "iPhone 大小類別")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

- 當裝置處於直向模式時，螢幕會**水準且垂直**地具有**精簡**類別
- 當裝置處於橫向模式時，螢幕類別會從直向模式反轉。

### <a name="iphone-6-plus-size-classes"></a>iPhone 6 加上大小類別

大小與較舊的 Iphone 在直向時相同，但在橫向中不同：

[![](unified-storyboards-images/iphone6sizeclasses.png "iPhone 6 加上大小類別")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

由於 iPhone 6 Plus 具有夠大的螢幕，因此在橫向模式中可以有一般寬度大小的類別。

### <a name="support-for-a-new-screen-scale"></a>支援新的螢幕縮放

IPhone 6 Plus 使用新的 Retina HD 顯示器，螢幕縮放比例為3.0 （原始 iPhone 螢幕解析度的三倍）。 若要在這些裝置上提供最佳的體驗，請加入專為此螢幕縮放而設計的新作品。 在 Xcode 6 和更新版本中，資產目錄可以包含介於1x、2x 和3倍的影像;只要新增影像資產，iOS 就會在 iPhone 6 Plus 上執行時，選擇正確的資產。

IOS 中的映射載入行為也會辨識`@3x`影像檔案的尾碼。 例如，如果開發人員在應用程式的配套中包含具有下列檔案名的影像資產（以不同的解析度）： `MonkeyIcon.png`、 `MonkeyIcon@2x.png`和`MonkeyIcon@3x.png`。 在 iPhone 6 加上， `MonkeyIcon@3x.png`如果開發人員使用下列程式碼載入影像，就會自動使用此影像：

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```

`MonkeyIcon.png`或者`MonkeyIcon@3x.png` ，如果使用 iOS 設計工具將影像指派給 UI 元素，則會在 iPhone 6 Plus 上再次使用。

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>動態啟動畫面

啟動畫面檔案會在 iOS 應用程式啟動時顯示為啟動顯示畫面，以提供對使用者實際啟動應用程式的意見反應。 在 iOS 8 之前，開發人員必須針對應用程式執行`Default.png`所在的每種裝置類型、方向和螢幕解析度，包含多個映射資產。

IOS 8 的新手，開發人員可以在 Xcode 中建立單一`.xib` 、不可部分完成的檔案，該檔案使用自動設定和大小類別來建立*動態啟動畫面*，適用于每個裝置、解析度和方向。 這不僅能減少開發人員建立和維護所有必要映射資產所需的工作量，還能減少應用程式已安裝的配套大小。

## <a name="traits"></a>特性

特性是可以用來判斷版面配置如何隨著環境變更而變更的屬性。 其包含一組屬性`HorizontalSizeClass` （和`VerticalSizeClass`以為基礎`UIUserInterfaceSizeClass`），以及介面的用法（ `UIUserInterfaceIdiom`）和顯示比例。

上述所有狀態都會包裝在 Apple 視為特性集合（ `UITraitCollection`）的容器中，其中不僅包含屬性，還包含其值。

## <a name="trait-environment"></a>特性環境

特性環境是 iOS 8 中的新介面，而且能夠傳回下列物件的特性集合：

- 畫面（ `UIScreens` ）。
- Windows （ `UIWindows` ）。
- 視圖控制器（ `UIViewController` ）。
- Views （ `UIView` ）。
- 展示控制器（ `UIPresentationController` ）。

開發人員使用特性環境所傳回的特性集合，以決定應該如何配置使用者介面。

所有特性環境都會構成階層，如下圖所示：

 [![](unified-storyboards-images/viewhierarchy.png "特性環境階層圖")](unified-storyboards-images/viewhierarchy.png#lightbox)

根據預設，上述每個特性環境都有的特性集合會從父系流向子環境。

除了取得目前的特性集合之外，特性環境還具有方法， `TraitCollectionDidChange`可以在 view 或 view Controller 子類別中覆寫。 開發人員可以使用這個方法，在這些特性變更時，修改相依于特性的任何 UI 元素。

## <a name="typical-trait-collections"></a>一般特性集合

本節將涵蓋使用者在使用 iOS 8 時將會經歷的一般特性集合類型。

以下是開發人員可能會在 iPhone 上看到的一般特性集合：

|屬性|值|
|--- |--- |
|`HorizontalSizeClass`|小巧|
|`VerticalSizeClass`|Regular|
|`UserInterfaceIdom`|Phone|
|`DisplayScale`|2.0|

上述集合會代表完整的特性集合，因為它有其所有特性屬性的值。

特性集合也可能缺少某些值（Apple 將其稱為*未指定*）：

|屬性|值|
|--- |--- |
|`HorizontalSizeClass`|小巧|
|`VerticalSizeClass`|未指定|
|`UserInterfaceIdom`|未指定|
|`DisplayScale`|未指定|

不過，一般而言，當開發人員詢問特性環境的特性集合時，它會傳回完整的集合，如上述範例所示。

如果特性環境（例如視圖或視圖控制器）不在目前的視圖階層內，開發人員可能會針對一或多個特性屬性傳回未指定的值。

如果開發人員使用 Apple 提供的其中一種建立方法（例如`UITraitCollection.FromHorizontalSizeClass`）來建立新的集合，則也會取得部分合格的特性集合。

可以在多個特性集合上執行的一項作業，是將它們彼此進行比較，包括詢問一個特性集合（如果它包含另一個特性）。 內含*專案的意義是，* 針對第二個集合中指定的任何特性，此值必須與第一個集合中的值完全相符。

若要測試兩個特性`Contains` ，請使用`UITraitCollection`傳入要測試之特徵值的方法。

開發人員可以在程式碼中手動執行比較，以決定如何配置視圖或視圖控制器。 不過， `UIKit`會在內部使用此方法來提供部分功能，例如外觀 Proxy。

## <a name="appearance-proxy"></a>外觀 Proxy

外觀 Proxy 是在舊版 iOS 中引進，可讓開發人員自訂其 Views 的屬性。 它已在 iOS 8 中擴充，以支援特性集合。

外觀 Proxy 現在包含新的方法， `AppearanceForTraitCollection`它會針對已傳入的指定特性集合傳回新的外觀 Proxy。 開發人員在該外觀 Proxy 上執行的任何自訂，只會在符合指定特性集合的視圖上生效。

一般而言，開發人員會將部分指定的特性集合傳遞給`AppearanceForTraitCollection`方法，例如，只指定 compact 的水準大小類別，以便在應用程式中自訂水準精簡的任何視圖。

## <a name="uiimage"></a>UIImage

另一個 Apple 已新增特性集合的類別為`UIImage`。 在過去，開發人員必須指定要包含@1X在@2x應用程式中的任何點陣圖圖形資產的和版本（例如圖示）。

iOS 8 已擴充為可讓開發人員根據特性集合，在影像目錄中包含多個版本的映射。 例如，開發人員可能會包含較小的影像，以使用精簡的特性類別和任何其他集合的完整大小影像。

在`UIImageView`類別內使用其中一個影像時，影像視圖會自動顯示其特性集合的正確映射版本。 如果特性環境變更（例如使用者將裝置從直向垂直切換到橫向），則影像視圖會自動選取新的影像大小以符合新的特性集合，並變更其大小以符合目前的映射版本看到.

## <a name="uiimageasset"></a>UIImageAsset

Apple 已將新的類別新增至 iOS 8 `UIImageAsset` ，稱為，讓開發人員更充分掌控影像選取專案。

影像資產會匯總映射的所有不同版本，並可讓開發人員要求符合已傳入之特性集合的特定影像。 影像資產可以即時新增或移除映射。

如需影像資產的詳細資訊，請參閱 Apple 的[UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)檔。

## <a name="combining-trait-collections"></a>結合特性集合

開發人員可以在特性集合上執行的另一個函式，是將會產生合併集合的兩個函式相加，其中一個集合中未指定的值會由第二個集合中的指定值所取代。 這是使用`FromTraitsFromCollections` `UITraitCollection`類別的方法來完成。

如上所述，如果其中一個特性集合中未指定任何特徵，而且指定于另一個，則值將會設定為指定的版本。 不過，如果指定的值有多個版本，則最後一個特性集合中的值將會是所使用的值。

## <a name="adaptive-view-controllers"></a>調適型視圖控制器

本節將涵蓋 iOS 視圖和視圖控制器如何採用特性和大小類別的概念，以在開發人員的應用程式中自動增加彈性的詳細資訊。

### <a name="split-view-controller"></a>分割視圖控制器

在 iOS 8 中已變更最大的其中一個 View Controller 類別就是`UISplitViewController`類別。 在過去，開發人員通常會在 iPad 版的應用程式上使用分割視圖控制器，然後他們必須針對 iPhone 版本的應用程式提供完全不同版本的視圖階層。

在 iOS 8 `UISplitViewController`中，類別適用于兩種平臺（iPad 和 iPhone），可讓開發人員建立一個可在 iPhone 和 iPad 上運作的視圖控制器階層。

當 iPhone 處於橫向時，分割視圖控制器會並排顯示其 Views，就如同在 iPad 上顯示一樣。

### <a name="overriding-traits"></a>覆寫特性

特徵環境從父容器向下級聯到子容器，如下圖所示，以橫向顯示iPad上的分割視圖控制器：

 [![](unified-storyboards-images/cascadingclasses01.png "IPad 上的分割視圖控制器（橫向）")](unified-storyboards-images/cascadingclasses01.png#lightbox)

由於 iPad 具有水準和垂直方向的一般大小類別，因此分割視圖會同時顯示主要和詳細資料檢視。

在 iPhone 上，[大小] 類別在兩個方向都是精簡的，分割視圖控制器只會顯示詳細資料檢視，如下所示：

 [![](unified-storyboards-images/cascadingclasses02.png "分割視圖控制器只會顯示詳細資料檢視")](unified-storyboards-images/cascadingclasses02.png#lightbox)

在開發人員希望在橫向顯示iPhone上的主視圖和詳細視圖的應用程序中，開發人員必須為分割視圖控制器插入父容器並覆蓋特徵集合。 如下圖所示：

 [![](unified-storyboards-images/cascadingclasses03.png "開發人員必須插入分割視圖控制器的父容器，並覆寫特性集合")](unified-storyboards-images/cascadingclasses03.png#lightbox)

會設定為分割視圖控制器的父系`SetOverrideTraitCollection` ，並在此視圖上呼叫方法，傳入新的特性集合，並以分割視圖控制器為目標。 `UIView` 新的特徵集合將覆蓋`HorizontalSizeClass`，設置為`Regular`，以便分割視圖控制器將以橫向顯示iPhone上的主視圖和詳細視圖。

請注意， `unspecified` `Compact VerticalSizeClass`已設定為，允許將新的特性集合加入至父代上的特性集合，進而產生子分割視圖控制器的。 `VerticalSizeClass`

### <a name="trait-changes"></a>特性變更

本節將詳細說明特性集合在特性環境變更時的轉換方式。 例如，當裝置從直向直向橫向旋轉時。

 [![](unified-storyboards-images/traittransitions01.png "直向橫向特徵變更總覽")](unified-storyboards-images/traittransitions01.png#lightbox)

首先，iOS 8 會進行一些設定，以準備進行轉換。 接下來，系統會以動畫呈現轉換狀態。 最後，iOS 8 會清除轉換期間所需的任何暫時性狀態。

iOS 8 提供幾個回呼，讓開發人員可以用來參與特性變更，如下表所示：

|Phase|回呼|說明|
|--- |--- |--- |
|安裝程式|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>在特徵集合設定為新值之前，會在特性變更的開頭呼叫這個方法。</li><li>當特性集合的值已變更，但在任何動畫發生之前，會呼叫方法。</li></ul>|
|動畫|`WillTransitionToTraitCollection`|傳遞至這個方法的轉換協調器具有`AnimateAlongside`屬性，可讓開發人員加入將與預設動畫一起執行的動畫。|
|清理|`WillTransitionToTraitCollection`|提供一種方法，讓開發人員在轉換之後加入自己的清除程式碼。|

`WillTransitionToTraitCollection`方法非常適合用來以動畫顯示視圖控制器以及特性集合變更。 方法僅適用于 View controller （ `UIViewController`），而不適用於其他特性環境（例如`UIViews`）。 `WillTransitionToTraitCollection`

非常適合用來處理`UIView`類別，而開發人員想要在特性變更時`TraitCollectionDidChange`更新 UI。

### <a name="collapsing-the-split-view-controllers"></a>折迭分割視圖控制器

現在讓我們仔細看一下，當分割視圖控制器從兩個數據行折迭到一個資料行視圖時，會發生什麼事。 在這項變更中，有兩個需要進行的進程：

- 根據預設，分割視圖控制器會在折迭發生後，使用主要視圖控制器做為視圖。 開發人員可以覆寫這個行為`GetPrimaryViewControllerForCollapsingSplitViewController` `UISplitViewControllerDelegate` ，其方式是覆寫的方法，並提供他們想要在折迭狀態中顯示的任何視圖控制器。
- 次要視圖控制器必須合併到主要視圖控制器。 開發人員通常不需要針對此步驟採取任何動作;分割視圖控制器包含以硬體裝置為基礎的此階段自動處理。 不過，在某些特殊情況下，開發人員可能會想要與這種變更進行互動。 呼叫的`UISplitViewControllerDelegate`方法，可在折迭發生時顯示主要視圖控制器，而不是詳細資料檢視。 `CollapseSecondViewController`

### <a name="expanding-the-split-view-controller"></a>展開分割視圖控制器

現在讓我們進一步瞭解當分割視圖控制器從折迭狀態展開時，會發生什麼事。 同樣地，需要進行兩個階段：

- 首先，定義新的主要視圖控制器。 根據預設，分割視圖控制器會自動從折迭的視圖使用主要視圖控制器。 同樣地，開發人員可以使用`GetPrimaryViewControllerForExpandingSplitViewController`的方法`UISplitViewControllerDelegate`來覆寫此行為。
- 選擇主要視圖控制器後，就必須重新建立次要視圖控制器。 同樣地，分割視圖控制器包含以硬體裝置為基礎的此階段自動處理。 開發人員可以藉由呼叫`SeparateSecondaryViewController`的方法`UISplitViewControllerDelegate`來覆寫此行為。

在分割視圖控制器中，主要視圖控制器會藉由執行`CollapseSecondViewController`的和`SeparateSecondaryViewController`方法`UISplitViewControllerDelegate`，在展開和折迭的瀏覽器中播放部分。 `UINavigationController`會執行這些方法，以自動推送和彈出次要視圖控制器。

### <a name="showing-view-controllers"></a>顯示視圖控制器

Apple 對 iOS 8 所做的另一項變更，就是開發人員顯示視圖控制器的方式。 在過去，如果應用程式有分葉視圖控制器（例如資料表視圖控制器），而開發人員顯示了不同的控制項（例如，回應使用者按下資料格），則應用程式會透過控制器階層回到流覽視圖控制器並對其`PushViewController`呼叫方法，以顯示新的視圖。

這在流覽控制器與其執行所在的環境之間呈現非常緊密的結合。 在 iOS 8 中，Apple 藉由提供兩種新方法來將其解除耦合：

- `ShowViewController`–調整以根據其環境顯示新的視圖控制器。 例如，在中`UINavigationController` ，它只會將新的視圖推送至堆疊上。 在分割視圖控制器中，新的視圖控制器會顯示在左側，做為新的主要視圖控制器。 如果沒有容器視圖控制器存在，新的視圖會顯示為強制回應視圖控制器。
- `ShowDetailViewController`-的運作方式與類似`ShowViewController`，但會在分割視圖控制器上執行，以將詳細資料檢視取代為傳入的新視圖控制器。 如果分割視圖控制器已折迭（如 iPhone 應用程式中所示），則會將呼叫重新導向至`ShowViewController`方法，而新的視圖會顯示為主要視圖控制器。 同樣地，如果沒有容器視圖控制器存在，新的視圖就會顯示為強制回應視圖控制器。

這些方法的運作方式是從分葉視圖控制器開始，並向上切入視圖階層，直到找到正確的容器視圖控制器來處理新視圖的顯示為止。

開發人員可以`ShowViewController`在`ShowDetailViewController`自己`UINavigationController`的自訂視圖控制器中執行和，以取得與所`UISplitViewController`提供的相同自動化功能。

### <a name="how-it-works"></a>運作方式

在本節中，我們將探討如何在 iOS 8 中實際實作為這些方法。 首先，我們來看一下新`GetTargetForAction`方法：

 [![](unified-storyboards-images/gettargetforaction.png "新的 GetTargetForAction 方法")](unified-storyboards-images/gettargetforaction.png#lightbox)

這個方法會逐步引導階層鏈，直到找到正確的容器視圖控制器為止。 例如：

1. 如果呼叫`ShowViewController`方法，則執行此方法之鏈中的第一個 View 控制器是「流覽控制器」，因此它會當做新視圖的父系使用。
1. 如果改為呼叫方法，分割視圖控制器就是第一個要執行它的viewcontroller，因此它會當做父系使用。`ShowDetailViewController`

`GetTargetForAction`方法的運作方式是尋找可執行指定動作的視圖控制器，然後在它想要接收該動作時，詢問該視圖控制器。 因為此方法是公用的，所以開發人員可以建立自己的自訂方法，其運作`ShowViewController`方式`ShowDetailViewController`就像內建和方法一樣。

## <a name="adaptive-presentation"></a>調適型簡報

在 iOS 8 中，Apple 也製作了 Popover `UIPopoverPresentationController`簡報（）的自動調整功能。 因此，Popover 簡報視圖控制器會自動在一般大小類別中呈現一般的 Popover 視圖，但會在水準壓縮大小類別（例如 iPhone）中以全螢幕顯示。

為了配合整合的分鏡腳本系統內的變更，已建立一個新的控制器物件來管理呈現的視圖`UIPresentationController`控制器—。 此控制器會從顯示視圖控制器的時間建立，直到其關閉為止。 當它是管理類別時，可以將其視為超級類別，因為它會回應影響視圖控制器的裝置變更（例如方向），然後再送回給簡報控制器所控制的視圖控制器。

當開發人員使用`PresentViewController`方法呈現視圖控制器時，會將呈現流程的管理`UIKit`交給。 UIKit 會處理所建立之樣式的正確控制器，唯一的例外是當視圖控制器的樣式設定為`UIModalPresentationCustom`時。 在這裡，應用程式可以提供它自己的 PresentationController，而不`UIKit`是使用控制器。

### <a name="custom-presentation-styles"></a>自訂呈現樣式

使用自訂的呈現樣式，開發人員可以選擇使用自訂的呈現控制器。 這個自訂控制器可以用來修改所 allied 之視圖的外觀和行為。

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>使用大小類別

本文中所包含的調適型相片 Xamarin 專案，提供在 iOS 8 整合介面應用程式中使用大小類別和適應性視圖控制器的實用範例。

雖然應用程式會從程式碼完全建立其 UI，而不是使用 IOS 設計工具和建立整合的分鏡腳本，但同樣的技術也適用。 在本文稍後，我們將示範如何在 Xamarin 應用程式中搭配使用大小類別與整合的腳本和 iOS 設計工具。

現在讓我們進一步瞭解調適型相片專案如何在 iOS 8 中執行數種大小類別功能，以建立彈性應用程式。

### <a name="adapting-to-trait-environment-changes"></a>適應特性環境變更

在 iPhone 上執行調適型相片應用程式時，當使用者將裝置從直向直向橫向時，分割視圖控制器會同時顯示 [主要] 和 [詳細資料] 視圖：

 [![](unified-storyboards-images/rotation.png "分割視圖控制器會同時顯示 [主要] 和 [詳細資料] 視圖，如下所示")](unified-storyboards-images/rotation.png#lightbox)

這是藉由覆寫`UpdateConstraintsForTraitCollection` View Controller 的方法來完成，並根據的值`VerticalSizeClass`來調整條件約束。 例如：

```csharp
public void UpdateConstraintsForTraitCollection (UITraitCollection collection)
{
    var views = NSDictionary.FromObjectsAndKeys (
        new object[] { TopLayoutGuide, ImageView, NameLabel, ConversationsLabel, PhotosLabel },
        new object[] { "topLayoutGuide", "imageView", "nameLabel", "conversationsLabel", "photosLabel" }
    );

    var newConstraints = new List<NSLayoutConstraint> ();
    if (collection.VerticalSizeClass == UIUserInterfaceSizeClass.Compact) {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide][imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.Add (NSLayoutConstraint.Create (ImageView, NSLayoutAttribute.Width, NSLayoutRelation.Equal,
            View, NSLayoutAttribute.Width, 0.5f, 0.0f));
    } else {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]-20-[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));
    }

    if (constraints != null)
        View.RemoveConstraints (constraints.ToArray ());

    constraints = newConstraints;
    View.AddConstraints (constraints.ToArray ());
}
```

### <a name="adding-transition-animations"></a>加入轉換動畫

調適型相片應用程式中的分割視圖控制器從折迭到展開時，會覆寫 View 控制器的`WillTransitionToTraitCollection`方法，以將動畫新增至預設動畫。 例如：

```csharp
public override void WillTransitionToTraitCollection (UITraitCollection traitCollection, IUIViewControllerTransitionCoordinator coordinator)
{
    base.WillTransitionToTraitCollection (traitCollection, coordinator);
    coordinator.AnimateAlongsideTransition ((UIViewControllerTransitionCoordinatorContext) => {
        UpdateConstraintsForTraitCollection (traitCollection);
        View.SetNeedsLayout ();
    }, (UIViewControllerTransitionCoordinatorContext) => {
    });
}
```

### <a name="overriding-the-trait-environment"></a>覆寫特性環境

如上所示，調適型相片應用程式會強制「分割視圖控制器」在 iPhone 裝置位於橫向視圖時，同時顯示詳細資料和主要視圖。

這是在 View Controller 中使用下列程式碼來完成：

```csharp
private UITraitCollection forcedTraitCollection = new UITraitCollection ();
...

public UITraitCollection ForcedTraitCollection {
    get {
        return forcedTraitCollection;
    }

    set {
        if (value != forcedTraitCollection) {
            forcedTraitCollection = value;
            UpdateForcedTraitCollection ();
        }
    }
}
...

public override void ViewWillTransitionToSize (SizeF toSize, IUIViewControllerTransitionCoordinator coordinator)
{
    ForcedTraitCollection = toSize.Width > 320.0f ?
         UITraitCollection.FromHorizontalSizeClass (UIUserInterfaceSizeClass.Regular) :
         new UITraitCollection ();

    base.ViewWillTransitionToSize (toSize, coordinator);
}

public void UpdateForcedTraitCollection ()
{
    SetOverrideTraitCollection (forcedTraitCollection, viewController);
}
```

### <a name="expanding-and-collapsing-the-split-view-controller"></a>展開和折迭分割視圖控制器

接下來，我們將探討如何在 Xamarin 中實作為分割視圖控制器的展開和折迭行為。 在中`AppDelegate`，建立分割視圖控制器時，會指派其委派來處理這些變更：

```csharp
public class SplitViewControllerDelegate : UISplitViewControllerDelegate
{
    public override bool CollapseSecondViewController (UISplitViewController splitViewController,
        UIViewController secondaryViewController, UIViewController primaryViewController)
    {
        AAPLPhoto photo = ((CustomViewController)secondaryViewController).Aapl_containedPhoto (null);
        if (photo == null) {
            return true;
        }

        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            var viewControllers = new List<UIViewController> ();
            foreach (var controller in ((UINavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containsPhoto");

                if ((bool)method.Invoke (controller, new object[] { null })) {
                    viewControllers.Add (controller);
                }
            }

            ((UINavigationController)primaryViewController).ViewControllers = viewControllers.ToArray<UIViewController> ();
        }

        return false;
    }

    public override UIViewController SeparateSecondaryViewController (UISplitViewController splitViewController,
        UIViewController primaryViewController)
    {
        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            foreach (var controller in ((CustomNavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containedPhoto");

                if (method.Invoke (controller, new object[] { null }) != null) {
                    return null;
                }
            }
        }

        return new AAPLEmptyViewController ();
    }
}
```

`SeparateSecondaryViewController`方法會測試是否顯示相片，並根據該狀態採取動作。 如果沒有顯示任何相片，它會折迭次要視圖控制器，以顯示主要視圖控制器。

`CollapseSecondViewController`方法是在展開分割視圖控制器時使用，以查看是否有任何相片存在於堆疊上，如果是，則會折迭回到該視圖。

### <a name="moving-between-view-controllers"></a>在視圖控制器之間移動

接下來，我們來看一下，調適型相片應用程式如何在視圖控制器之間移動。 在類別中，當使用者從資料表選取儲存格時，會`ShowDetailViewController`呼叫方法來顯示詳細資料檢視： `AAPLConversationViewController`

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    var photo = PhotoForIndexPath (indexPath);
    var controller = new AAPLPhotoViewController ();
    controller.Photo = photo;

    int photoNumber = indexPath.Row + 1;
    int photoCount = (int)Conversation.Photos.Count;
    controller.Title = string.Format ("{0} of {1}", photoNumber, photoCount);
    ShowDetailViewController (controller, this);
}
```

### <a name="displaying-disclosure-indicators"></a>顯示洩漏指示器

在調適型相片應用程式中，有幾個地方會根據特性環境的變更隱藏或顯示洩漏指示器。 這會使用下列程式碼來處理：

```csharp
public bool Aapl_willShowingViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}

public bool Aapl_willShowingDetailViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingDetailViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}
```

這些會使用上述詳細`GetTargetViewControllerForAction`討論的方法來執行。

當資料表視圖控制器顯示資料時，它會使用上述的方法來查看是否要進行推播，以及是否要據以顯示或隱藏洩漏指示器：

```csharp
public override void WillDisplay (UITableView tableView, UITableViewCell cell, NSIndexPath indexPath)
{
    bool pushes = ShouldShowConversationViewForIndexPath (indexPath) ?
         Aapl_willShowingViewControllerPushWithSender () :
         Aapl_willShowingDetailViewControllerPushWithSender ();

    cell.Accessory = pushes ? UITableViewCellAccessory.DisclosureIndicator : UITableViewCellAccessory.None;
    var conversation = ConversationForIndexPath (indexPath);
    cell.TextLabel.Text = conversation.Name;
}
```

### <a name="new-showdetailtargetdidchangenotification-type"></a>新增`ShowDetailTargetDidChangeNotification`類型

Apple 新增了一種新的通知類型， `ShowDetailTargetDidChangeNotification`可讓您從分割視圖控制器內使用大小類別和特性環境。 每當分割視圖控制器的目標詳細資料檢視變更時，例如當控制器展開或折迭時，就會傳送此通知。

調適型相片應用程式會在詳細資料檢視控制器變更時，使用此通知來更新洩漏指示器的狀態：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    TableView.RegisterClassForCellReuse (typeof(UITableViewCell), AAPLListTableViewControllerCellIdentifier);
    NSNotificationCenter.DefaultCenter.AddObserver (this, new Selector ("showDetailTargetDidChange:"),
        UIViewController.ShowDetailTargetDidChangeNotification, null);
    ClearsSelectionOnViewWillAppear = false;
}
```

進一步瞭解彈性相片應用程式，以查看大小類別、特性集合和調適型視圖控制器的所有方式，可以用來輕鬆地在 Xamarin 中建立整合應用程式。

## <a name="unified-storyboards"></a>整合的 Storyboard

IOS 8 的新手，整合的分鏡腳本可讓開發人員建立一個整合的分鏡腳本檔案，並以多個大小類別為目標，在 iPhone 和 iPad 裝置上顯示。 藉由使用統一的分鏡腳本，開發人員會撰寫較少的 UI 特定程式碼，而且只有一個介面設計可供建立和維護。

整合分鏡腳本的主要優點如下：

- 針對 iPhone 和 iPad 使用相同的分鏡腳本檔案。
- 向 iOS 6 和 iOS 7 回溯部署。
- 在 Xamarin iOS 設計工具內，預覽不同裝置、方向和 OS 版本的版面配置。

這項功能在 Visual Studio for Mac 中受到完整支援

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>啟用大小類別

根據預設，任何新的 Xamarin iOS 專案都會調整類別的大小。 若要在較舊的專案中使用分鏡腳本內的大小類別和調適型 Segue，必須先從 iOS 設計工具中將它轉換成 Xcode 6 整合分鏡腳本格式。

若要這麼做，請在 iOS 設計工具中開啟要轉換的分鏡腳本，並核取 [**使用大小類別**] 核取方塊：

 [![](unified-storyboards-images/sizeclass01.png "[使用大小類別] 核取方塊")](unified-storyboards-images/sizeclass01.png#lightbox)

IOS 設計工具會確認開發人員想要將分鏡腳本的格式轉換成使用大小類別：

 [![](unified-storyboards-images/sizeclass02.png "使用大小類別警示")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> 自動設定也必須檢查大小類別，才能正常運作。

### <a name="generic-device-types"></a>一般裝置類型

將分鏡腳本轉換成使用大小類別之後，它會在 Design Surface 中重新**顯示**，而裝置將會是一般的：

 [![](unified-storyboards-images/sizeclass03.png "以一般裝置類型的形式顯示")](unified-storyboards-images/sizeclass03.png#lightbox)

選取 [一般] 裝置類型時，所有視圖控制器都會調整大小為 600 x 600 正方形。 此正方形代表任何寬度和任何高度的大小。 當 iOS 設計工具處於此模式時，所有的編輯都將套用至所有大小類別。

開發人員也可以選擇以 iPhone 的形式來觀看設計介面：

 [![](unified-storyboards-images/sizeclass04.png "以 iPhone 的形式查看設計介面")](unified-storyboards-images/sizeclass04.png#lightbox)

或者，以 iPad 的形式來觀看：

 [![](unified-storyboards-images/sizeclass05.png "以 iPad 的形式來觀看設計介面")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>選取大小類別

[大小] 類別選取器按鈕位於 Design Surface 的左上角（靠近視圖的下拉式清單）。 它可讓開發人員選取目前正在編輯的大小類別：

 [![](unified-storyboards-images/sizeclass06.png "選取大小類別")](unified-storyboards-images/sizeclass06.png#lightbox)

選取器會以 3 x 3 方格的形式呈現大小類別選取範圍。 方格中的每個方塊都代表 Width 類別和 Height 類別的組合。 中間方形會選取 [任何寬度]/[任何高度大小] 類別（這是統一分鏡腳本的預設視圖）。 選取此方塊時，開發人員會編輯預設的配置，這會由所有其他設定繼承。

方格左上角的正方形代表 Compact Width/Compact Height Size 類別：

 [![](unified-storyboards-images/sizeclass07.png "精簡寬度/壓縮高度大小類別")](unified-storyboards-images/sizeclass07.png#lightbox)

此模式對應於橫向的iPhone。 方格右下角的正方形代表一般寬度/一般高度大小類別，代表 iPad：

 [![](unified-storyboards-images/sizeclass08.png "一般寬度/一般高度大小類別")](unified-storyboards-images/sizeclass08.png#lightbox)

若要在垂直方向編輯 iPhone 的版面配置，請選取左下角的正方形。 這代表精簡寬度/一般高度大小的類別：

 [![](unified-storyboards-images/sizeclass09.png "精簡寬度/一般高度大小類別")](unified-storyboards-images/sizeclass09.png#lightbox)

按一下方形加以選取，Design Surface 將會變更視圖控制器的大小，以符合新的選取專案：

 [![](unified-storyboards-images/sizeclass10.png "Design Surface 會變更視圖控制器的大小，使其符合新的選取專案，如下所示")](unified-storyboards-images/sizeclass10.png#lightbox)

如需大小類別的詳細資訊，以及它們如何影響 Iphone 和 Ipad 的版面配置，請參閱本文的 Size 類別一節。

### <a name="adaptive-segue-types"></a>適應性 Segue 類型

如果開發人員之前已使用過分鏡**腳本，則**會熟悉推送、**強制**回應和**Popover**的現有 segue 類型。 在統一的分鏡腳本檔案上啟用大小類別時，會提供下列調適型 Segue 類型（對應至上面討論的新 View Controller API）：**顯示**並**顯示詳細資料**。

> [!IMPORTANT]
> 當啟用大小類別時，任何現有的 segue 都會轉換成新的類型。

採用 iOS 8 應用程式的範例，其使用整合的分鏡腳本，其具有主要視圖中具有簡單遊戲導覽功能表的分割視圖控制器。 如果使用者按一下功能表按鈕，則在 iPad 上執行時，會在分割視圖控制器的 [詳細資料] 區段中顯示所選取專案的 View Controller。 在 iPhone 上，應將專案的 View Controller 推送到導覽堆疊上。

若要達到此效果，請在 iOS 設計工具控制項中按一下按鈕，並將線條拖曳至要顯示的視圖控制器。 放開滑鼠按鍵時，從 [Segue `Show Detail`類型] 快顯功能表中選取：

 [![](unified-storyboards-images/segue01.png "從 [Segue 類型] 快顯功能表中選取 [顯示詳細資料]")](unified-storyboards-images/segue01.png#lightbox)

新的 segue 將會建立在按鈕和視圖控制器之間。 現在，在 iPhone 模擬器中執行應用程式，將會顯示主功能表：

 [![](unified-storyboards-images/segue02.png "主功能表")](unified-storyboards-images/segue02.png#lightbox)

按一下 [**選取遊戲**] 按鈕，即會將專案的 View Controller 推送至導覽堆疊：

 [![](unified-storyboards-images/segue03.png "[專案] 視圖控制器將會推送到導覽堆疊上，如下所示")](unified-storyboards-images/segue03.png#lightbox)

停止 iPhone 模擬器，然後在 iPad 模擬器中執行應用程式。 切換至 [橫向]，並再次顯示主功能表：

 [![](unified-storyboards-images/segue04.png "顯示的主功能表")](unified-storyboards-images/segue04.png#lightbox)

同樣地，按一下 [**選取遊戲**] 按鈕，專案的 view controller 會顯示在分割視圖控制器的 [詳細資料] 區段中：

 [![](unified-storyboards-images/segue05.png "分割視圖控制器的 [詳細資料] 區段中顯示的專案視圖控制器")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>從大小類別中排除元素

有時候特定的大小類別內不需要指定的元素（例如 View、Control 或 Constraint）。 若要從大小類別中排除元素，請選取要在**Design Surface**中排除的所需專案。 流覽至 [**屬性瀏覽器**] 的底部，然後按一下**齒輪**下拉式功能表。 選取 [**寬度**] 和 [**高度**] 的組合，以排除專案：

[![](unified-storyboards-images/exclude-a.png "選取寬度和高度的組合")](unified-storyboards-images/exclude-a.png#lightbox)

新的*排除案例*將會加入至**屬性瀏覽器**底部的元素中。 接下來，取消核取指定大小類別的 [**已安裝**] 核取方塊：

[![](unified-storyboards-images/exclude-b.png "取消核取 [已安裝] 核取方塊")](unified-storyboards-images/exclude-b.png#lightbox)

將 Design Surface 切換為排除專案的寬度和高度，它已從指定的大小類別中移除，但不是整個 UI 設計：

 [![](unified-storyboards-images/exclude02.png "將 Design Surface 切換為排除專案的寬度和高度")](unified-storyboards-images/exclude02.png#lightbox)

切換回 [任何寬度]/[任何高度大小] 類別，而元素仍在原地：

 [![](unified-storyboards-images/exclude03.png "切換回任何寬度/任何高度大小的類別")](unified-storyboards-images/exclude03.png#lightbox)

當應用程式在 iPad 模擬器中執行時，會顯示元素：

 [![](unified-storyboards-images/exclude04.png "在 iPad 模擬器中執行的應用程式時所顯示的元素")](unified-storyboards-images/exclude04.png#lightbox)

而當應用程式在 iPhone 模擬器上執行時，缺少元素：

 [![](unified-storyboards-images/exclude05.png "IPhone 模擬器中執行中的應用程式時，遺漏元素")](unified-storyboards-images/exclude05.png#lightbox)

若要從專案中移除排除案例，只要選取  **Design Surface**中的專案， **-** 然後在 **屬性瀏覽器** 的底部按一下按鈕，即可移除。

若要查看整合的分鏡腳本的執行， `UnifiedStoryboard`請查看附加到本檔的範例 Xamarin iOS 8 應用程式。

## <a name="dynamic-launch-screens"></a>動態啟動畫面

啟動畫面檔案會在 iOS 應用程式啟動時顯示為啟動顯示畫面，以提供對使用者實際啟動應用程式的意見反應。 在 iOS 8 之前，開發人員必須針對應用程式執行`Default.png`所在的每種裝置類型、方向和螢幕解析度，包含多個映射資產。 例如， `Default@2x.png` `Default-Landscape@2x~ipad.png`、 、等。`Default-Portrait@2x~ipad.png`

在新的 iphone 6 和 iPhone 6 Plus 裝置（以及即將推出的 Apple Watch）中，使用所有現有的 iphone 和 iPad 裝置來進行分解，這代表的是一大組大小`Default.png` 、方向以及啟動畫面影像資產的解析度，必須建立和維護。 此外，這些檔案可能相當大，而且會「膨脹」交付物應用程式套件組合，以增加從 iTunes App Store 下載應用程式所需的時間（可能會讓它無法透過行動電話通訊網路傳遞）並增加使用者裝置上所需的儲存體數量。

IOS 8 的新手，開發人員可以在 Xcode 中建立單一`.xib` 、不可部分完成的檔案，該檔案使用自動設定和大小類別來建立*動態啟動畫面*，適用于每個裝置、解析度和方向。 這不僅能減少開發人員建立和維護所有必要映射資產所需的工作量，還能大幅減少應用程式已安裝的配套大小。

動態啟動畫面具有下列限制和考慮：

- 僅`UIKit`使用類別。
- 使用屬於`UIView`或`UIViewController`物件的單一根視圖。
- 不要對應用程式的程式碼進行任何連接（不要新增**動作**或**輸出**）。
- 不要加入`UIWebView`物件。
- 請勿使用任何自訂類別。
- 請勿使用執行時間屬性。

考慮上述指導方針之後，讓我們來看一下如何將動態啟動畫面新增至現有的 Xamarin iOS 8 專案。

請執行下列動作：

1. 開啟**Visual Studio for Mac**並載入**方案**，以將動態啟動畫面新增至。
2. 在 **方案總管**中，以滑鼠右鍵`MainStoryboard.storyboard`按一下檔案，然後選取 **以** >  **Xcode Interface Builder**開啟：

    [![](unified-storyboards-images/dls01.png "使用 Xcode 開啟 Interface Builder")](unified-storyboards-images/dls01.png#lightbox)
3. 在 Xcode 中，**選取** > [檔案] [**新增** > 檔案 **...** ]：

    [![](unified-storyboards-images/dls02.png "選取檔案/新增")](unified-storyboards-images/dls02.png#lightbox)
4. 選取 [ **iOS**  > **使用者介面** > **啟動畫面**]，然後按 [**下一步]** 按鈕：

    [![](unified-storyboards-images/dls03.png "選取 iOS/使用者介面/啟動畫面")](unified-storyboards-images/dls03.png#lightbox)
5. 將`LaunchScreen.xib`檔案命名為，然後按一下 [**建立**] 按鈕：

    [![](unified-storyboards-images/dls04.png "將檔案命名為 LaunchScreen. xib")](unified-storyboards-images/dls04.png#lightbox)
6. 藉由新增圖形元素並使用版面配置條件約束，針對指定的裝置、方向和螢幕大小，來編輯啟動畫面的設計：

    [![](unified-storyboards-images/dls05.png "編輯啟動畫面的設計")](unified-storyboards-images/dls05.png#lightbox)
7. 將變更儲存至`LaunchScreen.xib`。
8. 選取 [**應用程式] 目標**和 [**一般**] 索引標籤：

    [![](unified-storyboards-images/dls06.png "選取 [應用程式] 目標和 [一般] 索引標籤")](unified-storyboards-images/dls06.png#lightbox)
9. 按一下 [**選擇 plist** ] 按鈕，選取 [ `Info.plist`針對 Xamarin 應用程式]，然後按一下 [**選擇**] 按鈕：

    [![](unified-storyboards-images/dls07.png "選取 Xamarin 應用程式的 plist")](unified-storyboards-images/dls07.png#lightbox)
10. 在 [**應用程式圖示和啟動映射**] 區段中，開啟 [**啟動畫面**檔案] `LaunchScreen.xib`下拉式清單，然後選擇上面所建立的：

    [![](unified-storyboards-images/dls08.png "選擇 [LaunchScreen] xib。")](unified-storyboards-images/dls08.png#lightbox)
11. 將變更儲存至檔案，並返回 Visual Studio for Mac。
12. 等候 Visual Studio for Mac 完成與 Xcode 同步變更。
13. 在 **方案總管**中，以滑鼠右鍵按一下 **資源** 資料夾，然後選取 **新增** >  **新增檔案 ...** ：

    [![](unified-storyboards-images/dls09.png "選取 [新增/新增檔案 ...]")](unified-storyboards-images/dls09.png#lightbox)
14. 選取上方`LaunchScreen.xib`建立的檔案，然後按一下 [**開啟**] 按鈕：

    [![](unified-storyboards-images/dls10.png "選取 LaunchScreen xib 檔案")](unified-storyboards-images/dls10.png#lightbox)
15. 建置應用程式。

### <a name="testing-the-dynamic-launch-screen"></a>測試動態啟動畫面

在 Visual Studio for Mac 中，選取 [iPhone 4 Retina 模擬器] 並執行應用程式。 動態啟動畫面將以正確的格式和方向顯示：

[![](unified-storyboards-images/dls11.png "以垂直方向顯示的動態啟動畫面")](unified-storyboards-images/dls11.png#lightbox)

在 Visual Studio for Mac 中停止應用程式，然後選取 iPad iOS 8 裝置。 執行應用程式，系統會針對此裝置和方向正確地格式化啟動畫面：

[![](unified-storyboards-images/dls12.png "以水準方向顯示的動態啟動畫面")](unified-storyboards-images/dls12.png#lightbox)

返回 Visual Studio for Mac，並停止執行應用程式。

### <a name="working-with-ios-7"></a>使用 iOS 7

為了維持與 ios 7 的回溯相容性，只要在`Default.png` ios 8 應用程式中納入一般的映射資產就可以了。 iOS 會回到先前的行為，並在 iOS 7 裝置上執行時，使用這些檔案作為啟動畫面。

若要在 Xamarin 中查看動態啟動畫面的執行，請查看附加到本檔的[動態啟動畫面](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)範例 iOS 8 應用程式。

## <a name="summary"></a>總結

本文快速查看大小類別，以及它們如何影響 iPhone 和 iPad 裝置的版面配置。 它討論了特性、特性環境和特性集合如何搭配大小類別來建立整合介面。 它會稍微瞭解調適型視圖控制器，以及它們如何與整合介面內的大小類別搭配使用。 它探討了如何完全從C# Xamarin iOS 8 應用程式中的程式碼來執行大小類別和整合介面。

最後，本文涵蓋了使用 Xamarin iOS 設計工具建立整合的分鏡腳本的基本概念，可在 iOS 裝置上工作，並建立單一的動態啟動畫面，在每個 iOS 8 裝置上顯示為啟動畫面。

## <a name="related-links"></a>相關連結

- [調適型相片（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-adaptivephotos)
- [動態啟動畫面（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
- [IOS8 中的動態版面配置-進化2014（影片）](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
