---
title: "使用 Xamarin 設計工具，適用於 iOS 的自動配置"
description: "本指南介紹 iOS 自動配置和新條件約束的工作流程使用 iOS 的 Xamarin 設計工具中。"
ms.topic: article
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: d9e38f055414ff0eca70df4a4e38aa959b7d7237
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>使用 Xamarin 設計工具，適用於 iOS 的自動配置

_本指南介紹 iOS 自動配置和新條件約束的工作流程使用 iOS 的 Xamarin 設計工具中。_

自動配置 （也稱為 「 自動調整的版面配置 」） 是一種回應式設計方法。 不同於這個過渡期的版面配置系統，其中每個元素的位置是硬式編碼至螢幕上的點，自動配置是關於*關聯性*-設計介面上的其他項目位置的項目的位置。 自動配置的核心是 條件約束或規則，以定義位置的項目或項目集在螢幕上的其他項目內容中的概念。 在螢幕上的特定位置，不會繫結項目，因為條件約束會協助建立起來不錯上不同的螢幕大小和裝置的方向調整版面配置。

在本指南中，我們會造成條件約束，以及如何在 Xamarin iOS 設計工具中使用它們。 本指南並未涵蓋以程式設計方式使用條件約束。 如需以程式設計方式使用自動配置資訊，請參閱[Apple 文件](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html)。

## <a name="requirements"></a>需求

適用於 Visual Studio 2015 和 2017年中的 Mac 在 Windows 上的 Visual Studio 中使用 Xamarin 設計工具，適用於 iOS。

本指南假設您已經在設計工具元件從知識[iOS 設計工具簡介](~/ios/user-interface/designer/introduction.md)指南。

## <a name="introduction-to-constraints"></a>條件約束的簡介

條件約束是在螢幕上的兩個項目之間的關聯性的數學表示法。 代表 UI 元素的位置做為數學關聯性可解決聯硬式編碼的 UI 項目位置的幾個問題。 例如，如果我們將從畫面底部的按鈕 20px 直向模式中，按鈕的位置會以橫向模式螢幕。 若要避免這個問題，我們無法設定放置按鈕 20px，從檢視底部的下邊緣的條件約束。 然後會按鈕邊緣的位置計算為*button.bottom = view.bottom-20px*，這會將檢視底部的按鈕 20px 放在縱向或橫向模式。 計算放置數學關聯性為基礎的能力就是條件約束有用 UI 設計中。

當我們設定的條件約束時，我們建立`NSLayoutConstraint`要限制的物件和屬性，會做為引數的物件或*屬性*，條件約束會採取動作。 在 iOS 設計工具中，屬性包括邊緣例如*左*，*右*，*頂端*，和*底部*的項目。 其中也包括大小屬性例如*高度*和*寬度*，和中心點的位置， *centerX*和*centerY*。 例如，當我們將兩個按鈕的左邊界的位置上的條件約束時，設計工具產生背後的下列程式碼：

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

下一節涵蓋了使用 iOS 設計工具，包括啟用和停用自動配置和使用條件約束工具列的條件約束使用。

## <a name="enable-auto-layout"></a>啟用自動配置

預設 iOS Designer 設定已啟用的條件約束模式。 不過，如果您需要啟用或停用它以手動方式，您可以在兩個步驟進行：

1.  按一下設計介面上的空白處。 這會取消選取任何項目，並啟動分鏡腳本文件的屬性。
1.  選取或取消選取**使用 Autolayout**屬性面板中的核取方塊：

    ![](designer-auto-layout-images/image01.png "使用自動版面配置中的核取 [屬性] 面板")


根據預設，沒有條件約束會建立或在介面上看見。 相反地，它們會自動推斷在編譯時期的框架資訊。 若要新增條件約束，我們需要選取設計介面上的項目並將它新增條件約束。 我們可以進行使用**條件約束工具列**。

