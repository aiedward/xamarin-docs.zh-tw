---
title: 使用 Xamarin Designer for iOS 的自動版面配置
description: 本指南介紹 iOS Auto 版面配置，並說明如何使用 Xamarin Designer for iOS 來建立和編輯使用條件約束的版面配置。 它也會討論如何修改程式碼中的條件約束、將條件約束變更動畫，以及其他更多
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 37d90bc42e843dd3b3c8f07689e0e229225ff57d
ms.sourcegitcommit: d1f0e0a9100548cfe0960ed2225b979cc1d7c28f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439466"
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>使用 Xamarin Designer for iOS 的自動版面配置

> [!WARNING]
> IOS 設計工具將開始在 Visual Studio 2019 16.8 版和 Visual Studio 2019 for Mac 8.8 版中推出。
> 建立 iOS 使用者介面的建議方式是直接在執行 Xcode 的 Mac 上執行。 如需詳細資訊，請參閱 [使用 Xcode 設計使用者介面](../storyboards/index.md)。 

自動版面配置 (也稱為「彈性配置」 ) 是回應式設計方法。 不同于轉換版面配置系統，其中每個專案的位置都已硬式編碼為螢幕上的某個點，而 [自動設定] 則是 *關聯* 性，也就是專案相對於設計介面上其他元素的位置。 「自動設定」的核心是一種條件約束或規則的概念，這些條件約束或規則會在螢幕上的其他元素內容中定義專案或專案集的位置。 因為元素未系結至畫面上的特定位置，條件約束有助於建立可在不同螢幕大小和裝置方向上美觀的彈性版面配置。

在本指南中，我們會介紹條件約束，以及如何在 Xamarin iOS 設計工具中使用它們。 本指南未涵蓋以程式設計方式使用條件約束。 如需以程式設計方式使用自動設定的詳細資訊，請參閱 [Apple 檔](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html)。

## <a name="requirements"></a>規格需求

Xamarin Designer for iOS 可在 Windows 上 Visual Studio 2017 和更新版本的 Visual Studio for Mac 中取得。

本指南假設您已瞭解 [IOS 設計](~/ios/user-interface/designer/introduction.md) 工具指南簡介中的設計工具元件。

## <a name="introduction-to-constraints"></a>條件約束簡介

「條件約束」（constraint）是螢幕上兩個元素之間關聯性的數學標記法。 以數學關聯性表示 UI 元素的位置，可解決幾個與對 UI 元素的位置進行硬式編碼相關的問題。 例如，如果我們要在直向模式下從畫面底部將按鈕20px，按鈕的位置就會在橫向模式下關閉畫面。 為了避免這種情況，我們可以設定一個條件約束，將按鈕20px 的下邊緣放在視圖的底部。 按鈕邊緣的位置將會計算為 *按鈕. 底端 = view. 底端 20px*，這會將按鈕從視圖底部的20px 放在直向和橫向模式。 根據數學關聯性計算放置的能力，是讓條件約束在 UI 設計方面很有用的功能。

當我們設定條件約束時，我們會建立一個物件，此物件會將 `NSLayoutConstraint` 要限制的物件當做引數，以及條件約束將作用的屬性（attribute）或 *屬性*（attribute）。 在 iOS 設計工具中，屬性包含邊緣，例如元素的 *左邊*、 *右邊*、 *頂端* 和 *底部* 。 它們也包含大小屬性，例如 *高度* 和 *寬度*，以及中心點位置、 *system.windows.media.rotatetransform.centerx* 和 *centerY*。 例如，當我們在兩個按鈕的左邊界位置上加入條件約束時，設計工具會在幕後產生下列程式碼：

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

下一節將說明如何使用 iOS 設計工具來使用條件約束，包括啟用和停用自動設定以及使用條件約束工具列。

## <a name="enable-auto-layout"></a>啟用自動版面配置

預設的 iOS 設計工具設定已啟用條件約束模式。 但是，如果您需要手動啟用或停用它，您可以使用兩個步驟來執行這項作業：

1. 按一下設計介面上的空白空間。 這會取消所有元素的設定，並顯示分鏡指令檔的屬性。
1. 核取或取消核取 [屬性] 面板中的 [ **使用自動佈局** ] 核取方塊：

    ![屬性面板中的 [使用自動佈局] 核取方塊](designer-auto-layout-images/image01.png)

