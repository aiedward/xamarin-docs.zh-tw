---
title: Xamarin 中的 tvOS-內部
description: 描述 Xamarin 上 tvOS 的內部運作方式，以 Xamarin 為基礎的檔。 連結內容討論群組件、目標 framework 和相關的 iOS 概念。
ms.prod: xamarin
ms.assetid: 8C076FED-9C03-44DE-9723-0E20272DD16B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 850ddad9c1d83d15f5116bfb4efc58e42164c20d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434988"
---
# <a name="tvos-in-xamarin-internals"></a>Xamarin 中的 tvOS-內部 

## <a name="assemblies"></a>[組件](~/ios/tvos/internals/assemblies.md)

Xamarin 針對您的 tvOS 應用程式所支援的元件清單。

## <a name="target-frameworks"></a>[目標 Framework](~/ios/tvos/internals/frameworks.md)

本文涵蓋 tvOS 中可用的目標 Framework 類型 (基礎類別庫) 可在中使用，以及為您的 tvOS 應用程式選取特定目標的含意。

## <a name="related-ios-articles"></a>相關的 iOS 文章

下列文章專屬於 iOS，但與 tvOS (相關，因為 tvOS 9 是 iOS 9) 的子集。

### <a name="unified-api"></a>[Unified API](~/cross-platform/macios/unified/index.md)

引進新的整合 Api，可讓您更輕鬆地在 Apple TV 與 iOS 程式碼基底之間共用程式碼，以及引進64位 Api 和64位編譯的支援。  

### <a name="api-design"></a>[API 設計](~/ios/internals/api-design/index.md)

說明 API 系結背後的設計原則。

### <a name="limitations"></a>[限制](~/ios/internals/limitations.md)

本節說明關於 Xamarin 的問題和限制，其中許多都適用于 Xamarin. tvOS。

### <a name="linker"></a>[連結器](~/ios/deploy-test/linker.md)

說明連結器的運作方式，以確保可能的最小應用程式套件，以及如何修改其設定和使用方式。

### <a name="localization-and-internationalization"></a>[當地語系化和國際化](~/ios/app-fundamentals/localization/index.md)

本指南涵蓋將編碼新增至支援國際化的 Xamarin iOS 應用程式。

### <a name="mtouch"></a>[mtouch](~/ios/deploy-test/mtouch.md)

針對 mtouch.exe 的附註和資訊。mtouch.exe 為能將您的專案建置為可供 iOS 使用之應用程式的命令列工具。

### <a name="linking-native-libraries"></a>[連結原生程式庫](~/ios/platform/native-interop.md)

Xamarin 支援使用原生 C 程式庫和目標 C 程式庫來連結。 本檔討論如何連結您的原生 C 程式庫與您的 Xamarin. iOS 專案。 如需針對目標 C 程式庫執行相同操作的詳細資訊，請參閱系結 &nbsp; [目標-c 類型](~/ios/platform/binding-objective-c/index.md) &nbsp; 檔。

## <a name="objective-c-selectors"></a>[目標-C 選取器](~/ios/internals/objective-c-selectors.md)

直接) 呼叫 (方法的附注和使用方式。

### <a name="systemdata"></a>[System.Data](~/ios/data-cloud/system.data.md)

使用 System. Data 來存取內建 SQLite 資料庫系統的資訊和指示。

### <a name="threading"></a>[執行緒](~/ios/app-fundamentals/threading.md)

在 Xamarin iOS 應用程式中使用執行緒的注意事項。

### <a name="xib-code-generation"></a>[XIB 程式碼產生](~/ios/internals/xib-code-generation.md)

Visual Studio for Mac 如何與 Xcode 的 Interface Builder 整合，讓您能夠使用 Interface Builder 來設計 UI。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)