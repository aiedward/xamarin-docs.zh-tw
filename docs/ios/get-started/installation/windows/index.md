---
title: 在 Windows 上安裝 Xamarin.iOS
description: 本文說明如何設定 Windows 電腦和 Mac 組建主機來進行 Xamarin.iOS 開發。
ms.prod: xamarin
ms.assetid: abf85d3e-a365-44a2-b1a4-6c572c7f76dd
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/16/2018
ms.openlocfilehash: e6f50a48481be3ca5c64332f5a182e44715740c0
ms.sourcegitcommit: dc6ccf87223942088ca926c0dadd5b5478c683cb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2018
---
# <a name="installing-xamarinios-on-windows"></a>在 Windows 上安裝 Xamarin.iOS

_本文說明如何設定 Windows 電腦和 Mac 組建主機來進行 Xamarin.iOS 開發。_

## <a name="overview"></a>總覽

若要在 Windows 上使用 Visual Studio 2017 建置 Xamarin.iOS 應用程式，則您需要：
 
-  已安裝 Visual Studio 2017 的 Windows 電腦。 這可以是實體或虛擬機器。
    - [Windows 系統需求](~/cross-platform/get-started/requirements.md#windows-requirements)
    
-  已設定 Apple 組建工具和 Xamarin.iOS 的網路可存取 Mac。 Visual Studio 2017 透過網路連線存取這部電腦，以使用用於編譯原生 iOS 應用程式所需的 Apple 組建工具。 
    - [Mac 系統需求](~/cross-platform/get-started/requirements.md#macos-requirements)

## <a name="setup"></a>安裝程式

若要在 Visual Studio 2017 中設定進行 Xamarin.iOS 開發，請遵循下列步驟：

1. 設定 Windows (安裝 Visual Studio 2017)

    Xamarin.iOS 會在獨立或虛擬機器上使用 Visual Studio 2017 Community、Professional 和 Enterprise Edition。
    
    - [安裝 Visual Studio 2017](~/cross-platform/get-started/installation/windows.md).

2. 設定 Mac (安裝 Xcode 和 Visual Studio for Mac)

    若要建置、偵錯和簽署 iOS 應用程式進行散發，Visual Studio 2017 必須透過網路存取已設定 Apple 開發人員工具 (Xcode) 和 Xamarin.iOS 的 Mac 組建主機。

    - [從 Mac App Store 下載和安裝 Xcode](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)。 
    - [安裝 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation)，這也會安裝 Xamarin.iOS。

    > [!NOTE] 
    > 如果您偏好不要安裝 Visual Studio for Mac，則從 [Visual Studio 2017 15.6 版](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning)開始，Visual Studio 2017 可以自動設定具有建置 Xamarin.iOS 應用程式所需軟體的 Mac 組建主機。 如需詳細資訊，請參閱[自動 Mac 佈建](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning)。

3. 與 Mac 配對 (將 Visual Studio 2017 連線至 Mac)

    若要讓 Visual Studio 2017 在 Mac 上使用 iOS 組建工具，兩部電腦必須透過網路連線。

    - [閱讀與 Mac 配對指南](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

## <a name="summary"></a>總結

本文說明如何設定 Windows 電腦和其相關聯 Mac 組建主機來進行 Xamarin.iOS 開發。

## <a name="next-steps"></a>後續步驟

- [Xamarin.iOS for Visual Studio 簡介](introduction-to-xamarin-ios-for-visual-studio.md)
- [設定 Visual Studio 2017](config-options.md)
- [裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)
