---
title: Xamarin.Forms 需求
description: Xamarin.Forms 的平台和開發系統需求。
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/19/2018
ms.openlocfilehash: ce3f2bcf6acc36239fc431bb7f5edece15d2e139
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2018
---
# <a name="xamarinforms-requirements"></a>Xamarin.Forms 需求

_Xamarin.Forms 的平台和開發系統需求。_

如需適用於跨平台的安裝和設定作法概觀，請參閱[安裝](~/cross-platform/get-started/installation/index.md)文章。

## <a name="target-platforms"></a>目標平台

您可以針對下列作業系統撰寫 Xamarin.Forms 應用程式：

-  iOS 8 或更高版本
-  Android 4.0.3 (API 15) 或更高版本 ([更多詳細資料](#android))
-  Windows 10 通用 Windows 平台 ([更多詳細資料](#windows10))
-  *Windows 8.1 / Windows Phone 8.1 WinRT (已被取代)*
-  *Windows Phone 8 Silverlight (已過時)*

假設開發人員已熟悉[可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)和[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)。

<a name="android" />

### <a name="android"></a>Android

您應該已安裝最新的 Android SDK 工具和 Android API 平台。 您可以使用 [Android SDK 管理員](~/android/get-started/installation/android-sdk.md)更新至最新版本。

此外，Android 專案的目標/編譯版本**必須**設定為*使用最新安裝的平台*。 不過，最低版本可以設定為 API 15，讓您可以繼續支援使用 Android 4.0.3 及更新版本的裝置。 這些值是在 [專案選項] 中設定的：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[專案選項] > [應用程式] > [應用程式屬性]

![](installation-images/options-android-vs-sml.png "Visual Studio 中的 Android 建置選項")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[建置] > [一般]

![](installation-images/options-general-sml.png "[建置] > [一般]")

[建置] > [Android 應用程式]

![](installation-images/options-android-sml.png "[建置] > [Android 應用程式]")

-----

<a name="windows10" />

### <a name="universal-windows-platform"></a>通用 Windows 平台

在 macOS 上建立方案時，不會新增 Windows 10 UWP 專案。 如需有關如何將這些專案新增至現有方案的指示，請參閱[設定 Windows 專案](~/xamarin-forms/platform/windows/installation/index.md)。

## <a name="development-system-requirements"></a>開發系統需求

您可以在 macOS 及 Windows 上開發 Xamarin.Forms 應用程式。 不過，需要有 Windows 和 Visual Studio 才能產生應用程式的 Windows 版本。

## <a name="mac-system-requirements"></a>Mac 系統需求

您可以使用 Visual Studio for Mac，在 OS X El Capitan (10.11) 或更新版本上開發 Xamarin.Forms 應用程式。 若要開發 iOS 應用程式，建議至少已安裝 iOS 10 SDK 和 Xcode 8。

> [!NOTE]
>  無法在 macOS 上開發 Windows 應用程式。

## <a name="windows-system-requirements"></a>Windows 系統需求

在支援 Xamarin 開發的任何 Windows 版本上，都可以建置適用於 iOS 和 Android 的 Xamarin.Forms 應用程式。 這需要在 Windows 7 或更新版本上執行 Visual Studio 2017 或更新版本。 對於 iOS 開發，需要有連線到網路的 Mac。

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

開發適用於 UWP 的 Xamarin.Forms 應用程式需要：

* Windows 10 (建議使用 Fall Creators Update)

* Visual Studio 2017

* [Windows 10 SDK](https://dev.windows.com/downloads/windows-10-sdk)

UWP 專案會包含在使用 Visual Studio 2015 和 Visual Studio 2017 建立的 Xamarin.Forms 方案中。
您也可以[將通用 Windows 平台 (UWP) 應用程式新增](~/xamarin-forms/platform/windows/installation/index.md)至現有的 Xamarin.Forms 方案。
