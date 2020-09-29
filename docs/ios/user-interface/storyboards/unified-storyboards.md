---
title: Xamarin 中的統一分鏡腳本
description: 本檔說明 Xamarin 中的統一分鏡腳本。 整合的分鏡腳本可讓開發人員透過單一介面定義支援多個螢幕大小。
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: fdfdd385563ee425f0e8767aed13304ae9541b94
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435364"
---
# <a name="unified-storyboards-in-xamarinios"></a>Xamarin 中的統一分鏡腳本

iOS 8 包含全新、更簡單易用的機制，可讓您建立使用者介面，也就是統一的分鏡腳本。 您可以使用單一分鏡腳本來涵蓋所有不同的硬體螢幕大小，以「設計一次、使用-多」樣式來建立快速且回應式的觀點。

由於開發人員不再需要為 iPhone 和 iPad 裝置建立個別且特定的分鏡腳本，因此可以彈性地使用通用介面來設計應用程式，然後針對不同的大小類別自訂該介面。 如此一來，應用程式就可以調整為每個外型規格的優勢，而且每個使用者介面都可以調整以提供最佳體驗。

<a name="size-classes"></a>

## <a name="size-classes"></a>大小類別

在 iOS 8 之前，開發人員使用 `UIInterfaceOrientation` 和 `UIInterfaceIdiom` 來區分直向和橫向模式，以及在 IPhone 和 iPad 裝置之間區分。 在 iOS8 中，方向和裝置是藉由使用 *大小類別*來決定。

裝置是由垂直和水準軸中的大小類別所定義，而在 iOS 8 中有兩種類型的大小類別：

