---
title: "統一的分鏡腳本"
description: "統一的分鏡腳本讓 iOS 開發人員建立的使用者介面的單一分鏡腳本，而不是多個分鏡腳本，涵蓋的裝置的螢幕大小擴充的範圍。 這篇文章被為了提供更深入概觀內 Xamarin.iOS 統一的分鏡腳本的作業。"
ms.topic: article
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 60b2e6fa65226631fe2d2c847a56852ac9ae63d2
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="unified-storyboards"></a>統一的分鏡腳本

_統一的分鏡腳本讓 iOS 開發人員建立的使用者介面的單一分鏡腳本，而不是多個分鏡腳本，涵蓋的裝置的螢幕大小擴充的範圍。這篇文章被為了提供更深入概觀內 Xamarin.iOS 統一的分鏡腳本的作業。_

iOS 8 包含新、 較簡單易用的機制，來建立使用者介面 — 統一的分鏡腳本。 單一分鏡腳本可以涵蓋各種不同硬體的螢幕大小，以快速且回應迅速的檢視中可建立 「 設計-一次的使用對多 」 樣式。

開發人員不再需要建立適用於 iPhone 和 iPad 裝置的個別和特定分鏡腳本，因為它們更有彈性地設計應用程式及公用介面，然後以自訂該介面的大小不同類別。 如此一來，應用程式，可調整到每個表單係數的優勢，並可以微調每個使用者介面，以提供最佳體驗。

<a name="size-classes" />

## <a name="size-classes"></a>大小類別

在之前 iOS 8，開發人員使用`UIInterfaceOrientation`和`UIInterfaceIdiom`以直向及橫向模式之間，以及 iPhone 和 iPad 裝置之間加以區分。 在 iOS8、 方向和裝置由決定是使用*大小類別*。

裝置是由定義大小的類別，在垂直和水平軸，而有兩種類型的大小在 iOS 8 中的類別：

-  **一般**– 這是大型的螢幕大小 （例如 iPad) 或小工具，可讓大型大小的印象 (例如 `UIScrollView`
-  **Compact** – 這是針對較小裝置 （例如 iPhone)。 此大小會考慮到裝置的方向。


