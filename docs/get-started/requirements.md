---
title: Xamarin。表單需求
description: Xamarin的平臺和開發系統需求。構成.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2019
no-loc:
- Xamarin
- Xamarin.Forms
- Xamarin.Android
- Xamarin.Essentials
- Xamarin.iOS
- Xamarin.Mac
ms.openlocfilehash: d12daa358917399fc5fd1febf02d4f96a647f360
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607850"
---
# <a name="opno-locxamarinforms-requirements"></a>Xamarin。表單需求

_Xamarin的平臺和開發系統需求。構成._

如需適用於跨平台的安裝和設定作法概觀，請參閱[安裝](installation/index.md)文章。

## <a name="target-platforms"></a>目標平台

Xamarin。您可以針對下列作業系統撰寫表單應用程式：

- iOS 9 或更新版本
- Android 4.4 (API 19) 或更高版本 ([更多詳細資料](#android))
- Windows 10 通用 Windows 平台 ([更多詳細資料](#windows10))

不過，建議使用 Android 5.0 （API 21）作為最低 API。 這可確保與所有 Android 支援程式庫完全相容，同時仍以大部分的 Android 裝置為目標。

假設開發人員已熟悉[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)。

### <a name="additional-platform-support"></a>其他平台支援

這些平臺的狀態可在Xamarin上取得[。表單 GitHub](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support)：

- Samsung Tizen
- macOS
- GTK#
- WPF

### <a name="android"></a>Android

您應該已安裝最新的 Android SDK 工具和 Android API 平台。 您可以使用 [Android SDK 管理員](~/android/get-started/installation/android-sdk.md)更新至最新版本。

此外，Android 專案的目標/編譯版本**必須**設定為*使用最新安裝的平台*。 不過，最低版本可以設定為 API 19，讓您可以繼續支援使用 Android 4.4 及更新版本的裝置。 這些值是在 [專案選項] 中設定的：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[專案選項] > [應用程式] > [應用程式屬性]

![Visual Studio 中的 Android 組建選項](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[建置] > [一般]

![選取最新的目標架構](requirements-images/options-general-sml.png)

[建置] > [Android 應用程式]

![選取適用于您應用程式的最小和目標 Android 版本](requirements-images/options-android-sml.png)

-----

## <a name="development-system-requirements"></a>開發系統需求

Xamarin。您可以在 macOS 和 Windows 上開發表單應用程式。 不過，需要有 Windows 和 Visual Studio 才能產生應用程式的 Windows 版本。

## <a name="mac-system-requirements"></a>Mac 系統需求

您可以使用 Visual Studio for Mac 來開發 Xamarin。MacOS 高塞拉里昂（10.13）或更新版本上的表單應用程式。 若要開發 iOS 應用程式，我們建議使用最新版本的 Xcode、iOS 和 macOS。 如需特定版本需求，請參閱最新的[XamariniOS 版本](/xamarin/ios/release-notes/)資訊。

> [!NOTE]
> 無法在 macOS 上開發 Windows 應用程式。

## <a name="windows-system-requirements"></a>Windows 系統需求

Xamarin。適用于 iOS 和 Android 的表單應用程式可以建置於支援 Xamarin 開發的任何 Windows 安裝上。 如需目前平臺功能的完整支援，請使用最新版本的 Visual Studio。 

使用最新版本的 Xcode 和 Apple 指定的 macOS 最低版本，進行 iOS 開發時，需要有網路的 Mac。

<a name="windows10" />

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

開發 Xamarin。適用于 UWP 的表單應用程式需要：

- Windows 10 （建議的最新版本，秋季建立者更新最少）

- 建議 Visual Studio 2019

- [Windows 10 SDK](https://dev.windows.com/downloads/windows-10-sdk)

您可以[將通用 Windows 平臺（UWP）應用程式新增](~/xamarin-forms/platform/windows/installation/index.md)至現有的 Xamarin。表單解決方案。

## <a name="deprecated-platforms"></a>已淘汰的平臺

使用 Xamarin時，不支援這些平臺。表單3.0 或更新版本：

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*
