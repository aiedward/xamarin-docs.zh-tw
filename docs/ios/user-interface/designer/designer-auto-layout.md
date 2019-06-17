---
title: 使用適用於 iOS 的 Xamarin 設計工具的自動版面配置
description: 本指南會介紹 iOS 自動版面配置，並說明如何使用適用於 iOS 的 Xamarin 設計工具，來建立和編輯配置使用條件約束。 它也會討論在程式碼，以動畫顯示條件約束的變更，以及其他修改條件約束。
ms.prod: xamarin
ms.assetid: CAC7A715-55BB-45E2-BB6D-2168D36D428F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 41a9ec90b4b734dde7a982ac3d4b2e7b2082321c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61412971"
---
# <a name="auto-layout-with-the-xamarin-designer-for-ios"></a>使用適用於 iOS 的 Xamarin 設計工具的自動版面配置

自動版面配置 （也稱為 「 自動調整的版面配置 」） 是回應式設計的方法。 不同於過渡期的版面配置系統，其中每個元素的位置是硬式編碼為畫面上的點，自動版面配置是關於*關聯性*-項目相對於其他元素的設計介面上的位置。 自動版面配置的核心是條件約束或規則，在螢幕上的其他項目內容中定義位置的項目或項目集的概念。 項目不會繫結到螢幕上的特定位置，因為條件約束來協助建立彈性的配置，看起來沒問題，在不同螢幕大小和裝置方向。

本指南中，我們將介紹條件約束，以及如何在 Xamarin iOS 設計工具中使用它們。 本指南並未涵蓋以程式設計方式使用條件約束。 如需以程式設計方式使用自動版面配置資訊，請參閱[Apple 文件](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html)。

## <a name="requirements"></a>需求

在 Visual Studio for Mac 在 Visual Studio 2017 和更新版本上的 Windows 中使用 Xamarin iOS 設計工具。

本指南假設您已經從設計工具的元件的知識[iOS 設計工具簡介](~/ios/user-interface/designer/introduction.md)指南。

## <a name="introduction-to-constraints"></a>條件約束的簡介

條件約束是在螢幕上的兩個項目之間的關聯性的數學表示法。 表示 UI 元素的位置，做為數學關聯性可解決與硬式編碼的 UI 項目位置相關聯的幾個問題。 例如，如果我們將從畫面底部的按鈕 20px 放在直向模式中，按鈕的位置會在螢幕中以橫向模式。 若要避免這個問題，我們無法設定放置按鈕 20px，從檢視底部的下邊緣的條件約束。 按鈕邊緣的位置則會以計算*button.bottom = view.bottom-20px*，這會將從底部的檢視 按鈕 20px 放在直向和橫向模式。 計算放置數學關聯性為基礎的功能有何限制這麼有幫助在 UI 設計。

當我們設定的條件約束時，我們會建立`NSLayoutConstraint`物件會做為引數會限定物件和屬性，或是*屬性*，條件約束會採取動作。 IOS 設計工具中，在屬性包含邊緣這類*左*，*右*，*頂端*，並*底部*的項目。 其中也包括大小屬性這類*高度*並*寬度*，和中心點的位置， *centerX*並*centerY*。 例如，當我們加入兩個按鈕的左邊界的位置上的條件約束時，設計工具產生，涵蓋下列程式碼：

```csharp
View.AddConstraint (NSLayoutConstraint.Create (Button1, NSLayoutAttribute.Left, NSLayoutRelation.Equal, Button2, NSLayoutAttribute.Left, 1, 10));
```

下一節涵蓋使用 「 使用 iOS 設計工具，包括啟用和停用自動版面配置和使用條件約束工具列的條件約束。

## <a name="enable-auto-layout"></a>啟用自動版面配置

預設 iOS 設計工具設定已啟用的條件約束模式。 不過，如果您需要啟用或停用它以手動的方式，您可以使用下列兩個步驟：

1.  按一下設計介面上的空白區域。 這會取消選取任何項目，並啟動分鏡腳本文件的屬性。
1.  選取或取消選取**使用自動版面配置**屬性面板中的核取方塊：

    ![](designer-auto-layout-images/image01.png "使用自動版面配置中的核取 [屬性] 面板")


