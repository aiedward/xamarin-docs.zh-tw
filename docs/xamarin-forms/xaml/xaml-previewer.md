---
title: 適用於 Xamarin.Forms XAML 預覽程式
description: 這篇文章說明如何使用 XAML 預覽程式，請參閱您的 Xamarin.Forms 版面配置呈現您輸入。 XAML 預覽程式位於 Visual Studio 2017 和 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/31/2018
ms.openlocfilehash: def7a7a3bdd9e165252c5ad1928b89ec654e5d74
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108078"
---
# <a name="xaml-previewer-for-xamarinforms"></a>適用於 Xamarin.Forms XAML 預覽程式

_當您輸入的轉譯程式 Xamarin.Forms 版面配置，請參閱 ！_

## <a name="requirements"></a>需求

專案需要 XAML 預覽程式運作的最新的 Xamarin.Forms NuGet 套件。 預覽的 Android 應用程式需要[JDK 1.8 x64](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

中的詳細資訊[版本資訊](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Xamarin_Forms_Previewer)。

## <a name="getting-started"></a>快速入門

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

XAML 預覽程式預設為開啟，並可控制從**工具 > 選項 > Xamarin > Forms 預覽程式**對話方塊。 在這個對話方塊中，您可以選取預設文件檢視和分割方向。

[![在 Visual Studio 中的 ListView 控制項預覽](xaml-previewer-images/xamlp-options-vs.png "Visual Studio 中的表單可於預覽程式選項")](xaml-previewer-images/xamlp-options-vs.png#lightbox "Visual Studio 中的表單可於預覽程式選項")

當開啟 XAML 頁面將會分割編輯器取決於中所選取的設定**工具 > 選項 > Xamarin > Forms 預覽程式**對話方塊。 不過，可以變更這些喜好設定，在編輯器視窗中。

## <a name="xaml-preview-controls"></a>XAML 預覽控制項

[編輯器] 視窗頂端有按鈕來選取哪一個窗格正在使用中，切換至 [設計] 窗格上方的按鈕與切換至 [來源] 窗格的 [底端] 按鈕。 中間的按鈕會交換窗格順序。

[![在 Visual Studio 中的 ListView 控制項預覽](xaml-previewer-images/xamlp-controls-vs.png "Forms 預覽程式窗格會控制在 Visual Studio")](xaml-previewer-images/xamlp-controls-vs.png#lightbox "控制在 Visual Studio 中的表單可於預覽程式窗格")

[編輯器] 視窗的底部具有垂直和水平分割窗格中，並以展開或摺疊目前的子窗格的按鈕。

[![在 Visual Studio 中的 ListView 控制項預覽](xaml-previewer-images/xamlp-controls2-vs.png "Forms 預覽程式窗格會控制在 Visual Studio")](xaml-previewer-images/xamlp-controls2-vs.png#lightbox "控制在 Visual Studio 中的表單可於預覽程式窗格")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

**預覽**開啟 XAML 頁面時，將會顯示在編輯器中的按鈕。 [預覽] 窗格可以顯示或隱藏藉由按下**預覽**任何 XAML 文件視窗的右上角的按鈕：

[![在 Visual Studio for Mac 的 ListView 控制項預覽](xaml-previewer-images/xamlp-list-sml.png "Forms 預覽程式，在 Visual Studio for Mac")](xaml-previewer-images/xamlp-list.png#lightbox "Forms 預覽程式，在 Visual Studio for Mac")

-----

## <a name="xaml-preview-options"></a>XAML 預覽選項

[預覽] 窗格頂端的選項如下：

* **電話**– 電話大小螢幕中轉譯
* **Tablet** – 呈現在平板電腦畫面 （請注意右下方窗格的沒有縮放控制項）
* **Android** – 顯示在畫面的 Android 版本
* **iOS** – 顯示在畫面的 iOS 版本
* Portait （圖示）-針對預覽使用直式方向
* 橫向 （圖示） – 使用橫向方向預覽

## <a name="adding-design-time-data"></a>新增設計階段資料

某些版面配置可能難想像沒有任何繫結至使用者介面控制項的資料。 若要使預覽更加實用，指派一些靜態的資料控制項的硬式編碼繫結內容 （無論是在程式碼後置中或使用 XAML）。

請參閱 James Montemagno[新增設計階段資料的部落格文章](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data)以了解如何將繫結至 XAML 中的靜態 ViewModel。

## <a name="detecting-design-mode"></a>偵測的設計模式

靜態[ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled)屬性可檢查以判斷應用程式是否正執行於預覽程式。 這可讓您指定只會在預覽程式在執行應用程式時，所執行的程式碼：

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}
```

## <a name="troubleshooting"></a>疑難排解

檢查下列問題並[Xamarin 論壇](https://forums.xamarin.com/categories/xamarin-forms)，如果您遇到問題。

### <a name="xaml-preview-isnt-showing"></a>未顯示 XAML 預覽

檢查下列項目：

* 應建置專案 （編譯） 之前嘗試預覽 XAML 檔案。
* 設計工具代理程式必須設定第一次預覽 XAML 檔-此問題之前準備好，將會出現在預覽程式，以及進行訊息的進度列指示器。
* 請嘗試關閉再重新開啟 XAML 檔案。
* 請確認您`App`類別具有無參數建構函式。

### <a name="invalid-xaml-the-android-project-needs-to-built-before-preview-can-be-created"></a>無效的 XAML: Android 專案需要建置才能建立預覽

XAML 預覽程式需要建置專案，然後才轉譯頁面。
如果下列錯誤會出現在頂端的 [預覽] 窗格中，重新建置應用程式，並再試一次。

![錯誤訊息： 必須先建置專案](xaml-previewer-images/error-not-built-sml.png "錯誤訊息： 重建專案")
