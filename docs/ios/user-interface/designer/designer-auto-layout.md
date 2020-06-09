---
title: 使用 Xamarin Designer for iOS 的自動版面配置
description: 本指南介紹 iOS 自動版面配置，並說明如何使用 Xamarin Designer for iOS 來建立和編輯使用條件約束的版面配置。 同時討論修改程式碼中的條件約束、製作條件約束變更的動畫等等。
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 5aa3baa6aba76483866911d905687be6c3a5ae4e
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569825"
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>使用 Xamarin Designer for iOS 的自動版面配置

自動設定（也稱為「調適型版面配置」）是一種回應式設計方法。 不同于過渡配置系統，其中每個專案的位置都是硬式編碼至螢幕上的某個點，自動設定是關於*關聯*性-專案相對於設計介面上其他元素的位置。 自動設定的核心是條件約束或規則的概念，其定義了在螢幕上其他元素內容中的專案或專案集合的位置。 由於元素不會系結至螢幕上的特定位置，因此條件約束有助於建立可在不同螢幕大小和裝置方向上看起來良好的調適型版面配置。

在本指南中，我們引進了條件約束，以及如何在 Xamarin iOS 設計工具中使用它們。 本指南未涵蓋以程式設計方式使用條件約束。 如需以程式設計方式使用自動設定的詳細資訊，請參閱[Apple 檔](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html)。

## <a name="requirements"></a>規格需求

Xamarin Designer for iOS 適用于 Windows 上 Visual Studio 2017 和更新版本中的 Visual Studio for Mac。

本指南假設您已從[IOS 設計工具簡介](~/ios/user-interface/designer/introduction.md)指南中瞭解設計工具的元件。

## <a name="introduction-to-constraints"></a>條件約束簡介

「條件約束」（constraint）是螢幕上兩個元素之間關聯性的數學標記法。 將 UI 專案的位置表示為數學關聯性，可解決許多與硬式編碼 UI 元素位置相關的問題。 例如，如果我們要在直向模式下將按鈕從畫面底部20px，按鈕的位置會在橫向模式中關閉螢幕。 若要避免這種情況，我們可以設定一個條件約束，將按鈕20px 的下邊緣放在視圖的底部。 然後按鈕邊緣的位置會計算為*button. 底端 = view. 底端-20px*，這會將按鈕20px 從視圖底部的直向和橫向模式。 根據數學關聯性計算位置的能力，是讓條件約束在 UI 設計中很有用的功能。

當我們設定條件約束時，我們會建立 `NSLayoutConstraint` 物件，它會接受要限制的物件以及條件約束將作用的屬性（attribute）（ *attribute）* 做為引數。 在 iOS 設計工具中，屬性包括邊緣，例如元素的*左邊*、*右邊*、*上方*和*底部*。 它們也包括大小屬性，例如*height*和*width*，以及中心點位置*system.windows.media.rotatetransform.centerx*和*centerY*。 例如，當我們在兩個按鈕的左邊界位置加入條件約束時，設計工具會在幕後產生下列程式碼：

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

下一節將說明如何使用 iOS 設計工具來處理條件約束，包括啟用和停用自動設定，以及使用條件約束工具列。

## <a name="enable-auto-layout"></a>啟用自動版面配置

預設的 iOS 設計工具設定已啟用條件約束模式。 不過，如果您需要以手動方式啟用或停用它，您可以使用兩個步驟來執行此動作：

1. 按一下設計介面上的空白處。 這會取消設定任何元素，並顯示分鏡指令檔的屬性。
1. 勾選或取消核取 [屬性] 面板中的 [**使用自動**功能] 核取方塊：

    ![](designer-auto-layout-images/image01.png "The Use Autolayout checkbox in the property panel")

根據預設，介面上不會建立或顯示任何條件約束。 相反地，它們會在編譯時期自動從框架資訊推斷出來。 若要加入條件約束，我們需要選取設計介面上的專案，並在其中加入條件約束。 我們可以使用**條件約束工具列**來執行此動作。