根據預設，沒有任何條件約束會建立或介面上顯示。 相反地，它們會自動推斷在編譯時期的框架資訊。 若要新增條件約束，我們需要選取設計介面上的項目，並將條件約束加入至它。 我們可以執行的方法使用**限制式工具列**。

## <a name="constraints-toolbar"></a>限制式工具列

 [![](designer-auto-layout-images/toolbarnew.png "內容功能表命令")](designer-auto-layout-images/toolbarnew.png#lightbox)

限制式工具列已更新，且現在包含兩個主要部分：

- **條件約束模式按鈕切換**:先前，您輸入限制式模式，再按一下設計介面上選取的檢視。 您現在應使用此切換按鈕的條件約束列中：

  ![條件約束模式切換](designer-auto-layout-images/constraints.png)

- **中的 < 更新條件約束 」 按鈕：** 請務必請注意，視變更您在編輯模式的條件約束。
  - 條件約束的編輯模式下此按鈕會調整以符合項目範圍的條件約束。
  - 框架編輯模式中這個按鈕會調整以符合的條件約束所定義的位置的項目框架。


## <a name="surface-based-constraint-editing"></a>編輯介面為基礎的條件約束

在前一節中，我們已了解如何加入預設條件約束移除條件約束使用條件約束工具列。 針對多個編輯微調條件約束，我們可以直接在設計介面上的條件約束與互動。 本節將介紹基本概念的編輯介面為基礎的條件約束，包括 pin 間距的控制項、 拖放區和使用不同類型的條件約束。

### <a name="creating-constraints"></a>建立條件約束

IOS Designer 工具提供兩種類型的控制項，以操作設計介面上的項目。 *將控制項拖曳*並*pin 間距控制項*，如下圖所示：

![檢視控制項](designer-auto-layout-images/controls.png)

這些被切換所選取條件約束列中的條件約束的 [模式] 按鈕。

4 的 T 形狀會處理每一端的項目定義*頂端*，*右*，*底部*，以及*左*之項目的邊緣條件約束。 兩個我圖形控點的右側和底部項目定義*高度*並*寬度*條件約束分別。 中間的方形處理兩者*centerX*並*centerY*條件約束。

若要建立條件約束，挑選一個控點並將它拖曳某處的設計介面上。 當您開始拖曳時，一系列的綠色的線條方塊會出現在告訴您什麼介面上您可以限制。 例如，在以下的螢幕擷取畫面，我們要限制中間的按鈕上方：

 [![](designer-auto-layout-images/image07.png "限制中間的按鈕上方")](designer-auto-layout-images/image07.png#lightbox)

在其他兩個按鈕，請注意三個虛線的綠線。 綠色線條表示*拖放區*，或其他項目，我們可以限制的屬性。 在上面的螢幕擷取畫面，其他兩個按鈕會提供 3 個垂直的卸除區域 (*底部*， *centerY*，*頂端*) 來限制我們的按鈕。 綠色檢視頂端的虛線表示檢視控制器提供頂端的檢視中，條件約束和綠色方塊表示將檢視控制器提供最上層的版面配置輔助線以下的條件約束。

> [!IMPORTANT]
> 版面配置輔助線為特殊類型的條件約束的目標，讓我們建立頂端和底部的長條的系統，例如狀態列或工具列存在時納入考量的條件約束。 其中一個主要用途是有相容於 iOS 6 和 iOS 7 之間的應用程式，因為最新版本有狀態列以下延伸的 [容器] 檢視。 如需有關最上層的版面配置輔助線的詳細資訊，請參閱[Apple 文件](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/transitionguide/AppearanceCustomization.html#//apple_ref/doc/uid/TP40013174-CH15-SW2)。



接下來三節導入與不同類型的條件約束的工作。

### <a name="size-constraints"></a>大小條件約束

大小條件約束-*高度*並*寬度*-您有兩個選項。 第一個選項是拖曳控點的鄰近項目大小，以限制，如上述範例所示。 另一個選項是按兩下 建立自我條件約束的控制代碼。 這可讓我們指定常數大小值，如以下螢幕擷取畫面所示：

 [![](designer-auto-layout-images/sizec.png "拖曳控點的鄰近項目大小，以限制，如下所示")](designer-auto-layout-images/sizec.png#lightbox)

### <a name="center-constraints"></a>Center 條件約束

方形控點會建立*centerX*或是*centerY*條件約束，視內容而定。 方形控點拖曳，會亮起其他項目提供這兩個垂直和水平的拖放區，如以下螢幕擷取畫面所示：

 [![](designer-auto-layout-images/centerc.png "Center 條件約束")](designer-auto-layout-images/centerc.png#lightbox)

如果您選擇垂直下拉式區域中， *centerY*將建立條件約束。 如果您選擇的水平的置放區時，會根據回條件約束*centerX*。

### <a name="combinational-constraints"></a>Combinational 的條件約束

若要建立對齊和兩個項目之間的大小相等條件約束，您可以從頂端的工具列指定才能--水平對齊、 垂直對齊和大小等式中，選取項目，如以下螢幕擷取畫面所示：

 [![](designer-auto-layout-images/image06.png "Combinational 的條件約束")](designer-auto-layout-images/image06.png#lightbox)

### <a name="visualizing-and-editing-constraints"></a>視覺化及編輯條件約束

當您新增的條件約束時，它會在設計介面上以藍線時選取的項目：

 [![](designer-auto-layout-images/image09.png "以視覺化方式檢視的條件約束")](designer-auto-layout-images/image09.png#lightbox)

您可以按一下藍色線並編輯的條件約束值，直接在 [屬性] 面板中選取條件約束。 或者，按兩下藍線會顯示 popover，可讓您編輯直接在設計介面上的值：

 [![](designer-auto-layout-images/image08.png "編輯條件約束")](designer-auto-layout-images/image08.png#lightbox)

## <a name="constraint-issues"></a>條件約束的問題

使用條件約束時，可能會發生數種類型的問題：

-  **衝突的條件約束**— 這是因為多個條件約束強制要有衝突的值，屬性的項目，且條件約束引擎無法協調它們。
-  **Underconstrained 項目**— 元素的屬性 （位置和大小） 必須完全涵蓋其設定的條件約束和內建大小為有效的條件約束。 如果這些值都是模稜兩可的即稱為項目，underconstrained。
-  **框架弄丟**— 這發生於項目的畫面格與它那組條件約束定義兩個不同的結果矩形。


本節將詳細說明在上面所列之三個問題，並提供處理方式的詳細資料。

### <a name="conflicting-constraints"></a>衝突的條件約束

衝突的條件約束會標示為紅色，並有警告符號。 將滑鼠停留的警告符號會顯示 popover，衝突的相關資訊：

 [![](designer-auto-layout-images/image11.png "衝突的條件約束警告")](designer-auto-layout-images/image11.png#lightbox)

### <a name="underconstrained-items"></a>Underconstrained 的項目

Underconstrained 的項目會以橙色顯示和觸發程序的檢視控制器物件列中的橘色標記圖示的外觀：

 [![](designer-auto-layout-images/image02.png "以橙色顯示 underconstrained 的項目")](designer-auto-layout-images/image02.png#lightbox)

如果您按一下該標記圖示，場景中取得 underconstrained 項目的相關資訊，並解決問題，所產生的任一種完全加以限制，或藉由移除其條件約束，如以下螢幕擷取畫面所示：

 [![](designer-auto-layout-images/image10.png "修正 Underconstrained 的項目")](designer-auto-layout-images/image10.png#lightbox)

### <a name="frame-misplacement"></a>框架弄丟

框架弄丟 underconstrained 的項目使用相同的色彩代碼。 一律會使用其原生框架，在介面上呈現的項目，但在框架錯置的情況下以紅色矩形會將其中項目將得到應用程式執行時，如以下螢幕擷取畫面所示：

 [![](designer-auto-layout-images/image05.png "範例畫面格弄丟檢視")](designer-auto-layout-images/image05.png#lightbox)

若要解決框架弄丟錯誤，請選取**更新畫面格根據條件約束**從限制式工具列 （最右邊的按鈕） 按鈕：

 [![](designer-auto-layout-images/image03.png "更新限制式工具列按鈕為基礎的框架")](designer-auto-layout-images/image03.png#lightbox)

這會自動調整以符合控制項所定義的位置的項目框架。

<a name="modifying-in-code" />

## <a name="modifying-constraints-in-code"></a>修改程式碼中的條件約束

根據您的應用程式的需求，有可能是當您需要修改程式碼中的條件約束。 例如，調整大小，或重新定位檢視條件約束附加至，若要變更的條件約束的優先順序，或完全停用條件約束。

若要存取程式碼中的條件約束，您必須先將它公開在 iOS 設計工具中執行下列動作：

1. 建立條件約束來當作一般 （使用任何上述方法）。
2. 在 **文件大綱總管**，尋找所需的條件約束，並選取它：

    [![](designer-auto-layout-images/modify01.png "[總管] 中的文件大綱")](designer-auto-layout-images/modify01.png#lightbox)
3. 接下來，指派**名稱**中的條件約束**Widget**索引標籤**屬性總管**:

    [![](designer-auto-layout-images/modify02.png "小工具 索引標籤")](designer-auto-layout-images/modify02.png#lightbox)
4. 儲存您的變更。

使用上述變更之後，您可以存取程式碼中的條件約束，並修改其屬性。 例如，您可以使用下列設定設為零的 [附加] 檢視的高度：

```csharp
ViewInfoHeight.Constant = 0;
```

IOS 設計工具中指定的條件約束的下列設定：

[![](designer-auto-layout-images/modify03.png "編輯屬性總管 中的條件約束")](designer-auto-layout-images/modify03.png#lightbox)

### <a name="the-deferred-layout-pass"></a>延後的版面配置階段

而不是立即更新以回應變更條件約束連接的檢視，排程自動版面配置引擎_延後版面配置階段_在不久的未來。 在此延後的階段，不只是指定的檢視限制式更新，條件約束會重新計算階層中的每個檢視，並將其更新為新的版面配置進行調整。

在任何時間點，您可以排程您自己的延後版面配置階段藉由呼叫`SetNeedsLayout`或`SetNeedsUpdateConstraints`父檢視的方法。 

延遲版面配置階段是由兩個唯一通過檢視階層所組成：

- **更新傳遞**-在此階段中，自動版面配置引擎周遊的檢視階層，並叫用`UpdateViewConstraints`上所有的檢視控制器方法和`UpdateConstraints`所有檢視的方法。
- **在版面配置階段**-同樣地，自動版面配置引擎會周遊檢視階層中，但這次會叫用`ViewWillLayoutSubviews`上所有的檢視控制器方法和`LayoutSubviews`所有檢視的方法。 `LayoutSubviews`方法會更新`Frame`屬性與矩形的每一個子檢視的計算方式是自動版面配置引擎。

### <a name="animating-constraint-changes"></a>建立條件約束變更的動畫

除了修改條件約束的屬性，您可以使用核心動畫來以動畫顯示變更檢視的條件約束。 例如：

```csharp
UIView.BeginAnimations("OpenInfo");
UIView.SetAnimationDuration(1.0f);
ViewInfoHeight.Constant = 237;
View.LayoutIfNeeded();

//Execute Animation
UIView.CommitAnimations();
```

這裡的重點呼叫`LayoutIfNeeded`動畫區塊內的父檢視的方法。 這會告訴繪製動畫的位置或大小變更的每個 「 框架 」 檢視。 而不需要這一行，檢視會直接貼齊的最終版本而不以動畫顯示。

## <a name="summary"></a>總結

本指南會介紹 iOS 自動 （或"adaptive"） 配置和條件約束當做的設計介面上的項目之間的關聯性的數學表示法的概念。 它說明如何在 iOS 設計工具中，使用啟用自動版面配置**限制式工具列**，和編輯個別的設計介面上的條件約束。 接下來，它會說明如何針對三個常見的條件約束問題進行疑難排解。 最後，它會示範如何修改程式碼中的條件約束。

## <a name="related-links"></a>相關連結

- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
- [iOS 設計的控制項逐步解說](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Android 設計工具的概觀](~/android/user-interface/android-designer/index.md)
- [以程式設計方式的條件約束](~/ios/user-interface/programmatic-layout-constraints.md)
- [Apple-自動版面配置輔助線](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/AutolayoutPG/Introduction/Introduction.html#/apple_ref/doc/uid/TP40010853-CH13-SW1)
