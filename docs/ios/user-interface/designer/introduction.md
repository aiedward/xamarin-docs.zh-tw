---
title: iOS 設計工具基本概念
description: 本指南介紹 Xamarin Designer for iOS。 它會示範如何使用 iOS 設計工具，以視覺化方式配置控制項、如何在程式碼中存取這些控制項，以及如何編輯屬性。
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/31/2018
ms.openlocfilehash: 07f3e313a743c9d95f8baf2be7a31f16c6ca4f7e
ms.sourcegitcommit: d1f0e0a9100548cfe0960ed2225b979cc1d7c28f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439478"
---
# <a name="ios-designer-basics"></a>iOS 設計工具基本概念

_本指南介紹 Xamarin Designer for iOS。它會示範如何使用 iOS 設計工具，以視覺化方式配置控制項、如何在程式碼中存取這些控制項，以及如何編輯屬性。_

> [!WARNING]
> IOS 設計工具將開始在 Visual Studio 2019 16.8 版和 Visual Studio 2019 for Mac 8.8 版中推出。
> 建立 iOS 使用者介面的建議方式是直接在執行 Xcode 的 Mac 上執行。 如需詳細資訊，請參閱 [使用 Xcode 設計使用者介面](../storyboards/index.md)。 

Xamarin Designer for iOS 是類似 Xcode 的 Interface Builder 和 Android Designer 的視覺化介面設計工具。 其中一些功能包括與適用于 Windows 和 Mac 的 Visual Studio 的緊密整合、拖放編輯、設定事件處理常式的介面，以及轉譯自訂控制項的能力。

## <a name="requirements"></a>規格需求

IOS 設計工具可在 Windows 上的 Visual Studio for Mac 和 Visual Studio 2017 和更新版本中使用。 在適用于 Windows 的 Visual Studio 中，iOS 設計工具需要連接到已正確設定的 Mac 組建主機，但 Xcode 不需要執行。

本指南假設您已經熟悉 [消費者入門指南](~/ios/get-started/index.md)中涵蓋的內容。

<a name="how-it-works"></a>

## <a name="how-the-ios-designer-works"></a>IOS 設計工具的運作方式

本節說明 iOS 設計工具如何協助建立使用者介面，並將其連接至程式碼。

