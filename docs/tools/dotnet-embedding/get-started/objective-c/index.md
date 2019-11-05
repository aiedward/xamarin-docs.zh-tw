---
title: 開始使用目標-C
description: 本檔說明如何開始搭配使用 .NET 內嵌與目標-C。 其中討論需求、從 NuGet 安裝 .NET 內嵌，以及支援的平臺。
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: bf2f832056c164e199cfb779766f298b2b3817de
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007094"
---
# <a name="getting-started-with-objective-c"></a>開始使用目標-C

這是目標-C 的「使用者入門」頁面，其中涵蓋所有支援平臺的基本概念。

## <a name="requirements"></a>需求

若要搭配使用 .NET 內嵌與目標-C，您將需要執行的 Mac：

- macOS 10.12 （塞拉里昂）或更新版本
- Xcode 8.3.2 或更新版本
- [Mono 5。0](https://www.mono-project.com/download/)

您可以安裝[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)來編輯和編譯器C#代碼。

> [!NOTE]
>
> - 較早版本的 macOS、Xcode 和 Mono_可能可以_使用，但尚未經過測試且不受支援
> - 程式碼產生可以在 Windows 上完成，但只能在已安裝 Xcode 的 Mac 電腦上進行編譯。

## <a name="installing-net-embedding-from-nuget"></a>從 NuGet 安裝 .NET 內嵌

請遵循這些[指示](~/tools/dotnet-embedding/get-started/install/install.md)，為您的專案安裝和設定 .net 內嵌。

[MacOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md)和[iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md)快速入門手冊中列出了命令調用的範例。

## <a name="platforms"></a>平台

目標-C 是一種語言，最常用來撰寫 macOS、iOS、tvOS 和 watchOS 的應用程式; .NET 內嵌可支援所有這些平臺。 使用每個平臺意指一些[主要差異，這裡有說明](~/tools/dotnet-embedding/objective-c/platforms.md)。

### <a name="macos"></a>macOS

[建立 macOS 應用程式](~/tools/dotnet-embedding/get-started/objective-c/macos.md)最簡單，因為它不需要額外的步驟，例如設定身分識別、如設定檔、模擬器和裝置。 建議您先從適用于 iOS 的 macOS 檔開始。

### <a name="ios--tvos"></a>iOS/tvOS

請先確定您已設定好開發 iOS 應用程式，再嘗試使用 .NET 內嵌來建立它。 [下列指示](~/tools/dotnet-embedding/get-started/objective-c/ios.md)假設您已成功從電腦建立並部署 iOS 應用程式。

TvOS 的支援與 iOS 的運作方式類似，只要使用 Ide 中的 tvOS 專案（Visual Studio 和 Xcode），而不是 iOS 專案。

> [!NOTE]
> WatchOS 的支援將在未來的版本中提供，而且非常類似于 iOS/tvOS。

## <a name="further-reading"></a>進一步閱讀

- [適用于目標的 .NET 內嵌功能-C](~/tools/dotnet-embedding/objective-c/index.md)
- [目標的最佳作法-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
- [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
- [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
- [錯誤碼和描述](~/tools/dotnet-embedding/errors.md)
- [目標平臺](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>相關連結

- [氣象範例（iOS & macOS）](https://github.com/jamesmontemagno/embeddinator-weather)
