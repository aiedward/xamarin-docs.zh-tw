---
title: tvOS 中 Xamarin – 內部
description: 描述 tvOS 的內部運作，xamarin，以 Xamarin.iOS 為基礎的文件。 連結內容討論的組件的目標 framework，，和相關的 iOS 概念。
ms.prod: xamarin
ms.assetid: 8C076FED-9C03-44DE-9723-0E20272DD16B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: 3eca425e38a01053f084ddbc5ad2edb93f6f6427
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61395114"
---
# <a name="tvos-in-xamarin-internals"></a>tvOS 中 Xamarin – 內部 

##  <a name="assembliesiostvosinternalsassembliesmd"></a>[組件](~/ios/tvos/internals/assemblies.md)

Xamarin 支援 Xamarin.tvOS 應用程式的組件清單。

##  <a name="target-frameworksiostvosinternalsframeworksmd"></a>[目標架構](~/ios/tvos/internals/frameworks.md)

本文章涵蓋 Xamarin.tvOS 和選取 Xamarin.tvOS 應用程式的特定目標的含意中可用的目標架構 （基底類別程式庫） 的類型。

## <a name="related-ios-articles"></a>相關的 iOS 文件

下列文章都為 iOS 專屬，但與 tvOS （因為 tvOS 9 的 iOS 9 子集）。

###  <a name="unified-apicross-platformmaciosunifiedindexmd"></a>[Unified API](~/cross-platform/macios/unified/index.md)

導入了新的統一的 Api 可讓 Apple TV 與 iOS 之間共用簡單的程式碼程式碼基底以及引進支援 64 位元 Api 和 64 位元編譯的。  

###  <a name="api-designiosinternalsapi-designindexmd"></a>[API 設計](~/ios/internals/api-design/index.md)

說明 API 的繫結背後的設計原則。

###  <a name="limitationsiosinternalslimitationsmd"></a>[限制](~/ios/internals/limitations.md)

本章節將說明陷阱，以及限制需要注意的方面 Xamarin.iOS，其中有許多是 Xamarin.tvOS 適用於使用。

###  <a name="linkeriosdeploy-testlinkermd"></a>[連結器](~/ios/deploy-test/linker.md)

說明連結器以確保最小的可能的應用程式封裝，以及如何修改其設定和使用方式的運作方式。

###  <a name="localization-and-internationalizationiosapp-fundamentalslocalizationindexmd"></a>[當地語系化和國際化](~/ios/app-fundamentals/localization/index.md)

本指南涵蓋新增至 Xamarin.iOS 應用程式以支援國際化的編碼方式。

###  <a name="mtouchiosdeploy-testmtouchmd"></a>[mtouch](~/ios/deploy-test/mtouch.md)

針對 mtouch.exe 的附註和資訊。mtouch.exe 為能將您的專案建置為可供 iOS 使用之應用程式的命令列工具。

###  <a name="linking-native-librariesiosplatformnative-interopmd"></a>[連結的原生程式庫](~/ios/platform/native-interop.md)

Xamarin.iOS 可支援使用原生 C 程式庫和 OBJECTIVE-C 程式庫連結。 本文件討論如何連結您原生的 C 程式庫與您的 Xamarin.iOS 專案。 如需有關執行相同的 Objective C 程式庫，請參閱&nbsp;[繫結 Objective C 類型](~/ios/platform/binding-objective-c/index.md)&nbsp;文件。

##  <a name="objective-c-selectorsiosinternalsobjective-c-selectorsmd"></a>[OBJECTIVE-C 選取器](~/ios/internals/objective-c-selectors.md)

附註和使用方式的直接呼叫 OBJECTIVE-C 選取器 （方法）。

###  <a name="systemdataiosdata-cloudsystemdatamd"></a>[System.Data](~/ios/data-cloud/system.data.md)

資訊和使用 System.Data 存取內建的 SQLite 資料庫系統上的指示。

###  <a name="threadingiosapp-fundamentalsthreadingmd"></a>[執行緒處理](~/ios/app-fundamentals/threading.md)

使用 Xamarin.iOS 應用程式中執行緒的注意事項。

###  <a name="xib-code-generationiosinternalsxib-code-generationmd"></a>[XIB 程式碼產生](~/ios/internals/xib-code-generation.md)

如何與 Xcode 的 Interface Builder 可讓您使用 Interface Builder 來設計 UI 整合 Visual Studio for Mac。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