IOS 設計工具可讓開發人員以視覺化方式設計應用程式的使用者介面。 如分鏡腳本指南的 [簡介](~/ios/user-interface/storyboards/index.md) 所述，分鏡腳本會描述構成應用程式的畫面 (view 控制器) 、) 放置於這些視圖控制器上的介面 (元素，以及應用程式的整體流覽流程。 

視圖控制器有兩個部分： iOS 設計工具中的視覺標記法，以及相關聯的 c # 類別：

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![IOS 設計工具中的視圖控制器](introduction-images/1-storyboardwithviewcontroller-vsmac.png "IOS 設計工具中的視圖控制器")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![視圖控制器的程式碼](introduction-images/2-viewcontrollercode-vsmac.png "視圖控制器的程式碼")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![IOS 設計工具中的視圖控制器](introduction-images/1-storyboardwithviewcontroller-vs.png "IOS 設計工具中的視圖控制器")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![視圖控制器的程式碼](introduction-images/2-viewcontrollercode-vs.png "視圖控制器的程式碼")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

在其預設狀態下，view controller 不提供任何功能;它必須填入控制項。 這些控制項放在 view 控制器的 view 中，也就是包含所有螢幕內容的矩形區域。 大部分的視圖控制器都包含按鈕、標籤和文字欄位等常見的控制項，如下列螢幕擷取畫面所示，顯示包含按鈕的視圖控制器： 

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![包含按鈕的視圖控制器](introduction-images/3-viewcontrollerwithbutton-vsmac.png "包含按鈕的視圖控制器")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![包含按鈕的視圖控制器](introduction-images/3-viewcontrollerwithbutton-vs.png "包含按鈕的視圖控制器")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

某些控制項（例如包含靜態文字的標籤）可以新增至 view controller，並保持獨立。 但是，您必須以程式設計的方式自訂控制項。 例如，上面加入的按鈕應該在點擊時採取動作，因此必須在程式碼中加入事件處理常式。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

為了在程式碼中存取和操作按鈕，它必須有唯一的識別碼。 選取按鈕、開啟 **Properties Pad**，並將其 [ **名稱** ] 欄位設定為值（例如 "SubmitButton"），以提供唯一識別碼：

[![在 Properties Pad 中設定按鈕的名稱](introduction-images/4-settingbuttonname-vsmac.png "在 Properties Pad 中設定按鈕的名稱")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

為了在程式碼中存取和操作按鈕，它必須有唯一的識別碼。 選取按鈕，開啟 [ **屬性] 視窗**，並將其 [ **名稱** ] 欄位設定為值（例如 "SubmitButton"），以提供唯一識別碼：

[![在 [屬性] 視窗中設定按鈕的名稱](introduction-images/4-settingbuttonname-vs.png "在 [屬性] 視窗中設定按鈕的名稱")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

現在按鈕有名稱，可以在程式碼中存取。 但這如何運作？

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在 **Solution Pad** 中，流覽至 **ViewController.cs** ，然後按一下洩漏指標，顯示 view 控制器的 `ViewController` 類別定義橫跨兩個檔案，其中每個檔案都包含 [部分類別](/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) 定義：

[![組成 ViewController 類別的兩個檔案： ViewController.cs 和 ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "組成 ViewController 類別的兩個檔案： ViewController.cs 和 ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在 **方案總管** 中，流覽至 **ViewController.cs** ，然後按一下洩漏指標，顯示 view 控制器的 `ViewController` 類別定義橫跨兩個檔案，其中每個檔案都包含 [部分類別](/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) 定義：

[![組成 ViewController 類別的兩個檔案： ViewController.cs 和 ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "組成 ViewController 類別的兩個檔案： ViewController.cs 和 ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- **ViewController.cs** 應該填入與類別相關的自訂程式碼 `ViewController` 。 在此檔案中， `ViewController` 類別可以回應各種 iOS view controller 生命週期方法、自訂 UI，以及回應使用者輸入，例如按鈕點擊。

- **ViewController.designer.cs** 是產生的檔案，由 iOS 設計工具建立，以將以視覺化方式呈現的介面對應至程式碼。 因為對此檔案所做的變更將會遭到覆寫，所以不應該修改。 此檔案中的屬性宣告讓類別中的程式碼 `ViewController` 能夠存取在 IOS 設計工具中設定的控制項（依 **名稱**）。 開啟 **ViewController.designer.cs** 會顯示下列程式碼：

```csharp
namespace Designer
{
    [Register ("ViewController")]
    partial class ViewController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        UIKit.UIButton SubmitButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (SubmitButton != null) {
                SubmitButton.Dispose ();
                SubmitButton = null;
            }
        }
    }
}
```

`SubmitButton`屬性宣告會將整個類別（ `ViewController` 不只是 **ViewController.designer.cs** 檔）連接到分鏡腳本中定義的按鈕。 由於 **ViewController.cs** 會定義類別的一部分 `ViewController` ，因此它可以存取 `SubmitButton` 。

下列螢幕擷取畫面說明 IntelliSense 現在會辨識 `SubmitButton` **ViewController.cs** 中的參考：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![識別 SubmitButton 參考的 IntelliSense](introduction-images/6-submitbuttonintellisense-vsmac.png "識別 SubmitButton 參考的 IntelliSense")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![識別 SubmitButton 參考的 IntelliSense](introduction-images/6-submitbuttonintellisense-vs.png "識別 SubmitButton 參考的 IntelliSense")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

本節示範如何在 iOS 設計工具中建立按鈕，以及如何在程式碼中存取該按鈕。

本檔的其餘部分提供 iOS 設計工具的進一步總覽。

## <a name="ios-designer-basics"></a>iOS 設計工具基本概念

本節介紹 iOS 設計工具的各個部分，並提供其功能的導覽。

### <a name="launching-the-ios-designer"></a>啟動 iOS 設計工具

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

使用 Visual Studio for Mac 建立的 Xamarin. iOS 專案包含分鏡腳本。 若要查看分鏡腳本的內容，請按兩下 **Solution Pad** 中的分鏡腳本檔案：

[![在 iOS 設計工具中開啟的分鏡腳本](introduction-images/7-storyboardopen-vsmac.png "在 iOS 設計工具中開啟的分鏡腳本")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

大部分使用 Visual Studio 建立的 Xamarin. iOS 專案都包含分鏡腳本。 若要查看分鏡腳本的內容，請按兩下 **方案總管** 中的分鏡腳本檔案：

[![在 iOS 設計工具中開啟的分鏡腳本](introduction-images/7-storyboardopen-vs.png "在 iOS 設計工具中開啟的分鏡腳本")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"></a>

### <a name="ios-designer-features"></a>iOS 設計工具功能

IOS 設計工具有六個主要區段：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![IOS 設計工具的區段](introduction-images/8-sixpartsofiosdesigner-vsmac.png "IOS 設計工具的區段")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **Design Surface** – iOS 設計工具的主要工作區。 顯示在檔區域中，可讓使用者介面的視覺化結構。
2. **條件約束工具列** –可讓您在畫面格編輯模式和條件約束編輯模式之間切換，這兩種不同的方式可在使用者介面中定位元素。
3. **工具箱** –列出可拖曳至設計介面並新增至使用者介面的控制器、物件、控制項、資料檢視、手勢辨識器、視窗和橫條。
4. **Properties Pad** –顯示所選控制項的屬性，包括身分識別、視覺樣式、協助工具、配置和行為。
5. [**檔大綱**]-顯示為編輯的介面撰寫版面配置的控制項樹狀結構。 按一下樹狀結構中的專案，會在 iOS 設計工具中選取專案，並在  **Properties Pad** 中顯示其屬性。 這對於在深度嵌套的使用者介面中選取特定的控制項很方便。
6. **底部工具列** ：包含變更 IOS 設計工具如何顯示腳本或 xib 檔案的選項，包括裝置、方向和縮放。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![IOS 設計工具的區段](introduction-images/8-sixpartsofiosdesigner-vs.png "IOS 設計工具的區段")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **Design Surface** – iOS 設計工具的主要工作區。 顯示在檔區域中，可讓使用者介面的視覺化結構。
2. **條件約束工具列** –可讓您在畫面格編輯模式和條件約束編輯模式之間切換，這兩種不同的方式可在使用者介面中定位元素。
3. **工具箱** –列出可拖曳至設計介面並新增至使用者介面的控制器、物件、控制項、資料檢視、手勢辨識器、視窗和橫條。
4. [**屬性] 視窗**-顯示所選控制項的屬性，包括身分識別、視覺樣式、協助工具、配置和行為。
5. [**檔大綱**]-顯示為編輯的介面撰寫版面配置的控制項樹狀結構。 按一下樹狀結構中的專案，會在 iOS 設計工具中選取專案，並在 [  **屬性] 視窗** 中顯示其屬性。 這對於在深度嵌套的使用者介面中選取特定的控制項很方便。
6. **底部工具列** ：包含變更 IOS 設計工具如何顯示腳本或 xib 檔案的選項，包括裝置、方向和縮放。

-----

### <a name="design-workflow"></a>設計工作流程

#### <a name="adding-a-control-to-the-interface"></a>將控制項新增至介面

若要將控制項加入至介面，請將它從 [ **工具箱** ] 拖曳到設計介面上。 當您新增或放置控制項時，垂直和水準的指導方針會醒目提示常用的版面配置位置，例如垂直置中、水準中心和邊界：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![在設計介面上，指導方針會醒目提示一般使用的版面配置位置](introduction-images/9-layoutguides-vsmac.png "在設計介面上，指導方針會醒目提示一般使用的版面配置位置")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![在設計介面上，指導方針會醒目提示一般使用的版面配置位置](introduction-images/9-layoutguides-vs.png "在設計介面上，指導方針會醒目提示一般使用的版面配置位置")

-----

上述範例中的藍色虛線提供水準中心視覺對齊指導方針，可協助您進行按鈕放置。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

#### <a name="context-menu-commands"></a>內容功能表命令

您可以在設計介面和 **檔大綱** 中使用內容功能表。 此功能表提供所選控制項及其父系的命令，這在使用嵌套階層中的視圖時很有説明：

[![設計介面上的內容功能表](introduction-images/10-contextmenudesignsurface-vsmac.png "設計介面上的內容功能表")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

-----

### <a name="constraints-toolbar"></a>條件約束工具列

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![條件約束工具列](introduction-images/11-constraintstoolbar-vsmac.png "條件約束工具列")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![條件約束工具列](introduction-images/11-constraintstoolbar-vs.png "條件約束工具列")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

條件約束工具列已更新，現在包含兩個控制項：畫面格編輯模式/條件約束編輯模式切換和更新條件約束/更新框架按鈕。

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>畫面格編輯模式/條件約束編輯模式切換

在舊版的 iOS 設計工具中，按一下設計介面上已選取的視圖，在畫面格編輯模式與條件約束編輯模式之間切換。 現在，[條件約束] 工具列中的切換控制項會在這些編輯模式之間切換。

- 畫面格編輯模式：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![畫面格編輯模式按鈕](introduction-images/12a-frameeditingmode-vsmac.png "畫面格編輯模式按鈕")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![畫面格編輯模式按鈕](introduction-images/12a-frameeditingmode-vs.png "畫面格編輯模式按鈕")

-----

- 條件約束編輯模式：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![條件約束編輯模式按鈕](introduction-images/12b-constrainteditingmode-vsmac.png "條件約束編輯模式按鈕")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![條件約束編輯模式按鈕](introduction-images/12b-constrainteditingmode-vs.png "條件約束編輯模式按鈕")

-----

#### <a name="update-constraints--update-frames-button"></a>更新條件約束/更新畫面格按鈕

[更新條件約束/更新框架] 按鈕位於 [畫面格編輯模式]/[條件約束編輯模式] 切換的右邊。

- 在 [畫面格編輯] 模式中，按一下這個按鈕會調整任何選取專案的框架，以符合其條件約束。
- 在條件約束編輯模式中，按一下這個按鈕會調整任何選取專案的條件約束，使其符合其框架。

### <a name="bottom-toolbar"></a>底部工具列

底部工具列提供在 iOS 設計工具中選取用來查看腳本或 xib 檔案的裝置、方向和縮放的方法：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![用來選取設計介面之裝置和方向的底部工具列](introduction-images/13-bottomtoolbar-vsmac.png "用來選取設計介面之裝置和方向的底部工具列")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![用來選取設計介面之裝置和方向的底部工具列](introduction-images/13-bottomtoolbar-vs.png "用來選取設計介面之裝置和方向的底部工具列")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>裝置和方向

展開時，底部工具列會顯示適用于目前檔的所有裝置、方向和/或採用原音。 按一下它們會變更設計介面上顯示的視圖。 

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![底部工具列，展開以顯示裝置和方向](introduction-images/14-bottomtoolbarexpanded-vsmac.png "底部工具列，展開以顯示裝置和方向")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![底部工具列，展開以顯示裝置和方向](introduction-images/14-bottomtoolbarexpanded-vs.png "底部工具列，展開以顯示裝置和方向")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

請注意，選取裝置和方向只會變更 iOS 設計工具預覽設計的方式。 無論目前的選取範圍為何，除非已使用 [ **編輯特性** ] 按鈕來指定，否則新加入的條件約束會套用到所有裝置和方向。

當 [大小類別](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes)[啟用](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes)時，[**編輯特性**] 按鈕會出現在展開的底部工具列中。  按一下 [ **編輯特性** ] 按鈕會顯示根據所選裝置和方向所代表的大小類別來建立介面變化的選項。 請考慮以下範例：

- 如果選取 **iPhone SE** 直向  /  ****，popover 會提供選項來建立 compact width、regular height 大小類別的介面變化。 
- 如果已選取 **iPad Pro 9.7** 的  /  **Landscape**  /  **全螢幕畫面**，popover 會提供選項來建立一般寬度、一般高度大小類別的介面變化。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![用來依大小類別改變介面的底部工具列](introduction-images/15-edittraitsbutton-vsmac.png "用來依大小類別改變介面的底部工具列")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![用來依大小類別改變介面的底部工具列](introduction-images/15-edittraitsbutton-vs.png "用來依大小類別改變介面的底部工具列")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>縮放控制項

設計介面支援透過數個控制項進行縮放：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![底部工具列中的縮放控制項](introduction-images/16-zoomcontrols-vsmac.png "底部工具列中的縮放控制項")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![底部工具列中的縮放控制項](introduction-images/16-zoomcontrols-vs.png "底部工具列中的縮放控制項")

-----

這些控制項包括下列各項：

1. 縮放至適當比例
2. 縮小
3. 放大
4. 實際大小 (1:1 圖元大小) 

這些控制項會調整設計介面上的縮放比例。 它們在執行時間不會影響應用程式的使用者介面。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

### <a name="properties-pad"></a>Properties Pad

使用 **Properties Pad** 來編輯控制項的身分識別、視覺樣式、協助工具和行為。 下列螢幕擷取畫面說明按鈕的 **Properties Pad** 選項：

[![按鈕的 Properties Pad](introduction-images/17-buttonpropertiespad-vsmac.png "按鈕的 Properties Pad")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>Properties Pad 區段

**Properties Pad** 包含三個區段：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

### <a name="properties-window"></a>屬性視窗

您可以使用 [ **屬性] 視窗** 來編輯控制項的身分識別、視覺樣式、協助工具和行為。 下列螢幕擷取畫面說明按鈕的 [ **屬性] 視窗** 選項：

[![按鈕的 [屬性] 視窗](introduction-images/17-buttonpropertieswindow-vs.png "按鈕的 [屬性] 視窗")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>屬性視窗區段

[ **屬性] 視窗** 包含三個區段：

-----

1. **Widget** –控制項的主要屬性，例如名稱、類別、樣式屬性等。管理控制項內容的屬性通常會放在這裡。
2. **版面** 配置–追蹤控制項位置和大小的屬性，包括條件約束和框架，如下所示。
3. **事件** –在此指定事件和事件處理常式。 適用于處理觸控、攻點、拖曳等事件。您也可以直接在程式碼中處理事件。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

#### <a name="editing-properties-in-the-properties-pad"></a>編輯 Properties Pad 中的屬性

除了在設計介面上的視覺化編輯之外，iOS 設計工具還支援編輯 **Properties Pad** 中的屬性。 可用的屬性會根據選取的控制項進行變更，如下列螢幕擷取畫面所示：

[![按鈕屬性](introduction-images/18a-buttonpropertiespad-vsmac.png "按鈕屬性")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![查看控制器屬性](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "查看控制器屬性")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

#### <a name="editing-properties-in-the-properties-window"></a>在 [屬性] 視窗中編輯屬性

除了在設計介面上的視覺化編輯之外，iOS 設計工具還支援編輯 [ **屬性] 視窗** 中的屬性。 可用的屬性會根據選取的控制項進行變更，如下列螢幕擷取畫面所示：

[![按鈕屬性](introduction-images/18a-buttonpropertieswindow-vs.png "按鈕屬性")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![查看控制器屬性](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "查看控制器屬性")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> Properties Pad 的 [識別] 區段現在會顯示 **模組** 欄位。 只有在與 Swift 類別交互操作時，才需要填入此區段。 您可以使用它來輸入 Swift 類別的模組名稱，也就是命名空間。

#### <a name="default-values"></a>預設值

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

**Properties Pad** 中的許多屬性都不會顯示任何值或預設值。 不過，應用程式的程式碼仍可能會修改這些值。 **Properties Pad** 不會顯示在程式碼中設定的值。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[ **屬性] 視窗** 中的許多屬性都不會顯示任何值或預設值。 不過，應用程式的程式碼仍可能會修改這些值。 [ **屬性] 視窗** 不會顯示在程式碼中設定的值。

-----

#### <a name="event-handlers"></a>事件處理常式

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要指定各種事件的自訂事件處理常式，請使用 **Properties Pad** 的 [**事件**] 索引標籤。 例如，在下列螢幕擷取畫面中， `HandleClick` 方法會處理按鈕在事件 **內的觸控** ：

[![Properties Pad，其中包含針對按鈕設定的事件處理常式](introduction-images/19-buttonpropertiespadevents-vsmac.png "Properties Pad，其中包含針對按鈕設定的事件處理常式")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要指定各種事件的自訂事件處理常式，請使用 [**屬性] 視窗** 的 [**事件**] 索引標籤。 例如，在下列螢幕擷取畫面中， `HandleClick` 方法會處理按鈕在事件 **內的觸控** ：

[![[屬性] 視窗，其中包含針對按鈕設定的事件處理常式](introduction-images/19-buttonpropertieswindowevents-vs.png "[屬性] 視窗，其中包含針對按鈕設定的事件處理常式")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

一旦指定了事件處理常式，就必須將相同名稱的方法加入至對應的 view controller 類別。 否則， `unrecognized selector` 當您按下按鈕時，就會發生例外狀況：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![無法辨識的選取器例外狀況](introduction-images/20-unrecognizedselector-vsmac.png "無法辨識的選取器例外狀況")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

請注意，在 **Properties Pad** 中指定事件處理常式之後，iOS 設計工具會立即開啟對應的程式碼檔案，並提供插入方法宣告的功能。 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![無法辨識的選取器例外狀況](introduction-images/20-unrecognizedselector-vs.png "無法辨識的選取器例外狀況")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

如需使用自訂事件處理常式的範例，請參閱 [Hello，iOS 消費者入門指南](~/ios/get-started/hello-ios/index.md)。

### <a name="outline-view"></a>大綱檢視

IOS 設計工具也可以將介面的控制項階層顯示為大綱。 選取 [ **檔大綱** ] 索引標籤即可取得大綱，如下所示：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![檔大綱](introduction-images/21-buttonoutlineview-vsmac.png "檔大綱")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![檔大綱](introduction-images/21-buttonoutlineview-vs.png "檔大綱")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

大綱視圖中選取的控制項會與設計介面上選取的控制項保持同步。  這項功能適用于從深度嵌套介面階層中選取專案。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="revert-to-xcode"></a>還原為 Xcode

您可以使用 iOS 設計工具和 Xcode Interface Builder 交換。 若要在 Xcode Interface Builder 中開啟分鏡腳本或 xib 檔案，請以滑鼠右鍵按一下檔案，然後選取 [ **開啟方式] > Xcode Interface Builder**，如下列螢幕擷取畫面所示：

[![在 Xcode 中開啟分鏡腳本 Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "在 Xcode 中開啟分鏡腳本 Interface Builder")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

在 Xcode Interface Builder 中進行編輯之後，請儲存檔案，並返回 Visual Studio for Mac。 這些變更將會同步處理至 Xamarin. iOS 專案。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="revert-to-xcode"></a>還原為 Xcode

您可以使用 iOS 設計工具和 Xcode Interface Builder 交換，不過 Xcode Interface Builder 只能在 Mac 上使用。 若要在 Mac 上開啟 Xcode Interface Builder 中的分鏡腳本或 xib 檔案，請在 [Visual Studio for Mac](/visualstudio/mac/)中開啟包含 Xamarin 專案的方案，以滑鼠右鍵按一下檔案，然後選取 [ **開啟方式 > Xcode Interface Builder**，如下列螢幕擷取畫面所示：

[![在 Xcode 中開啟分鏡腳本 Interface Builder](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "在 Xcode 中開啟分鏡腳本 Interface Builder")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

在 Xcode Interface Builder 中進行編輯之後，請儲存檔案，並返回 Visual Studio for Mac。 這些變更將會同步處理至 Xamarin. iOS 專案。

-----

## <a name="xib-support"></a>。 xib 支援人員

IOS 設計工具支援 xib 檔案的建立、編輯和管理。 這些是 respresent 單一自訂視圖的 XML 檔案，您可以將其加入至應用程式的 view 階層。 Xib 檔案通常代表應用程式中單一視圖或畫面的介面，而分鏡腳本則代表許多畫面和它們之間的轉換。

有許多關於 xib 檔案、分鏡腳本或程式碼的意見，最適合用來建立和維護使用者介面。 事實上，並沒有完美的解決方案，而且一定要考慮適合工作的最佳工具。 話雖如此，當使用 xib 檔案表示應用程式中多個位置所需的自訂視圖時（例如自訂資料表視圖資料格），通常最強大。 

如需有關使用 xib 檔案的詳細檔，請參閱下列食譜：

- [使用 xib 範本](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/using_the_ios_view_xib_template)
- [使用. xib 建立自訂 TableViewCell](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/tables/custom-tableviewcell)
- [使用 xib 建立啟動畫面](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)

如需有關使用分鏡腳本的詳細資訊，請參閱分鏡腳本 [簡介](~/ios/user-interface/storyboards/index.md)。

這和其他 iOS 設計工具相關的指南會說明如何使用分鏡腳本做為建立使用者介面的標準方法，因為大部分的 Xamarin 預設都提供分鏡腳本。

## <a name="summary"></a>摘要

本指南提供 iOS 設計工具的簡介，說明其功能，並概述它為設計美觀的使用者介面所提供的工具。

## <a name="related-links"></a>相關連結

- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
- [iOS 設計的控制項逐步解說](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Hello, iOS 多重畫面](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Android Designer 總覽](~/android/user-interface/android-designer/index.md)
- [部分類別和方法](/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [深入瞭解 Xamarin Designer for iOS-演進 2014 (影片) ](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [使用 iOS 設計工具建立啟動畫面 (影片) ](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
