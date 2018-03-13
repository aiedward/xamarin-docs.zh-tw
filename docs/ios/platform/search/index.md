---
title: "新的搜尋應用程式開發介面"
description: "本文涵蓋使用 iOS 9 所提供的新應用程式搜尋 Api 可讓使用者搜尋的資訊及 Xamarin.iOS 應用程式內的功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 6ec8cb9b6fdb391afcb8f9baaa641da5aec38f6d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="new-search-apis"></a>新的搜尋應用程式開發介面

_本文涵蓋使用 iOS 9 所提供的新應用程式搜尋 Api 可讓使用者搜尋的資訊及 Xamarin.iOS 應用程式內的功能。_

搜尋已展開於 iOS 9 提供絕佳的新方法，以存取資訊和 Xamarin.iOS 應用程式內的功能。 使用新的應用程式搜尋 Api，應用程式的內容會透過精選和 Safari 搜尋結果中，遞交 Siri 提醒和建議的搜尋。 這可讓使用者快速存取活動和深層應用程式中的資訊。

此外，新的搜尋應用程式開發介面讓您更輕鬆地整合應用程式不在前一個搜尋實作體驗中的搜尋。 因為這個緣故，Apple 的宣告，通常需要花費幾小時的時間讓通用搜尋使用應用程式搜尋 iOS 9 應用程式的內容。

[![](images/intro01.png "IOS 9 通用搜尋使用的搜尋應用程式的應用程式內容的範例")](images/intro01.png#lightbox)

應用程式內搜尋是三個不同的應用程式開發介面所組成：

1. [**NSUserActivity** ](nsuseractivity.md) -這是發行 Apple iOS 8 的遞移式 api 延伸模組。 它用來使應用程式互動的歷程記錄可供搜尋，公開或私人） 使用者。

2. [**核心精選**](corespotlight.md) -可讓應用程式的索引在搜尋結果中顯示其內容。 其運作方式類似資料庫 API 加入和移除項目，而且很應用程式中的私人內容索引的最佳方式。

3. [**WebMarkup** ](web-markup.md) -如果要讓應用程式可讓您透過 web 介面，其內容的存取 (不只會從應用程式內)。 Web 內容可以標示出，而對於將編目 Apple，並提供深層連結應用程式使用者的 iOS 9 裝置上的特殊連結。

## <a name="selecting-an-app-search-approach"></a>選取應用程式內搜尋方法

決定哪種方法來實作取決於您的應用程式所提供的互動的類型，它會呈現的內容類型。

使用下列指導方針：

- [**NSUserActivity** ](nsuseractivity.md) – 使用此架構以提供同時公用和私用的內容以及您的應用程式內的瀏覽點的搜尋能力的搜尋能力。

- [**核心精選**](corespotlight.md) – 使用此架構提供儲存在裝置上的私用資料的搜尋能力。

- [**Web 標記**](web-markup.md) – 使用這個架構的應用程式的呈現其內容，不只會從應用程式，但從應用程式的網站也提供搜尋能力。

使用每個應用程式搜尋方法各不相同，而且可以是個別，不過 Apple 設計，它們共同運作。 當使用多個方法來編製索引的特定項目，請務必使用相同**項目 ID**上每一種方法，因此個別連結的工作。

使用一個以上的方法不只可確保會由終端使用者找到您的內容，但也可協助改善您的項目排名從搜尋中。

中的排名程序時幾近透明的程式開發人員，指定的項目與使用者互動充分權衡有很大時這個陣序 （例如使用者點選連結）。
藉由提供豐富且有意義的項目，您可以確保，使用者將會 enticed 互動與您的內容，因此提高其排名。

## <a name="what-content-to-index"></a>內容索引

Apple 提供哪些內容和動作來提供應用程式中的搜尋索引下列建議：

 - 檢視、 建立或 curated 使用者透過應用程式內的任何內容。
 - 瀏覽點和應用程式內的功能。
 - 項目，例如新的訊息、 內容或其他類型的項目顯示您的應用程式最近下載到裝置。

## <a name="app-search-enhancements"></a>應用程式搜尋的增強功能

在 iOS 10 中的核心精選應用程式搜尋提供數個增強功能，例如：

- **（與差異隱私權） Crowdsourced 深層連結普及率**-提供方法來升級深層連結應用程式在搜尋結果中的內容。
- **應用程式內搜尋**-使用新`CSSearchQuery`類別，以提供應用程式內 Spotlight 搜尋功能類似郵件、 訊息和附註的應用程式的運作方式。
- **搜尋接續**-可讓使用者在精選或 Safari，開始搜尋，然後開啟應用程式，並繼續搜尋。
- **驗證結果的視覺效果**-Apple[應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool)preforming 測試時，現在會顯示網站的標記和深層連結的視覺表示法。
- **訊息應用程式映像共用**-可讓共用中 （透過訊息應用程式擴充功能） 的訊息出現在 Spotlight 搜尋所提供的常用在應用程式映像。

若要進一步了解，請參閱我們[應用程式搜尋的增強功能](~/ios/platform/search/app-search-enhancements.md)指南。

### <a name="proactive-suggestions"></a>主動式建議

iOS 10 會呈現驅動 engagement 應用程式的新方式讓系統主動有用的資訊會自動向使用者呈現在適當的時間。 就像 iOS 9 提供新增的 iOS 10 應用程式可以公開從系統中的下列位置可以呈現給使用者的功能與使用精選、 傳遞和 Siri 建議的應用程式深層的搜尋功能：

- 應用程式切換器
- 鎖定畫面
- CarPlay
- 地圖
- Siri 互動
- QuickType 建議 

應用程式公開此功能，以使用技術的集合，例如系統[NSUserActivity](https://developer.xamarin.com/api/type/Foundation.NSUserActivity/)，網頁標記中，核心精選、 MapKit、 Media Player 和 UIKit。

若要進一步了解，請參閱我們[主動式建議](~/ios/platform/search/proactive-suggestions.md)指南。

## <a name="summary"></a>總結

這篇文章已涵蓋的新功能搜尋 API Xamarin.iOS 應用程式提供該 iOS 9。 它涵蓋[NSUserActivity](nsuseractivity.md)，[核心精選](corespotlight.md)和[Web 標記](web-markup.md)編製內容索引的方法。 它已完成，但何時應該使用給定的搜尋方法和類型的內容應該是什麼的簡短討論編製索引。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式內搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
