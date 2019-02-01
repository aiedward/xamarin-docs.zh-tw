---
title: 系統需求
description: 本文件列出在 Mac 和 Windows 電腦上使用 Xamarin 建置應用程式的系統需求。 它也連結到安裝指示。
ms.prod: xamarin
ms.assetid: dd344d57-18e2-42a5-8c15-3f5be4123c72
author: conceptdev
ms.author: crdun
ms.date: 07/24/2018
ms.openlocfilehash: 87c3a189851db66e630095eae31434a590050154
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292268"
---
# <a name="system-requirements"></a>系統需求

Xamarin 產品依賴 Apple 和 Google 中的平台 SDK 來定位 iOS 或 Android，所以我們的系統需求與這些平台的需求相符。 此頁面列出了 Xamarin 平台的系統相容性、建議的開發環境與 SDK 版本。

如需關於取得軟體和所需 SDK 的詳細資訊，請參閱[安裝指示](#installation-instructions)。

## <a name="development-environments"></a>開發環境

此圖表顯示可使用不同開發工具和作業系統組合來建置的平台：

[!include[](~/cross-platform/includes/development-environment.md)]

> [!NOTE]
> 若要在 Windows 電腦上針對 iOS 進行開發，必須要有[可透過網路存取的 Mac 電腦](~/ios/get-started/installation/windows/connecting-to-mac/index.md)，以進行遠端編譯和偵錯。 這也同樣適用於您在 Mac 電腦上的 Windows VM 內運作 Visual Studio。

## <a name="macos-requirements"></a>macOS 需求

使用 Mac 電腦進行 Xamarin 開發需要以下軟體和 SDK 版本。 檢查您的作業系統版本，並遵循 [Xamarin 安裝程式](#installation-instructions)的指示操作。

[!include[](~/cross-platform/includes/macos-requirements.md)]

> [!NOTE]
> Xcode 可從 [developer.apple.com](https://developer.apple.com/xcode/download/) 或透過 Mac App Store 來安裝 (和更新)。

### <a name="testing--debugging-on-macos"></a>macOS 上的測試和偵錯

- 您可透過 USB 將 Xamarin 行動應用程式部署到實體裝置以進行測試和偵錯 (Apple Watch 應用程式首先部署至配對的 iPhone)。
- 您可以直接在開發電腦上測試 Xamarin.Mac 應用程式。

[!include[](~/cross-platform/includes/macos-testing.md)]

> [!WARNING]
> 即將推出的 Xamarin.Mac 4.8 版只會支援 macOS 10.9 或更高版本。
> 舊版 Xamarin.Mac 支援 macOS 10.7 或更高版本，但這些較舊的 macOS 版本缺乏足夠的 TLS 基礎結構，無法支援 TLS 1.2。 若要以 macOS 10.7 或 10.8 為目標，請使用 Xamarin.Mac 4.6 或更舊版本。

## <a name="windows-requirements"></a>Windows 需求

使用 Windows 電腦進行 Xamarin 開發需要以下軟體和 SDK 版本。
檢查您的作業系統版本 (請確認您使用的不是 Visual Studio 的 *Express* 版本：如果您使用的是該版本，請考慮更新至 *Community* 版本)。
Visual Studio 2017 安裝程式包括自動安裝 Xamarin 的選項 ([使用 .NET 進行行動開發])。

[!include[](~/cross-platform/includes/windows-requirements.md)]

> [!NOTE]
> - Xamarin for Visual Studio 支援任何版本的 Visual Studio 2017 (Community、Professional 和 Enterprise)。
> - 若要在通用 Windows 平台 (UWP) 開發 Xamarin.Forms 應用程式，需要已安裝 Visual Studio 2017 的 Windows 10。

### <a name="testing--debugging-on-windows"></a>Windows 上的測試和偵錯

您可透過 USB 或無線方式將 Xamarin 行動應用程式部署到實體裝置以進行測試和偵錯 (iOS 裝置必須連線到 Mac 電腦，而不是執行 Visual Studio 的電腦)。

[!include[](~/cross-platform/includes/windows-testing.md)]

## <a name="installation-instructions"></a>安裝指示

您可從 [xamarin.com/download](http://xamarin.com/download) 下載適用於 macOS 的最新版 Xamarin。 對於 Windows，請遵循 [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 安裝指示。

我們目前產品版本的完整列表，可在[目前的版本頁面](http://developer.xamarin.com/releases/current/)中找到。 此頁面也概述了我們 Beta 和 Alpha 管道的個別產品版本 (和版本資訊的連結)。

下列是特定於每個平台的[安裝](~/cross-platform/get-started/installation/index.md)指示：

- [Xamarin.iOS](~/ios/get-started/installation/index.md)
- [Xamarin.Android](~/android/get-started/installation/index.md)
- [Xamarin.Mac](~/mac/get-started/installation.md)

這裡也提供有關 [Xamarin.Forms 需求和支援平台](~/get-started/installation.md)的其他資訊。

## <a name="related-links"></a>相關連結

- [下載 Xamarin](https://visualstudio.microsoft.com/xamarin/)
- [目前版本](https://developer.xamarin.com/releases/current/)
