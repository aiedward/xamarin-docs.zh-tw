---
title: Objective C 使用者入門
description: 本文件說明如何開始使用.NET 嵌入目標 c。 它討論需求、 安裝 NuGet，和支援的平台的.NET 內嵌。
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: c5db0a55cc1d2597837ae5feb2c5167a0a21b494
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792976"
---
# <a name="getting-started-with-objective-c"></a>Objective C 使用者入門

這是使用者入門的開始使用 頁面的 OBJECTIVE-C，其中涵蓋了所有支援平台的基本概念。

## <a name="requirements"></a>需求

若要使用.NET 內嵌 Objective C，您需要執行 Mac:

* macOS 10.12 （利也） 或更新版本
* Xcode 8.3.2 或更新版本
* [單聲道 5.0](http://www.mono-project.com/download/)

您可以安裝[Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/)來編輯和編譯 C# 程式碼。

> [!NOTE]
> * 舊版 macOS、 Xcode 和 Mono_可能_工作，但會未經測試並不支援
> * 產生程式碼即可在 Windows 中，但它是只能夠安裝 Xcode 的 Mac 電腦上進行編譯

## <a name="installing-net-embedding-from-nuget"></a>安裝.NET NuGet 內嵌

請遵循這些[指示](~/tools/dotnet-embedding/get-started/install/install.md)安裝及設定專案的.NET 內嵌。

範例命令引動過程會列在[macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md)和[iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md)快速入門指南。

## <a name="platforms"></a>平台

Objective C 是最常用於撰寫 macOS、 iOS、 tvOS 和 watchOS 應用程式的語言;.NET 內嵌支援所有這些平台。 每個平台使用意味著一些[這裡說明的主要差異，以及這些](~/tools/dotnet-embedding/objective-c/platforms.md)。

### <a name="macos"></a>macOS

[建立 macOS 應用程式](~/tools/dotnet-embedding/get-started/objective-c/macos.md)是最簡單，因為它不包含許多額外的步驟，像是設定身分識別、 provisining 設定檔、 模擬器和裝置。 您會鼓勵開頭之前適用於 iOS 的 macOS 文件。

### <a name="ios--tvos"></a>iOS / tvOS

請確定您已設定好來開發 iOS 應用程式，然後再嘗試建立一個使用.NET 內嵌。 [指示](~/tools/dotnet-embedding/get-started/objective-c/ios.md)假設您有已經成功地建立和部署 iOS 應用程式從您的電腦。

類似於 iOS 的運作方式，只使用 Ide （Visual Studio 和 Xcode），而不是 iOS 專案中的 tvOS 專案 tvOS 支援。

> [!NOTE]
> 支援 watchOS 將可在未來的版本中，將會非常類似於 iOS/tvOS。

## <a name="further-reading"></a>進一步閱讀

* [.NET 內嵌 Objective C 的特定功能](~/tools/dotnet-embedding/objective-c/index.md)
* [OBJECTIVE-C 適用的最佳作法](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [.NET 內嵌限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [錯誤碼與描述](~/tools/dotnet-embedding/errors.md)
* [目標平台](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>相關連結

- [天氣範例 (iOS & macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
