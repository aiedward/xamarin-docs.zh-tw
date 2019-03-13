---
title: 在 Windows 和 macOS 上連線至 Visual Studio 2019 Preview
description: 本指南提供如何在 Windows 上使用 Visual Studio 2019 Preview 來建置 iOS 應用程式，同時在 macOS 上使用 Visual Studio 2019 for Mac Preview 來裝載組建的相關指示。
ms.prod: xamarin
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/04/2018
ms.openlocfilehash: 1eeb79737bd712da64ce34a6b4fe83ce2823e3eb
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899361"
---
# <a name="visual-studio-2019-preview-pairing"></a>Visual Studio 2019 Preview 配對

![預覽](~/media/shared/preview.png)

[Visual Studio for Mac 2019 Preview](https://docs.microsoft.com/visualstudio/mac/install-preview) 是第一次在 macOS 上完全支援 Visual Studio 並存安裝。 這會對建置 iOS 應用程式的 Windows 開發人員建立新的情況：如果其 Mac 組建主機同時有 Visual Studio 2017 for Mac (7.x 版) 與 Visual Studio 2019 for Mac (8.0 版)，哪一個會作為其 Windows 安裝組建主機執行？

依預設，無論您在 Windows 上使用的是 Visual Studio 2017 還是 Visual Studio 2019 Preview，Visual Studio 都會在 Windows 上使用穩定版本 &ndash;(Visual Studio 2017 for Mac (7.7 版)&ndash;。

在 Windows 和 macOS 上適當地測試 Visual Studio 2019 Preview：

1. 在您的 Windows 電腦上安裝及使用 Visual Studio 2019 (16.0 版) Preview。
2. 在您的 Mac 上安裝 Visual Studio 2019 for Mac (8.0 版) Preview。
3. 在 **Visual Studio 2019 for Mac** 中：

    a. 選擇 [Visual Studio] > [檢查更新] 功能表項目。

    b. 在 [Visual Studio Update] 視窗中，從更新通道選取 [Xamarin Preview]。 將出現下列警告：

    > [!WARNING]
    > 請嘗試使用最新的 Visual Studio 2019 for Mac Preview 組建 (內含最新的 Mono 執行階段和 Xamarin SDK)。 **從此通道安裝組建將會中斷 Visual Studio 2017 for Mac 版本。** 只有當您要在 Windows 作業系統上使用 Visual Studio 2019 Preview 版本建置 Xamarin 應用程式時，才使用此通道。

    c.  按下 [切換通道] 按鈕以啟用 Preview 組建主機。

4. 遵循[為 Xamarin.iOS 開發與 Mac 配對](index.md)的指示和[疑難排解提示](troubleshooting.md) (如有必要)。