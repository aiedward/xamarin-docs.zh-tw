---
title: Xamarin.Forms 支援的平台
description: Xamarin.Forms 的平台和開發系統需求。
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2020
ms.openlocfilehash: 5751983bf4a818e8ee91af98b0c05658efaca8b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304272"
---
# <a name="xamarinforms-supported-platforms"></a>Xamarin.Forms 支援的平台

您可以針對下列作業系統撰寫 Xamarin.Forms 應用程式：

- iOS 9 或更高版本。
- Android 4.4 (API 19) 或更高版本 ([更多詳細資料](#android-platform-support))。 不過，建議至少使用 Android 5.0 (API 21) 以上的 API 版本。 這可確保與所有 Android 支援程式庫完全相容，且其仍為大部分 Android 裝置所用的版本。
- Windows 10 通用 Windows 平台。

可以在 Visual Studio 中建置適用於 iOS、Android 和通用 Windows 平台 (UWP) 的 Xamarin.Forms 應用程式。 不過，使用 Apple 所指定最新版本 Xcode 與最舊版本 macOS 進行 iOS 開發時，需要有網路連線的 Mac。 如需詳細資訊，請參閱 [Windows 需求](~/cross-platform/get-started/requirements.md#windows-requirements)。

可以在 Visual Studio for Mac 中建置適用於 iOS 和 Android 的 Xamarin.Forms 應用程式。 如需詳細資訊，請參閱 [macOS 需求](~/cross-platform/get-started/requirements.md#macos-requirements)。

> [!NOTE]
> 使用 Xamarin.Forms 開發應用程式需要熟悉 [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)。

## <a name="additional-platform-support"></a>其他平台支援

Xamarin.Forms 可支援 iOS、Android 和 Windows 以外的其他平台：

- Samsung Tizen
- macOS
- GTK#
- WPF

這些平台的狀態可在 [Xamarin.Forms GitHub platform support wiki](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support) (Xamarin.Forms GitHub 平台支援 Wiki) 上取得。

## <a name="android-platform-support"></a>Android 平台支援

您應該已安裝最新的 Android SDK 工具和 Android API 平台。 您可以使用 [Android SDK 管理員](~/android/get-started/installation/android-sdk.md)更新至最新版本。

此外，Android 專案的目標/編譯版本**必須**設定為*使用最新安裝的平台*。 不過，最低版本可以設定為 API 19，讓您可以繼續支援使用 Android 4.4 及更新版本的裝置。 這些值是在 [專案選項]**** 中設定的：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[專案選項] > [應用程式] > [應用程式屬性]****

![Visual Studio 中的 Android 建置選項](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[建置] > [一般]****

![選取最新的目標 Framework](requirements-images/options-general-sml.png)

**[建置] > [Android 應用程式]**

![選取適用於應用程式的最舊及目標 Android 版本](requirements-images/options-android-sml.png)

-----

## <a name="deprecated-platforms"></a>已淘汰的平台

使用 Xamarin.Forms 3.0 或更新版本時不支援這些平台：

- *Windows 8.1 / Windows Phone 8.1 WinRT*
- *Windows Phone 8 Silverlight*
