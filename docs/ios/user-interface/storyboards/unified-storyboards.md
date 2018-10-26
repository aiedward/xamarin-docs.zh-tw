---
title: 在 Xamarin.iOS 中統一的分鏡腳本
description: 本文件說明在 Xamarin.iOS 中統一的分鏡腳本。 統一的分鏡腳本讓開發人員支援多種螢幕大小與單一的介面定義。
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 26aeaa3d230a5c104014edd899b8d9231ced31e9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108518"
---
# <a name="unified-storyboards-in-xamarinios"></a>在 Xamarin.iOS 中統一的分鏡腳本

iOS 8 包含新的、 簡單易用的機制，來建立使用者介面，統一的分鏡腳本。 單一分鏡腳本可以涵蓋各種不同的硬體螢幕大小，以快速且回應迅速的檢視中可建立 「 設計-一次的使用對多 」 樣式。

開發人員不再需要建立適用於 iPhone 和 iPad 裝置的個別和特定分鏡腳本，因為它們會有彈性地設計應用程式使用的通用介面，然後再自訂該介面的大小不同類別。 在這種方式，可調整應用程式，每個表單係數的長處，可以微調的每個使用者介面，以提供最佳體驗。

<a name="size-classes" />

## <a name="size-classes"></a>大小類別

在 iOS 8 之前，開發人員會使用`UIInterfaceOrientation`和`UIInterfaceIdiom`區分直向和橫向模式，以及 iPhone 與 iPad 裝置之間。 在 iOS8 方向和裝置由決定是使用*大小類別*。

裝置由大小類別定義中同時顯示垂直與水平軸，並有兩種類型的大小在 iOS 8 中的類別：

-  **一般**– 這是大型的螢幕大小 （例如 iPad) 或一個大型的印象小工具 (例如 `UIScrollView`
-  **Compact** – 這是較小的裝置 （例如 iPhone)。 此大小會考慮到裝置的方向。