## <a name="constraints-toolbar"></a>條件約束工具列

 [![](designer-auto-layout-images/toolbarnew.png "內容功能表命令")](designer-auto-layout-images/toolbarnew.png#lightbox)

條件約束工具列已更新，現在包含兩個主要部分：

- **條件約束模式按鈕切換**： 之前，您輸入的條件約束模式再次按一下設計介面上選取的檢視。 您現在應使用此切換按鈕的條件約束列中：

  ![條件約束模式切換](designer-auto-layout-images/constraints.png)

- **「 更新條件約束 」 按鈕：**很值得注意的變更，視您是在編輯模式的條件約束。
  - 在條件約束的編輯模式下此按鈕，調整以符合項目框架的條件約束。
  - 框架編輯模式中這個按鈕會調整以符合這些條件約束所定義的位置的項目框架。


## <a name="surface-based-constraint-editing"></a>編輯介面為基礎的條件約束

在前一個區段中，我們學到如何新增預設條件約束和移除條件約束使用條件約束工具列。 多個編輯微調的條件約束，我們可以直接在設計介面上的條件約束與互動。 本節將介紹基本概念的編輯介面為基礎的條件約束，包括 pin 間距控制項、 拖放區，以及如何使用不同類型的條件約束。

### <a name="creating-constraints"></a>建立條件約束

IOS 設計工具提供兩種類型的控制項管理設計介面上的項目。 *拖曳控制項*和*pin 間距控制項*，如下列影像所示：

![檢視控制項](designer-auto-layout-images/controls.png)

切換成這些條件約束列中選取 [條件約束模式] 按鈕。

4 的 T 形控點的項目每一端上定義*頂端*，*右*，*底部*，和*左*邊緣的項目條件約束。 兩個的 I 形控點右側和底部的項目定義*高度*和*寬度*條件約束分別。 中間的方形處理兩者*centerX*和*centerY*條件約束。

若要建立條件約束，挑選的控制代碼並拖曳設計介面上的某處。 當您開始拖曳時，一系列的綠色的線條方塊將會告訴您什麼介面上顯示您可以限制。 例如，在以下的螢幕擷取畫面，我們要限制中間按鈕的上方：

 [![](designer-auto-layout-images/image07.png "條件約束的中間按鈕上方")](designer-auto-layout-images/image07.png#lightbox)

請注意三個綠色虛線，跨兩個按鈕。 綠色的線條表示*拖放區*，或我們可以限制其他項目的屬性。 在上面的螢幕擷取畫面，其他兩個按鈕提供 3 垂直的卸除區域 (*底部*， *centerY*，*頂端*) 來限制我們 按鈕。 綠色檢視頂端的虛線表示檢視控制器提供頂端的檢視中，條件約束，而實線綠色方塊表示檢視控制器提供最上層配置輔助線的條件約束。

> [!IMPORTANT]
> **請注意**： 配置輔助線是特殊類型的條件約束的目標可讓我們來建立列入考量系統列，例如狀態列或工具列是否存在的上方和下方的條件約束。 主要用途是要有相容 iOS 6 和 iOS 7 之間的應用程式，因為最新版本具有以下狀態列延伸容器檢視。 如需最上層配置指南的詳細資訊，請參閱[Apple 文件](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2)。



接下來三個章節介紹使用不同類型的條件約束。

### <a name="size-constraints"></a>大小限制

大小限制-*高度*和*寬度*-有兩個選項。 第一個選項是拖曳控點以像素項目大小限制，如上述範例所示。 按兩下要建立自助條件約束的控制代碼為另一個選項。 這可讓我們指定常數大小值，如以下螢幕擷取畫面所示：

 [![](designer-auto-layout-images/sizec.png "拖曳控點以像素項目大小限制，如下圖所示")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>Center 條件約束

方形控點會建立*centerX*或*centerY*條件約束，視內容而定。 拖曳方形控點，將會亮起提供這兩個垂直和水平卸除區域，如以下螢幕擷取畫面所示的其他項目：

 [![](designer-auto-layout-images/centerc.png "Center 條件約束")](designer-auto-layout-images/centerc.png#lightbox)

如果您選擇的垂直置放區域， *centerY*將建立條件約束。 如果您選擇的水平的拖放區時，條件約束將會根據*centerX*。

### <a name="combinational-constraints"></a>Combinational 條件約束

若要建立對齊和兩個項目之間的大小相等條件約束，您可以從指定的順序為水平對齊方式、 垂直對齊方式及大小等的最上層工具列選取項目，如以下螢幕擷取畫面所示：

 [![](designer-auto-layout-images/image06.png "Combinational 條件約束")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>視覺化及編輯條件約束

當您新增條件約束時，它將會顯示在設計介面上藍線為選取的項目：

 [![](designer-auto-layout-images/image09.png "以視覺化方式檢視的條件約束")](designer-auto-layout-images/image09.png#lightbox)

您可以按一下藍線，並編輯條件約束值直接在 [屬性] 面板中的選取條件約束。 或者，按兩下藍線會顯示可讓您編輯的值，直接在設計介面上 popover:

 [![](designer-auto-layout-images/image08.png "編輯條件約束")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>條件約束的問題

使用條件約束時，可能會發生數種類型的問題：

-  **衝突的條件約束**— 多個條件約束強制項目具有屬性的值衝突，且條件約束引擎無法協調它們時會發生這個問題。
-  **Underconstrained 項目**— 元素的屬性 （位置和大小） 必須完全涵蓋其設定的條件約束和內建大小為有效的條件約束。 如果這些值模稜兩可，項目即 underconstrained。
-  **框架錯置**— 這發生在元素的框架和其一組條件約束定義兩個不同結果的矩形。


本節 elaborates 上面所列之三個問題，並提供有關如何處理它們的詳細資料。

### <a name="conflicting-constraints"></a>衝突的條件約束

衝突的條件約束標示為紅色，而有警告符號。 將滑鼠停留警告符號隨即開啟 popover 衝突的相關資訊：

 [![](designer-auto-layout-images/image11.png "衝突的條件約束警告")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Underconstrained 項目

Underconstrained 的項目顯示為橙色和觸發程序中檢視控制站物件的橙色標記圖示的外觀：

 [![](designer-auto-layout-images/image02.png "以橙色顯示 underconstrained 項目")](designer-auto-layout-images/image02.png#lightbox)

如果您按一下該標記圖示，場景中取得 underconstrained 項目的相關資訊，並解決問題，可能完全條件約束它們，或移除其條件約束，如以下螢幕擷取畫面所示：

 [![](designer-auto-layout-images/image10.png "修正 Underconstrained 項目")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>框架錯置

框架錯置 underconstrained 項目為使用相同的色彩代碼。 一律會使用其原生框架，在介面上呈現的項目，但在框架錯置的情況下以紅色矩形會將其中項目，最後將會在應用程式執行時，如以下螢幕擷取畫面所示：

 [![](designer-auto-layout-images/image05.png "框架錯置檢視範例")](designer-auto-layout-images/image05.png#lightbox)

若要解決框架錯置的錯誤，請選取**更新畫面格根據條件約束**從條件約束工具列 （最右側的按鈕） 的按鈕：

 [![](designer-auto-layout-images/image03.png "更新為基礎的條件約束工具列按鈕的畫面格")](designer-auto-layout-images/image03.png#lightbox)

這會自動調整以符合控制項所定義的位置的項目框架。

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>修改程式碼中的條件約束

根據您的應用程式的需求，可能的需要修改程式碼中的條件約束。 例如，調整大小或重新定位檢視條件約束連接到已連接，變更優先順序條件約束，或完全停用條件約束。

若要存取程式碼中的條件約束，您需要先將它公開 iOS 設計工具中執行下列動作：

1. 建立條件約束來當作標準 （使用任何上述所列的方法）。
2. 在**文件大綱總管**、 尋找所需的條件約束，然後選取：

    [![](designer-auto-layout-images/modify01.png "[總管] 中的文件大綱")](designer-auto-layout-images/modify01.png#lightbox)
3. 接下來，指派**名稱**給條件約束中**Widget**  索引標籤**屬性總管**:

    [![](designer-auto-layout-images/modify02.png "[小工具] 索引標籤")](designer-auto-layout-images/modify02.png#lightbox)
4. 儲存您的變更。

與上述變更之後，您可以存取程式碼中的條件約束，並修改其屬性。 例如，您可以使用下列設定為零的 [附加] 檢視的高度：

```csharp
ViewInfoHeight.Constant = 0;
```

IOS 設計工具中指定的條件約束的下列設定：

[![](designer-auto-layout-images/modify03.png "編輯屬性總管 中的條件約束")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>延後的配置傳遞

而不是立即更新以回應變更條件約束附加的檢視，排程自動版面配置引擎_延期配置傳遞_近期。 在此延後的階段期間不只是指定的檢視條件約束更新，條件約束的階層中的每個檢視會重新計算，且更新，以調整新版面配置。

在任何時間點，您可以藉由呼叫排程自己延後的配置傳遞`SetNeedsLayout`或`SetNeedsUpdateConstraints`父檢視的方法。 

延後配置傳遞兩個唯一通過檢視階層所組成：

- **更新傳遞**-在此階段中，自動版面配置引擎會周遊檢視階層架構，並叫用`UpdateViewConstraints`所有檢視控制站上的方法和`UpdateConstraints`上所有檢視的方法。
- **配置傳遞**-同樣地，自動版面配置引擎會周遊檢視階層架構中，但這次會叫用`ViewWillLayoutSubviews`所有檢視控制站上的方法和`LayoutSubviews`上所有檢視的方法。 `LayoutSubviews`方法會更新`Frame`計算方式是自動版面配置引擎的每個矩形的子檢視的屬性。

### <a name="animating-constraint-changes"></a>建立條件約束變更動畫

除了修改條件約束的屬性，您可以使用核心動畫顯示檢視的條件約束的變更。 例如: 

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

這裡的關鍵呼叫`LayoutIfNeeded`動畫區塊內的父檢視的方法。 這會告訴繪製動畫的位置或大小變更的每個"frame"檢視。 沒有這行中，檢視會只需貼齊至最終版本而不建立動畫。

## <a name="summary"></a>總結

本指南介紹 iOS 自動 （或"adaptive"） 配置和條件約束當做數學表示法在設計介面上的項目之間的關聯性的概念。 它說明如何使用在 iOS 設計工具中啟用自動配置**條件約束工具列**，和編輯在設計介面上個別的條件約束。 接下來，它會說明如何疑難排解三個條件約束的常見問題。 最後，它會示範如何修改程式碼中的條件約束。

## <a name="related-links"></a>相關連結

- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
- [iOS 可以設計控制項的逐步解說](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Android 設計工具的概觀](~/android/user-interface/android-designer/index.md)
- [以程式設計方式的條件約束](~/ios/user-interface/programmatic-layout-constraints.md)
- [Apple-自動配置指南](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/AutolayoutPG/Introduction/Introduction.html#/apple_ref/doc/uid/TP40010853-CH13-SW1)
