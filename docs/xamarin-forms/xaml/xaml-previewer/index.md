---
title: 適用於 Xamarin.Forms XAML 預覽程式
description: 這篇文章說明如何使用 XAML 預覽程式，請參閱您的 Xamarin.Forms 版面配置呈現您輸入。 XAML 預覽程式可用於 Visual Studio 2019 和 Visual Studio 2019 for mac。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 02/04/2019
ms.openlocfilehash: db243a9c8dcb25f51bc7926a7aa239531e9c24f6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61210792"
---
# <a name="xaml-previewer-for-xamarinforms"></a>適用於 Xamarin.Forms XAML 預覽程式

_請參閱您呈現您所輸入的 Xamarin.Forms 版面配置_

## <a name="overview"></a>總覽

XAML 預覽程式會顯示您的 Xamarin.Forms XAML 頁面在 iOS 和 Android 上所呈現的樣子。 當您變更您的 XAML 時，您會看到它們立即預覽您的程式碼之外。 XAML 預覽程式可用於 Visual Studio 和 Visual Studio for mac。

## <a name="getting-started"></a>使用者入門

::: zone pivot="windows"

### <a name="visual-studio-2019"></a>Visual Studio 2019

您可以開啟 XAML 預覽程式，依序按一下 [分割檢視] 窗格上的箭號。 如果您想要變更預設的分割檢視行為，請使用**工具 > 選項 > Xamarin > Forms 預覽程式**對話方塊。 在此對話方塊中，您可以選取預設文件檢視和分割方向。

[![在 Visual Studio 中的 Xamarin.Forms 預覽程式選項](xaml-previewer-images/xamlp-options-vs-sm.png "選項在 Visual Studio 中的 Xamarin.Forms 預覽程式")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

當您開啟 XAML 檔案時，編輯器會開啟完整大小或以預覽程式，根據設定中選取 下一步**工具 > 選項 > Xamarin > Forms 預覽程式**對話方塊。 不過，分割可針對每個檔案在編輯器視窗中變更。

#### <a name="xaml-preview-controls"></a>XAML 預覽控制項

選擇您想要查看您的程式碼，XAML 預覽程式，或同時選取這些按鈕在分割檢視 窗格。 中間的按鈕會交換哪些一邊預覽程式和您的程式碼位於：

[![Xamarin.Forms 可於預覽程式控制設計、 來源和 Visual Studio 中的 [分割] 檢視間切換](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "Xamarin.Forms 預覽程式控制項加入設計、 來源和 Visual Studio 中的 [分割] 檢視之間切換")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

您可以變更是否垂直或水平分割畫面，或完全摺疊一個窗格：

[![Visual Studio 中的 Xamarin.Forms 預覽程式窗格方向控制項](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "Visual Studio 中的 Xamarin.Forms 預覽程式窗格方向控制項")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**預覽**開啟 XAML 頁面時，將會顯示在編輯器中的按鈕。 顯示或隱藏預覽程式藉由按下**預覽**任何 XAML 文件視窗的右上角的按鈕：

[![Xamarin.Forms Previewer in Visual Studio for Mac](xaml-previewer-images/xamlp-list-sml.png "Xamarin.Forms Previewer in Visual Studio for Mac")](xaml-previewer-images/xamlp-list.png#lightbox)

::: zone-end

## <a name="xaml-previewer-options"></a>XAML 預覽程式選項

[預覽] 窗格頂端的選項如下：

* **Android** – 顯示在畫面的 Android 版本
* **iOS** – 顯示在畫面的 iOS 版本 (*附註：如果您在 Windows 上使用 Visual Studio，您必須是[與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)若要使用此模式*)
* **裝置**-Android 或 iOS 的裝置，包括解析度和螢幕大小的下拉式清單
* **直向 （圖示）** – 使用直式方向預覽
* **橫向 （圖示）** – 使用橫向方向預覽

## <a name="detect-design-mode"></a>偵測到設計模式

靜態[ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled)屬性能告訴您應用程式是否正在執行中預覽程式。 您可以使用它，來指定只會在應用程式，或不在預覽程式執行時所執行的程式碼：

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

這個屬性就很有用，如果您在設計階段執行失敗的頁面建構函式中初始化文件庫。

## <a name="troubleshooting"></a>疑難排解

請檢查下列問題並[Xamarin 論壇](https://forums.xamarin.com/categories/xamarin-forms)、 如果預覽程式無法運作。

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>XAML 預覽程式未顯示，或顯示錯誤

* 可能需要一些時間才能啟動預覽程式-您會看到 「 正在初始化轉譯 」 直到它準備。
* 請嘗試關閉再重新開啟 XAML 檔案。
* 請確認您`App`類別具有無參數建構函式。
* 檢查您的 Xamarin.Forms 版本-它必須至少是 Xamarin.Forms 3.6。 您可以更新為最新的 Xamarin.Forms 版本透過 NuGet。
* 檢查您的 JDK 安裝-預覽 Android 需要至少[JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* 包裝任何嘗試初始化類別在頁面中的C#中的程式碼後置`if (!DesignMode.IsDesignModeEnabled)`。

### <a name="custom-controls-arent-rendering"></a>自訂控制項不轉譯

請嘗試建置您的專案。 如果它無法呈現控制項，或如果控制項的建立者選擇向外延展的設計階段呈現預覽程式就會顯示控制項的基底類別。 如需詳細資訊，請參閱 <<c0> [ 呈現 XAML 預覽程式中的自訂控制項](render-custom-controls.md)。