如果兩個概念會搭配使用，結果會是一個 2x2 方格，其中定義可用於這兩個不同的方向，不同的可能大小，如下圖所示：

 [![](unified-storyboards-images/sizeclassgrid.png "在一般和 Compact 方向定義可以用不同的可能大小 2x2 方格")](unified-storyboards-images/sizeclassgrid.png#lightbox)

開發人員可以建立會使用四個可能性 （如同所見，如上圖所示），會導致不同的版面配置的任何一個檢視控制器。

### <a name="ipad-size-classes"></a>iPad 大小類別

IPad，因為大小，而具有**一般**類別針對這兩個方向的大小。

 [![](unified-storyboards-images/image1.png "iPad 大小類別")](unified-storyboards-images/image1.png#lightbox)


### <a name="iphone-size-classes"></a>iPhone 大小類別

IPhone 具有不同的大小類別，根據裝置的方向：

 [![](unified-storyboards-images/iphonesizeclasses.png "iPhone 大小類別")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

-  當裝置處於直向模式時，畫面會有**compact**水平類別和**規則**垂直
-  裝置在橫向模式中時，畫面類別會反轉從直向模式中。

### <a name="iphone-6-plus-size-classes"></a>iPhone 6 Plus 大小類別

大小是在直向，但使用不同架構中較早的 Iphone 相同：

[![](unified-storyboards-images/iphone6sizeclasses.png "iPhone 6 Plus 大小類別")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

因為 iPhone 6 Plus 已經夠大的螢幕，就能夠以橫向模式擁有一般寬度大小類別。

### <a name="support-for-a-new-screen-scale"></a>支援新的螢幕縮放比例

IPhone 6 Plus 將新的 Retina HD 顯示使用 3.0 （三次原始 iPhone 螢幕解析度） 畫面縮放比例。 若要在這些裝置上提供最佳的使用經驗，包括新的插圖這個畫面規模而設計的。 在 Xcode 6 及更新版本，資產目錄可包含 1 x，2 倍，以及 3 x 大小; 的影像只需新增 新的影像資產和 iOS 會選擇正確的資產在 iPhone 6 上執行時加上。

映像載入在 iOS 中的行為也會辨識`@3x`映像檔上的後置詞。 例如，如果開發人員在應用程式的套件組合，使用下列的檔案名稱中包含 （在不同的解決方式） 的影像資產： `MonkeyIcon.png`， `MonkeyIcon@2x.png`，和`MonkeyIcon@3x.png`。 在 iphone 6 Plus`MonkeyIcon@3x.png`如果開發人員載入映像使用下列程式碼映像也會自動使用：

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```
或如果他們將映像指派給使用 iOS 的 UI 項目做為設計工具`MonkeyIcon.png`，則`MonkeyIcon@3x.png`將會使用，自動重新 iPhone 6 上加上。

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>動態啟動畫面

啟動畫面檔案會顯示為啟動顯示畫面中，當 iOS 應用程式，就啟動應用程式會實際啟動增加的使用者提供意見反應。 在 iOS 8 之前，開發人員必須包含多個`Default.png`映像的應用程式會在執行每個裝置類型、 方向和螢幕解析度的資產。

新增至 iOS 8、 開發人員可以建立單一、 不可部分完成`.xib`檔案中使用自動版面配置和大小類別建立的 Xcode*動態啟動螢幕*，適用於每個裝置、 解析度和方向。 這不僅減少的工作所需的開發人員建立並維護所有必要的影像資產，但可減少應用程式的已安裝的套件組合的大小。

## <a name="traits"></a>特性

特性是可用來判斷如何配置變更為其環境變更的屬性。 它們包含一組屬性 (`HorizontalSizeClass`並`VerticalSizeClass`根據`UIUserInterfaceSizeClass`)，以及介面的慣用語 ( `UIUserInterfaceIdiom`) 和 顯示縮放比例。

所有上述的狀態會包裝在 Apple 將稱為特性集合的容器 ( `UITraitCollection`)，其中包含不只屬性，但其值。

## <a name="trait-environment"></a>特性環境

特性環境的新的介面，在 iOS 8 中，而且能夠傳回特性集合中的下列物件：

-  畫面 ( `UIScreens` )。
-  Windows ( `UIWindows` )。
-  檢視控制器 ( `UIViewController` )。
-  檢視 ( `UIView` )。
-  展示控制站 ( `UIPresentationController` )。


開發人員會使用以判斷如何使用者介面應該配置特性環境所傳回的特徵集合。

所有的特性環境會使階層架構，如下圖所示：

 [![](unified-storyboards-images/viewhierarchy.png "特性環境階層架構圖表")](unified-storyboards-images/viewhierarchy.png#lightbox)

特性集合，每個上述特性環境有流動，根據預設，從父代的子系的環境。

取得目前的特性集合，除了特性環境具有`TraitCollectionDidChange`可以檢視或檢視控制器的子類別中覆寫的方法。 開發人員可以使用這個方法來修改任何取決於特性，這些特性已變更時的 UI 項目。

## <a name="typical-trait-collections"></a>一般特性集合

本節將討論使用 iOS 8 時，使用者會體驗的特性集合的一般類型。

以下是開發人員可能會看到在 iPhone 的一般特性集合：

|屬性|值|
|--- |--- |
|`HorizontalSizeClass`|壓縮|
|`VerticalSizeClass`|Regular|
|`UserInterfaceIdom`|電話|
|`DisplayScale`|2.0|

上述的設定會代表完整限定特性集合，因為它具有所有其特性屬性的值。

它也可擁有遺失它的某些值的特徵集合 (Apple 為指向*未指定*):

|屬性|值|
|--- |--- |
|`HorizontalSizeClass`|壓縮|
|`VerticalSizeClass`|未指定|
|`UserInterfaceIdom`|未指定|
|`DisplayScale`|未指定|

一般而言，不過，當開發人員會要求其特性集合特性環境，它會傳回完整的集合在上述範例所示。

如果特性環境 （例如，檢視或檢視控制器） 不是在目前的檢視階層架構內，開發人員可能會得到未指定的值的一個或多個特性屬性。

如果它們使用其中一個這類 Apple，提供建立方法，開發人員也會取得部分限定的特性集合`UITraitCollection.FromHorizontalSizeClass`，以建立新的集合。

可以在多個特性集合執行的一項作業會比較它們彼此，其中包含要求一個特性集合是否包含另一個。 代表什麼意思*內含項目*是，針對第二個集合中指定的任何特性，值必須完全符合的第一個集合中的值。

若要測試使用兩種特點`Contains`方法的`UITraitCollection`傳入要測試之特性的值。

開發人員可以手動執行比較，來判斷程式碼中如何配置檢視或檢視控制器。 不過，`UIKit`提供其中一些功能，如同外觀 Proxy，例如在內部使用這個方法。

## <a name="appearance-proxy"></a>外觀 Proxy

外觀 Proxy 引進了在舊版 iOS，讓開發人員可以自訂其檢視的屬性。 它已經過擴充，在 iOS 8，以支援特性的集合。

外觀 Proxy 現在包含新的方法， `AppearanceForTraitCollection`，傳入的給定特性集合傳回新的外觀 Proxy。 開發人員可以執行的任何自訂項目外觀 Proxy 才會生效符合檢視表上指定的特徵集合。

通常開發人員將會傳入部分指定特性集合`AppearanceForTraitCollection`方法，例如剛才所指定水平大小類別的壓縮，以便他們可以自訂任何檢視會水平精簡應用程式中的其中一個。

## <a name="uiimage"></a>UIImage

Apple 已新增特性集合的另一個類別`UIImage`。 在過去，開發人員就必須指定@1X和@2x它們要在應用程式 （例如圖示） 中包含任何點陣圖圖形資產的版本。

iOS 8 已經過擴充，以允許開發人員根據特性集合映像目錄中包含多個版本的映像。 比方說，開發人員可能包含較小的影像，來使用 Compact 特性類別和完整大小的影像，為任何其他集合。

一個映像內的使用時機`UIImageView`映像檢視的類別，會自動顯示其特性集合的映像的正確版本。 如果特性環境變更 （例如使用者切換從直向裝置為橫向），映像檢視將會自動選取新的影像大小，以符合新的特徵集合，並變更其大小以符合目前版本的映像顯示此項目。

## <a name="uiimageasset"></a>UIImageAsset

Apple 已新增一個新類別至 iOS 8 `UIImageAsset` ，讓開發人員更多控制映像選取項目。

影像資產包裝所有不同版本的映像，並可讓開發人員，尋求特定的映像符合傳入的特徵集合。 可以新增或移除映像資產，從映像上即時。

如需有關影像資產的詳細資訊，請參閱 Apple [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)文件。

## <a name="combining-trait-collections"></a>結合特性集合

相加兩個可為開發人員可以執行特性集合的另一個函式會導致結合的集合，其中一個集合中未指定的值會取代的第二個指定的值。 這是使用`FromTraitsFromCollections`方法的`UITraitCollection`類別。

如上所述，如果任何特性其中一個特性集合沒有指定，而且在另一個指定，值會設定為指定的版本。 不過，如果有指定的值，指定多個版本，從最後一個值特性集合會所使用的值。

## <a name="adaptive-view-controllers"></a>調適性的檢視控制器

本節將討論如何檢視和檢視控制器的 iOS 已採用的特性和大小類別自動更具彈性的開發人員應用程式中的概念的詳細資料。

### <a name="split-view-controller"></a>分割檢視控制器

其中一個最多可以在 iOS 8 中已變更的檢視控制器類別是`UISplitViewController`類別。 在過去，開發人員通常會使用分割檢視控制器 iPad 版的應用程式，則必須以完全不同的版本，其檢視階層架構的應用程式的 iPhone 版本。

在 iOS 8`UISplitViewController`類別有兩種平台 （iPad 和 iPhone），可讓開發人員建立一個將 iPhone 與 iPad 函式的檢視控制器階層。

在橫向 iPhone 時，分割檢視控制器將會顯示其檢視-並存，就如同顯示在 iPad 上時。

### <a name="overriding-traits"></a>覆寫特性

特徵環境從父容器向下級聯到子容器，如下圖所示，以橫向顯示iPad上的分割視圖控制器：

 [![](unified-storyboards-images/cascadingclasses01.png "分割檢視控制器上以橫向 iPad")](unified-storyboards-images/cascadingclasses01.png#lightbox)

因為 iPad 具有一般的大小類別中的水平和垂直的方向，分割檢視會顯示在主要和詳細資料檢視。

在 iPhone 中，其中大小類別是在這兩個方向 compact，分割檢視控制器只會顯示詳細資料 檢視中，如下所示：

 [![](unified-storyboards-images/cascadingclasses02.png "分割檢視控制器只會顯示詳細資料檢視")](unified-storyboards-images/cascadingclasses02.png#lightbox)

在開發人員希望在橫向顯示iPhone上的主視圖和詳細視圖的應用程序中，開發人員必須為分割視圖控制器插入父容器並覆蓋特徵集合。 下圖所示：

 [![](unified-storyboards-images/cascadingclasses03.png "開發人員必須分割檢視控制器中插入的父容器，並覆寫特性集合")](unified-storyboards-images/cascadingclasses03.png#lightbox)

A`UIView`設定為分割檢視控制器的父系和`SetOverrideTraitCollection`檢視傳入新的特徵集合與目標分割檢視控制器上呼叫方法。 新的特徵集合將覆蓋`HorizontalSizeClass`，設置為`Regular`，以便分割視圖控制器將以橫向顯示iPhone上的主視圖和詳細視圖。

請注意，`VerticalSizeClass`已設為`unspecified`，這可讓新的特徵集合，可以加入至父項、 特性集合導致`Compact VerticalSizeClass`子分割檢視控制器。

### <a name="trait-changes"></a>特性變更

此區段會看，在詳細的說明，如何特性集合轉換特性環境變更時。 例如，當裝置從旋轉直向變成橫向。

 [![](unified-storyboards-images/traittransitions01.png "直向變成橫向特性變更概觀")](unified-storyboards-images/traittransitions01.png#lightbox)

首先，iOS 8 會執行某些安裝程式，以準備進行轉換。 接下來，系統以動畫顯示轉換狀態。 最後，iOS 8 清除在轉換期間所需的任何暫存狀態。

iOS 8 提供數個開發人員可以使用下表所示，參與特性變更的回呼：

|Phase|回呼|描述|
|--- |--- |--- |
|安裝程式|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>特性集合取得設為其新值之前，取得特性變更開頭呼叫這個方法。</li><li>特性集合的值變更時，但任何動畫發生之前，會呼叫方法。</li></ul>|
|動畫|`WillTransitionToTraitCollection`|取得傳遞至這個方法轉換協調器`AnimateAlongside`屬性，可讓開發人員加入將會執行，以及預設動畫的動畫。|
|清除|`WillTransitionToTraitCollection`|提供適用於開發人員，以轉換之後包含自己的清除程式碼的方法。|

`WillTransitionToTraitCollection`方法適合用來建立檢視控制器動畫以及特性集合變更。 `WillTransitionToTraitCollection`方法才可以使用檢視控制器 ( `UIViewController`) 而不是在其他特性的環境，例如`UIViews`。

`TraitCollectionDidChange`很適合使用`UIView`類別，開發人員，想要更新 UI，如特性會變更。

### <a name="collapsing-the-split-view-controllers"></a>摺疊分割檢視控制器

現在讓我們來仔細瞧瞧發生於分割檢視控制器選取範圍摺疊兩個資料行從一個資料行檢視。 隨著這項變更的詳細資訊，有兩個要發生的處理序：

-  根據預設，分割檢視控制器會使用主要檢視控制器與檢視摺疊發生之後。 開發人員可以藉由覆寫項目覆寫這個行為`GetPrimaryViewControllerForCollapsingSplitViewController`方法的`UISplitViewControllerDelegate`並提供他們想要在摺疊的狀態中顯示任何檢視控制器。
-  次要的檢視控制器，就必須取得合併到主要的檢視控制器。 通常開發人員不需要採取任何動作，此步驟中;分割檢視控制器包含自動處理基礎硬體裝置上的這個階段。 不過，可能有一些特殊的情況下，開發人員會希望這項變更進行互動。 呼叫`CollapseSecondViewController`方法的`UISplitViewControllerDelegate`允許摺疊的發生，而不是詳細資料檢視時要顯示的主版檢視控制器。


### <a name="expanding-the-split-view-controller"></a>展開 分割檢視控制器

現在讓我們來仔細瞧瞧時會分割檢視控制器會展開與摺疊的狀態。 同樣地，有必要進行的兩個階段：

-  首先，定義新的主要檢視控制器。 根據預設，分割檢視控制器會自動使用主要的檢視控制器，從摺疊的檢視。 同樣地，開發人員可以覆寫這個行為使用`GetPrimaryViewControllerForExpandingSplitViewController`方法的`UISplitViewControllerDelegate`。
-  一旦選擇主要的檢視控制器，就必須重新建立次要的檢視控制器。 同樣地，分割檢視控制器包含自動處理基礎硬體裝置上的這個階段。 開發人員可以藉由呼叫項目覆寫這個行為`SeparateSecondaryViewController`方法的`UISplitViewControllerDelegate`。


在分割檢視控制器中，主要的檢視控制器來扮演一個展開和摺疊的檢視，藉由實作`CollapseSecondViewController`並`SeparateSecondaryViewController`方法`UISplitViewControllerDelegate`。 `UINavigationController` 實作這些方法會自動推入和彈出次要的檢視控制器。

### <a name="showing-view-controllers"></a>顯示檢視控制器

Apple 對 iOS 8 的另一項變更是，開發人員會顯示檢視控制器的方式。 在過去，如果應用程式有分葉檢視控制器 （例如資料表檢視控制器），而且開發人員示範了 （比方說，為了回應使用者點選的儲存格上），另一個應用程式會透過控制器階層，以達到瀏覽檢視控制器，以及呼叫`PushViewController`方法比對以顯示新的檢視。

這顯示瀏覽控制器與環境中執行之間非常緊密結合。 在 iOS 8 中，Apple 已分離這藉由提供兩種新方法：

-  `ShowViewController` – 適應顯示新的檢視控制器，根據其環境而定。 例如，在`UINavigationController`它只會將推送至堆疊的新檢視。 在分割檢視控制器中，新的檢視控制器將會顯示在左邊為新的主要檢視控制器。 如果沒有容器檢視控制器，則新的檢視會顯示為強制回應的檢視控制器中。
-  `ShowDetailViewController` -運作方式類似與`ShowViewController`，但實作來取代傳入新的檢視控制器的詳細資料檢視分割檢視控制器上。 如果分割檢視控制器會摺疊 （可能會看到在 iPhone 應用程式），呼叫將會重新導向至`ShowViewController`方法和新的檢視會顯示為主要的檢視控制器。 同樣地，如果沒有容器檢視控制器，則新的檢視會顯示為強制回應的檢視控制器。


這些方法開始分葉檢視控制器來處理，而且走向檢視階層，直到他們發現正確的容器檢視控制器來處理新的檢視顯示。

開發人員可以實作`ShowViewController`並`ShowDetailViewController`自己自訂的檢視控制器，以取得在相同自動化功能，`UINavigationController`和`UISplitViewController`提供。

### <a name="how-it-works"></a>它的運作方式

在本節中，我們要看看如何實作這些方法會實際在 iOS 8 中。 第一個讓我們看看新`GetTargetForAction`方法：

 [![](unified-storyboards-images/gettargetforaction.png "新的 GetTargetForAction 方法")](unified-storyboards-images/gettargetforaction.png#lightbox)

這個方法會逐步引導的階層鏈結，直到找到正確的容器檢視控制器。 例如: 

1.  如果`ShowViewController`呼叫方法時，第一個檢視控制器會實作這個方法鏈結中，瀏覽控制器，它正做為新檢視的父系。
1.  如果`ShowDetailViewController`改為呼叫方法，分割檢視控制器是第一個檢視控制器以進行實作，所以它會使用與父代。


`GetTargetForAction`方法的運作方式是尋找檢視控制器會實作指定的動作，然後要求該檢視控制器，如果它要接收該動作。 這個方法是公用的因為開發人員可以建立自己的自訂方法的函式就像內建`ShowViewController`和`ShowDetailViewController`方法。

## <a name="adaptive-presentation"></a>自適性簡報

在 iOS 8 中，Apple 已 Popover 簡報 ( `UIPopoverPresentationController`) 自動調整以及。 因此 Popover 簡報檢視控制器會自動將會顯示在一般的大小類別中，標準的 Popover 檢視，但是會顯示它的全螢幕中水平 compact 的大小類別 (例如在 iPhone 上)。

為了符合統一的分鏡腳本系統內的變更，新的控制器物件建立來管理呈現的檢視控制器 — `UIPresentationController`。 此控制站會建立從檢視控制器會關閉的時間。 如同管理的類別，它可以視為超級類別透過檢視控制器回應裝置的變更會影響到檢視控制器 （例如方向），哪些則檢視控制器送回簡報控制站會控制。

開發人員提供的檢視控制器 using`PresentViewController`方法，在呈現程序管理交付給`UIKit`。 UIKit 處理 （還有其他項目） 所建立的樣式為正確的控制器，但唯一的例外狀況時正在檢視控制器有樣式設為`UIModalPresentationCustom`。 在這裡，應用程式可以提供自己 PresentationController，而非使用時會很`UIKit`控制站。

### <a name="custom-presentation-styles"></a>自訂的展示樣式

使用自訂的展示樣式、 開發人員可以選擇使用自訂的簡報控制器。 此自訂的控制站可用來修改外觀和行為，它要結盟的檢視。

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>使用大小類別

調適性的相片 Xamarin 專案隨附這篇文章提供 iOS 8 整合介面應用程式中使用大小類別和調適性的檢視控制器的工作範例。

雖然應用程式建立其 UI 完全從程式碼，而不是使用 IOS 設計工具，並建立統一的分鏡腳本，請套用相同的技術。 稍後在本文中，我們將示範如何使用統一的分鏡腳本和 iOS 的 Xamarin 應用程式中的設計工具中使用大小類別。

現在讓我們看看如何調適性的相片專案會實作數個大小類別功能在 iOS 8 建立自適性應用程式中。

### <a name="adapting-to-trait-environment-changes"></a>調整特性環境的變更

當使用者旋轉裝置從直向變成橫向時，請在 iPhone 中，執行自動調整的相片應用程式，分割檢視控制器會顯示在主要和詳細資料檢視：

 [![](unified-storyboards-images/rotation.png "分割檢視控制器會顯示這兩個主要和詳細資料檢視，如下所示")](unified-storyboards-images/rotation.png#lightbox)

這可以藉由覆寫`UpdateConstraintsForTraitCollection`的檢視控制器，並調整條件約束的方法為基礎的值`VerticalSizeClass`。 例如: 

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

### <a name="adding-transition-animations"></a>新增轉換動畫

分割檢視控制器的自適性應用程式會從的相簿摺疊時為擴充，動畫會新增至預設動畫覆寫`WillTransitionToTraitCollection`檢視控制器方法。 例如: 

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

如上面所示，調適性的相片應用程式時，會強制顯示同時詳細資料分割檢視控制器和主版檢視 iPhone 裝置為橫向檢視。

這是在檢視控制器中使用下列程式碼來完成：

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

### <a name="expanding-and-collapsing-the-split-view-controller"></a>展開和摺疊分割檢視控制器

下一步來看看如何在 Xamarin 中實作分割檢視控制器的展開和摺疊行為。 在  `AppDelegate`、 建立分割檢視控制器時，其委派指派給處理這些變更：

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

`SeparateSecondaryViewController`方法不會測試以查看相片是否正在顯示，而會依據該狀態的動作。 如果沒有相片顯示它摺疊次要的檢視控制器，以便顯示主版檢視控制器。

`CollapseSecondViewController`方法來擴充分割檢視控制器時是否在堆疊上，是否存在任何讓它回到該檢視會摺疊。

### <a name="moving-between-view-controllers"></a>檢視控制器之間移動

接下來，讓我們看看如何調適性的相片應用程式檢視控制器之間移動時。 在 `AAPLConversationViewController`類別，在使用者從資料表中，選取該儲存格`ShowDetailViewController`方法呼叫，以顯示詳細資料檢視：

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

自適性相片應用程式中有數個位置其中洩漏指示器會隱藏或顯示根據變更特性環境。 這被處理為下列程式碼：

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

這些會實使用`GetTargetViewControllerForAction`詳細上面討論的方法。

資料表檢視控制器會顯示資料時，它會使用以查看發生，即將推播，以及要顯示或隱藏洩漏指示器據此實作上述的方法：

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

### <a name="new-showdetailtargetdidchangenotification-type"></a>新`ShowDetailTargetDidChangeNotification`類型

Apple 已為使用大小類別和特性環境內分割檢視控制器，新增新的通知類型`ShowDetailTargetDidChangeNotification`。 每次變更目標分割檢視控制器的詳細資料檢視，例如控制器時展開或摺疊時，取得傳送此通知。

自適性相片應用程式會使用這項通知詳細資料檢視控制器變更時更新洩漏指標的狀態：

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

仔細看看調適性的相片應用程式，若要查看的各種方式的大小類別，可用來輕鬆建立在 Xamarin.iOS 中的 整合的應用程式特性集合和調適性的檢視控制器。

## <a name="unified-storyboards"></a>統一的分鏡腳本

新增至 iOS 8、 統一的分鏡腳本可讓開發人員建立一個，統一的分鏡腳本檔案可以顯示在 iPhone 和 iPad 裝置上，將目標設為多個大小類別。 藉由使用統一的分鏡腳本，開發人員撰寫較少 UI 特定的程式碼，並具有建立和維護的只有一個介面設計。

統一的分鏡腳本的主要優點如下：

-  IPhone 與 iPad，使用相同的分鏡腳本檔案。
-  部署至 iOS 6 和 iOS 7 的回溯。
-  預覽不同裝置、 方向和 OS 版本所有的 Xamarin iOS 設計工具內的版面配置。

這項功能完全支援 Visual Studio for Mac

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>啟用大小類別

根據預設，任何新的 Xamarin.iOS 專案將使用大小類別。 若要從較舊的專案分鏡腳本內使用大小類別和設定 Segue 的自適性，它必須先轉換成 iOS 設計工具內的 從 Xcode 6 統一的分鏡腳本格式。

若要執行這會開啟要轉換在設計工具並檢查 iOS 分鏡腳本**使用大小類別**核取方塊：

 [![](unified-storyboards-images/sizeclass01.png "使用大小類別 核取方塊")](unified-storyboards-images/sizeclass01.png#lightbox)

IOS 設計工具將會確認開發人員想要轉換的使用大小類別將分鏡腳本格式：

 [![](unified-storyboards-images/sizeclass02.png "使用大小類別警示")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> 自動配置也必須檢查大小類別才能正常運作。

### <a name="generic-device-types"></a>泛型裝置類型

一旦將分鏡腳本已轉換成使用大小類別，它將會重新顯示在設計介面中，**檢視為**裝置將會是泛型：

 [![](unified-storyboards-images/sizeclass03.png "檢視為泛用的裝置類型")](unified-storyboards-images/sizeclass03.png#lightbox)

選取的泛用的裝置類型時，所有的檢視控制器會調整大小為 600 x 600 方形。 此方塊代表任何寬度和高度的大小。 當 iOS 設計工具在此模式中，任何的編輯會套用至所有大小類別。

開發人員也可以選擇檢視為 iPhone 的設計介面：

 [![](unified-storyboards-images/sizeclass04.png "IPhone 檢視的設計介面")](unified-storyboards-images/sizeclass04.png#lightbox)

或者，檢視為 iPad:

 [![](unified-storyboards-images/sizeclass05.png "IPad 檢視的設計介面")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>選取的大小類別

大小類別選取器按鈕位於設計介面 （靠近檢視方式 下拉式清單中） 的左上角。 它可讓開發人員選取目前正在編輯的大小類別：

 [![](unified-storyboards-images/sizeclass06.png "選取的大小類別")](unified-storyboards-images/sizeclass06.png#lightbox)

選取器會提供為 3x3 的方格的大小類別選取項目。 每一個方格中的平方代表類別寬度和高度類別的組合。 中央的矩形會選取任何 Width/Any 高度大小 （也就是統一的分鏡腳本的預設檢視）。 選取此方塊時，開發人員就編輯預設的版面配置，該層會繼承的所有其他設定。

在方格的左上角正方形代表 Compact 的 Compact 寬度/高度的大小類別：

 [![](unified-storyboards-images/sizeclass07.png "Compact 的寬度/Compact 高度大小類別")](unified-storyboards-images/sizeclass07.png#lightbox)

此模式對應於橫向的iPhone。 在方格的右下角正方形代表規則一般寬度/高度的大小類別，其代表 iPad:

 [![](unified-storyboards-images/sizeclass08.png "一般的寬度/一般高度大小類別")](unified-storyboards-images/sizeclass08.png#lightbox)

若要編輯適用於 iPhone 的將縱向列印版面配置，請在左下角選取平方。 這代表 Compact 的一般寬度/高度的大小類別：

 [![](unified-storyboards-images/sizeclass09.png "Compact 的寬度/一般高度大小類別")](unified-storyboards-images/sizeclass09.png#lightbox)

按一下以選取它的矩形，並在設計介面，將檢視控制器，以符合新的選取範圍的大小：

 [![](unified-storyboards-images/sizeclass10.png "在設計介面，將檢視控制器，以符合新的選取範圍，如所示的大小")](unified-storyboards-images/sizeclass10.png#lightbox)

大小類別和它們如何影響適用於 Iphone 和 Ipad 的版面配置，請參閱這篇文章，如需詳細資訊的大小類別一節。

### <a name="adaptive-segue-types"></a>自適性 Segue 類型

如果開發人員已使用分鏡腳本，則將其與現有的 segue 類型的熟悉**推播**，**強制回應**並**Popover**。 統一的分鏡腳本檔案上啟用大小類別時, 都會提供下列自適性 Segue 類型 （對應至新的檢視控制器 API 上面所討論）：**顯示**並**顯示詳細資料**.

> [!IMPORTANT]
> 啟用大小類別時，任何現有的 segue 會轉換成新的類型。

取得範例 iOS 8 的應用程式會使用分割檢視控制器在主版檢視具有簡單的遊戲導覽功能表中的統一的分鏡腳本。 如果使用者按一下功能表按鈕上，選取的項目檢視控制器時，應該顯示在分割檢視控制器的詳細資料區段在 iPad 上執行。 在 iPhone 上的項目檢視控制器應該推送到導覽堆疊上。

若要達成這個效果，iOS 設計工具中控制項-按一下按鈕，將一條線拖曳至要顯示檢視控制器。 當放開滑鼠按鈕時，選取`Show Detail`Segue 類型快顯功能表：

 [![](unified-storyboards-images/segue01.png "從 Segue 類型快顯功能表中選取 顯示詳細資料")](unified-storyboards-images/segue01.png#lightbox)

按鈕和檢視控制器之間，將會建立新的 segue。 現在在 iPhone 模擬器中執行應用程式，將會顯示主功能表：

 [![](unified-storyboards-images/segue02.png "在主功能表")](unified-storyboards-images/segue02.png#lightbox)

按一下 **選取遊戲**按鈕和項目的檢視控制器會推送到導覽堆疊上：

 [![](unified-storyboards-images/segue03.png "將檢視控制器的項目推送到導覽堆疊上所示")](unified-storyboards-images/segue03.png#lightbox)

停止模擬器 iPhone 和 iPad 模擬器中執行應用程式。 切換至 橫印 」 和 「 主功能表會再次顯示：

 [![](unified-storyboards-images/segue04.png "顯示在主功能表")](unified-storyboards-images/segue04.png#lightbox)

同樣地，按一下**選取遊戲**按鈕和項目的檢視控制器會顯示在分割檢視控制器的詳細資料區段中：

 [![](unified-storyboards-images/segue05.png "項目顯示在分割檢視控制器的詳細資料區段中的檢視控制器")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>排除的大小類別中的項目

有指定的項目 （例如檢視、 控制項或條件約束） 時不需要在特定的大小類別內的時間。 若要排除大小類別中的項目，請選取 所需的項目中排除**設計介面**。 捲動至底部**屬性總管**然後按一下**齒輪**下拉式功能表。 選取的組合**寬度**並**高度**排除項目：

[![](unified-storyboards-images/exclude-a.png "選取寬度和高度的組合")](unified-storyboards-images/exclude-a.png#lightbox)

新*排除案例*下方的項目將會加入**屬性總管**。 接下來，取消核取**已安裝**核取方塊，指定的大小類別：

[![](unified-storyboards-images/exclude-b.png "取消核取 已安裝的核取方塊")](unified-storyboards-images/exclude-b.png#lightbox)

切換至 項目已從排除的高度與寬度的設計介面，它已從指定的大小類別，但不是完整的 UI 設計：

 [![](unified-storyboards-images/exclude02.png "切換至 項目已從排除的高度與寬度的設計介面")](unified-storyboards-images/exclude02.png#lightbox)

切換回到 Any Width/Any 高度大小類別和項目仍在：

 [![](unified-storyboards-images/exclude03.png "切換回 Any Width/Any 高度大小類別")](unified-storyboards-images/exclude03.png#lightbox)

在 iPad 模擬器中執行應用程式時，會出現的項目：

 [![](unified-storyboards-images/exclude04.png "時所顯示的項目在 iPad 模擬器中執行的應用程式")](unified-storyboards-images/exclude04.png#lightbox)

並在 iPhone 模擬器上執行應用程式時，遺漏的元素：

 [![](unified-storyboards-images/exclude05.png "當遺失的項目在 iPhone 模擬器中執行的應用程式")](unified-storyboards-images/exclude05.png#lightbox)

若要移除的項目排除情況下，只需選取中的項目**設計介面**，捲動到底部**屬性總管**然後按一下**-** 大小寫，以移除旁邊的按鈕。

若要查看實作統一的分鏡腳本，看看`UnifiedStoryboard`範例 Xamarin iOS 8 應用程式連接到這份文件。

## <a name="dynamic-launch-screens"></a>動態啟動畫面

啟動畫面檔案會顯示為啟動顯示畫面中，當 iOS 應用程式，就啟動應用程式會實際啟動增加的使用者提供意見反應。 在 iOS 8 之前，開發人員必須包含多個`Default.png`映像的應用程式會在執行每個裝置類型、 方向和螢幕解析度的資產。 例如， `Default@2x.png`， `Default-Landscape@2x~ipad.png`，`Default-Portrait@2x~ipad.png`等等。

將新的 iPhone 6 和 iPhone 6 加上裝置 （和即將推出的 Apple Watch） 與所有現有的 iPhone 和 iPad 裝置，這表示大型陣列的不同大小、 方向和解析度的`Default.png`必須的啟動螢幕影像資產建立與維護。 此外，這些檔案可能相當大，並將"膨脹 」 交付項目應用程式套件組合，增加的時間才能從 iTunes App Store （可能使其能夠透過行動電話通訊網路傳遞） 下載應用程式和提高使用者的裝置上所需的儲存體數量。

新增至 iOS 8、 開發人員可以建立單一、 不可部分完成`.xib`檔案中使用自動版面配置和大小類別建立的 Xcode*動態啟動螢幕*，適用於每個裝置、 解析度和方向。 這不僅能減輕開發人員建立和維護的所有必要的影像資產，所需的工作量，但可大幅降低應用程式的已安裝的套件組合的大小。


動態啟動畫面有下列限制與考量：

 - 僅使用`UIKit`類別。
 - 使用的單一根檢視`UIView`或`UIViewController`物件。
 - 不要讓任何連線到應用程式的程式碼 (不要加上**動作**或是**輸出**)。
 - 不要新增`UIWebView`物件。
 - 請勿使用任何自訂的類別。
 - 請勿使用執行階段屬性。

記住上述指導方針，讓我們看看新增至現有的 Xamarin iOS 8 專案的動態的啟動螢幕。

請執行下列動作：

1. 開啟**Visual Studio for Mac**並載入**解決方案**新增至動態啟動螢幕。
2. 在 [**方案總管] 中**，以滑鼠右鍵按一下`MainStoryboard.storyboard`檔案，然後選取**開啟** > **Xcode Interface Builder**:

    [![](unified-storyboards-images/dls01.png "Xcode 介面產生器開啟")](unified-storyboards-images/dls01.png#lightbox)
3. 在 Xcode 中，選取**檔案** > **新增** > **檔案...**:

    [![](unified-storyboards-images/dls02.png "選取檔案 / 新增")](unified-storyboards-images/dls02.png#lightbox)
4. 選取 [ **iOS** > **使用者介面** > **啟動畫面**然後按一下**下一步]** 按鈕：

    [![](unified-storyboards-images/dls03.png "選取 iOS / 使用者介面 / 啟動畫面")](unified-storyboards-images/dls03.png#lightbox)
5. 將檔案命名`LaunchScreen.xib`，按一下 **建立**按鈕：

    [![](unified-storyboards-images/dls04.png "將檔案命名為 LaunchScreen.xib")](unified-storyboards-images/dls04.png#lightbox)
6. 請編輯啟動螢幕的設計藉由新增圖形元素，並使用指定的裝置、 方向和螢幕大小往的版面配置條件約束：

    [![](unified-storyboards-images/dls05.png "編輯啟動螢幕的設計")](unified-storyboards-images/dls05.png#lightbox)
7. 變更儲存到`LaunchScreen.xib`。
8. 選取 [**應用程式目標**並**一般**] 索引標籤：

    [![](unified-storyboards-images/dls06.png "選取應用程式目標和 [一般] 索引標籤")](unified-storyboards-images/dls06.png#lightbox)
9. 按一下 **選擇 Info.plist**按鈕，選取`Info.plist`Xamarin 應用程式，然後按一下 **選擇**按鈕：

    [![](unified-storyboards-images/dls07.png "選取 Xamarin 應用程式的 Info.plist")](unified-storyboards-images/dls07.png#lightbox)
10. 在 **應用程式圖示和啟動影像**區段中，開啟**啟動螢幕檔案**下拉式清單中，然後選擇 `LaunchScreen.xib`上面所建立：

    [![](unified-storyboards-images/dls08.png "選擇 LaunchScreen.xib")](unified-storyboards-images/dls08.png#lightbox)
11. 將變更儲存至檔案，並返回 Visual Studio for mac。
12. 等候 Visual Studio for Mac 將會完成同步處理與 Xcode 的變更。
13. 在 [**方案總管] 中**，以滑鼠右鍵按一下**資源**資料夾，然後選取**新增** > **新增檔案...**:

    [![](unified-storyboards-images/dls09.png "選取新增/新增檔案...")](unified-storyboards-images/dls09.png#lightbox)
14. 選取 `LaunchScreen.xib`上面所建立的檔案，然後按一下**開啟**按鈕：

    [![](unified-storyboards-images/dls10.png "選取 LaunchScreen.xib 檔案")](unified-storyboards-images/dls10.png#lightbox)
15. 建置應用程式。

### <a name="testing-the-dynamic-launch-screen"></a>測試動態啟動畫面

在 Visual Studio for Mac 中，選取 iPhone 4 Retina 模擬器並執行應用程式。 [動態] 啟動畫面會顯示在正確的格式和方向：

[![](unified-storyboards-images/dls11.png "顯示在垂直方向動態啟動螢幕")](unified-storyboards-images/dls11.png#lightbox)

停止在 Visual Studio for Mac 的應用程式，然後選取 iPad iOS 8 裝置。 執行應用程式，並在啟動畫面將會正確格式化此裝置和方向：

[![](unified-storyboards-images/dls12.png "顯示在水平方向的動態啟動畫面")](unified-storyboards-images/dls12.png#lightbox)

返回 Visual Studio for Mac，並停止執行的應用程式。

### <a name="working-with-ios-7"></a>使用 iOS 7

若要維護回溯相容性的 iOS 7，只包含一般`Default.png`映像平常一樣在 iOS 8 應用程式的資產。 iOS 會返回先前的行為，並在 iOS 7 裝置上執行時，使用這些檔案為啟動螢幕。

若要查看的動態啟動螢幕，在 Xamarin 中實作，看看[動態啟動畫面](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)範例 iOS 8 應用程式連接到這份文件。

## <a name="summary"></a>總結

這篇文章介紹了快速大小類別和它們如何影響 iPhone 和 iPad 裝置中的版面配置。 它討論特性、 特性環境和特性集合如何使用大小類別建立統一的介面。 花費的調適性的檢視控制器的概要以及如何使用統一的介面內的大小類別。 它探討實作大小類別和統一的介面，完全從C#Xamarin iOS 8 應用程式內的程式碼。

最後，本文涵蓋使用 Xamarin iOS 設計工具，可在 iOS 裝置上建立統一的分鏡腳本的基本概念，並建立一個動態啟動螢幕會顯示為每個 iOS 8 裝置上的啟動螢幕。

## <a name="related-links"></a>相關連結

- [自適性相片 （範例）](https://developer.xamarin.com/samples/monotouch/ios8/AdaptivePhotos/)
- [StoryboardIntro 範例](https://developer.xamarin.com/samples/monotouch/StoryboardIntro/)
- [動態啟動畫面 （範例）](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
- [IOS8-發展 2014 （影片） 中的動態配置](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
