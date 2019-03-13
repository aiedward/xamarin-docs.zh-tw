---
title: Objective C 使用者入門
description: 本文件說明如何開始使用.NET 內嵌 OBJECTIVE-C 它討論需求、 安裝.NET 內嵌從 NuGet 和支援的平台。
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
---

# <a name="getting-started-with-objective-c"></a>Objective C 使用者入門

這是適用於 OBJECTIVE-C，其中涵蓋了所有支援平台的基本概念開始使用 頁面。

## <a name="requirements"></a>需求

若要使用.NET 內嵌 Objective C，您將需要執行的 Mac:

* macOS 10.12 (Sierra) 或更新版本
* Xcode 8.3.2 或更新版本
* [Mono 5.0](https://www.mono-project.com/download/)

您可以安裝[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)編輯並編譯您C#程式碼。

> [!NOTE]
> * 較早版本的 mac Os、 Xcode 和 Mono_可能_運作，但是未經測試，並且不受支援
> * 產生程式碼可以在 Windows，但它是只能夠在已安裝 Xcode 的 Mac 電腦上編譯

## <a name="installing-net-embedding-from-nuget"></a>安裝來自 NuGet 的.NET 內嵌

請遵循這些[指示](~/tools/dotnet-embedding/get-started/install/install.md)安裝及設定.NET 內嵌為您的專案。

範例命令引動過程會列在[macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md)並[iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md)入門指南。

## <a name="platforms"></a>平台

OBJECTIVE-C 撰寫適用於 macOS、 iOS、 tvOS 和 watchOS 應用程式最常用的語言，.NET 內嵌支援所有這些平台。 每個平台使用表示有些[這裡說明的主要差異，以及這些](~/tools/dotnet-embedding/objective-c/platforms.md)。

### <a name="macos"></a>macOS

[建立 macOS 應用程式](~/tools/dotnet-embedding/get-started/objective-c/macos.md)是最簡單的因為它不需要多個額外的步驟，像是設定身分識別，如設定檔、 模擬器和裝置。 您可以建議之前，一個用於 iOS 與 macOS 文件開始。

### <a name="ios--tvos"></a>iOS / tvOS

請確定您已經設定好開發 iOS 應用程式，然後再嘗試建立一個使用.NET 內嵌。 [指示](~/tools/dotnet-embedding/get-started/objective-c/ios.md)假設，您已經順利建置並部署 iOS 應用程式從您的電腦。

TvOS 的支援相當於 iOS 的運作方式，只使用 Ide （Visual Studio 和 Xcode），而不是 iOS 專案中的 tvOS 專案。

> [!NOTE]
> WatchOS 即將在未來的版本，因此非常類似於 iOS/tvOS 的支援。

## <a name="further-reading"></a>進一步閱讀

* [.NET 內嵌 Objective C 的特定功能](~/tools/dotnet-embedding/objective-c/index.md)
* [適用於 OBJECTIVE-C 的最佳作法](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [.NET 內嵌限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [錯誤碼和描述](~/tools/dotnet-embedding/errors.md)
* [目標平台](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>相關連結

- [天氣範例 （iOS 和 macOS）](https://github.com/jamesmontemagno/embeddinator-weather)
