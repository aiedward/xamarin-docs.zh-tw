---
title: 的 XAML 預覽程式 Xamarin.Forms
description: 本文說明如何使用 XAML 預覽程式來查看您 Xamarin.Forms 輸入時所呈現的版面配置。 XAML 預覽程式可在 Visual Studio 2019 和適用于 Mac 的 Visual Studio 2019 中取得。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dff01728c986b23e59af702edb3bfd8c74ad744a
ms.sourcegitcommit: d2aa3a8bf9a60b6708db55b10b0c6893c06d3256
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93331444"
---
# <a name="xaml-previewer-for-no-locxamarinforms"></a>的 XAML 預覽程式 Xamarin.Forms

_查看您 Xamarin.Forms 鍵入時所呈現的版面配置_

> [!WARNING]
> 預覽 XAML 的建議方式是現在有 **[XAML 熱重新載入](~/xamarin-forms/xaml/hot-reload.md)** 。

## <a name="overview"></a>概觀

XAML 預覽程式會顯示您的 Xamarin.Forms xaml 頁面在 iOS 和 Android 上的外觀。 當您對 XAML 進行變更時，您會看到它們立即與您的程式碼一起預覽。 XAML 預覽程式可在 Visual Studio 和 Visual Studio for Mac 中取得。

## <a name="getting-started"></a>開始使用

::: zone pivot="windows"

### <a name="visual-studio-2019"></a>Visual Studio 2019

您可以按一下 [分割視圖] 窗格上的箭號來開啟 XAML 預覽程式。 如果您想要變更預設的分割視圖行為，請使用 [ **Xamarin > Xamarin.Forms XAML 預覽** 器] 對話方塊中的 [工具] > 選項 >。 在這個對話方塊中，您可以選取預設的檔視圖和分割方向。

[![：：：非 loc (Xamarin. Forms) ：：：預覽器選項 Visual Studio](xaml-previewer-images/xamlp-options-vs-sm.png "：：：非 loc (Xamarin. Forms) ：：：預覽器選項 Visual Studio")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

當您開啟 XAML 檔案時，編輯器會根據 [ **工具] > 選項 > Xamarin > Xamarin.Forms XAML 預覽** 器] 對話方塊中選取的設定，開啟預覽工具的完整大小或旁邊。 不過，您可以在編輯器視窗中變更每個檔案的分割。

#### <a name="xaml-preview-controls"></a>XAML 預覽控制項

在 [分割視圖] 窗格上選取這些按鈕，選擇您想要查看程式碼、XAML 預覽程式或兩者。 中間按鈕會交換預覽程式和您的程式碼所在的位置：

[![：：：非 loc (Xamarin. Forms) ：：：預覽器控制項，以便在 Visual Studio 的設計、來源和分割視圖之間切換](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "：：：非 loc (Xamarin. Forms) ：：：預覽器控制項，以便在 Visual Studio 的設計、來源和分割視圖之間切換")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

您可以變更是否以垂直或水準方式分割螢幕，或全部折迭一個窗格：

[![：：：非 loc (Xamarin. Forms) ：：：預覽窗格的方向控制項 Visual Studio](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "：：：非 loc (Xamarin. Forms) ：：：預覽窗格的方向控制項 Visual Studio")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

#### <a name="enable-or-disable-the-xaml-previewer"></a>啟用或停用 XAML 預覽器

您可以選取 [ **預設 XML 編輯器** ] 作為 **預設的 XAML 編輯器** ，以在 [ **工具] > 選項 > Xamarin > Xamarin.Forms xaml 預覽** 程式] 對話方塊中關閉 xaml 預覽程式。 這也會關閉 [檔大綱]、[屬性面板] 和 [XAML 工具箱]。 若要將 XAML 預覽程式和這些工具重新開啟，請將您的 **預設 XAML 編輯器** 變更為 **Xamarin.Forms 預覽** 程式。

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

當您開啟 XAML 頁面時，編輯器上會顯示 [ **預覽** ] 按鈕。 按下任何 XAML 文件視窗左下角的 [ **預覽** ] 或 [ **分割** ] 按鈕，以顯示或隱藏預覽程式：

[![：：：非 loc (Xamarin. Forms) ：：：預覽器已啟用預覽或分割按鈕](xaml-previewer-images/xamlp-list-sml.png)](xaml-previewer-images/xamlp-list.png#lightbox)

> [!NOTE]
> 在較舊版本的 Visual Studio for Mac 中，[ **預覽** ] 按鈕位於視窗右上方。

#### <a name="enable-or-disable-the-xaml-previewer"></a>啟用或停用 XAML 預覽器

您可以選取 [ **預設 XML 編輯器** ] 做為 **預設的 xaml 編輯器** ，在 **Visual Studio > 喜好設定 > 文字編輯器 > XAML** ] 對話方塊中關閉 xaml 預覽程式。 這也會關閉 [檔大綱]、[屬性面板] 和 [XAML 工具箱]。 若要將 XAML 預覽程式和這些工具重新開啟，請將您的 **預設 XAML 編輯器** 變更為 **Xamarin.Forms 預覽** 程式。

::: zone-end

## <a name="xaml-previewer-options"></a>XAML 預覽器選項

[預覽] 窗格頂端的選項如下：

* **Android** –顯示 android 版本的畫面
* **ios** –顯示 ios 版的螢幕 ( *注意：如果您在 Windows 上使用 Visual Studio，則必須將 [與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md) ，才能使用此模式* ) 
* Android 或 iOS 裝置的 **裝置** 下拉式清單，包括解析度和螢幕大小
* 直向 **(圖示)** –使用預覽的直向方向
* **橫向 (圖示)** –使用預覽的橫向方向

## <a name="detect-design-mode"></a>偵測設計模式

靜態 [`DesignMode.IsDesignModeEnabled`](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) 屬性會告訴您應用程式是否正在預覽程式中執行。 您可以使用它來指定程式碼，只有當應用程式處於或未在預覽程式中執行時，才會執行此程式碼：

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}

if (!DesignMode.IsDesignModeEnabled)
{
  // Don't run in the Previewer  
}
```

如果您在頁面的函式中初始化無法在設計階段執行的程式庫，這個屬性就很有用。

## <a name="troubleshooting"></a>疑難排解

如果預覽器無法運作，請檢查下列問題和 [Xamarin 論壇](https://forums.xamarin.com/categories/xamarin-forms)。

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>XAML 預覽程式未顯示或顯示錯誤

* 預覽程式可能需要一些時間才能啟動，您會看到「正在初始化轉譯」，直到它就緒為止。
* 請嘗試關閉並重新開啟 XAML 檔案。
* 確定您的 `App` 類別具有無參數的函式。
* 檢查您 Xamarin.Forms 的版本-必須至少為 Xamarin.Forms 3.6。 您可以透過 NuGet 更新至最新 Xamarin.Forms 版本。
* 檢查您的 JDK 安裝-預覽 Android 至少需要 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* 請嘗試將頁面 c # 程式碼中的任何初始化類別包裝在中 `if (!DesignMode.IsDesignModeEnabled)` 。

### <a name="custom-controls-arent-rendering"></a>自訂控制項不會呈現

嘗試建立您的專案。 如果您無法轉譯控制項，或控制項的建立者退出宣告設計階段轉譯，則預覽程式會顯示控制項的基類。 如需詳細資訊，請參閱在 [XAML 預覽程式中呈現自訂控制項](render-custom-controls.md)。