依預設，不會在介面上建立或顯示任何條件約束。 相反地，它們會在編譯時期從框架資訊自動推斷。 若要加入條件約束，我們必須在設計介面上選取專案，並在其中加入條件約束。 我們可以使用 **條件約束工具列** 來這麼做。

## <a name="constraints-toolbar"></a>條件約束工具列

 [![內容功能表命令](designer-auto-layout-images/toolbarnew.png)](designer-auto-layout-images/toolbarnew.png#lightbox)

條件約束工具列已更新，現在包含兩個主要部分：

- **條件約束模式按鈕切換**：之前，您可以在設計介面上選取的視圖上再次按一下，藉以進入條件約束模式。 您現在應該在 [條件約束] 列中使用此切換按鈕：

  ![條件約束模式切換](designer-auto-layout-images/constraints.png)

- **[更新條件約束] 按鈕：** 請務必注意，這些變更取決於您是否處於條件約束編輯模式。
  - 在 [條件約束編輯模式] 中，這個按鈕會調整條件約束，使其符合元素框架。
  - 在畫面格編輯模式中，這個按鈕會調整元素框架，使其符合條件約束所定義的位置。

## <a name="constraints-editing-popover"></a>條件約束編輯 popover

[條件約束編輯器] 快顯視窗可讓我們一次加入和更新選取視圖的多重條件約束。 我們可以建立多個間距、外觀比例和對齊條件約束，例如將視圖對齊兩個視圖的左邊緣。

若要在選取的視圖上編輯條件約束，請按一下省略號來顯示 [popover： ![ 條件約束編輯 popover](designer-auto-layout-images/constraints-popup.png)

開啟 [條件約束] popover 時，會顯示視圖上的任何預設條件約束。 我們 **可以從右上** 角的下拉式方塊中，設定所有的間距條件約束，然後選取 [ **全部清除** ] 來移除它們。

**W** 將設定 Width 和 **H** 會設定高度條件約束。 當您檢查 **外觀比例** 時，視圖的高度和寬度將會以不同的螢幕大小來控制，而視圖的寬度會當做比例的分子，以及高度作為分母。

![條件約束間距](designer-auto-layout-images/constraints-spacing.png)

間距條件約束的四個下拉式方塊會列出錨定條件約束的相鄰視圖

## <a name="surface-based-constraint-editing"></a>Surface-Based 條件約束編輯

如需更微調的條件約束編輯，我們可以直接在設計介面上與條件約束互動。 本節介紹以介面為基礎之條件約束編輯的基本概念，包括釘選間距控制項、卸載區域，以及使用不同類型的條件約束。

### <a name="creating-constraints"></a>建立條件約束

IOS 設計工具提供兩種類型的控制項，可在設計介面上操作專案。 *拖曳控制項* 和 *釘選間距控制項*，如下圖所示：

![view 控制項](designer-auto-layout-images/controls.png)

在條件約束列中選取 [條件約束模式] 按鈕，即可切換這些方法。

元素之每一端的4個 T 形控點會定義條件約束之元素的 *上*、 *右邊*、 *下* 邊緣和 *左邊* 緣。 元素右邊和底部的兩個 I 形控點會分別定義 *高度* 和 *寬度* 條件約束。 中間的正方形會處理 *system.windows.media.rotatetransform.centerx* 和 *centerY* 條件約束。

若要建立條件約束，請挑選一個控點，並將它拖曳到設計介面上的某個位置。 當您開始拖曳時，介面上會出現一系列的綠線/方塊，告訴您可限制的內容。 例如，在下列螢幕擷取畫面中，我們會限制中間按鈕的頂端：

 [![限制中間按鈕的頂端](designer-auto-layout-images/image07.png)](designer-auto-layout-images/image07.png#lightbox)

請注意，其他兩個按鈕之間的三條綠色虛線。 綠線表示 *放置區*，或其他可限制的元素屬性。 在上面的螢幕擷取畫面中，其他兩個按鈕提供3個垂直的放置區域 ( *底部*、 *centerY*、 *top*) 來限制按鈕。 視圖頂端的綠色虛線表示 view 控制器會在視圖頂端提供一個條件約束，而綠色的綠色方塊表示 view 控制器會在最上層的版面配置指南中提供條件約束。

> [!IMPORTANT]
> 版面配置指南是特殊類型的條件約束目標，可讓我們建立最上層和底部的條件約束，以考慮系統列的存在，例如狀態列或工具列。 其中一個主要用途是讓應用程式在 iOS 6 和 iOS 7 之間相容，因為最新版本的容器視圖會擴充到狀態列下方。 如需有關最上層版面配置指南的詳細資訊，請參閱 [Apple 檔](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2)。

接下來的三節將介紹不同類型的條件約束。

### <a name="size-constraints"></a>大小條件約束

具有大小限制- *高度* 和 *寬度* -您有兩個選項。 第一個選項是拖曳控點來限制為鄰近元素大小，如上述範例所示。 另一個選項是按兩下控點來建立自我條件約束。 如此一來，我們就可以指定固定大小的值，如下列螢幕擷取畫面所示：

 [![拖曳控點以限制為鄰近元素大小，如下所示](designer-auto-layout-images/sizec.png)](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>中心限制

視內容而定，方形控點會建立 *system.windows.media.rotatetransform.centerx* 或 *centerY* 條件約束。 拖曳正方形控點會讓其他元素越亮，以提供垂直和水準放置區，如下列螢幕擷取畫面所示：

 [![中心限制](designer-auto-layout-images/centerc.png)](designer-auto-layout-images/centerc.png#lightbox)

如果您選擇垂直的放置區，將會建立 *centerY* 條件約束。 如果您選擇水準放置區，則條件約束將以 *system.windows.media.rotatetransform.centerx* 為基礎。

### <a name="combinational-constraints"></a>Combinational 條件約束

若要建立兩個專案之間的對齊和大小相等條件約束，您可以從頂端工具列中選取專案，以指定-依序水準對齊、垂直對齊和大小 equalities，如下列螢幕擷取畫面所示：

 [![Combinational 條件約束](designer-auto-layout-images/image06.png)](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>視覺化和編輯條件約束

當您加入條件約束時，當您選取專案時，它會在設計介面上顯示為藍線：

 [![視覺化條件約束](designer-auto-layout-images/image09.png)](designer-auto-layout-images/image09.png#lightbox)

您可以按一下藍色線並直接在屬性面板中編輯條件約束值，來選取條件約束。 或者，按兩下藍線會顯示一個 popover，讓您直接在設計介面上編輯值：

 [![編輯條件約束](designer-auto-layout-images/image08.png)](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>條件約束問題

使用條件約束時可能會發生數種類型的問題：

- **衝突的條件約束** ：當多個條件約束強制專案具有衝突的屬性值，而且條件約束引擎無法調解它們時，就會發生這種情況。
- **Underconstrained 專案：專案** 的屬性 (位置 + 大小) 必須完全由其一組條件約束和內建大小所涵蓋，條件約束才會有效。 如果這些值不明確，則表示該專案是 underconstrained。
- **Frame misplacement** -當專案的框架及其一組條件約束定義兩個不同的結果矩形時，就會發生此情況。

本節將介紹上述三個問題，並提供如何處理這些問題的詳細資料。

### <a name="conflicting-constraints"></a>衝突的條件約束

衝突的條件約束會以紅色標示，並具有警告符號。 將滑鼠停留在警告符號上會顯示 popover，其中包含衝突的相關資訊：

 [![衝突的條件約束警告](designer-auto-layout-images/image11.png)](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Underconstrained 專案

Underconstrained 專案會顯示在橙色中，並在 view controller 物件列中觸發橙色標記圖示的外觀：

 [![Underconstrained 專案以橙色顯示](designer-auto-layout-images/image02.png)](designer-auto-layout-images/image02.png#lightbox)

如果您按一下該標記圖示，就可以取得有關場景中 underconstrained 專案的資訊，並藉由完全限制它們或移除其條件約束來解決問題，如下列螢幕擷取畫面所示：

 [![修正 Underconstrained 專案](designer-auto-layout-images/image10.png)](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>畫面格 Misplacement

畫面格 misplacement 使用與 underconstrained 專案相同的色彩代碼。 專案一律會使用其原生框架在介面上轉譯，但在畫面格 misplacement 中，紅色矩形會標示應用程式執行時專案最後的位置，如下列螢幕擷取畫面所示：

 [![範例畫面格 Misplacement 視圖](designer-auto-layout-images/image05.png)](designer-auto-layout-images/image05.png#lightbox)

若要解決畫面格 misplacement 錯誤，請從 [條件約束] 工具列中選取 [ **依據條件約束更新框架** ] 按鈕，)  (最右邊的按鈕：

 [![根據條件約束工具列按鈕更新框架](designer-auto-layout-images/image03.png)](designer-auto-layout-images/image03.png#lightbox)

這會自動調整元素框架，以符合控制項所定義的位置。

<a name="modifying-in-code"></a>

## <a name="modifying-constraints-in-code"></a>修改程式碼中的條件約束

根據您的應用程式需求，有時候您可能需要修改程式碼中的條件約束。 例如，若要調整條件約束所附加的視圖大小或調整大小，請變更條件約束的優先順序，或完全停用條件約束。

若要存取程式碼中的條件約束，您必須先執行下列動作，以在 iOS 設計工具中公開它：

1. 使用上述) 所列的任何方法，以一般 (建立條件約束。
2. 在 [ **檔大綱] 瀏覽器** 中，尋找所需的條件約束，然後選取它：

    [![檔大綱瀏覽器](designer-auto-layout-images/modify01.png)](designer-auto-layout-images/modify01.png#lightbox)
3. 接下來，在 [**屬性瀏覽器**] 的 [ **Widget** ] 索引標籤中，將 **名稱** 指派給條件約束：

    [![[Widget] 索引標籤](designer-auto-layout-images/modify02.png)](designer-auto-layout-images/modify02.png#lightbox)
4. 儲存您的變更。

有了上述變更之後，您就可以在程式碼中存取條件約束，並修改其屬性。 例如，您可以使用下列程式，將附加的視圖高度設定為零：

```csharp
ViewInfoHeight.Constant = 0;
```

在 iOS 設計工具中，指定條件約束的下列設定：

[![在屬性瀏覽器中編輯條件約束](designer-auto-layout-images/modify03.png)](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>延遲的版面設定階段

自動設定引擎不會立即更新附加的視圖以回應條件約束變更，而是在不久的未來排程 _延遲的版面配置傳遞_ 。 在這個延遲的階段中，不僅會更新指定的視圖條件約束，也會重新計算階層中每個視圖的條件約束，並更新以調整新的配置。

在任何時間點，您都可以藉由呼叫 `SetNeedsLayout` 父視圖的或方法，來排程您自己的延遲版面配置傳遞 `SetNeedsUpdateConstraints` 。

延遲的版面設定階段包含兩個透過 view 階層的唯一階段：

- 這次 **的更新傳遞**，自動設定引擎會遍歷視圖階層，並在所有視圖控制器上叫用方法，並在 `UpdateViewConstraints` `UpdateConstraints` 所有視圖上叫用方法。
- **版面** 配置會再次通過，而自動設定引擎會遍歷視圖階層，但這次會在所有視圖控制器上叫用 `ViewWillLayoutSubviews` 方法，並在 `LayoutSubviews` 所有視圖上叫用方法。 `LayoutSubviews`方法會 `Frame` 使用自動設定引擎所計算的矩形，更新每個子視圖的屬性。

### <a name="animating-constraint-changes"></a>將條件約束變更動畫

除了修改條件約束屬性之外，您還可以使用核心動畫來以動畫顯示視圖條件約束的變更。 例如：

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

這裡的關鍵是呼叫 `LayoutIfNeeded` 動畫區塊內父視圖的方法。 這會告訴視圖繪製動畫位置或大小變更的每個「框架」。 如果沒有這一行，視圖就只會貼上最終版本，而不會產生動畫。

## <a name="summary"></a>摘要

本指南引進了 iOS 自動 (或「適應性」 ) 配置，以及在設計介面上專案之間的數學標記法的條件約束概念。 它描述如何在 iOS 設計工具中啟用自動設定、使用 **條件約束工具列**，以及在設計介面上個別編輯條件約束。 接下來，它會說明如何針對三個常見的條件約束問題進行疑難排解。 最後，它會示範如何在程式碼中修改條件約束。

## <a name="related-links"></a>相關連結

- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
- [iOS 設計的控制項逐步解說](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Android Designer 總覽](~/android/user-interface/android-designer/index.md)
- [程式設計條件約束](~/ios/user-interface/programmatic-layout-constraints.md)