如果同時使用兩個概念，則結果會是 2 x 2 方格，定義可用於這兩個不同的方向，不同可能大小，如下列圖表所示：

 [![](unified-storyboards-images/sizeclassgrid.png "定義可以用不同可能大小一般和壓縮的方向進行 2x2 方格")](unified-storyboards-images/sizeclassgrid.png#lightbox)

開發人員可以建立會使用四個可能值 （如同上面的圖形中），會導致不同的版面配置的任何檢視控制器。

### <a name="ipad-size-classes"></a>iPad 大小類別

IPad，大小，因為有**規則**類別這兩個方向的大小。

 [![](unified-storyboards-images/image1.png "iPad 大小類別")](unified-storyboards-images/image1.png#lightbox)


### <a name="iphone-size-classes"></a>iPhone 大小類別

在 iPhone 具有不同大小的類別，根據裝置的方向：

 [![](unified-storyboards-images/iphonesizeclasses.png "iPhone 大小類別")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

-  在螢幕裝置直向模式中時，具有**壓縮**水平類別和**規則**垂直
-  裝置以橫向模式時，就會反轉螢幕類別從直向模式。

### <a name="iphone-6-plus-size-classes"></a>iPhone 6 Plus 大小類別

大小為縱向方向，但在橫向不同在較早的 Iphone 相同：

[![](unified-storyboards-images/iphone6sizeclasses.png "iPhone 6 Plus 大小類別")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

因為在 iPhone 6 Plus 擁有夠大的螢幕，您能夠以橫向模式擁有一般寬度大小類別。

### <a name="support-for-a-new-screen-scale"></a>新的螢幕縮放的支援

IPhone 6 Plus 3.0 （三次原始 iPhone 螢幕解析度） 的螢幕縮放比例會使用新的視網膜 HD 顯示。 若要在這些裝置上提供最佳的使用經驗，包括此畫面標尺的設計新的圖檔。 Xcode 6 和更新版本，資產目錄可以包含在 1 x、 2 倍，以及 3 x 大小; 映像只要加入新的影像資產和 iOS 將正確的資產執行時所選擇在 iPhone 6 Plus。

映像載入在 iOS 中的行為也會辨識`@3x`映像檔案的後置字元。 例如，如果開發人員應用程式的組合，以下列檔案名稱中包含影像資產 （位於不同的解決方式）： `MonkeyIcon.png`， `MonkeyIcon@2x.png`，和`MonkeyIcon@3x.png`。 在 iphone 6 Plus`MonkeyIcon@3x.png`如果開發人員載入映像使用下列程式碼影像也會自動使用：

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```
如果它們指派給使用 iOS 的 UI 項目的映像或設計工具為`MonkeyIcon.png`、`MonkeyIcon@3x.png`將會使用，會自動重新 iPhone 6 上加上。

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>動態啟動螢幕

啟動螢幕檔案會顯示為啟動顯示畫面中，而 iOS 應用程式，就啟動意見反應提供給使用者的應用程式會實際啟動總。 之前 iOS 8，開發人員必須包括多個`Default.png`影像資產的應用程式會在執行每個裝置類型、 方向和螢幕解析度。

新增 ios 8 的開發人員可以建立單一、 不可部分完成`.xib`檔案中使用自動配置和大小類別建立的 Xcode*動態啟動螢幕*，可用於每個裝置、 解析度和方向。 這不只會減少開發人員建立和維護的所有必要的影像資產，所需的工作量，但是它會減少應用程式的安裝套件的大小。

## <a name="traits"></a>特性

特性是可用來判斷如何配置變更為其環境變更的屬性。 其中包含的一組屬性 (`HorizontalSizeClass`和`VerticalSizeClass`根據`UIUserInterfaceSizeClass`)，以及介面慣用語 ( `UIUserInterfaceIdiom`) 和顯示的小數位數。

所有上述狀態會包裝在 Apple 指特性集合的容器 ( `UITraitCollection`)，其中包含屬性不僅以及其值。

## <a name="trait-environment"></a>特性環境

特性環境中 iOS 8 的新介面，而無法傳回特性集合中的下列物件：

-  螢幕 ( `UIScreens` )。
-  Windows ( `UIWindows` ).
-  檢視控制器 ( `UIViewController` )。
-  檢視 ( `UIView` )。
-  簡報控制站 ( `UIPresentationController` )。


開發人員會使用特性環境所傳回的特性集合，來決定如何使用者介面應該配置。

所有特性環境使階層架構，如下列圖表所示：

 [![](unified-storyboards-images/viewhierarchy.png "特性環境階層架構圖表")](unified-storyboards-images/viewhierarchy.png#lightbox)

特性集合各上述特性環境有流動，根據預設，從父代的子系的環境。

取得目前的特性集合時，除了特性環境有`TraitCollectionDidChange`方法，可以檢視或檢視控制器子類別中被覆寫。 開發人員可以使用這個方法來修改任何這些特性已變更時，取決於特性的 UI 項目。

## <a name="typical-trait-collections"></a>一般特性集合

本節將討論使用 iOS 8 時，使用者將經歷的特性集合的一般類型。

以下是開發人員可能會看到在 iPhone 的一般特性集合：

|屬性|值|
|--- |--- |
|`HorizontalSizeClass`|壓縮|
|`VerticalSizeClass`|Regular|
|`UserInterfaceIdom`|電話|
|`DisplayScale`|2.0|

上述組代表完整限定特性集合，因為它具有所有其特性屬性的值。

也是可能有遺漏某些值，其特性集合 (它會做為參考 Apple*未指定*):

|屬性|值|
|--- |--- |
|`HorizontalSizeClass`|壓縮|
|`VerticalSizeClass`|未指定|
|`UserInterfaceIdom`|未指定|
|`DisplayScale`|未指定|

一般而言，不過，當開發人員會要求其特性集合特性環境，它會傳回完整的集合在上述範例所示。

如果特性環境 （例如，檢視或檢視控制器） 不是目前的檢視階層架構內，開發人員可能會得到未指定的值的一個或多個特性屬性。

如果使用其中一個這類 Apple，提供建立方法，開發人員也會得到不完整的特性集合`UITraitCollection.FromHorizontalSizeClass`，以建立新的集合。

可在多個特性集合執行的一項作業進行比較，其中牽涉到詢問一個特性集合是否包含另一個。 代表什麼意思*內含項目*是，在第二個集合中指定的任何特性的值必須符合完全與第一個集合中的值。

若要測試使用兩個特性`Contains`方法`UITraitCollection`傳入要測試之特性的值。

開發人員可以手動執行比較，判斷程式碼中如何配置檢視或檢視控制器。 不過，`UIKit`在內部會使用這個方法，以提供的某些功能，如同外觀 Proxy，例如。

## <a name="appearance-proxy"></a>外觀 Proxy

IOS 可讓開發人員自訂檢視其屬性的舊版本中引進了外觀 Proxy。 它已經過擴充，在 iOS 8，以支援特性集合中。

外觀 Proxy 現在包含新方法， `AppearanceForTraitCollection`，傳回新的外觀 Proxy 已傳入的給定特性集合。 開發人員可以執行的任何自訂外觀 Proxy 才會生效符合檢視表上指定的特性集合。

通常開發人員將會傳入部分指定特性集合`AppearanceForTraitCollection`方法，例如只指定水平大小類別的壓縮，如此他們無法自訂的任何檢視中的應用程式 compact 水平的其中一個。

## <a name="uiimage"></a>UIImage

Apple 已新增特性集合的另一個類別`UIImage`。 在過去，開發人員必須指定@1X和@2x他們打算在應用程式 （例如圖示） 中包含任何點陣圖圖形資產的版本。

iOS 8 已擴充，能夠讓開發人員在特性集合為基礎的映像目錄中包含多個版本的映像。 例如，開發人員可能會使用壓縮的特性類別和其他集合的完整大小映像的較小的影像。

當內使用其中一個映像`UIImageView`類別，映像檢視自動顯示其特性集合的正確版本的映像。 如果特性環境變更 （例如切換使用者將裝置從直向變成橫向），映像檢視將會自動選取新映像大小以符合新特性集合，然後變更其大小以符合目前之映像的版本顯示。

## <a name="uiimageasset"></a>UIImageAsset

Apple 已加入新的類別 ios 8 稱為`UIImageAsset`讓開發人員更多控制映像選取項目。

影像資產包裝所有的不同版本的映像，並可讓開發人員以取得符合已傳入的特性集合的特定映像。 可以加入或移除從影像資產，映像上作業。

如需有關影像資產的詳細資訊，請參閱 Apple [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)文件。

## <a name="combining-trait-collections"></a>結合特性集合

加入兩個在一起的就是為開發人員可以在特性集合執行的另一個函式會導致結合的集合，其中一個集合中未指定的值會取代第二個指定的值。 這是使用`FromTraitsFromCollections`方法`UITraitCollection`類別。

如前所述，如果任何特性的其中一個特性集合並未指定，且在另一個指定，此值將設定成指定的版本。 不過，如果沒有指定的給定值的多個版本，最後一個值特性集合會使用的值。

## <a name="adaptive-view-controllers"></a>自動調整檢視控制器

本節將討論如何檢視和檢視控制器 iOS 採用特性和大小的類別可以自動在開發人員的應用程式中更彈性的概念的詳細資料。

### <a name="split-view-controller"></a>分割檢視控制器

最多可以在 iOS 8 中已變更的檢視控制器類別的其中一個是`UISplitViewController`類別。 在過去，開發人員通常會使用分割檢視控制器 iPad 版的應用程式，則必須為 iPhone 新版應用程式提供其檢視階層架構以完全不同版本。

在 iOS 8，`UISplitViewController`類別位於兩個平台 （iPad 和 iPhone），可讓開發人員建立一個檢視控制器階層將 iPhone 和 iPad 函式。

橫向 iPhone 時，分割檢視控制器將會顯示其檢視-並存，就像是在 iPad 上顯示時。

### <a name="overriding-traits"></a>覆寫特性

特性環境重疊顯示父容器下的子容器，如同下圖顯示分割檢視控制器 iPad 上：

 [![](unified-storyboards-images/cascadingclasses01.png "分割檢視控制器上以橫向 iPad")](unified-storyboards-images/cascadingclasses01.png#lightbox)

因為 iPad 水平和垂直方向進行一般大小類別，分割檢視會顯示在主要和詳細資料檢視。

在 iPhone 中，其中大小類別是這兩個方向進行壓縮，分割檢視控制器只會顯示詳細資料檢視，如下所示：

 [![](unified-storyboards-images/cascadingclasses02.png "分割檢視控制器只會顯示詳細資料檢視")](unified-storyboards-images/cascadingclasses02.png#lightbox)

應用程式開發人員要顯示在 iPhone 上的 master 和詳細資料檢視中，開發人員必須分割檢視控制器中插入的父容器，並覆寫特性集合。 下圖所示：

 [![](unified-storyboards-images/cascadingclasses03.png "開發人員必須分割檢視控制器中插入的父容器，並覆寫特性集合")](unified-storyboards-images/cascadingclasses03.png#lightbox)

A`UIView`設定為分割檢視控制器的父系和`SetOverrideTraitCollection`檢視傳入新特性集合與目標分割檢視控制站上呼叫方法。 新的特性集合會覆寫`HorizontalSizeClass`，將它設定為`Regular`，如此一來，分割檢視控制器將會顯示在 iPhone 上的主要和詳細檢視。

請注意，`VerticalSizeClass`已設為`unspecified`，這可讓新特性集合加入至其父系，特性集合導致`Compact VerticalSizeClass`子分割檢視的控制器。

### <a name="trait-changes"></a>特性變更

本節會看，在詳細，特性集合的轉換方式特性環境變更時。 例如，當裝置從旋轉直向變成橫向。

 [![](unified-storyboards-images/traittransitions01.png "直向變成橫向特性變更概觀")](unified-storyboards-images/traittransitions01.png#lightbox)

首先，iOS 8 會執行某些安裝程式以準備進行轉換。 接下來，系統以動畫顯示轉換狀態。 最後，iOS 8 清除轉換期間所需的任何暫存狀態。

iOS 8 提供數個回呼開發人員可以用來參與特性變更下, 表所示：

|Phase|回呼|描述|
|--- |--- |--- |
|安裝程式|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>特性集合取得設為新的值之前的特性變更開頭取得呼叫這個方法。</li><li>取得呼叫這個方法，但特性集合的值變更時進行任何動畫之前。</li></ul>|
|動畫|`WillTransitionToTraitCollection`|取得傳遞至這個方法轉換協調器`AnimateAlongside`屬性，可讓開發人員加入將會執行，以及預設動畫的動畫。|
|清除|`WillTransitionToTraitCollection`|提供方法讓開發人員轉換之後，包含其本身的清除程式碼。|

`WillTransitionToTraitCollection`方法適合用來建立檢視控制器動畫以及特性集合變更。 `WillTransitionToTraitCollection`方法才可以使用檢視控制器 ( `UIViewController`) 而不是在其他特性的環境，例如`UIViews`。

`TraitCollectionDidChange`適合用來使用`UIView`類別，開發人員要更新 UI 的特性會變更。

### <a name="collapsing-the-split-view-controllers"></a>摺疊分割檢視控制器

現在讓我們採取進一步查看功能時不會分割檢視控制器選取範圍摺疊兩個資料行從一個資料行檢視。 這項變更的一部分，有兩個必須執行的處理序：

-  根據預設，分割檢視控制器將會使用主要檢視控制器與檢視摺疊發生之後。 開發人員可以藉由覆寫項目覆寫這個行為`GetPrimaryViewControllerForCollapsingSplitViewController`方法`UISplitViewControllerDelegate`並提供他們想要顯示中摺疊狀態的任何檢視控制站。
-  取得合併至主要檢視控制器有次要檢視控制器。 通常開發人員不需要採取任何動作，此步驟。分割檢視控制器包括基礎硬體裝置上的此階段的自動處理。 不過，可能有某些特殊情況下，開發人員將要互動這項變更。 呼叫`CollapseSecondViewController`方法`UISplitViewControllerDelegate`允許摺疊，就會發生，而不是詳細資料檢視時所要顯示的主版檢視控制站。


### <a name="expanding-the-split-view-controller"></a>展開分割檢視控制器

現在讓我們來分割檢視控制器展開與摺疊的狀態時，就會發生採取進一步查看項目。 同樣地，有必要進行的兩個階段：

-  首先，定義新的主要檢視控制站。 根據預設，分割檢視控制器會自動使用主要檢視控制器從摺疊的檢視。 同樣地，開發人員可以覆寫這個行為使用`GetPrimaryViewControllerForExpandingSplitViewController`方法`UISplitViewControllerDelegate`。
-  一旦選擇主要檢視控制器，就必須重新建立次要檢視控制器。 同樣地，分割檢視控制器包括基礎硬體裝置上的此階段的自動處理。 開發人員可以覆寫這個行為藉由呼叫`SeparateSecondaryViewController`方法`UISplitViewControllerDelegate`。


分割檢視的控制站，在主要檢視控制器扮演一個角色的展開和摺疊的檢視，藉由實作`CollapseSecondViewController`和`SeparateSecondaryViewController`方法`UISplitViewControllerDelegate`。 `UINavigationController` 實作這些方法來自動 push 和 pop 檢視次要控制站。

### <a name="showing-view-controllers"></a>顯示檢視控制器

另一項變更 Apple 對 iOS 8 處於開發人員顯示檢視控制站的方式。 在過去，如果應用程式必須分葉檢視控制器 （例如資料表檢視控制站），而且開發人員顯示 （例如，為了回應使用者點選，在儲存格上），另一個應用程式會連線到控制器的階層，以透過回傳瀏覽檢視控制器 」 和 「 呼叫`PushViewController`比對以顯示新檢視的方法。

這提供導覽控制站之間的環境中執行相當緊密結合在一起。 在 iOS 8，Apple 已分離這藉由提供兩種新方法：

-  `ShowViewController` – 會調整，以顯示新的檢視控制器根據其環境。 例如，在`UINavigationController`只需將推送至堆疊的新檢視。 分割檢視的控制站，在新檢視控制器將會出現在左邊為新的主要檢視控制站。 如果沒有容器檢視控制站存在時，新檢視將顯示為強制回應檢視控制站。
-  `ShowDetailViewController` – 以類似的方式運作`ShowViewController`，但實作取代傳入的新檢視控制器的詳細資料檢視分割檢視控制站上。 如果已摺疊分割檢視控制器，（如同可能會在 iPhone 應用程式），呼叫會重新導向至`ShowViewController`方法，以及新的檢視將顯示為主要檢視控制站。 同樣地，如果沒有容器檢視控制站存在時，新檢視將顯示為強制回應檢視控制站。


這些方法的運作方式是開始分葉檢視控制器，並逐步檢視階層架構往上，直到他們找到正確的容器檢視控制器，以處理新的檢視顯示。

開發人員可以實作`ShowViewController`和`ShowDetailViewController`自己自訂的檢視控制站以取得相同自動化功能，`UINavigationController`和`UISplitViewController`提供。

### <a name="how-it-works"></a>它的運作方式

本節中，我們將看看這些方法中 iOS 8 如何實作。 第一個讓我們看看新`GetTargetForAction`方法：

 [![](unified-storyboards-images/gettargetforaction.png "新的 GetTargetForAction 方法")](unified-storyboards-images/gettargetforaction.png#lightbox)

這個方法會逐步引導的階層鏈結，直到發現正確的容器檢視控制站。 例如: 

1.  如果`ShowViewController`呼叫方法時，第一個檢視控制站，在鏈結中，會實作這個方法是瀏覽控制站，以便使用它來為新檢視的父系。
1.  如果`ShowDetailViewController`改為呼叫方法，分割檢視控制器是第一個檢視控制器進行實作，以便使用它來做為父系。


`GetTargetForAction`方法的運作方式是尋找檢視控制站會實作指定的動作，並再詢問該檢視控制器，如果它要接收該動作。 由於這個方法是公用的開發人員可以建立自己的自訂方法的函式像內建在`ShowViewController`和`ShowDetailViewController`方法。

## <a name="adaptive-presentation"></a>自動調整簡報

在 iOS 8，Apple 已 Popover 簡報 ( `UIPopoverPresentationController`) 自動調整以及。 因此 Popover 簡報檢視控制器會自動出現在一般大小類別內的一般的 Popover 檢視表，但是會顯示全螢幕 compact 水平大小類別中 (例如在 iPhone 上)。

為了符合統一的分鏡腳本系統內的變更，新的控制器物件已建立以管理呈現的檢視控制器 — `UIPresentationController`。 此控制站會建立從檢視控制器會關閉之前的時間。 因為它是管理類別，它可以被視為超級類別檢視控制器透過裝置的變更會影響到檢視控制器 （例如方向） 簡報控制站會控制哪些就檢視控制器送回回應。

開發人員提供檢視控制器使用`PresentViewController`方法，在簡報程序管理傳至`UIKit`。 UIKit 處理 （以及其他項目） 所建立的樣式為正確的控制器、 唯一的例外狀況時正在檢視控制器已設定為樣式`UIModalPresentationCustom`。 在這裡，應用程式能夠提供它自己 PresentationController，而不要使用`UIKit`控制站。

### <a name="custom-presentation-styles"></a>自訂的展示樣式

使用自訂的展示樣式，開發人員可以使用自訂的簡報控制站的選項。 此自訂控制器可以用來修改的外觀和行為，它以結盟的檢視。

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>使用大小類別

隨附於本文 Adaptive 相片 Xamarin 專案提供 iOS 8 統一介面應用程式中使用大小類別和自動調整檢視的控制器工作的範例。

雖然應用程式會建立其 UI 完全從程式碼，而不是使用 IOS 設計工具，並建立統一的分鏡腳本，套用相同的技術。 在本文中，稍後我們將示範如何使用統一的分鏡腳本與 iOS Xamarin 應用程式中的設計工具的大小類別。

現在讓我們來看看如何調整相片專案所實作的數個大小類別功能中 iOS 8 建立自動調整應用程式。

### <a name="adapting-to-trait-environment-changes"></a>改寫特性環境的變更

執行時調整相片應用程式在 iPhone 中，當使用者旋轉裝置從直向變成橫向，分割檢視控制器將會顯示在主要和詳細資料檢視：

 [![](unified-storyboards-images/rotation.png "分割檢視控制器將會顯示這兩個主要和詳細資料檢視，如下所示")](unified-storyboards-images/rotation.png#lightbox)

這會透過覆寫`UpdateConstraintsForTraitCollection`檢視控制器，並調整這些條件約束的方法為基礎的值`VerticalSizeClass`。 例如: 

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

分割檢視控制器，應用程式會從調整相片中摺疊，展開，動畫會加入預設動畫藉由覆寫`WillTransitionToTraitCollection`檢視控制器方法。 例如: 

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

如上面所示，自動調整相片應用程式強制顯示同時詳細資料分割檢視控制器和主版檢視的 iPhone 裝置處於 [橫向] 檢視時。

這被透過使用下列程式碼中檢視控制器：

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

下一步] 讓我們來檢查 [分割檢視控制器的展開和摺疊行為已實作的 Xamarin 的方式。 在`AppDelegate`、 建立分割檢視控制器時，其委派指派給處理這些變更：

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

`SeparateSecondaryViewController`方法測試以查看相片顯示，且會依據該狀態的動作。 沒有相片是如果正在顯示摺疊次要檢視控制器，因此會顯示主版檢視控制器。

`CollapseSecondViewController`方法時，使用展開分割檢視控制器是否在堆疊上，是否存在任何相片因此它摺疊回該檢視。

### <a name="moving-between-view-controllers"></a>檢視控制站之間移動

接下來，讓我們看看調整相片應用程式檢視控制站之間移動的方式。 在`AAPLConversationViewController`類別，當使用者從資料表中，選取儲存格`ShowDetailViewController`呼叫方法來顯示詳細資料檢視：

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

### <a name="displaying-disclosure-indicators"></a>顯示洩漏標記

在自動調整相片應用程式中，有幾個位置其中洩漏指標會隱藏或顯示根據變更特性環境。 這可處理下列程式碼：

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

這些實作使用`GetTargetViewControllerForAction`詳細上面討論的方法。

資料表檢視控制器會顯示資料時，它會使用以查看推入即將發生，以及要顯示或隱藏洩漏指標據以實作上述的方法：

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

Apple 已為使用大小類別和特性環境，從內分割檢視的控制站，新增新的通知類型`ShowDetailTargetDidChangeNotification`。 目標詳細資料檢視分割檢視控制站的變更，例如控制器時展開或摺疊時，會傳送此通知。

自動調整相片應用程式會使用這項通知詳細資料檢視控制器變更時更新洩漏指標的狀態：

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

詳述在調整相片應用程式中的所有方式查看該大小的類別，可用來輕鬆地建立統一的應用程式中 Xamarin.iOS 特性集合與調整檢視的控制器。

## <a name="unified-storyboards"></a>統一的分鏡腳本

新整合的分鏡腳本可讓開發人員建立一個，將 iOS 8，整合的分鏡腳本檔案可以在 iPhone 和 iPad 裝置上顯示將目標設為多個大小類別。 藉由使用統一的分鏡腳本，開發人員撰寫較少 UI 特定的程式碼，且只能有一個介面的設計，建立和維護。

統一的分鏡腳本的主要優點為：

-  適用於 iPhone 和 iPad 使用相同的分鏡腳本檔案。
-  部署至 iOS 6 和 iOS 7 的回溯。
-  預覽不同裝置、 方向和 Xamarin iOS 設計工具內的作業系統版本從全部的版面配置。

這項功能完全支援在 Visual Studio for Mac

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>啟用大小類別

根據預設，任何新的 Xamarin.iOS 專案將使用調整大小的類別。 分鏡腳本從較舊的專案中使用大小類別和自動調整 Segues，它必須先轉換成 Xcode 6 統一分鏡腳本格式從 iOS 設計工具內。

為此開啟轉換在設計工具並檢查 iOS 分鏡腳本**使用大小類別**核取方塊：

 [![](unified-storyboards-images/sizeclass01.png "使用大小類別核取方塊")](unified-storyboards-images/sizeclass01.png#lightbox)

IOS 設計工具將會確認開發人員想要轉換的使用大小類別分鏡腳本格式：

 [![](unified-storyboards-images/sizeclass02.png "使用大小類別警示")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> 也必須檢查自動配置大小類別才能正確運作。

### <a name="generic-device-types"></a>一般裝置類型

一旦分鏡腳本已轉換成使用大小類別，將系統的設計介面中和**檢視為**裝置將會泛型：

 [![](unified-storyboards-images/sizeclass03.png "檢視一般裝置類型")](unified-storyboards-images/sizeclass03.png#lightbox)

選取的泛用的裝置類型時，所有檢視控制器會重新都調整 600 x 600 正方形。 這個方塊代表任何寬度和高度的大小。 當 iOS 設計工具在此模式中，所有的編輯會套用至所有大小類別。

開發人員也可以檢視設計介面上的做為 iPhone 的選項：

 [![](unified-storyboards-images/sizeclass04.png "在 iPhone 與檢視設計介面")](unified-storyboards-images/sizeclass04.png#lightbox)

或在 iPad 為：

 [![](unified-storyboards-images/sizeclass05.png "在 iPad 與檢視設計介面")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>選取大小類別

大小類別選取器按鈕位於設計介面 （附近的檢視為下拉式清單） 的左上角。 它可讓開發人員選取目前正在編輯的大小類別：

 [![](unified-storyboards-images/sizeclass06.png "選取大小類別")](unified-storyboards-images/sizeclass06.png#lightbox)

選取器會顯示為 3 x 3 方格大小類別選取項目。 平方方格中的每個代表類別寬度和高度類別的組合。 中央的矩形會選取任何 Width/Any 高度大小類別 （這是整合的分鏡腳本的預設檢視）。 選取此方塊時，開發人員正在編輯預設版面配置，會繼承由所有其他組態。

在方格的左上角的方塊表示 Compact Compact 寬度/高度大小的類別：

 [![](unified-storyboards-images/sizeclass07.png "壓縮的寬度 Compact 高度大小類別")](unified-storyboards-images/sizeclass07.png#lightbox)

此模式會對應至 iPhone。 在方格的右手邊角正方形代表規則一般寬度/高度大小類別，其代表 iPad:

 [![](unified-storyboards-images/sizeclass08.png "規則的寬度規則高度大小類別")](unified-storyboards-images/sizeclass08.png#lightbox)

若要編輯適用於 iPhone 的直式方向版面配置，選取在左下角的平方。 這代表 Compact 的一般寬度/高度大小類別：

 [![](unified-storyboards-images/sizeclass09.png "壓縮的寬度規則高度大小類別")](unified-storyboards-images/sizeclass09.png#lightbox)

按一下以選取它的矩形，並在設計介面會變更檢視控制器，以符合新的選取範圍的大小：

 [![](unified-storyboards-images/sizeclass10.png "在設計介面會變更檢視控制器，以符合新的選取範圍，如下所示的大小")](unified-storyboards-images/sizeclass10.png#lightbox)

大小類別和它們如何影響 Iphone 和 Ipad 的版面配置，請參閱這篇文章，如需詳細資訊的大小類別小節。

### <a name="adaptive-segue-types"></a>自動調整話題類型

如果開發人員已經使用分鏡腳本，則它們會很熟悉的現有 segue 類型**推送**，**強制回應**和**Popover**。 當大小類別會啟用統一分鏡腳本檔案時，會提供下列彈性話題類型 （對應至新的檢視控制器 API 上面所討論）：**顯示**和**顯示詳細資料**.

> [!IMPORTANT]
> 當啟用大小類別時，任何現有 segues 會轉換成新類型。

主版檢視中有一個簡單的遊戲導覽功能表的分割檢視控制器使用統一的分鏡腳本的 8 應用程式需要 iOS 的範例。 如果使用者按一下功能表按鈕上，選取的項目檢視控制器應該會顯示詳細資料區段中的分割檢視控制器在 iPad 上執行時。 在 iPhone 上的項目檢視控制器應該推巡覽堆疊。

若要達成這個效果，iOS 設計工具中控制項-按一下按鈕，將一條線拖曳至要顯示檢視控制器。 當您放開滑鼠按鈕時，選取`Show Detail`話題類型快顯功能表：

 [![](unified-storyboards-images/segue01.png "從話題類型快顯功能表選取 顯示詳細資料")](unified-storyboards-images/segue01.png#lightbox)

按鈕和檢視控制站之間會建立新 segue。 現在 iPhone 模擬器中執行應用程式，而且會顯示主功能表：

 [![](unified-storyboards-images/segue02.png "主功能表")](unified-storyboards-images/segue02.png#lightbox)

按一下**選取遊戲**按鈕和項目的檢視控制器會立即推送至瀏覽堆疊：

 [![](unified-storyboards-images/segue03.png "項目檢視控制器就會立即推送至瀏覽堆疊所示")](unified-storyboards-images/segue03.png#lightbox)

停止模擬器 iPhone 和 iPad 模擬器中執行應用程式。 切換至 橫印 」 和 「 主要再次顯示功能表：

 [![](unified-storyboards-images/segue04.png "顯示在主功能表")](unified-storyboards-images/segue04.png#lightbox)

再按一下**選取遊戲**分割檢視控制器的詳細資料區段中已顯示按鈕和項目的檢視控制器：

 [![](unified-storyboards-images/segue05.png "項目檢視控制器分割檢視控制器的詳細資料區段中顯示")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>從大小類別排除項目

但有些指定的項目 （例如檢視、 控制或條件約束） 時不需要在特定的大小類別內的時候。 若要排除大小類別中的項目，選取所需的項目排除在**設計介面**。 捲動至底部**屬性總管**按一下**齒輪**下拉式功能表。 選取的組合**寬度**和**高度**排除項目：

[![](unified-storyboards-images/exclude-a.png "選取寬度和高度的組合")](unified-storyboards-images/exclude-a.png#lightbox)

新*排除案例*底端的項目將會加入**屬性總管**。 接下來，取消核取**已安裝**核取方塊指定大小的類別：

[![](unified-storyboards-images/exclude-b.png "取消核取 已安裝的核取方塊")](unified-storyboards-images/exclude-b.png#lightbox)

切換至 項目中已排除的高度與寬度的設計介面，它已從指定的大小的類別，但不是整個 UI 設計：

 [![](unified-storyboards-images/exclude02.png "切換至 項目中已排除的高度與寬度的設計介面")](unified-storyboards-images/exclude02.png#lightbox)

切換回 Any Width/Any 高度大小類別和項目仍在為：

 [![](unified-storyboards-images/exclude03.png "切換回 Any Width/Any 高度大小類別")](unified-storyboards-images/exclude03.png#lightbox)

在 iPad 模擬器中執行應用程式時，會出現的項目：

 [![](unified-storyboards-images/exclude04.png "時顯示的項目在 iPad 模擬器中執行的應用程式")](unified-storyboards-images/exclude04.png#lightbox)

與 iPhone 模擬器上執行應用程式時，遺漏的元素：

 [![](unified-storyboards-images/exclude05.png "當遺失的項目在 iPhone 模擬器中執行的應用程式")](unified-storyboards-images/exclude05.png#lightbox)

若要移除的項目排除案例，只需選取中的項目**設計介面**，捲動到底部**屬性總管**按一下 **-** 大小寫，以移除旁邊的按鈕。

若要查看統一分鏡腳本的實作，看看`UnifiedStoryboard`範例 Xamarin iOS 8 應用程式附加至這份文件。

## <a name="dynamic-launch-screens"></a>動態啟動螢幕

啟動螢幕檔案會顯示為啟動顯示畫面中，而 iOS 應用程式，就啟動意見反應提供給使用者的應用程式會實際啟動總。 之前 iOS 8，開發人員必須包括多個`Default.png`影像資產的應用程式會在執行每個裝置類型、 方向和螢幕解析度。 例如， `Default@2x.png`， `Default-Landscape@2x~ipad.png`，`Default-Portrait@2x~ipad.png`等等。

建構新的 iphone 6 和 iPhone 6 Plus 裝置 （與即將進行的 Apple Watch） 與所有現有的 iPhone 和 iPad 裝置，這代表不同大小、 方向和解決方案的大型陣列`Default.png`必須啟動螢幕影像資產建立和維護。 此外，這些檔案可能很龐大，並將"壅塞，而 「 交貨的應用程式套件組合，增加從 iTunes App Store （可能保持無法傳遞透過行動電話通訊網路） 下載應用程式所需的時間量並提高使用者的裝置上所需的儲存體數量。

新增 ios 8 的開發人員可以建立單一、 不可部分完成`.xib`檔案中使用自動配置和大小類別建立的 Xcode*動態啟動螢幕*，可用於每個裝置、 解析度和方向。 這不只會減少開發人員建立和維護的所有必要的影像資產，所需的工作量，但它可大幅降低應用程式的安裝套件的大小。


動態啟動螢幕會有下列限制和考量：

 - 只使用`UIKit`類別。
 - 使用的單一根檢視`UIView`或`UIViewController`物件。
 - 不要讓任何連接至應用程式的程式碼 (不要新增**動作**或**插座**)。
 - 請勿將加入`UIWebView`物件。
 - 請勿使用任何自訂的類別。
 - 請勿使用執行階段屬性。

請注意上述的指導方針，讓我們看看將動態啟動螢幕加入至現有的 Xamarin iOS 8 專案。

請執行下列動作：

1. 開啟**Visual Studio for Mac**並載入**方案**來加入可動態啟動螢幕。
2. 在**方案總管 中**，以滑鼠右鍵按一下`MainStoryboard.storyboard`檔案，然後選取**開啟** > **Xcode 介面產生器**:

    [![](unified-storyboards-images/dls01.png "Xcode 介面產生器開啟")](unified-storyboards-images/dls01.png#lightbox)
3. 在 Xcode 中，選取**檔案** > **新增** > **檔案...**:

    [![](unified-storyboards-images/dls02.png "選取 檔案 / 新增")](unified-storyboards-images/dls02.png#lightbox)
4. 選取**iOS** > **使用者介面** > **啟動螢幕**按一下**下一步**按鈕：

    [![](unified-storyboards-images/dls03.png "選取 iOS / 使用者介面 / 啟動螢幕")](unified-storyboards-images/dls03.png#lightbox)
5. 將檔案命名`LaunchScreen.xib`按一下**建立**按鈕：

    [![](unified-storyboards-images/dls04.png "LaunchScreen.xib 的檔案名稱")](unified-storyboards-images/dls04.png#lightbox)
6. 加入圖形項目，並使用給定的裝置、 方向和螢幕大小將配置條件約束，以編輯啟動螢幕的設計：

    [![](unified-storyboards-images/dls05.png "編輯啟動螢幕的設計")](unified-storyboards-images/dls05.png#lightbox)
7. 變更儲存到`LaunchScreen.xib`。
8. 選取**應用程式目標**和**一般** 索引標籤：

    [![](unified-storyboards-images/dls06.png "選取應用程式目標和 [一般] 索引標籤")](unified-storyboards-images/dls06.png#lightbox)
9. 按一下**選擇 Info.plist**按鈕，選取`Info.plist`Xamarin 應用程式並按一下**選擇**按鈕：

    [![](unified-storyboards-images/dls07.png "選取 Xamarin 應用程式 Info.plist")](unified-storyboards-images/dls07.png#lightbox)
10. 在**應用程式圖示和啟動映像**區段中，開啟**啟動螢幕檔案**下拉式清單中選擇 `LaunchScreen.xib`上面所建立：

    [![](unified-storyboards-images/dls08.png "選擇 LaunchScreen.xib")](unified-storyboards-images/dls08.png#lightbox)
11. 將變更儲存至檔案，並返回 Visual Studio for mac。
12. 等候 Visual Studio for Mac 完成同步處理具有 Xcode 的變更。
13. 在**方案總管 中**，以滑鼠右鍵按一下**資源**資料夾，然後選取**新增** > **加入檔案...**:

    [![](unified-storyboards-images/dls09.png "選擇加入 / 加入檔案...")](unified-storyboards-images/dls09.png#lightbox)
14. 選取`LaunchScreen.xib`上面所建立的檔案，然後按一下**開啟**按鈕：

    [![](unified-storyboards-images/dls10.png "選取 LaunchScreen.xib 檔案")](unified-storyboards-images/dls10.png#lightbox)
15. 建置應用程式。

### <a name="testing-the-dynamic-launch-screen"></a>測試動態啟動螢幕

在 Visual Studio for Mac 選取 iPhone 4 Retina 模擬器並執行應用程式。 [動態] 啟動畫面將會顯示在正確的格式和方向：

[![](unified-storyboards-images/dls11.png "在垂直方向中顯示動態啟動螢幕")](unified-storyboards-images/dls11.png#lightbox)

停止在 Visual Studio for Mac 應用程式，並選取 iPad iOS 8 裝置。 執行應用程式，並啟動螢幕會針對此裝置和方向正確格式化：

[![](unified-storyboards-images/dls12.png "顯示水平方向的動態啟動畫面")](unified-storyboards-images/dls12.png#lightbox)

返回 Visual Studio for Mac，並停止執行應用程式。

### <a name="working-with-ios-7"></a>使用 iOS 7

若要維持回溯相容性的 iOS 7，只包含一般`Default.png`映像正常 iOS 8 應用程式中的資產。 iOS 會返回先前的行為，並執行 iOS 7 裝置上時，使用這些檔案做為啟動螢幕。

若要查看在 Xamarin 的動態啟動螢幕的實作，看看[動態啟動螢幕](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)範例 iOS 8 應用程式附加至這份文件。

## <a name="summary"></a>總結

這篇文章所花費的快速瀏覽大小類別和它們如何影響 iPhone 和 iPad 裝置中的配置。 它將討論如何特性、 特性環境和特性集合工作大小類別來建立統一的介面。 花費適應性檢視控制器的簡短探討，以及它們如何使用統一的介面內的大小類別。 它看從內部 Xamarin iOS 8 應用程式的 C# 程式碼完全實作大小類別和統一的介面。

最後，本文章涵蓋使用 Xamarin iOS 將適用於所有 iOS 裝置的設計工具建立統一的分鏡腳本的基礎，並建立單一動態啟動螢幕會顯示為每個 iOS 8 裝置上的啟動螢幕。

## <a name="related-links"></a>相關連結

- [自動調整相片 （範例）](https://developer.xamarin.com/samples/monotouch/ios8/AdaptivePhotos/)
- [StoryboardIntro 範例](https://developer.xamarin.com/samples/monotouch/StoryboardIntro/)
- [動態啟動螢幕 （範例）](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
- [動態的版面配置中 iOS8-發展 2014 （影片）](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
