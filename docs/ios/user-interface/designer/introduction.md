---
title: iOS 設計工具的基本概念
description: 本指南介紹 ios Xamarin 設計工具。 它會示範如何使用 iOS 設計工具以視覺化方式配置控制項、 如何存取這些控制項中的程式碼，以及如何編輯內容。
ms.prod: xamarin
ms.assetid: E7045E41-0DEF-416B-BCDB-52502350F61C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/31/2018
ms.openlocfilehash: 7e36a402619813214e821f3060e053d76c99cfb7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30784254"
---
# <a name="ios-designer-basics"></a>iOS 設計工具的基本概念

_本指南介紹 ios Xamarin 設計工具。它會示範如何使用 iOS 設計工具以視覺化方式配置控制項、 如何存取這些控制項中的程式碼，以及如何編輯內容。_

適用於 iOS Xamarin 設計工具是類似於 Xcode 的介面產生器的視覺介面設計工具] 和 [Android 設計工具。 其許多功能包括與 Visual Studio for Mac 和 Visual Studio 2015 和 2017年、 拖放編輯、 設定事件處理常式、 介面和呈現的自訂控制項的能力緊密整合。

## <a name="requirements"></a>需求

IOS 設計工具是在 Visual Studio for Mac 並在 Visual Studio 2015 和 2017年可用在 Windows 上。 在 Visual Studio 2015 或 2017年，iOS 設計工具需要的連接已正確設定的 Mac 組建主機，雖然 Xcode 不需要在執行。

本指南假設熟悉內容涵蓋[快速入門引導](~/ios/get-started/index.md)。

<a name="how-it-works" />

## <a name="how-the-ios-designer-works"></a>IOS 設計工具的運作方式

本章節描述 iOS 設計工具可協助建立使用者介面並將其連接至程式碼。

IOS 設計工具可讓開發人員以視覺化方式設計應用程式的使用者介面。 中所述[簡介分鏡腳本](~/ios/user-interface/storyboards/index.md)指南中，分鏡腳本描述畫面 （檢視控制站） 構成應用程式中，放在這些檢視控制站以及應用程式的整體導覽流程的介面項目 （檢視）. 

檢視控制器有兩個部分： 在 iOS 設計工具中的視覺表示法和相關聯的 C# 類別：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![在 iOS 設計工具中的檢視控制器](introduction-images/1-storyboardwithviewcontroller-vsmac.png "iOS 設計工具中的檢視控制器")](introduction-images/1-storyboardwithviewcontroller-vsmac-large.png#lightbox)

[![檢視控制器的程式碼](introduction-images/2-viewcontrollercode-vsmac.png "檢視控制器的程式碼")](introduction-images/2-viewcontrollercode-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![在 iOS 設計工具中的檢視控制器](introduction-images/1-storyboardwithviewcontroller-vs.png "iOS 設計工具中的檢視控制器")](introduction-images/1-storyboardwithviewcontroller-vs-large.png#lightbox)

[![檢視控制器的程式碼](introduction-images/2-viewcontrollercode-vs.png "檢視控制器的程式碼")](introduction-images/2-viewcontrollercode-vs-large.png#lightbox)

-----

在預設狀態，檢視控制器未提供任何功能。它必須填入控制項。 這些控制項放置在檢視控制站的檢視中，包含所有螢幕的內容矩形區域。 大部分檢視控制器包含常見的控制項，例如按鈕、 標籤和文字欄位，如下列螢幕擷取畫面，顯示包含按鈕檢視控制器所示： 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![包含按鈕檢視控制器](introduction-images/3-viewcontrollerwithbutton-vsmac.png "包含按鈕的檢視控制站")](introduction-images/3-viewcontrollerwithbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![包含按鈕檢視控制器](introduction-images/3-viewcontrollerwithbutton-vs.png "包含按鈕的檢視控制站")](introduction-images/3-viewcontrollerwithbutton-vs-large.png#lightbox)

-----

可以加入至檢視控制器及單獨留一些控制項，例如包含靜態文字標籤。 不過，常常會，控制項必須進行自訂以程式設計的方式。 比方說，上述新增的按鈕應該執行一些動作時點選，因此必須在程式碼加入事件處理常式。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要存取及管理程式碼中的按鈕，它必須有唯一的識別項。 藉由選取按鈕，開啟提供的唯一識別碼**屬性板**，並設定其**名稱**欄位設為值，例如 「 交付按鈕 」:

[![設定按鈕的名稱屬性板](introduction-images/4-settingbuttonname-vsmac.png "屬性輸入板中設定按鈕的名稱")](introduction-images/4-settingbuttonname-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要存取及管理程式碼中的按鈕，它必須有唯一的識別項。 藉由選取按鈕，開啟提供的唯一識別碼**屬性 視窗**，並設定其**名稱**欄位設為值，例如 「 交付按鈕 」:

[![在 [屬性] 視窗中設定按鈕的名稱](introduction-images/4-settingbuttonname-vs.png "設定 [屬性] 視窗中的按鈕名稱")](introduction-images/4-settingbuttonname-vs-large.png#lightbox)

-----

既然按鈕具有的名稱，它可以在程式碼中存取。 但此運作方式？

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在**方案板**、 瀏覽至**ViewController.cs**按一下洩漏標記上會顯示，並檢視控制站的`ViewController`類別定義跨兩個檔案，其中每個包含[部分類別](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)定義：

[![兩個檔案 ViewController 類別構成： ViewController.cs 和 ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vsmac.png "兩個檔案 ViewController 類別構成： ViewController.cs 和 ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在**方案總管 中**、 瀏覽至**ViewController.cs**按一下洩漏標記上會顯示，並檢視控制站的`ViewController`類別定義跨越兩個檔案，每個其中包含[部分類別](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)定義：

[![兩個檔案 ViewController 類別構成： ViewController.cs 和 ViewController.designer.cs](introduction-images/5-twoviewcontrollerfiles-vs.png "兩個檔案 ViewController 類別構成： ViewController.cs 和 ViewController.designer.cs")](introduction-images/5-twoviewcontrollerfiles-vs-large.png#lightbox)

-----

- **ViewController.cs**應該填入相關的自訂程式碼`ViewController`類別。 在此檔案中，`ViewController`類別可以回應各種 iOS 檢視控制器存留週期方法，自訂 UI，並回應使用者輸入，例如按鈕點選。

- **ViewController.designer.cs**是 iOS 設計工具以視覺化方式建構介面對應的程式碼所建立的產生的檔案。 變更這個檔案將覆寫，因為您不應該進行修改。 此檔案中的屬性宣告可讓程式碼中`ViewController`由類別來存取，**名稱**，向上控制組 iOS 設計工具中。 開啟**ViewController.designer.cs**會顯示下列程式碼：

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

`SubmitButton`屬性宣告連接在整個`ViewController`類別-不要只**ViewController.designer.cs**檔案 – 至分鏡腳本中定義的按鈕。 因為**ViewController.cs**定義的一部分`ViewController`類別，其可存取`SubmitButton`。

下列螢幕擷取畫面說明 IntelliSense 現在辨識`SubmitButton`中參考**ViewController.cs**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![IntelliSense 可辨識的交付按鈕參考](introduction-images/6-submitbuttonintellisense-vsmac.png "辨識交付按鈕參考的 IntelliSense")](introduction-images/6-submitbuttonintellisense-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![IntelliSense 可辨識的交付按鈕參考](introduction-images/6-submitbuttonintellisense-vs.png "辨識交付按鈕參考的 IntelliSense")](introduction-images/6-submitbuttonintellisense-vs-large.png#lightbox)

-----

本節示範如何建立 iOS 設計工具中的按鈕，並存取程式碼中的按鈕。

這份文件的其餘部分提供進一步 iOS 設計工具的概觀。

## <a name="ios-designer-basics"></a>iOS 設計工具的基本概念

本節介紹 iOS 設計工具的組件，並提供其功能的導覽。

### <a name="launching-the-ios-designer"></a>啟動 iOS 設計工具

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

使用 Visual Studio 建立適用於 Mac 的 Xamarin.iOS 專案包含分鏡腳本。 若要檢視分鏡腳本的內容，請按兩下.storyboard 檔案中的**方案板**:

[![在 iOS 設計工具中開啟分鏡腳本](introduction-images/7-storyboardopen-vsmac.png "iOS 設計工具中開啟分鏡腳本")](introduction-images/7-storyboardopen-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

使用 Visual Studio 2015 或 2017年建立的大多數 Xamarin.iOS 專案都包含分鏡腳本。 若要檢視分鏡腳本的內容，請按兩下.storyboard 檔案中的**方案總管 中**:

[![在 iOS 設計工具中開啟分鏡腳本](introduction-images/7-storyboardopen-vs.png "iOS 設計工具中開啟分鏡腳本")](introduction-images/7-storyboardopen-vs-large.png#lightbox)

-----

<a name="iOS_Designer_features"/>

### <a name="ios-designer-features"></a>iOS 設計工具功能

IOS 設計工具會有六個主要區段：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![IOS 設計工具的區段](introduction-images/8-sixpartsofiosdesigner-vsmac.png "iOS 設計工具的區段")](introduction-images/8-sixpartsofiosdesigner-vsmac-large.png#lightbox)

1. **設計介面**– iOS 設計工具的主要工作區。 文件區域中所示，它可讓使用者介面的視覺方式建構。
2. **條件約束工具列**– 可讓使用者介面中定位項目編輯模式和條件約束編輯模式，兩種不同方式的畫面格之間切換。
3. **工具箱**– 列出控制站、 物件、 控制項、 資料檢視、 筆勢辨識器、 windows 和長條圖中，可以加入至使用者介面並拖曳至設計介面。
4. **屬性板**– 顯示所選的控制項，包括身分識別、 視覺化樣式，協助工具、 配置和行為的屬性。
5. **文件大綱**– 顯示構成正在編輯之介面的版面配置控制項的樹狀結構。 按一下樹狀目錄中的項目選取 iOS 設計工具中，並顯示其屬性**屬性板**。 這是很方便用來選取特定的控制項，深度巢狀使用者介面中。
6. **底部工具列**– 包含用來改變 iOS 設計工具顯示.storyboard 或.xib 檔案，包括裝置、 方向與縮放選項。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![IOS 設計工具的區段](introduction-images/8-sixpartsofiosdesigner-vs.png "iOS 設計工具的區段")](introduction-images/8-sixpartsofiosdesigner-vs-large.png#lightbox)

1. **設計介面**– iOS 設計工具的主要工作區。 文件區域中所示，它可讓使用者介面的視覺方式建構。
2. **條件約束工具列**– 可讓使用者介面中定位項目編輯模式和條件約束編輯模式，兩種不同方式的畫面格之間切換。
3. **工具箱**– 列出控制站、 物件、 控制項、 資料檢視、 筆勢辨識器、 windows 和長條圖中，可以加入至使用者介面並拖曳至設計介面。
4. **屬性視窗**– 顯示所選的控制項，包括身分識別、 視覺化樣式，協助工具、 配置和行為的屬性。
5. **文件大綱**– 顯示構成正在編輯之介面的版面配置控制項的樹狀結構。 按一下樹狀目錄中的項目選取 iOS 設計工具中，並顯示其屬性**屬性 視窗**。 這是很方便用來選取特定的控制項，深度巢狀使用者介面中。
6. **底部工具列**– 包含用來改變 iOS 設計工具顯示.storyboard 或.xib 檔案，包括裝置、 方向與縮放選項。

-----

### <a name="design-workflow"></a>設計工作流程

#### <a name="adding-a-control-to-the-interface"></a>將控制項加入至介面

若要將控制項加入至介面，將它從拖曳**工具箱**並將它放在設計介面上。 當新增或定位控制項、 垂直和水平的指導方針反白顯示常用的配置位置，例如垂直置中、 水平置中和邊界：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
 
![在設計介面上的指導方針反白顯示常用的配置位置](introduction-images/9-layoutguides-vsmac.png "設計介面上，指導方針反白顯示常用的配置位置")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![在設計介面上的指導方針反白顯示常用的配置位置](introduction-images/9-layoutguides-vs.png "設計介面上，指導方針反白顯示常用的配置位置")

-----

上述範例中的藍色虛線提供水平置中對齊視覺指導方針來協助按鈕位置。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

#### <a name="context-menu-commands"></a>內容功能表命令

內容功能表會在設計介面上和**文件大綱**。 此功能表提供的命令所選的控制項和其父代處理的巢狀階層中的檢視時，會很有幫助：

[![在設計介面上的內容功能表](introduction-images/10-contextmenudesignsurface-vsmac.png "設計介面上的內容功能表")](introduction-images/10-contextmenudesignsurface-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-----

### <a name="constraints-toolbar"></a>條件約束工具列

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
 
[![條件約束工具列](introduction-images/11-constraintstoolbar-vsmac.png "條件約束工具列")](introduction-images/11-constraintstoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![條件約束工具列](introduction-images/11-constraintstoolbar-vs.png "條件約束工具列")](introduction-images/11-constraintstoolbar-vs-large.png#lightbox)

-----

條件約束工具列已更新，現在包含兩個控制項： 編輯模式的框架 / 條件約束的編輯模式切換和更新條件約束 / 更新畫面格按鈕。

#### <a name="frame-editing-mode--constraint-editing-mode-toggle"></a>編輯模式的畫面格 / 條件約束的編輯模式切換

在舊版的 ios 設計工具中，按一下 已選取的檢視，在設計介面上編輯模式和條件約束的編輯模式的畫面格之間切換。 現在，條件約束工具列中的切換控制項切換這些編輯模式。

- 畫面格的編輯模式：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![編輯模式按鈕框架](introduction-images/12a-frameeditingmode-vsmac.png "框架編輯模式按鈕")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![編輯模式按鈕框架](introduction-images/12a-frameeditingmode-vs.png "框架編輯模式按鈕")

-----

- 條件約束的編輯模式：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![編輯模式 按鈕的條件約束](introduction-images/12b-constrainteditingmode-vsmac.png "條件約束的編輯模式按鈕")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![編輯模式 按鈕的條件約束](introduction-images/12b-constrainteditingmode-vs.png "條件約束的編輯模式按鈕")

-----

#### <a name="update-constraints--update-frames-button"></a>更新條件約束/更新畫面格按鈕

更新條件約束 / 更新畫面格按鈕位於右邊的編輯模式的框架 / 條件約束的編輯模式切換。

- 在框架中編輯模式，按一下此按鈕，將會調整任何選取的項目，以符合其條件約束的框架。
- 在條件約束的編輯模式下，按一下此按鈕，將會調整任何選取的項目，以符合其框架的條件約束。

### <a name="bottom-toolbar"></a>底部工具列

底部工具列會提供選取裝置、 方向與用來檢視分鏡腳本或.xib 檔案 iOS 設計工具中的縮放方式：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![底部工具列中用來選取裝置，以及在設計介面的方向](introduction-images/13-bottomtoolbar-vsmac.png "底部工具列中用來選取裝置，以及在設計介面的方向")](introduction-images/13-bottomtoolbar-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![底部工具列中用來選取裝置，以及在設計介面的方向](introduction-images/13-bottomtoolbar-vs.png "底部工具列中用來選取裝置，以及在設計介面的方向")](introduction-images/13-bottomtoolbar-vs-large.png#lightbox)

-----

#### <a name="device-and-orientation"></a>裝置和方向

展開時，底部工具列會顯示所有裝置、 方向，及/或地適用於目前文件。 按一下這些變更設計介面上顯示的檢視。 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![底部工具列中，展開並顯示裝置與方向](introduction-images/14-bottomtoolbarexpanded-vsmac.png "底部工具列中，展開，以顯示裝置與方向")](introduction-images/14-bottomtoolbarexpanded-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![底部工具列中，展開並顯示裝置與方向](introduction-images/14-bottomtoolbarexpanded-vs.png "底部工具列中，展開，以顯示裝置與方向")](introduction-images/14-bottomtoolbarexpanded-vs-large.png#lightbox)

-----

請注意，選取 裝置和方向變更只 iOS 設計工具預覽的設計方式。 不論目前的選取範圍，新加入的所有裝置和方向會都套用條件約束，除非**編輯特性**按鈕已用來指定，否則。

當[大小類別](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes)是[啟用](~/ios/user-interface/storyboards/unified-storyboards.md#enabling-size-classes)、**編輯特性**按鈕會出現在展開的底部工具列。  按一下**編輯特性**按鈕會顯示建立根據所選取的裝置方向表示的大小類別介面變化的選項。 請參考下列範例：

- 如果**iPhone SE** / **縱向**，已選取，popover 會提供選項來建立壓縮的寬度規則高度大小類別介面變化。 
- 如果**iPad Pro 9.7"** / **橫向** / **全螢幕**已選取，popover 會提供用於建立介面變數選項規則的寬度，高度一般大小類別。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![依大小類別用來改變介面底部工具列](introduction-images/15-edittraitsbutton-vsmac.png "大小類別用來改變介面底部工具列")](introduction-images/15-edittraitsbutton-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![依大小類別用來改變介面底部工具列](introduction-images/15-edittraitsbutton-vs.png "大小類別用來改變介面底部工具列")](introduction-images/15-edittraitsbutton-vs-large.png#lightbox)

-----

#### <a name="zoom-controls"></a>縮放控制項

設計介面，支援透過數種控制縮放：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
 
![在底部工具列的縮放控制項](introduction-images/16-zoomcontrols-vsmac.png "在底部工具列的縮放控制項")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![在底部工具列的縮放控制項](introduction-images/16-zoomcontrols-vs.png "在底部工具列的縮放控制項")

-----

下列控制項：

1. 縮放至適當比例
2. 縮小
3. 放大
4. 實際大小 （1:1 像素大小）

這些控制項調整設計介面上的顯示比例。 它們不會影響在執行階段應用程式的使用者介面。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

### <a name="properties-pad"></a>屬性填補

使用**屬性板**編輯身分識別、 視覺化樣式，存取範圍，與控制項的行為。 下列螢幕擷取畫面說明**屬性板**按鈕的選項：

[![按鈕內容板](introduction-images/17-buttonpropertiespad-vsmac.png "屬性板按鈕")](introduction-images/17-buttonpropertiespad-vsmac-large.png#lightbox)
#### <a name="properties-pad-sections"></a>屬性板區段

**屬性板**包含三個區段：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="properties-window"></a>屬性視窗

使用**屬性 視窗**編輯身分識別、 視覺化樣式，存取範圍，與控制項的行為。 下列螢幕擷取畫面說明**屬性 視窗**按鈕的選項：

[![按鈕的 [屬性] 視窗](introduction-images/17-buttonpropertieswindow-vs.png "按鈕的 [屬性] 視窗")](introduction-images/17-buttonpropertieswindow-vs-large.png#lightbox)

#### <a name="properties-window-sections"></a>屬性視窗區段

**屬性 視窗**包含三個區段：

-----

1.  **Widget** – 控制項，例如名稱、 類別、 樣式屬性等等的主要屬性。管理控制項的內容屬性通常用來放置此處。
2.  **版面配置**– 此處列出的位置和大小的控制項，包含條件約束和框架，追蹤的屬性。
3.  **事件**– 此處指定的事件和事件處理常式。 適用於處理事件，例如觸控、 點選、 拖曳等等。也可以直接在程式碼中處理事件。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

#### <a name="editing-properties-in-the-properties-pad"></a>編輯屬性輸入板中的屬性

除了視覺化設計介面上編輯，iOS 設計工具支援在編輯屬性**屬性板**。 可用的屬性變更，根據選取的控制項，如以下螢幕擷取畫面所示：

[![按鈕內容](introduction-images/18a-buttonpropertiespad-vsmac.png "按鈕屬性")](introduction-images/18a-buttonpropertiespad-vsmac-large.png#lightbox)

[![檢視控制器屬性](introduction-images/18b-viewcontrollerpropertiespad-vsmac.png "檢視控制器屬性")](introduction-images/18b-viewcontrollerpropertiespad-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

#### <a name="editing-properties-in-the-properties-window"></a>在 [屬性] 視窗中編輯屬性

除了視覺化設計介面上編輯，iOS 設計工具支援在編輯屬性**屬性 視窗**。 可用的屬性變更，根據選取的控制項，如以下螢幕擷取畫面所示：

[![按鈕內容](introduction-images/18a-buttonpropertieswindow-vs.png "按鈕屬性")](introduction-images/18a-buttonpropertieswindow-vs-large.png#lightbox)

[![檢視控制器屬性](introduction-images/18b-viewcontrollerpropertieswindow-vs.png "檢視控制器屬性")](introduction-images/18b-viewcontrollerpropertieswindow-vs-large.png#lightbox)

-----

> [!IMPORTANT]
> 識別區段的內容板現在顯示**模組**欄位。 它是為了 Swift 類別間的互通性時，才填入這一節。 您可以使用它來輸入 Swift 類別，也就是已命名空間的模組名稱。

#### <a name="default-values"></a>預設值

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

中的許多屬性**屬性板**顯示任何值或預設值。 不過，應用程式的程式碼仍可能會修改這些值。 **屬性板**未顯示在程式碼中設定的值。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

中的許多屬性**屬性 視窗**顯示任何值或預設值。 不過，應用程式的程式碼仍可能會修改這些值。 **屬性 視窗**未顯示在程式碼中設定的值。

-----

#### <a name="event-handlers"></a>事件處理常式

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要指定各種事件的自訂事件處理常式，請使用**事件** 索引標籤**屬性板**。 例如，在下列螢幕擷取畫面`HandleClick`方法處理按鈕的**觸控向上內**事件：

[![屬性輸入板中，設定按鈕的事件處理常式與](introduction-images/19-buttonpropertiespadevents-vsmac.png "屬性板，以設定按鈕的事件處理常式")](introduction-images/19-buttonpropertiespadevents-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要指定各種事件的自訂事件處理常式，請使用**事件** 索引標籤**屬性 視窗**。 例如，在下列螢幕擷取畫面`HandleClick`方法處理按鈕的**觸控向上內**事件：

[![[屬性] 視窗中，設定按鈕的事件處理常式與](introduction-images/19-buttonpropertieswindowevents-vs.png "[屬性] 視窗，設定按鈕的事件處理常式")](introduction-images/19-buttonpropertieswindowevents-vs-large.png#lightbox)

-----

一旦已指定的事件處理常式，具有相同名稱的方法必須加入對應的檢視控制器類別。 否則，`unrecognized selector`所點選按鈕時，會發生例外狀況：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![無法辨識的選取器例外狀況](introduction-images/20-unrecognizedselector-vsmac.png "無法辨識的選取器例外狀況")](introduction-images/20-unrecognizedselector-vsmac-large.png#lightbox)

請注意，之後的事件處理常式中已指定**屬性板**，iOS 設計工具會立即開啟對應的程式碼檔案，並提供要插入的方法宣告。 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![無法辨識的選取器例外狀況](introduction-images/20-unrecognizedselector-vs.png "無法辨識的選取器例外狀況")](introduction-images/20-unrecognizedselector-vs-large.png#lightbox)

-----

如需使用自訂事件處理常式的範例，請參閱[Hello，iOS 使用者入門指南](~/ios/get-started/hello-ios/index.md)。

### <a name="outline-view"></a>大綱檢視

IOS 設計工具也可以為概要顯示控制項的介面階層架構。 外框使用，請選取**文件大綱**索引標籤，如下所示：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![文件大綱](introduction-images/21-buttonoutlineview-vsmac.png "文件大綱")](introduction-images/21-buttonoutlineview-vsmac-large.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![文件大綱](introduction-images/21-buttonoutlineview-vs.png "文件大綱")](introduction-images/21-buttonoutlineview-vs-large.png#lightbox)

-----

大綱檢視中選取的控制項與保持同步的設計介面上選取的控制項。  這項功能可用於從深的巢狀介面階層架構中選取項目。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="revert-to-xcode"></a>還原為 Xcode

它是可以交替使用 iOS 設計工具] 和 [Xcode 介面產生器。 若要在 Xcode 介面產生器中開啟分鏡腳本或.xib 檔案，以滑鼠右鍵按一下該檔案並選取**開啟 > Xcode 介面產生器**，如以下螢幕擷取畫面所示：

[![在 Xcode 介面產生器中開啟分鏡腳本](introduction-images/22-openinxcodeinterfacebuilder-vsmac.png "Xcode 介面產生器中開啟分鏡腳本")](introduction-images/22-openinxcodeinterfacebuilder-vsmac-large.png#lightbox)

在 Xcode 介面產生器中進行編輯後, 儲存檔案，並返回 Visual Studio for mac。 所做的變更會同步至 Xamarin.iOS 專案。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-----

## <a name="xib-support"></a>.xib 支援

IOS 設計工具支援建立、 編輯和管理.xib 檔案。 這些是 XML 檔案，也可以加入至應用程式的檢視階層該代表單一的自訂檢視。 .Xib 檔案通常代表單一檢視] 或 [應用程式中，螢幕的介面，而分鏡腳本代表許多畫面和它們之間轉換。

有哪些解決方案 （.xib 檔案、 分鏡腳本或程式碼） 最適合用於建立和維護的使用者介面的許多意見。 事實上，有沒有完整的解決方案，而且永遠是值得考慮手邊的工作的最佳工具。 話雖如此，.xib 檔案是通常最強大時用來表示應用程式，例如自訂資料表檢視儲存格的多個位置中所需的自訂檢視。 

關於使用.xib 檔案的詳細文件位於下列配方：

- [使用檢視.xib 範本](https://developer.xamarin.com/recipes/ios/general/templates/using_the_ios_view_xib_template/)
- [建立使用.xib 自訂 TableViewCell](https://developer.xamarin.com/recipes/ios/content_controls/tables/custom-tableviewcell/)
- [建立使用.xib 啟動螢幕](https://developer.xamarin.com/recipes/ios/general/templates/launchscreen-xib/)

如需有關使用的分鏡腳本，請參閱[簡介分鏡腳本](~/ios/user-interface/storyboards/index.md)。

這和其他 iOS 設計工具相關的輔助線是指使用分鏡腳本做的標準方法來建立使用者介面，因為大部分 Xamarin.iOS 新專案範本提供預設的分鏡腳本。

## <a name="summary"></a>總結

本指南介紹 iOS 設計工具中，說明其功能，以及大綱，設計美觀使用者介面提供的工具。

## <a name="related-links"></a>相關連結

- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
- [iOS 可以設計控制項的逐步解說](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Hello, iOS 多重畫面](~/ios/get-started/hello-ios-multiscreen/index.md)
- [Android 設計工具的概觀](~/android/user-interface/android-designer/index.md)
- [部分類別和方法](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [一頭栽進 Xamarin 設計工具，適用於 iOS 的發展 2014 （影片）](https://www.youtube.com/watch?v=W4H9uLjoEjM)
- [使用 iOS 設計工具來建立啟動螢幕 （影片）](https://university.xamarin.com/lightninglectures/using-the-ios-designer-to-create-a-launch-screen)
