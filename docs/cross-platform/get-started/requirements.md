---
title: 系統需求
description: 使用 Xamarin 的必要條件
ms.prod: xamarin
ms.assetid: dd344d57-18e2-42a5-8c15-3f5be4123c72
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 08/28/2017
ms.openlocfilehash: ed9992eb162b57cd9c0dd1bc9f4abda4235bac12
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="system-requirements"></a>系統需求

_使用 Xamarin 的必要條件_

Xamarin 產品依賴 Apple 和 Google 中的平台 SDK 來定位 iOS 或 Android，所以我們的系統需求與這些平台的需求相符。 此頁面列出了 Xamarin 平台的系統相容性、建議的開發環境與 SDK 版本。

- [開發環境](#devenv)
- [macOS 需求](#mac)
- [Windows 需求](#windows)

如需關於取得軟體和所需 SDK 的資訊，請參閱[安裝指示](#install)。

<a name="devenv" />

## <a name="development-environments"></a>開發環境

此圖表顯示可使用不同開發工具和作業系統組合來建置的平台：

[!include[](~/cross-platform/includes/development-environment.md)]


> [!NOTE]
> 若要在 Windows 電腦上針對 iOS 進行開發，必須要有[可透過網路存取的 Mac 電腦](~/ios/get-started/installation/windows/connecting-to-mac/index.md)，以進行遠端編譯和偵錯。 這也同樣適用於您在 Mac 電腦上的 Windows VM 內運作 Visual Studio。

<a name="mac" />

## <a name="macos-requirements"></a>macOS 需求

使用 Mac 電腦進行 Xamarin 開發需要以下軟體和 SDK 版本。 檢查您的作業系統版本，並遵循 [Xamarin 安裝程式](#install)的指示操作。

[!include[](~/cross-platform/includes/macos-requirements.md)]

> [!NOTE]
> Xcode 可從 [developer.apple.com](https://developer.apple.com/xcode/download/) 或透過 Mac App Store 來安裝 (和更新)。

### <a name="testing--debugging-on-macos"></a>macOS 上的測試和偵錯

您可透過 USB 將 Xamarin 行動應用程式部署到實體裝置以進行測試和偵錯 (可直接在開發電腦上測試 Xamarin.Mac 應用程式；Apple Watch 應用程式首先部署至配對的 iPhone）。

[!include[](~/cross-platform/includes/macos-testing.md)]


<a name="windows" />

## <a name="windows-requirements"></a>Windows 需求

使用 Windows 電腦進行 Xamarin 開發需要以下軟體和 SDK 版本。
檢查您的作業系統版本 (請確認您使用的不是 Visual Studio 的 *Express* 版本：如果您使用的是該版本，請考慮更新至 *Community* 版本)。
Visual Studio 2015 和 2017 安裝程式包含自動安裝 Xamarin 的選項。

[!include[](~/cross-platform/includes/windows-requirements.md)]


> [!NOTE]
>
>* Xamarin for Visual Studio 支援任何版本的 Visual Studio 2015 或 2017 (Community、Professional 和 Enterprise)。
>
>* 若要在通用 Windows 平台 (UWP) 開發 Xamarin.Forms 應用程式，需要安裝有 Visual Studio 2015 或 2017 的 Windows 10。


### <a name="testing--debugging-on-windows"></a>Windows 上的測試和偵錯

您可透過 USB 將 Xamarin 行動應用程式部署到實體裝置以進行測試和偵錯 (iOS 裝置必須連線到 Mac 電腦，而不是執行 Visual Studio 的電腦)。

[!include[](~/cross-platform/includes/windows-testing.md)]


> [!NOTE]
>
>* [Windows Phone 8.1 模擬器下載](https://www.microsoft.com/download/details.aspx?id=43719)。
>* Windows Phone 10 模擬器隨附於 Visual Studio 2015 UWP SDK。

<a name="install" />

## <a name="installation-instructions"></a>安裝指示

您可從 [xamarin.com/download](http://xamarin.com/download) 下載適用於 macOS 的最新版 Xamarin。 對於 Windows，請遵循 [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 安裝指示。

我們目前產品版本的完整列表，可在[目前的版本頁面](http://developer.xamarin.com/releases/current/)中找到。 此頁面也概述了我們 Beta 和 Alpha 管道的個別產品版本 (和版本資訊的連結)。

下列是特定於每個平台的[安裝](~/cross-platform/get-started/installation/index.md)指示：

- [Xamarin.iOS](~/ios/get-started/installation/index.md)
- [Xamarin.Android](~/android/get-started/installation/index.md)
- [Xamarin.Mac](~/mac/get-started/installation.md)

這裡也提供有關 [Xamarin.Forms 需求和支援平台](~/xamarin-forms/get-started/installation.md)的其他資訊。


## <a name="related-links"></a>相關連結

- [下載 Xamarin](https://xamarin.com/download/)
- [目前版本](https://developer.xamarin.com/releases/current/)
