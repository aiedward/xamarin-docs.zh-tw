---
title: 在 Xamarin – 內部 tvOS
description: 在 Xamarin，根據 Xamarin.iOS 描述 tvOS 的內部工作的文件。 連結內容討論組件，目標架構，以及相關 iOS 概念。
ms.prod: xamarin
ms.assetid: 8C076FED-9C03-44DE-9723-0E20272DD16B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: 0132eac4edd4ecb9f693828bd58288dfbcb1c008
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789141"
---
# <a name="tvos-in-xamarin--internals"></a>在 Xamarin – 內部 tvOS 

##  <a name="assembliesiostvosinternalsassembliesmd"></a>[組件](~/ios/tvos/internals/assemblies.md)

Xamarin 支援 Xamarin.tvOS 應用程式的組件清單。

##  <a name="target-frameworksiostvosinternalsframeworksmd"></a>[目標架構](~/ios/tvos/internals/frameworks.md)

本文涵蓋 Xamarin.tvOS，以及選取 Xamarin.tvOS 應用程式的特定目標中可用的目標架構 （基底類別程式庫） 的類型。

## <a name="related-ios-articles"></a>IOS 相關的文章

下列文件為 iOS 專屬，但與 tvOS （因為 tvOS 9 是 iOS 9 的子集）。

###  <a name="unified-apicross-platformmaciosunifiedindexmd"></a>[Unified API](~/cross-platform/macios/unified/index.md)

導入了新的統一的 Api 可讓更簡單的程式碼 Apple TV 與 iOS 之間共用程式碼基底以及提昇 64 位元應用程式開發介面和 64 位元編譯的。  

###  <a name="api-designiosinternalsapi-designindexmd"></a>[API 設計](~/ios/internals/api-design/index.md)

說明背後的 API 繫結的設計原則。

###  <a name="limitationsiosinternalslimitationsmd"></a>[限制](~/ios/internals/limitations.md)

本節說明陷阱以及限制需要注意的相關事宜 Xamarin.iOS，其中有許多 Xamarin.tvOS 適用於與。

###  <a name="linkeriosdeploy-testlinkermd"></a>[連結器](~/ios/deploy-test/linker.md)

說明連結器以確保最小的應用程式封裝中，以及如何修改它的設定和使用方式的運作方式。

###  <a name="localization-and-internationalizationiosapp-fundamentalslocalizationindexmd"></a>[因為當地語系化和國際化](~/ios/app-fundamentals/localization/index.md)

本指南涵蓋 Xamarin.iOS 應用程式，以支援國際化的編碼方式加入。

###  <a name="mtouchiosdeploy-testmtouchmd"></a>[mtouch](~/ios/deploy-test/mtouch.md)

針對 mtouch.exe 的附註和資訊。mtouch.exe 為能將您的專案建置為可供 iOS 使用之應用程式的命令列工具。

###  <a name="linking-native-librariesiosplatformnative-interopmd"></a>[連結的原生程式庫](~/ios/platform/native-interop.md)

Xamarin.iOS 支援原生 C 程式庫和 Objective C 程式庫連結。 本文將討論如何連結 Xamarin.iOS 專案具有您原生 C 程式庫。 如需執行 Objective C 程式庫相同的動作，請參閱&nbsp;[繫結 Objective C 類型](~/ios/platform/binding-objective-c/index.md)&nbsp;文件。

##  <a name="objective-c-selectorsiosinternalsobjective-c-selectorsmd"></a>[Objective C 的選取器](~/ios/internals/objective-c-selectors.md)

附註和使用方式的直接呼叫 Objective C 的選取器 （方法）。

###  <a name="systemdataiosdata-cloudsystemdatamd"></a>[System.Data](~/ios/data-cloud/system.data.md)

資訊以及使用 System.Data 存取內建 SQLite 資料庫系統的相關指示。

###  <a name="threadingiosapp-fundamentalsthreadingmd"></a>[執行緒處理](~/ios/app-fundamentals/threading.md)

使用執行緒 Xamarin.iOS 應用程式中的注意事項。

###  <a name="xib-code-generationiosinternalsxib-code-generationmd"></a>[XIB 程式碼產生](~/ios/internals/xib-code-generation.md)

如何 Visual Studio for Mac 整合使用 Xcode 的介面產生器，讓您可以使用介面產生器來設計 UI。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