- [**一般**]-這適用于大型螢幕大小 (例如 iPad) 或小工具，可提供很大大小的印象 (例如`UIScrollView`
- **Compact** –這適用于較小的裝置 (例如 iPhone) 。 此大小會考慮裝置的方向。

如果同時使用這兩個概念，則結果會是 2 x 2 個方格，其中定義可用於不同方向的不同可能大小，如下圖所示：

 [![2 x 2 方格，定義可用於一般和 Compact 方向的不同可能大小](unified-storyboards-images/sizeclassgrid.png)](unified-storyboards-images/sizeclassgrid.png#lightbox)

開發人員可以建立使用四種可能產生不同配置 (的查看控制器，如) 的圖形所示。

### <a name="ipad-size-classes"></a>iPad 大小類別

因為大小的緣故，iPad 具有適用于這兩個方向的 **一般** 類別大小。

 [![iPad 大小類別](unified-storyboards-images/image1.png)](unified-storyboards-images/image1.png#lightbox)

### <a name="iphone-size-classes"></a>iPhone 大小類別

根據裝置的方向，iPhone 具有不同的大小類別：

 [![iPhone 大小類別](unified-storyboards-images/iphonesizeclasses.png)](unified-storyboards-images/iphonesizeclasses.png#lightbox)

- 當裝置處於直向模式時，螢幕會**水準且垂直**地具有**compact**類別
- 當裝置處於橫向模式時，螢幕類別會從直向模式反轉。

### <a name="iphone-6-plus-size-classes"></a>iPhone 6 加上大小類別

在直向時，大小與先前的 Iphone 相同，但橫向則不同：

[![iPhone 6 加上大小類別](unified-storyboards-images/iphone6sizeclasses.png)](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

由於 iPhone 6 Plus 具有夠大的螢幕，因此在橫向模式中可以有一般的寬度大小類別。

### <a name="support-for-a-new-screen-scale"></a>支援新的螢幕縮放

IPhone 6 Plus 使用 Retina 螢幕縮放比例為3.0 的新 HD 顯示器， (原始 iPhone 螢幕解析度) 的三倍。 若要在這些裝置上提供最佳體驗，請包含針對此螢幕縮放設計的新圖稿。 在 Xcode 6 （含）以上版本中，資產目錄可包含具有1x、2x 和3倍大小的影像;只要新增映射資產，iOS 就會在 iPhone 6 Plus 上執行時選擇正確的資產。

IOS 中的映射載入行為也會辨識 `@3x` 影像檔案上的尾碼。 例如，如果開發人員在應用程式套件組合中包含不同解析度) 的影像資產 (，請使用下列檔案名： `MonkeyIcon.png` 、 `MonkeyIcon@2x.png` 和 `MonkeyIcon@3x.png` 。 在 iPhone 6 上， `MonkeyIcon@3x.png` 如果開發人員使用下列程式碼載入影像，則會自動使用該映射：

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```

或者，如果使用者使用 iOS 設計工具將影像指派給 UI 專案 `MonkeyIcon.png` ，則 `MonkeyIcon@3x.png` 會在 IPhone 6 Plus 上自動使用。

<a name="dynamic-launch-screens"></a>

### <a name="dynamic-launch-screens"></a>動態啟動畫面

啟動畫面檔案會在 iOS 應用程式啟動時顯示為啟動顯示畫面，以提供意見反應給使用者，指出應用程式正在啟動。 在 iOS 8 之前，開發人員必須 `Default.png` 針對應用程式執行所在的每種裝置類型、方向和螢幕解析度，包含多個影像資產。

第8版的新功能，開發人員可以在 Xcode 中建立單一的不可部分完成檔案，其 `.xib` 使用自動設定和大小類別來建立 *動態啟動畫面* ，以供每個裝置、解析度和方向使用。 這不僅能減少開發人員建立及維護所有必要影像資產所需的工作量，還可減少應用程式安裝的配套大小。

## <a name="traits"></a>特性

特性是一些屬性，可用來判斷版面配置如何隨著環境變更而變更。 它們包含一組屬性 `HorizontalSizeClass` ， (和 `VerticalSizeClass` 根據 `UIUserInterfaceSizeClass`) ，以及介面 ( `UIUserInterfaceIdiom`) 和顯示比例的介面。

上述所有狀態都會包裝在 Apple 視為特性集合 ( 的容器中 `UITraitCollection`) ，其中不僅包含屬性，也包含其值。

## <a name="trait-environment"></a>特性環境

特性環境是 iOS 8 中的新介面，而且可以傳回下列物件的特性集合：

- 畫面 ( `UIScreens` ) 。
- Windows ( `UIWindows` ) 。
- 查看控制器 ( `UIViewController` ) 。
- Views ( `UIView` ) 。
- 簡報控制器 ( `UIPresentationController` ) 。

開發人員會使用特性環境所傳回的特性集合，來決定應該如何配置使用者介面。

所有特性環境都會建立如下圖所示的階層：

 [![特性環境階層圖](unified-storyboards-images/viewhierarchy.png)](unified-storyboards-images/viewhierarchy.png#lightbox)

依預設，上述特性環境的特性集合會從父系流向子環境。

除了取得目前的特性集合之外，特性環境還有一個 `TraitCollectionDidChange` 方法，可以在 View 或 View Controller 子類別中覆寫。 當這些特性變更時，開發人員可以使用這個方法來修改任何相依于特性的 UI 元素。

## <a name="typical-trait-collections"></a>一般特性集合

本節將涵蓋使用者在使用 iOS 8 時將會經歷的一般特性集合類型。

以下是開發人員可能會在 iPhone 上看到的一般特性集合：

|屬性|值|
|--- |--- |
|`HorizontalSizeClass`|精簡|
|`VerticalSizeClass`|定期|
|`UserInterfaceIdom`|電話|
|`DisplayScale`|2.0|

上述設定會代表完整的特性集合，因為它具有其所有特性屬性的值。

您也可以將缺少某些值的特性集合 (Apple 參考為 *未指定*) ：

|屬性|值|
|--- |--- |
|`HorizontalSizeClass`|精簡|
|`VerticalSizeClass`|[未指定]|
|`UserInterfaceIdom`|[未指定]|
|`DisplayScale`|[未指定]|

不過，一般而言，當開發人員要求特性環境進行其特性集合時，會傳回完整的集合，如上述範例所示。

如果特性環境 (類似于視圖或視圖控制器) 不在目前的視圖階層內，開發人員可能會針對一或多個特性屬性取回未指定的值。

如果開發人員使用 Apple 所提供的其中一種建立方法（例如 `UITraitCollection.FromHorizontalSizeClass` ）來建立新的集合，則也會取得部分限定的特性集合。

可在多個特性集合上執行的一項作業，就是將它們彼此進行比較，這牽涉到一個特性集合（如果有的話）會包含另一個。 內含 *專案的意義是，* 針對第二個集合中指定的任何特性，此值必須與第一個集合中的值完全相符。

若要測試兩個特性，請使用 `Contains` `UITraitCollection` 傳遞要測試之特性值的方法。

開發人員可以在程式碼中手動執行比較，以決定如何配置視圖或查看控制器。 不過， `UIKit` 在內部使用此方法來提供其部分功能，例如在外觀 Proxy 中。

## <a name="appearance-proxy"></a>外觀 Proxy

外觀 Proxy 是在舊版 iOS 中引進，可讓開發人員自訂其視圖的屬性。 它已在 iOS 8 中擴充，以支援特性集合。

外觀 Proxy 現在包含新的方法， `AppearanceForTraitCollection` 它會針對已傳入的指定特性集合，傳回新的外觀 Proxy。 開發人員在該外觀 Proxy 上執行的任何自訂，只會在符合指定特性集合的視圖上生效。

開發人員通常會將部分指定的特性集合傳遞至 `AppearanceForTraitCollection` 方法，例如只指定 compact 的水準大小類別，讓他們可以自訂應用程式中水準壓縮的任何視圖。

## <a name="uiimage"></a>UIImage

Apple 已新增特徵集合的另一個類別為 `UIImage` 。 在過去，開發人員必須指定要 @1X @2x 包含在應用程式中的任何點陣圖圖形資產的和版本 (例如圖示) 。

iOS 8 已擴充，可讓開發人員根據特性集合，在映射目錄中包含多個版本的影像。 例如，開發人員可以包含較小的影像來使用 Compact 特性類別，以及任何其他集合的完整大小影像。

當其中一個影像用於類別內部時 `UIImageView` ，影像視圖會自動顯示其特性集合的正確影像版本。 如果特性環境變更 (例如使用者將裝置從縱向切換為橫向) ，影像視圖會自動選取新的影像大小以符合新的特性集合，並變更其大小以符合目前所顯示影像的目前版本。

## <a name="uiimageasset"></a>UIImageAsset

Apple 已將名為的新類別新增至 iOS 8， `UIImageAsset` 讓開發人員更能控制選取的影像。

影像資產會包裝所有不同版本的映射，並可讓開發人員要求符合已傳入之特性集合的特定影像。 您可以隨時從影像資產中新增或移除影像。

如需影像資產的詳細資訊，請參閱 Apple 的 [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset) 檔。

## <a name="combining-trait-collections"></a>組合特性集合

開發人員可以在特性集合上執行的另一個函式，是將兩個組合在一起，以產生結合的集合，其中某個集合的未指定值會由第二個集合中的指定值所取代。 這是使用類別的 `FromTraitsFromCollections` 方法來完成 `UITraitCollection` 。

如上所述，如果其中一個特性集合未指定任何特性，而且在另一個特性集合中指定，則此值將設定為指定的版本。 但是，如果指定的指定值有多個版本，則最後一個特性集合中的值將會是使用的值。

## <a name="adaptive-view-controllers"></a>調適型視圖控制器

本節將討論 iOS 視圖和視圖控制器如何採用特性和大小類別的概念，在開發人員的應用程式中自動進行調整。

### <a name="split-view-controller"></a>分割視圖控制器

在 iOS 8 中已變更最大的視圖控制器類別之一，就是 `UISplitViewController` 類別。 在過去，開發人員通常會在 iPad 版應用程式上使用分割視圖控制器，然後他們必須為 iPhone 版應用程式提供完全不同的視圖階層版本。

在 iOS 8 中， `UISplitViewController` 類別適用于 (iPad 和 iphone) 的平臺，可讓開發人員建立一個可同時適用于 iPhone 和 iPad 的視圖控制器階層。

當 iPhone 處於橫向時，「分割視圖」控制器將並排顯示其觀點，就像在 iPad 上顯示一樣。

### <a name="overriding-traits"></a>覆寫特性

特性環境會從父容器向下串聯至子容器，如下圖所示，在 iPad 上以橫向顯示分割視圖控制器：

 [![以橫向顯示的 iPad 上的分割視圖控制器](unified-storyboards-images/cascadingclasses01.png)](unified-storyboards-images/cascadingclasses01.png#lightbox)

因為 iPad 的水準和垂直方向都有一般大小的類別，所以分割視圖會同時顯示主圖和詳細資料檢視。

在 iPhone 上，在兩個方向的大小類別都是 compact 的情況下，[分割視圖控制器] 只會顯示詳細資料檢視，如下所示：

 [![分割視圖控制器只會顯示詳細資料檢視](unified-storyboards-images/cascadingclasses02.png)](unified-storyboards-images/cascadingclasses02.png#lightbox)

在開發人員想要在 iPhone 上以橫向顯示主要和詳細資料檢視的應用程式中，開發人員必須插入分割 View 控制器的父容器，並覆寫特性集合。 如下圖所示：

 [![開發人員必須插入分割 View 控制器的父容器，並覆寫特性集合](unified-storyboards-images/cascadingclasses03.png)](unified-storyboards-images/cascadingclasses03.png#lightbox)

`UIView`會設定為分割視圖控制器的父系，並在瀏覽器 `SetOverrideTraitCollection` 上呼叫方法，而該視圖會傳入新的特性集合，並以分割視圖控制器為目標。 新的特性集合會覆寫 `HorizontalSizeClass` ，並將其設定為 `Regular` ，讓「分割視圖控制器」在橫向顯示 iPhone 上的主要和詳細資料檢視。

請注意， `VerticalSizeClass` 已設定為 `unspecified` ，可讓新的特性集合新增至父代上的特性集合，進而產生 `Compact VerticalSizeClass` 子分割視圖控制器的。

### <a name="trait-changes"></a>特性變更

本節將詳細探討特性環境變更時，特性集合的轉換方式。 例如，當裝置從縱向旋轉為橫向時。

 [![直向橫向特徵變更總覽](unified-storyboards-images/traittransitions01.png)](unified-storyboards-images/traittransitions01.png#lightbox)

首先，iOS 8 會進行一些設定，以準備進行轉換。 接下來，系統會將轉換狀態動畫。 最後，iOS 8 會清除轉換期間所需的任何暫時狀態。

iOS 8 提供數個回呼，讓開發人員可以用來參與特性變更，如下表所示：

|階段|回呼|描述|
|--- |--- |--- |
|安裝程式|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>在特性集合設為新值之前，會先在特性變更的開頭呼叫這個方法。</li><li>當特性集合的值已變更，但在任何動畫發生之前，會呼叫方法。</li></ul>|
|動畫|`WillTransitionToTraitCollection`|傳遞至這個方法的轉換協調器具有 `AnimateAlongside` 屬性，可讓開發人員加入將與預設動畫一起執行的動畫。|
|清除|`WillTransitionToTraitCollection`|提供方法，讓開發人員在進行轉換之後，包含自己的清除程式碼。|

`WillTransitionToTraitCollection`方法非常適合用來製作視圖控制器的動畫，以及特徵集合變更。 此 `WillTransitionToTraitCollection` 方法僅適用于視圖控制器 ( `UIViewController`) ，而不是在其他特性環境上（例如） `UIViews` 。

`TraitCollectionDidChange`很適合用來處理 `UIView` 類別，開發人員想要在特性變更時更新 UI。

### <a name="collapsing-the-split-view-controllers"></a>折迭分割視圖控制器

現在讓我們仔細看看分割視圖控制器從兩個數據行折迭為一個資料行視圖時，會發生什麼事。 在這項變更中，有兩個需要進行的處理常式：

- 依預設，分割視圖控制器會在折迭發生之後，使用主要視圖控制器作為 view。 開發人員可以覆寫此行為，方法是覆寫的  `GetPrimaryViewControllerForCollapsingSplitViewController` 方法  `UISplitViewControllerDelegate` ，並提供他們想要以折迭狀態顯示的任何視圖控制器。
- 次要視圖控制器必須合併到主要視圖控制器中。 一般來說，開發人員不需要採取任何動作，就能執行此步驟;分割視圖控制器包含以硬體裝置為基礎的這個階段自動處理。 不過，在某些特殊情況下，開發人員可能會想要與此變更互動。 呼叫的方法可在發生折迭  `CollapseSecondViewController`  `UISplitViewControllerDelegate` 時顯示主視圖控制器，而不是詳細資料檢視。

### <a name="expanding-the-split-view-controller"></a>展開分割視圖控制器

現在讓我們仔細看看當分割視圖控制器從折迭狀態展開時，會發生什麼事。 同樣地，需要進行兩個階段：

- 首先，請定義新的主要視圖控制器。 根據預設，「分割視圖控制器」會自動從折迭的視圖使用主要視圖控制器。 同樣地，開發人員可以使用的方法來覆寫這個行為  `GetPrimaryViewControllerForExpandingSplitViewController`  `UISplitViewControllerDelegate` 。
- 選擇主要視圖控制器之後，就必須重新建立次要視圖控制器。 同樣地，「分割視圖控制器」也包含以硬體裝置為基礎的這個階段自動處理。 開發人員可以藉由呼叫的方法來覆寫這個行為  `SeparateSecondaryViewController`  `UISplitViewControllerDelegate` 。

在分割視圖控制器中，主要視圖控制器會藉由執行的和方法，在展開和折迭的過程中扮演一部分 `CollapseSecondViewController` `SeparateSecondaryViewController` `UISplitViewControllerDelegate` 。 `UINavigationController` 會執行這些方法來自動推送和彈出次要視圖控制器。

### <a name="showing-view-controllers"></a>顯示視圖控制器

Apple 對 iOS 8 所做的另一項變更，就是開發人員顯示視圖控制器的方式。 在過去，如果應用程式有分葉視圖控制器 (例如，資料表視圖控制器) ，而開發人員顯示不同的 (例如，為了回應使用者在資料格上的點擊) ，應用程式會透過控制器階層切換至導覽視圖控制器，並對 `PushViewController` 其呼叫方法以顯示新的視圖。

這會顯示流覽控制器與其執行所在環境之間的緊密結合。 在 iOS 8 中，Apple 藉由提供兩種新方法來分離此項：

- `ShowViewController` –調整以根據其環境顯示新的視圖控制器。 例如，在中，  `UINavigationController` 它只會將新的視圖推送至堆疊上。 在分割視圖控制器中，新的視圖控制器會在左側顯示為新的主要視圖控制器。 如果沒有任何容器視圖控制器存在，新的視圖會顯示為強制回應視圖控制器。
- `ShowDetailViewController` –的運作方式類似于  `ShowViewController` ，但會在分割視圖控制器上執行，以將詳細資料檢視取代為傳入的新視圖控制器。 如果已折迭分割視圖控制器 (可能會在 iPhone 應用程式) 中看到，則會將呼叫重新導向至  `ShowViewController` 方法，而新的視圖會顯示為主要視圖控制器。 同樣地，如果沒有任何容器視圖控制器存在，新的視圖會顯示為強制回應視圖控制器。

這些方法的運作方式是從分葉視圖控制器開始，並向上查看視圖階層，直到找到正確的容器視圖控制器來處理新的視圖顯示。

開發人員可以 `ShowViewController` `ShowDetailViewController` 在自己的自訂視圖控制器中執行和，以取得和提供的相同自動化功能 `UINavigationController` `UISplitViewController` 。

### <a name="how-it-works"></a>運作方式

在本節中，我們將探討如何在 iOS 8 中實際執行這些方法。 首先讓我們看看新 `GetTargetForAction` 方法：

 [![新的 GetTargetForAction 方法](unified-storyboards-images/gettargetforaction.png)](unified-storyboards-images/gettargetforaction.png#lightbox)

此方法會引導階層鏈，直到找到正確的容器視圖控制器為止。 例如：

1. 如果  `ShowViewController` 呼叫方法，則執行此方法之鏈中的第一個 View 控制器就是流覽控制器，因此它會用來做為新視圖的父系。
1. 如果  `ShowDetailViewController` 改為呼叫方法，則分割視圖控制器是要執行的第一個 View 控制器，因此它會當做父系使用。

`GetTargetForAction`方法的運作方式是找出可執行指定動作的視圖控制器，然後在想要接收該動作時要求該 View controller。 由於這個方法是公用的，因此開發人員可以建立自己的自訂方法，其運作方式就像內建 `ShowViewController` 和 `ShowDetailViewController` 方法一樣。

## <a name="adaptive-presentation"></a>適應性簡報

在 iOS 8 中，Apple 讓 Popover 簡報 () 調適型 `UIPopoverPresentationController` 。 因此，Popover Presentation View 控制器會自動在一般大小類別中呈現一般 Popover 視圖，但是會在水準壓縮大小類別 (（例如在 iPhone) 上顯示全螢幕。

為了容納統一的腳本系統內的變更，已建立一個新的控制器物件來管理顯示的視圖控制器— `UIPresentationController` 。 此控制器是從顯示的視圖控制器開始時建立，直到它關閉為止。 因為它是一種管理類別，所以可以將它視為超級類別，因為它會回應影響視圖控制器 (的裝置變更，例如方向) ，然後再送回呈現控制器控制的 View Controller。

當開發人員使用方法出示 View Controller 時 `PresentViewController` ，會將簡報流程的管理交給 `UIKit` 。 UIKit 會為所建立的樣式) 正確的控制器 (處理其他專案，唯一的例外狀況是當視圖控制器將樣式設定為時 `UIModalPresentationCustom` 。 在此，應用程式可以提供它自己的 PresentationController，而不是使用 `UIKit` 控制器。

### <a name="custom-presentation-styles"></a>自訂呈現樣式

使用自訂的簡報樣式時，開發人員可以選擇使用自訂的呈現控制器。 您可以使用這個自訂控制器來修改 allied 的視圖外觀和行為。

<a name="size-classes"></a>

## <a name="working-with-size-classes"></a>使用大小類別

本文隨附的調適型相片 Xamarin 專案，提供在 iOS 8 整合介面應用程式中使用大小類別和調適型視圖控制器的實用範例。

雖然應用程式會從程式碼完全建立其 UI，而不是使用 IOS 設計工具和建立整合的分鏡腳本，但是同樣的技巧也適用。 稍後在本文中，我們將示範如何在 Xamarin 應用程式中搭配使用大小類別與整合分鏡腳本和 iOS 設計工具。

現在讓我們仔細看看調適型相片專案如何在 iOS 8 中執行數個大小類別功能，以建立適應性應用程式。

### <a name="adapting-to-trait-environment-changes"></a>適應特性環境變更

當使用者在 iPhone 上執行調適型相片應用程式時，當使用者將裝置從縱向旋轉為橫向時，[分割視圖控制器] 將同時顯示主要和詳細資料檢視：

 [![分割視圖控制器會顯示主要和詳細資料檢視，如下所示](unified-storyboards-images/rotation.png)](unified-storyboards-images/rotation.png#lightbox)

這是藉由覆寫 `UpdateConstraintsForTraitCollection` View 控制器的方法，並根據的值調整條件約束來達成 `VerticalSizeClass` 。 例如：

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

當調適型相片應用程式中的分割視圖控制器從折迭變成展開時，會藉由覆寫 View 控制器的方法，將動畫新增至預設動畫 `WillTransitionToTraitCollection` 。 例如：

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

### <a name="overriding-the-trait-environment"></a>覆寫特徵環境

如上圖所示，當 iPhone 裝置位於橫向視圖時，調適型相片應用程式會強制「分割視圖控制器」顯示詳細資料和主視圖。

這是使用 View Controller 中的下列程式碼來完成的：

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

接下來，讓我們來看看如何在 Xamarin 中執行分割視圖控制器的展開和折迭行為。 在中 `AppDelegate` ，建立分割視圖控制器時，會指派其委派來處理這些變更：

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

`SeparateSecondaryViewController`方法會進行測試，以查看是否正在顯示相片，並根據該狀態採取動作。 如果未顯示任何相片，則會折迭 [次要視圖控制器]，以顯示 [主視圖控制器]。

`CollapseSecondViewController`展開分割視圖控制器時，會使用方法來查看是否有任何相片存在於堆疊上，如果有的話，則會折迭至該視圖。

### <a name="moving-between-view-controllers"></a>在視圖控制器之間移動

接下來，讓我們看看調適型相片應用程式如何在視圖控制器之間移動。 `AAPLConversationViewController`當使用者選取資料表中的資料格時， `ShowDetailViewController` 會呼叫方法來顯示詳細資料檢視：

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

在調適型相片應用程式中，有幾個地方會根據特性環境的變更，隱藏或顯示洩漏指示器。 這會使用下列程式碼來處理：

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

這些會使用 `GetTargetViewControllerForAction` 上述詳細討論的方法來執行。

當資料表視圖控制器顯示資料時，它會使用上面所執行的方法來查看是否要進行推送，以及是否要據以顯示或隱藏洩漏指標：

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

### <a name="new-showdetailtargetdidchangenotification-type"></a>新 `ShowDetailTargetDidChangeNotification` 類型

Apple 已新增新的通知類型，可在分割視圖控制器中使用大小類別和特性環境 `ShowDetailTargetDidChangeNotification` 。 每當分割視圖控制器的目標詳細資料檢視變更時（例如控制器展開或折迭時），就會傳送此通知。

當詳細資料檢視控制器變更時，調適型相片應用程式會使用此通知來更新洩漏指標的狀態：

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

深入瞭解調適型相片應用程式，以查看大小類別、特性集合和調適型視圖控制器可用來在 Xamarin 中輕鬆建立統一應用程式的所有方法。

## <a name="unified-storyboards"></a>整合的 Storyboard

最新的 iOS 8，整合的分鏡腳本可讓開發人員建立一個統一的分鏡腳本檔案，該檔案可在 iPhone 和 iPad 裝置上以多個大小類別為目標來顯示。 藉由使用統一的分鏡腳本，開發人員會撰寫較少的 UI 特定程式碼，而且只有一個介面設計要建立和維護。

整合分鏡腳本的主要優點如下：

- 使用適用于 iPhone 和 iPad 的相同分鏡腳本檔案。
- 向後部署至 iOS 6 和 iOS 7。
- 在 Xamarin iOS 設計工具內，預覽不同裝置、方向和作業系統版本的版面配置。

這項功能在 Visual Studio for Mac 中受到完整支援

<a name="enabling-size-classes"></a>

### <a name="enabling-size-classes"></a>啟用大小類別

根據預設，任何新的 Xamarin 專案都會有大小的類別。 若要在腳本中使用來自較舊專案的大小類別和自我調整 Segue，必須先將它從 iOS 設計工具轉換成 Xcode 6 統一的分鏡腳本格式。

若要這樣做，請開啟要在 iOS 設計工具中轉換的分鏡腳本，然後選取 [ **使用大小類別** ] 核取方塊：

 [![[使用大小類別] 核取方塊](unified-storyboards-images/sizeclass01.png)](unified-storyboards-images/sizeclass01.png#lightbox)

IOS 設計工具會確認開發人員想要轉換分鏡腳本的格式，以使用大小類別：

 [![使用大小類別警示](unified-storyboards-images/sizeclass02.png)](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> 您也必須檢查自動設定的大小類別，才能正確運作。

### <a name="generic-device-types"></a>一般裝置類型

一旦將分鏡腳本轉換成使用大小類別之後，就會在 Design Surface 中重新 **顯示** ，且裝置將會是一般的：

 [![查看為一般裝置類型](unified-storyboards-images/sizeclass03.png)](unified-storyboards-images/sizeclass03.png#lightbox)

選取一般裝置類型時，所有視圖控制器都會調整大小為 600 x 600 正方形。 此正方形代表任何寬度和高度的大小。 當 iOS 設計工具處於此模式時，所有的編輯都會套用至所有大小類別。

開發人員也可以選擇以 iPhone 的形式來查看設計介面：

 [![以 iPhone 的形式來查看設計介面](unified-storyboards-images/sizeclass04.png)](unified-storyboards-images/sizeclass04.png#lightbox)

或以 iPad 的形式來觀看：

 [![以 iPad 的形式來查看設計介面](unified-storyboards-images/sizeclass05.png)](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>選取大小類別

[大小] ([選取器] 按鈕位於 Design Surface 的左上角，顯示為下拉式) 。 它可讓開發人員選取目前正在編輯的大小類別：

 [![選取大小類別](unified-storyboards-images/sizeclass06.png)](unified-storyboards-images/sizeclass06.png#lightbox)

選取器會將大小類別選取專案顯示為 3 x 3 方格。 方格中的每個方塊都代表一個寬度類別和高度類別的組合。 中央方形會選取 [任何寬度/任何高度大小] 類別 (這是統一分鏡腳本) 的預設視圖。 選取此方塊時，開發人員會編輯預設的配置，這會由其他所有設定繼承。

方格左上角的正方形代表 Compact Width/Compact Height Size 類別：

 [![Compact Width/Compact Height Size 類別](unified-storyboards-images/sizeclass07.png)](unified-storyboards-images/sizeclass07.png#lightbox)

此模式對應于橫向的 iPhone。 方格右下角的正方形代表一般寬度/一般高度大小類別，代表 iPad：

 [![一般寬度/一般高度大小類別](unified-storyboards-images/sizeclass08.png)](unified-storyboards-images/sizeclass08.png#lightbox)

若要在直向中編輯 iPhone 的版面配置，請選取左下角的正方形。 這代表 Compact Width/一般高度大小類別：

 [![Compact Width/一般高度大小類別](unified-storyboards-images/sizeclass09.png)](unified-storyboards-images/sizeclass09.png#lightbox)

按一下方塊以選取它，Design Surface 將變更視圖控制器的大小以符合新的選取專案：

 [![Design Surface 將變更視圖控制器的大小，使其符合新的選取專案，如下所示](unified-storyboards-images/sizeclass10.png)](unified-storyboards-images/sizeclass10.png#lightbox)

如需大小類別的詳細資訊，以及它們如何影響 Iphone 和 Ipad 的版面配置，請參閱本文的大小類別一節。

### <a name="adaptive-segue-types"></a>適應性 Segue 類型

如果開發人員先前已使用過腳本，就會熟悉 segue 的 **Push**、 **Modal** 和 **Popover**類型。 在統一的分鏡腳本檔案上啟用大小類別時，會 (對應到上述新 View Controller API 的下列自動調整 Segue 類型) 可供使用： **顯示** 並 **顯示詳細資料**。

> [!IMPORTANT]
> 當大小類別啟用時，任何現有的 segue 都會轉換成新的類型。

採用 iOS 8 應用程式的範例，此應用程式會使用整合分鏡腳本搭配具有主視圖中簡單遊戲導覽功能表的分割視圖控制器。 如果使用者按一下 [功能表] 按鈕，在 iPad 上執行時，[分割視圖控制器] 的 [詳細資料] 區段中應該會顯示所選取專案的 [視圖控制器]。 在 iPhone 上，專案的視圖控制器應該推送至導覽堆疊上。

若要達到此效果，請在 iOS 設計工具控制項中，按一下按鈕，然後將線條拖曳至要顯示的視圖控制器。 放開滑鼠按鍵時， `Show Detail` 從 [Segue 類型] 彈出功能表中選取：

 [![從 Segue 類型的快顯視窗中選取 [顯示詳細資料]](unified-storyboards-images/segue01.png)](unified-storyboards-images/segue01.png#lightbox)

新的 segue 會建立在按鈕和 View 控制器之間。 現在在 iPhone 模擬器中執行應用程式，將會顯示主功能表：

 [![主功能表](unified-storyboards-images/segue02.png)](unified-storyboards-images/segue02.png#lightbox)

按一下 [ **選取遊戲** ] 按鈕，就會將專案的視圖控制器推送至導覽堆疊：

 [![專案視圖控制器將會推送至導覽堆疊上，如下所示](unified-storyboards-images/segue03.png)](unified-storyboards-images/segue03.png#lightbox)

停止 iPhone 模擬器，並在 iPad 模擬器中執行應用程式。 切換至橫向方向，然後再次顯示主功能表：

 [![顯示的主功能表](unified-storyboards-images/segue04.png)](unified-storyboards-images/segue04.png#lightbox)

同樣地，按一下 [ **選取遊戲** ] 按鈕，專案的 View controller 就會顯示在 [分割視圖控制器] 的 [詳細資料] 區段中：

 [![分割區查看控制器的詳細資料區段中顯示的 items View 控制器](unified-storyboards-images/segue05.png)](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>從大小類別中排除元素

有時在特定大小類別內不需要指定的專案 (例如 View、Control 或 Constraint) 。 若要從大小類別中排除元素，請選取要在 **Design Surface**中排除的專案。 滾動至 **屬性瀏覽器** 的底部，然後按一下 **齒輪** 下拉式功能表。 選取 [ **寬度** ] 和 [ **高度** ] 的組合以排除專案：

[![選取寬度和高度的組合](unified-storyboards-images/exclude-a.png)](unified-storyboards-images/exclude-a.png#lightbox)

新的 *排除案例* 將會加入至 **屬性瀏覽器**底部的元素中。 接下來，針對指定的大小類別取消核取 [ **已安裝** ] 核取方塊：

[![取消核取 [已安裝] 核取方塊](unified-storyboards-images/exclude-b.png)](unified-storyboards-images/exclude-b.png#lightbox)

將 Design Surface 切換至已排除專案的寬度和高度，它已從指定的大小類別中移除，而不是從整個 UI 設計中移除：

 [![將 Design Surface 切換為排除專案的寬度和高度](unified-storyboards-images/exclude02.png)](unified-storyboards-images/exclude02.png#lightbox)

切換回 [任何寬度]/[任何高度大小] 類別，元素仍在原處：

 [![切換回任何寬度/任何高度大小類別](unified-storyboards-images/exclude03.png)](unified-storyboards-images/exclude03.png#lightbox)

當應用程式在 iPad 模擬器中執行時，元素會出現：

 [![IPad 模擬器中執行中的應用程式時所顯示的元素](unified-storyboards-images/exclude04.png)](unified-storyboards-images/exclude04.png#lightbox)

當應用程式在 iPhone 模擬器上執行時，元素就會遺失：

 [![IPhone 模擬器中執行中的應用程式時，遺漏的元素](unified-storyboards-images/exclude05.png)](unified-storyboards-images/exclude05.png#lightbox)

若要從專案中移除排除案例，只要選取 [ **Design Surface**中的專案，並移至 **屬性瀏覽器** 的底部，然後按一下 **-** 案例旁的按鈕即可移除。

若要查看整合分鏡腳本的執行，請查看 `UnifiedStoryboard` 附加到此檔的範例 Xamarin iOS 8 應用程式。

## <a name="dynamic-launch-screens"></a>動態啟動畫面

啟動畫面檔案會在 iOS 應用程式啟動時顯示為啟動顯示畫面，以提供意見反應給使用者，指出應用程式正在啟動。 在 iOS 8 之前，開發人員必須 `Default.png` 針對應用程式執行所在的每種裝置類型、方向和螢幕解析度，包含多個影像資產。 例如，、、等等 `Default@2x.png` `Default-Landscape@2x~ipad.png` `Default-Portrait@2x~ipad.png` 。

在新的 iPhone 6 和 iPhone 6 Plus 裝置中， (以及即將推出的 Apple Watch) 與所有現有的 iPhone 和 iPad 裝置相結合，這代表了一大範圍，也 `Default.png` 就是必須建立和維護的啟動畫面影像資產的各種大小、方向和解析度。 此外，這些檔案可能很大，而且會「膨脹」交付項應用程式套件組合，以增加從 iTunes 下載應用程式所需的時間 App Store (可能會使其無法透過行動電話通訊網路進行傳遞) 並增加終端使用者裝置上所需的儲存空間量。

第8版的新功能，開發人員可以在 Xcode 中建立單一的不可部分完成檔案，其 `.xib` 使用自動設定和大小類別來建立 *動態啟動畫面* ，以供每個裝置、解析度和方向使用。 這不僅能減少開發人員建立及維護所有必要影像資產所需的工作量，還可大幅減少應用程式的已安裝套件組合大小。

動態啟動畫面有下列限制與考慮：

- 只使用 `UIKit` 類別。
- 使用單一根視圖，也就是 `UIView` 或 `UIViewController` 物件。
- 請勿對應用程式的程式碼進行任何連接 (不會) 新增 **動作** 或 **輸出** 。
- 請勿新增 `UIWebView` 物件。
- 請勿使用任何自訂類別。
- 請勿使用執行時間屬性。

考慮上述指導方針，讓我們看看如何將動態啟動畫面新增至現有的 Xamarin iOS 8 專案。

執行下列動作：

1. 開啟 **Visual Studio for Mac** 並載入 **解決方案** ，以將動態啟動畫面新增至。
2. 在**方案總管**中，以滑鼠右鍵按一下檔案 `MainStoryboard.storyboard` ，然後選取 [**開啟方式**  >  **Xcode Interface Builder**：

    [![以 Xcode Interface Builder 開啟](unified-storyboards-images/dls01.png)](unified-storyboards-images/dls01.png#lightbox)
3. 在 [Xcode] 中 **，選取 [** 檔案  >  **新**檔案  >  **...**：

    [![選取 [檔案]/[新增]](unified-storyboards-images/dls02.png)](unified-storyboards-images/dls02.png#lightbox)
4. 選取**iOS**  >  **消費者介面**  >  **啟動畫面**，然後按 [**下一步]** 按鈕：

    [![選取 iOS/消費者介面/啟動畫面](unified-storyboards-images/dls03.png)](unified-storyboards-images/dls03.png#lightbox)
5. 為檔案命名 `LaunchScreen.xib` ，然後按一下 [ **建立** ] 按鈕：

    [![將檔案命名為 LaunchScreen. xib](unified-storyboards-images/dls04.png)](unified-storyboards-images/dls04.png#lightbox)
6. 藉由新增圖形元素並使用版面配置條件約束，來編輯啟動畫面的設計，以針對指定的裝置、方向和螢幕大小來放置它們：

    [![編輯啟動畫面的設計](unified-storyboards-images/dls05.png)](unified-storyboards-images/dls05.png#lightbox)
7. 將變更儲存至 `LaunchScreen.xib`。
8. 選取 [ **應用程式目標** ] 和 [ **一般** ] 索引標籤：

    [![選取 [應用程式目標] 和 [一般] 索引標籤](unified-storyboards-images/dls06.png)](unified-storyboards-images/dls06.png#lightbox)
9. 按一下 [ **選擇資訊] plist** 按鈕，選取 `Info.plist` Xamarin 應用程式的，然後按一下 [ **選擇** ] 按鈕：

    [![選取 Xamarin 應用程式的資訊 plist](unified-storyboards-images/dls07.png)](unified-storyboards-images/dls07.png#lightbox)
10. 在 [ **應用程式圖示和啟動影像** ] 區段中，開啟 [ **啟動畫面** 檔案] 下拉式清單，然後選擇 `LaunchScreen.xib` 上面建立的：

    [![選擇 [LaunchScreen] xib。](unified-storyboards-images/dls08.png)](unified-storyboards-images/dls08.png#lightbox)
11. 將變更儲存至檔案，並返回 Visual Studio for Mac。
12. 等候 Visual Studio for Mac 完成與 Xcode 同步處理變更。
13. 在 [**方案總管**中，以滑鼠右鍵按一下 [**資源**] 資料夾，然後選取 [**新增**  >  **新增檔案 ...**：

    [![選取 [新增/新增檔案 ...]](unified-storyboards-images/dls09.png)](unified-storyboards-images/dls09.png#lightbox)
14. 選取 `LaunchScreen.xib` 上面建立的檔案，然後按一下 [ **開啟** ] 按鈕：

    [![選取 LaunchScreen xib 檔案](unified-storyboards-images/dls10.png)](unified-storyboards-images/dls10.png#lightbox)
15. 建置應用程式。

### <a name="testing-the-dynamic-launch-screen"></a>測試動態啟動畫面

在 Visual Studio for Mac 中，選取 iPhone 4 Retina 模擬器，然後執行應用程式。 動態啟動畫面將會以正確的格式和方向顯示：

[![以垂直方向顯示的動態啟動畫面](unified-storyboards-images/dls11.png)](unified-storyboards-images/dls11.png#lightbox)

在 Visual Studio for Mac 中停止應用程式，然後選取 iPad iOS 8 裝置。 執行應用程式，此裝置和方向的啟動畫面將會正確格式化：

[![動態啟動畫面以水準方向顯示](unified-storyboards-images/dls12.png)](unified-storyboards-images/dls12.png#lightbox)

返回 Visual Studio for Mac 並停止執行應用程式。

### <a name="working-with-ios-7"></a>使用 iOS 7

若要維持與 iOS 7 的回溯相容性，只要 `Default.png` 在 ios 8 應用程式中包含一般的映射資產即可。 iOS 會回到先前的行為，並在 iOS 7 裝置上執行時，使用這些檔案作為啟動畫面。

若要在 Xamarin 中查看動態啟動畫面的執行，請查看附加至此檔的 [動態啟動畫面](/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen) 範例 iOS 8 應用程式。

## <a name="summary"></a>摘要

本文將快速查看大小類別，以及它們如何影響 iPhone 和 iPad 裝置中的版面配置。 其中討論了特性、特性環境和特性集合如何使用大小類別來建立整合介面。 它會簡短地查看調適型視圖控制器，以及它們如何搭配整合介面內的大小類別運作。 它探討了從 Xamarin iOS 8 應用程式內的 c # 程式碼完全執行大小類別和整合介面。

最後，本文涵蓋了使用 Xamarin iOS 設計工具建立整合分鏡腳本的基本概念，可跨 iOS 裝置運作，並建立單一動態啟動畫面，以在每個 iOS 8 裝置上顯示為啟動畫面。

## <a name="related-links"></a>相關連結

- [調適型相片 (範例) ](/samples/xamarin/ios-samples/ios8-adaptivephotos)
- [動態啟動畫面 (範例) ](/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
- [IOS8 中的動態配置-演進 2014 (影片) ](https://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)