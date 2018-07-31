---
title: 在 Xamarin.iOS 中的搜尋 Api
description: 這篇文章將說明如何使用 iOS 9 所提供的新應用程式搜尋 Api，可讓使用者搜尋資訊與您的 Xamarin.iOS 應用程式內的功能。
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 4e73e1bc34df8628790a3734e5b3b32a687fdf14
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351648"
---
# <a name="search-apis-in-xamarinios"></a>在 Xamarin.iOS 中的搜尋 Api

_這篇文章將說明如何使用 iOS 9 所提供的應用程式搜尋 Api，可讓使用者搜尋資訊與您的 Xamarin.iOS 應用程式內的功能。_

搜尋已展開於 iOS 9，提供絕佳的新方法，來存取資訊和 Xamarin.iOS 應用程式內的功能。 使用新的應用程式搜尋 Api，應用程式內容會透過焦點以及 Safari 搜尋結果中，遞移式及 Siri 提醒及建議的搜尋。 這可讓使用者快速存取活動和您的應用程式內的資訊。

此外，新的搜尋服務 Api 讓您更輕鬆地將搜尋-在您的應用程式，而不需要實作的先前的搜尋體驗整合。 因為這個緣故，Apple 會宣告它通常只需要幾小時的時間讓 iOS 9 應用程式的內容都可供搜尋使用應用程式內搜尋。

[![](images/intro01.png "IOS 9 應用程式內容都可使用應用程式搜尋搜尋範例")](images/intro01.png#lightbox)

應用程式搜尋是由三個不同的 Api 組成：

1. [**NSUserActivity** ](nsuseractivity.md) -這是 Apple 在 iOS 8 中所發行的遞移式 api 延伸模組。 它用來讓應用程式互動的歷程記錄可供搜尋，公開和私密） 使用者。

2. [**核心焦點**](corespotlight.md) -可讓應用程式來搜尋結果中顯示其內容編製索引。 其運作方式類似資料庫 API，可以新增和移除項目，以及它是在應用程式內的私人內容索引的最佳方式。

3. [**WebMarkup** ](web-markup.md) -提供透過 web 介面及其內容的存取權的應用程式 (不只是從應用程式內)。 Web 內容可以標記的特殊連結，將由 Apple 編目，並提供您使用者的 iOS 9 裝置上的應用程式的深層連結。

## <a name="selecting-an-app-search-approach"></a>選取的應用程式搜尋方法

決定哪一種來實作這些方法取決於您的應用程式所提供的互動的型別和它所呈現的內容類型。

使用下列指導方針：

- [**NSUserActivity** ](nsuseractivity.md) – 使用此架構以供公用和私人內容和也搜尋能力，您的應用程式內的瀏覽點的搜尋能力。

- [**核心焦點**](corespotlight.md) – 使用此架構提供私用的資料儲存在裝置上的搜尋能力。

- [**Web 標記**](web-markup.md) – 使用此架構的應用程式，顯示其內容不只是從應用程式，但從應用程式的網站也提供搜尋能力。

每個應用程式搜尋方法就會不同，而且可以是用來個別，不過 Apple 將它們設計為可一起搭配。 使用多個方法時要編製索引的特定項目，請確定您使用相同**項目 ID**上每一種方法，因此該人員連結運作在一起。

使用一個以上的方法不只可確保您的內容會找到使用者，但也有助於改善您的項目排名從內搜尋。

中的排名程序時開發人員，多半是一目了然使用者指定的項目與互動重大量此陣序規範 （例如使用者點選的連結） 時。
藉由提供豐富且有意義的項目，您可以確保，使用者會被 enticed 互動與您的內容，因此提高其排名。

## <a name="what-content-to-index"></a>內容索引

Apple 提供下列建議哪些內容和動作可提供您的應用程式中的搜尋索引：

 - 檢視、 建立或策劃使用者透過應用程式內的任何內容。
 - 瀏覽點和應用程式內的功能。
 - 項目，例如新訊息、 內容或其他類型的應用程式所顯示項目，最近下載到裝置。

## <a name="app-search-enhancements"></a>應用程式搜尋增強功能

在 iOS 10 中的核心焦點搜尋應用程式提供數個增強功能，例如：

- **群眾外包深層連結的熱門程度 （含差異隱私權）** -可用來提升搜尋結果中的深層連結應用程式內容。
- **應用程式內搜尋**-使用新`CSSearchQuery`類別，以提供應用程式內的郵件、 訊息和資訊的應用程式的運作方式類似的 Spotlight 搜尋能力。
- **搜尋接續**-可讓使用者在焦點或 Safari 中開始搜尋，然後開啟應用程式，並繼續搜尋。
- **驗證結果的視覺效果**-Apple[應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool)preforming 測試時，現在會顯示網站的標記和深層連結的視覺表示法。
- **訊息應用程式映像共用**-讓 Spotlight 搜尋中出現共用 （透過訊息應用程式的擴充功能） 的訊息中提供的熱門應用程式內映像。

若要深入了解，請參閱我們[應用程式搜尋增強功能](~/ios/platform/search/app-search-enhancements.md)指南。

### <a name="proactive-suggestions"></a>主動式建議

iOS 10 會呈現駕駛 engagement 應用程式的新方式讓系統主動很有幫助的資訊會自動向使用者呈現在適當的時間。 就如同 iOS 9 會提供能夠將深層搜尋新增至應用程式與 iOS 10 應用程式可以公開 （expose) 功能，可以呈現給使用者，從系統中的下列位置使用 Spotlight、 遞移式及 Siri 建議：

- 應用程式切換器
- 在鎖定畫面
- CarPlay
- 地圖
- Siri 互動
- QuickType 建議 

應用程式未公開這項功能才能使用的技術集合，例如系統[NSUserActivity](https://developer.xamarin.com/api/type/Foundation.NSUserActivity/)，網頁標記中，核心焦點、 MapKit、 Media Player 和 UIKit。

若要深入了解，請參閱我們[主動式建議](~/ios/platform/search/proactive-suggestions.md)指南。

## <a name="summary"></a>總結

這篇文章已涵蓋新的 Xamarin.iOS 應用程式提供的搜尋服務 API 功能的 iOS 9。 它涵蓋[NSUserActivity](nsuseractivity.md)，[核心焦點](corespotlight.md)並[Web 標記](web-markup.md)在編製內容索引的方法。 它已完成，但何時應該使用給定的搜尋方法和類型的內容應該是什麼的簡短討論編製索引。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
