---
title: 適用於 Xamarin.Forms XAML 預覽程式
description: 這篇文章說明如何使用 XAML 預覽程式，請參閱您的 Xamarin.Forms 版面配置呈現您輸入。 在 Visual Studio 2017，Visual Studio for Mac 和 Visual Studio 2019 （預覽） 中使用 XAML 預覽程式。
zone_pivot_groups: platform-dev16
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 02/04/2019
ms.openlocfilehash: 7834b87687db8fd1a3d51a40a4657b24e46bac17
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670398"
---
# <a name="xaml-previewer-for-xamarinforms"></a>適用於 Xamarin.Forms XAML 預覽程式

_當您輸入的轉譯程式 Xamarin.Forms 版面配置，請參閱 ！_

## <a name="requirements"></a>需求

專案需要 XAML 預覽程式運作的最新的 Xamarin.Forms NuGet 套件。 預覽的 Android 應用程式需要[JDK 1.8 x64](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

中的詳細資訊[版本資訊](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Xamarin_Forms_Previewer)。

## <a name="getting-started"></a>使用者入門

Xamarin.Forms XAML 預覽程式的 XAML 檔案的特定平台預覽即時顯示，當您在編輯時。

::: zone pivot="windows"

### <a name="visual-studio"></a>Visual Studio

XAML 預覽程式使用，請展開 [分割檢視] 窗格。 預設的分割檢視行為可以控制從**工具 > 選項 > Xamarin > Forms 預覽程式**對話方塊。 在此對話方塊中，您可以選取預設文件檢視和分割方向。

[![在 Visual Studio 中的 Xamarin.Forms 預覽程式選項](xaml-previewer-images/xamlp-options-vs-sm.png "選項在 Visual Studio 中的 Xamarin.Forms 預覽程式")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

開啟 XAML 頁面，編輯器會分割成根據設定中選取**工具 > 選項 > Xamarin > Forms 預覽程式**對話方塊。 不過，分割可針對每個檔案在編輯器視窗中變更。

#### <a name="xaml-preview-controls"></a>XAML 預覽控制項

[編輯器] 視窗頂端有按鈕來選取哪一個窗格正在使用中，切換至 [設計] 窗格上方的按鈕與切換至 [來源] 窗格的 [底端] 按鈕。 中間的按鈕會交換窗格順序。

[![Xamarin.Forms 可於預覽程式控制設計、 來源和 Visual Studio 中的 [分割] 檢視間切換](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "Xamarin.Forms 預覽程式控制項加入設計、 來源和 Visual Studio 中的 [分割] 檢視之間切換")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

[編輯器] 視窗的底部具有垂直和水平分割窗格中，並以展開或摺疊目前的子窗格的按鈕。

[![Visual Studio 中的 Xamarin.Forms 預覽程式窗格方向控制項](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "Visual Studio 中的 Xamarin.Forms 預覽程式窗格方向控制項")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**預覽**開啟 XAML 頁面時，將會顯示在編輯器中的按鈕。 [預覽] 窗格可以顯示或隱藏藉由按下**預覽**任何 XAML 文件視窗的右上角的按鈕：

[![Xamarin.Forms Previewer in Visual Studio for Mac](xaml-previewer-images/xamlp-list-sml.png "Xamarin.Forms Previewer in Visual Studio for Mac")](xaml-previewer-images/xamlp-list.png#lightbox)

::: zone-end
::: zone pivot="win-dev16"

### <a name="visual-studio-2019-preview"></a>Visual Studio 2019 （預覽）

XAML 預覽程式使用，請展開 [分割檢視] 窗格。 預設的分割檢視行為可以控制從**工具 > 選項 > Xamarin > Forms 預覽程式**對話方塊。 在此對話方塊中，您可以選取預設文件檢視和分割方向。

[![在 Visual Studio 中的 Xamarin.Forms 預覽程式選項](xaml-previewer-images/xamlp-options-vs-sm.png "選項在 Visual Studio 中的 Xamarin.Forms 預覽程式")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

開啟 XAML 頁面，編輯器會分割成根據設定中選取**工具 > 選項 > Xamarin > Forms 預覽程式**對話方塊。 不過，分割可針對每個檔案在編輯器視窗中變更。

#### <a name="xaml-preview-controls"></a>XAML 預覽控制項

[編輯器] 視窗頂端有按鈕來選取哪一個窗格正在使用中，切換至 [設計] 窗格上方的按鈕與切換至 [來源] 窗格的 [底端] 按鈕。 中間的按鈕會交換窗格順序。

[![Xamarin.Forms 可於預覽程式控制設計、 來源和 Visual Studio 中的 [分割] 檢視間切換](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "Xamarin.Forms 預覽程式控制項加入設計、 來源和 Visual Studio 中的 [分割] 檢視之間切換")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

[編輯器] 視窗的底部具有垂直和水平分割窗格中，並以展開或摺疊目前的子窗格的按鈕。

[![Visual Studio 中的 Xamarin.Forms 預覽程式窗格方向控制項](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "Visual Studio 中的 Xamarin.Forms 預覽程式窗格方向控制項")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end

## <a name="xaml-previewer-options"></a>XAML 預覽程式選項

[預覽] 窗格頂端的選項如下：

* **電話**– 電話大小螢幕上的預覽
* **Tablet** -調整大小的平板電腦螢幕上的預覽
* **Android** – 顯示在畫面的 Android 版本
* **iOS** – 顯示在畫面的 iOS 版本 (*附註：如果您在 Windows 上使用 Visual Studio，您必須是[與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)若要使用此模式*)
* **直向 （圖示）** – 使用直式方向預覽
* **橫向 （圖示）** – 使用橫向方向預覽

::: zone pivot="win-dev16"

## <a name="device-drop-down-menu"></a>裝置的下拉式選單

在 Visual Studio 2019 （預覽），您可以選取要預覽上，從下拉式清單中的 Android 或 iOS 裝置的裝置。 這個下拉式清單會取代 「 電話 」 及 「 數位板 」 選項。 當預覽 iOS，下拉式清單會顯示 iPhone 與 iPad 的裝置。 當預覽 Android，下拉式清單會顯示各種不同的 Android 手機和平板電腦。 這兩份清單包括螢幕大小和螢幕解析度。

::: zone-end

## <a name="detect-design-mode"></a>偵測到設計模式

靜態[ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled)屬性可檢查以判斷應用程式是否正執行於預覽程式。 這可讓您指定只會在預覽程式在執行應用程式時，所執行的程式碼：

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}
```

::: zone pivot="win-dev16"

## <a name="enable-design-time-rendering-for-custom-controls"></a>啟用自訂控制項的設計階段呈現

自訂控制項必須 opt-in 以設計階段呈現才會出現在預覽程式，不論控制項位於您的專案，或從程式庫匯入。 這可藉由新增[ `[DesignTimeVisible(true)]` ](xref:System.ComponentModel.DesignTimeVisibleAttribute)至您的控制項類別：

```csharp
namespace MyProject
{
  [DesignTimeVisible(true)]
  public class MyControl : BaseControl
  {
    // Your control's code here
  }

}
```

這個範例中可以看到[James Montemagno ImageCirclePlugin 基底類別](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs)。

::: zone-end

## <a name="add-design-time-data"></a>新增設計階段資料

某些版面配置可能難想像沒有任何繫結至使用者介面控制項的資料。 若要使預覽更加實用，指派一些靜態的資料控制項的硬式編碼繫結內容 （無論是在程式碼後置中或使用 XAML）。

請參閱 James Montemagno[新增設計階段資料的部落格文章](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data)以了解如何將繫結至 XAML 中的靜態 ViewModel。

## <a name="troubleshooting"></a>疑難排解

檢查下列問題並[Xamarin 論壇](https://forums.xamarin.com/categories/xamarin-forms)，如果您遇到問題。

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>XAML 預覽程式未顯示，或顯示錯誤

檢查下列項目：

* 設計工具代理程式必須設定第一次預覽 XAML 檔-此問題之前準備好，將會出現在預覽程式，以及進行訊息的進度列指示器。
* 請嘗試關閉再重新開啟 XAML 檔案。
* 請確認您`App`類別具有無參數建構函式。

::: zone pivot="win-dev16"

### <a name="custom-controls-arent-rendering"></a>自訂控制項不轉譯

請確定您的專案建置。 如果預覽程式無法轉譯的控制項不會產生錯誤，或控制項的建立者未不選擇位在設計階段呈現，預覽程式就會顯示控制項的基底類別。

::: zone-end
