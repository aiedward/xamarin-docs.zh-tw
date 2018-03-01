---
title: "Objective C 使用者入門"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 832ad2e6d462b39df7fa4a45739e97f7a7d6e5b5
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="getting-started-with-objective-c"></a>Objective C 使用者入門

這是使用者入門的開始使用 頁面的 OBJECTIVE-C，其中涵蓋了所有支援平台的基本概念。


## <a name="requirements"></a>需求

若要使用.NET 內嵌 Objective C 中，您將需要執行 Mac:

* macOS 10.12 （利也） 或更新版本
* Xcode 8.3.2 或更新版本
* [Mono 5.0](http://www.mono-project.com/download/)

您可以安裝[Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/)來編輯和編譯 C# 程式碼。


附註：

* 舊版 macOS、 Xcode 和 Mono_可能_運作，但是未經測試，並且不受支援;
* 產生程式碼即可在 Windows 中，但它是只能夠安裝 Xcode 的位置; 的 Mac 電腦上進行編譯


## <a name="installation"></a>安裝

您的下一個步驟是下載並安裝在 mac 上的 內嵌.NET

* [封裝](https://dl.xamarin.com/embeddinator/Xamarin.Embeddinator-4000-0.2.0.79.pkg)
* [版本資訊](https://github.com/mono/Embeddinator-4000/tree/master/docs/releases)

或者它是可以建置從我們[git 儲存機制](https://github.com/mono/Embeddinator-4000/tree/objc)，請參閱[參與](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)文件的指示。

安裝程式為基礎的標準版本的安裝程式：

![安裝程式簡介](images/install1.png)
![安裝程式安裝類型](images/install2.png)
![安裝程式摘要](images/install3.png)

安裝之後透過安裝程式，開始新的終端機工作階段之後，您可以使用`objcgen`命令。
否則就能隨時執行此工具透過它的絕對路徑： `/Library/Frameworks/Xamarin.Embeddinator-4000.framework/Commands/objcgen`。

## <a name="platforms"></a>平台

Objective C 是最常用於編寫 macOS、 iOS、 tvOS 和 watchOS; 的應用程式的語言而且 embeddinator 支援所有這些平台。 每個平台使用隱含的一些主要差異，並說明這些[這裡](~/tools/dotnet-embedding/objective-c/platforms.md)。

### <a name="macos"></a>macOS

[建立 macOS 應用程式](~/tools/dotnet-embedding/get-started/objective-c/macos.md)是最簡單，因為它不包含許多額外的步驟，像是設定身分識別、 provisining 設定檔、 模擬器和裝置。 您會鼓勵開頭之前適用於 iOS 的 macOS 文件。

### <a name="ios--tvos"></a>iOS / tvOS

請確定您已設定好後再嘗試建立一個使用 embeddinator 開發 iOS 應用程式。 [指示](~/tools/dotnet-embedding/get-started/objective-c/ios.md)假設您有已經成功地建立和部署 iOS 應用程式從您的電腦。

類似於 iOS 的運作方式，只使用 Ide （Visual Studio 和 Xcode），而不是 iOS 專案中的 tvOS 專案 tvOS 支援。

> [!NOTE]
> 注意： WatchOS 支援可在未來的版本中，且將 iOS/tvOS 非常類似。


## <a name="further-reading"></a>進一步閱讀

* [.NET 內嵌 Objective C 的特定功能](~/tools/dotnet-embedding/objective-c/index.md)
* [OBJECTIVE-C 適用的最佳作法](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [.NET 內嵌限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [錯誤碼與描述](~/tools/dotnet-embedding/errors.md)
* [目標平台](~/tools/dotnet-embedding/objective-c/platforms.md)


## <a name="related-links"></a>相關連結

- [天氣範例 (iOS & macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