## <a name="constraints-toolbar"></a>條件約束工具列

 [![](designer-auto-layout-images/toolbarnew.png "The Context Menu Commands")](designer-auto-layout-images/toolbarnew.png#lightbox)

[條件約束] 工具列已更新，現在包含兩個主要部分：

- **條件約束模式按鈕切換**：先前您已在設計介面上的選取視圖上再次按一下，進入條件約束模式。 您現在應該在條件約束列中使用這個切換按鈕：

  ![條件約束模式切換](designer-auto-layout-images/constraints.png)

- **[更新條件約束] 按鈕：** 請務必注意，視您是否處於條件約束編輯模式而定，會進行變更。
  - 在 [條件約束編輯模式] 中，此按鈕會調整條件約束以符合元素框架。
  - 在 [框架編輯模式] 中，此按鈕會調整元素框架，使其符合條件約束的定義位置。

## <a name="constraints-editing-popover"></a>條件約束編輯 popover

[條件約束編輯器] 快顯視窗可讓我們針對選取視圖，一次加入和更新多個條件約束。 我們可以建立多個間距、外觀比例和對齊條件約束，例如將視圖對齊兩個視圖的左邊緣。

針對選取之視圖上的編輯條件約束，按一下省略號以顯示 [popover： ![ 條件約束編輯] popover](designer-auto-layout-images/constraints-popup.png)

開啟 [條件約束] popover 時，它會在此視圖上顯示任何預設的條件約束。 我們**可以從右上**角的下拉式方塊中選取所有的間距條件約束，然後選取 [**全部清除**] 將它們移除。

**W**會設定 width，而**H**會設定 height 條件約束。 當您檢查**外觀比例**時，會以不同的螢幕大小來控制 views 的高度和寬度，視圖的寬度會當做比例的分子，而高度則做為分母。

![條件約束間距](designer-auto-layout-images/constraints-spacing.png)

四個間距條件約束的下拉式方塊列出要錨定條件約束的相鄰視圖

## <a name="surface-based-constraint-editing"></a>以介面為基礎的條件約束編輯

針對更微調的條件約束編輯，我們可以直接在設計介面上與條件約束互動。 本節介紹以介面為基礎之條件約束編輯的基本概念，包括固定間距控制項、放置區域，以及使用不同類型的條件約束。

### <a name="creating-constraints"></a>建立條件約束

IOS 設計工具提供兩種類型的控制項，用於操作設計介面上的元素。 *拖曳控制項*和*釘選間距控制項*，如下圖所示：

![view 控制項](designer-auto-layout-images/controls.png)

這些會藉由選取條件約束列中的 [條件約束模式] 按鈕來切換。

元素每一端上的4個 T 形狀控點會定義條件約束之元素的*上*、*右*、*下*邊緣和*左邊*緣。 元素右邊和底部的兩個 I 形狀控點會分別定義*height*和*width*條件約束。 中間方形會處理*system.windows.media.rotatetransform.centerx*和*centerY*條件約束。

若要建立條件約束，請挑選一個控點，並將它拖曳至設計介面上的某個位置。 當您開始拖曳時，一系列的綠色線條/方塊會出現在介面上，告訴您可以限制的內容。 例如，在下面的螢幕擷取畫面中，我們會限制中間按鈕的頂端：

 [![](designer-auto-layout-images/image07.png "Constraining the top side of the middle button")](designer-auto-layout-images/image07.png#lightbox)

請注意，在其他兩個按鈕中，三個虛線的綠色線條。 綠色線條表示*放置區域*，或是我們可以限制的其他元素的屬性。 在上方的螢幕擷取畫面中，其他兩個按鈕會提供3個垂直拖放區域（*底部*、 *centerY*、*頂端*）來限制按鈕。 視圖頂端的綠色虛線表示視圖控制器在視圖頂端提供條件約束，而綠色方塊表示 view controller 在頂端版面配置指南底下提供了條件約束。

> [!IMPORTANT]
> 版面配置指南是特殊類型的條件約束目標，可讓我們建立最上層和底部的條件約束，以考慮系統橫條的存在，例如狀態列或工具列。 其中一個主要用途是讓應用程式在 iOS 6 和 iOS 7 之間相容，因為最新版本的容器視圖會延伸到狀態列底下。 如需有關最上層版面配置指南的詳細資訊，請參閱[Apple 檔](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2)。

接下來的三個章節會介紹如何使用不同類型的條件約束。

### <a name="size-constraints"></a>大小條件約束

具有大小限制-*高度*和*寬度*-您有兩個選項。 第一個選項是拖曳控點來限制為鄰近的元素大小，如上述範例所示。 另一個選項是按兩下該控制碼，以建立自我條件約束。 這可讓我們指定常數大小值，如下列螢幕擷取畫面所示：

 [![](designer-auto-layout-images/sizec.png "Drag the handle to constrain to a neighbor element size, as illustrated here")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>中心條件約束

視內容而定，方形控點會建立*system.windows.media.rotatetransform.centerx*或*centerY*條件約束。 拖曳方形控點會使其他元素變亮，以同時提供垂直和水準放置區，如下列螢幕擷取畫面所示：

 [![](designer-auto-layout-images/centerc.png "Center Constraints")](designer-auto-layout-images/centerc.png#lightbox)

如果您選擇垂直放置區，將會建立*centerY*條件約束。 如果您選擇水準放置區，則條件約束會以*system.windows.media.rotatetransform.centerx*為基礎。

### <a name="combinational-constraints"></a>Combinational 條件約束

若要在兩個元素之間建立對齊和大小相等條件約束，您可以從頂端工具列選取專案，以指定順序水準對齊、垂直對齊和大小 equalities，如下列螢幕擷取畫面所示：

 [![](designer-auto-layout-images/image06.png "Combinational Constraints")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>視覺化和編輯條件約束

當您加入條件約束時，當您選取專案時，它會在設計介面上顯示為藍色線條：

 [![](designer-auto-layout-images/image09.png "Visualizing Constraints")](designer-auto-layout-images/image09.png#lightbox)

您可以按一下藍線，並直接在屬性面板中編輯條件約束值，以選取條件約束。 或者，按兩下藍線會顯示一個 popover，讓您直接在設計介面上編輯值：

 [![](designer-auto-layout-images/image08.png "Editing Constraints")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>條件約束問題

使用條件約束時，可能會發生數種類型的問題：

- **衝突的條件約束**：當多個條件約束強制元素具有衝突的屬性值，且條件約束引擎無法協調它們時，就會發生這種情況。
- **Underconstrained 專案**：元素的屬性（位置 + 大小）必須完全涵蓋于其條件約束集和內建大小，條件約束才會是有效的。 如果這些值不明確，則專案會被視為 underconstrained。
- **Frame misplacement** —當元素的框架及其條件約束集合定義兩個不同的結果矩形時，就會發生這個問題。

本節介紹上述三個問題，並提供如何處理它們的詳細資料。

### <a name="conflicting-constraints"></a>衝突的條件約束

衝突的條件約束會以紅色標示，並具有警告符號。 將滑鼠停留在警告符號上會顯示 popover，其中包含衝突的相關資訊：

 [![](designer-auto-layout-images/image11.png "Conflicting Constraints warning")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Underconstrained 專案

Underconstrained 專案會以橙色顯示，並在 [view controller] 物件列中觸發橙色標記圖示的外觀：

 [![](designer-auto-layout-images/image02.png "Underconstrained items appear in orange")](designer-auto-layout-images/image02.png#lightbox)

如果您按一下該標記圖示，您可以取得場景中 underconstrained 專案的相關資訊，並藉由完全限制它們或移除其條件約束來解決問題，如下列螢幕擷取畫面所示：

 [![](designer-auto-layout-images/image10.png "Fixing Underconstrained Items")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>框架 Misplacement

畫面格 misplacement 使用與 underconstrained 專案相同的色彩代碼。 專案一律會使用其原生框架轉譯在介面上，但在畫面格 misplacement 紅色矩形時，會標示在應用程式執行時專案最終的位置，如下列螢幕擷取畫面所示：

 [![](designer-auto-layout-images/image05.png "Sample Frame Misplacement view")](designer-auto-layout-images/image05.png#lightbox)

若要解決畫面格 misplacement 錯誤，請從 [條件約束] 工具列（最右邊的按鈕）選取 [**根據條件約束更新畫面**格] 按鈕：

 [![](designer-auto-layout-images/image03.png "Update Frames based on Constraints toolbar button")](designer-auto-layout-images/image03.png#lightbox)

這會自動調整元素框架，使其符合控制項所定義的位置。

<a name="modifying-in-code"></a>

## <a name="modifying-constraints-in-code"></a>修改程式碼中的條件約束

根據您的應用程式需求，有時候您可能需要修改程式碼中的條件約束。 例如，若要調整大小或重新置放已附加條件約束的視圖，請變更條件約束的優先順序，或完全停用條件約束。

若要存取程式碼中的條件約束，您必須先執行下列動作，在 iOS 設計工具中公開它：

1. 以一般方式建立條件約束（使用以上所列的任何方法）。
2. 在 [**檔大綱] 瀏覽器**中，尋找所需的條件約束並加以選取：

    [![](designer-auto-layout-images/modify01.png "The Document Outline Explorer")](designer-auto-layout-images/modify01.png#lightbox)
3. 接下來，在 [**屬性] Explorer**的 [ **Widget** ] 索引標籤中，將**名稱**指派給條件約束：

    [![](designer-auto-layout-images/modify02.png "The Widget Tab")](designer-auto-layout-images/modify02.png#lightbox)
4. 儲存您的變更。

就地變更之後，您就可以存取程式碼中的條件約束，並修改其屬性。 例如，您可以使用下列程式，將附加的視圖高度設定為零：

```csharp
ViewInfoHeight.Constant = 0;
```

針對 iOS 設計工具中的條件約束，提供下列設定：

[![](designer-auto-layout-images/modify03.png "Editing a Constraint in the Property Explorer")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>延遲的版面設定階段

自動設定引擎不會立即更新附加的視圖以回應條件約束變更，而是會在不久的未來排程延遲的配置_傳遞_。 在此延後行程期間，不僅會更新指定的視圖條件約束，也會重新計算階層中每個視圖的條件約束，並更新以調整新的版面配置。

在任何時間點，您都可以藉由呼叫 `SetNeedsLayout` 父視圖的或方法，來排程您自己的延遲版面配置傳遞 `SetNeedsUpdateConstraints` 。

延遲的版面配置傳遞是由兩個透過 view 階層的唯一階段所組成：

- **更新傳遞**-在此階段中，自動版面配置引擎會遍歷視圖階層，並在所有視圖控制器上叫用 `UpdateViewConstraints` 方法，並在 `UpdateConstraints` 所有視圖上叫用方法。
- **版面**配置會再次傳遞，自動版面配置引擎會遍歷視圖階層，但這次會在所有視圖控制器上叫用 `ViewWillLayoutSubviews` 方法，並在 `LayoutSubviews` 所有視圖上叫用方法。 `LayoutSubviews`方法會 `Frame` 使用自動設定引擎所計算的矩形，來更新每個子視圖的屬性。

### <a name="animating-constraint-changes"></a>動畫條件約束變更

除了修改條件約束屬性之外，您還可以使用核心動畫來建立對視圖條件約束變更的動畫。 例如：

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

此處的索引鍵是在 `LayoutIfNeeded` 動畫區塊內呼叫父視圖的方法。 這會告訴此視圖繪製動畫位置或大小變更的每個「框架」。 如果沒有這一行，此視圖只會貼齊最終版本，而不會產生動畫。

## <a name="summary"></a>總結

本指南引進 iOS 自動（或「調適型」）配置，以及在設計介面上專案之間的關聯性數學標記法的條件約束概念。 本文說明如何在 iOS 設計工具中啟用自動版面配置、使用**條件約束工具列**，以及在設計介面上個別編輯條件約束。 接下來，它會說明如何針對三個常見的條件約束問題進行疑難排解。 最後，它會示範如何修改程式碼中的條件約束。

## <a name="related-links"></a>相關連結

- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
- [iOS 設計的控制項逐步解說](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Android Designer 總覽](~/android/user-interface/android-designer/index.md)
- [程式設計條件約束](~/ios/user-interface/programmatic-layout-constraints.md)
