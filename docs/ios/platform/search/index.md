---
title: 在 Xamarin 中搜尋 Api
description: 本文說明如何使用 iOS 9 提供的新應用程式搜尋 Api，讓使用者搜尋您的 Xamarin iOS 應用程式內的資訊和功能。
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 63daa2f449685b39cb7d622790fd3bccd7590f62
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433211"
---
# <a name="search-apis-in-xamarinios"></a>在 Xamarin 中搜尋 Api

_本文說明如何使用 iOS 9 提供的應用程式搜尋 Api，讓使用者搜尋您的 Xamarin iOS 應用程式內的資訊和功能。_

IOS 9 中的搜尋已擴充，可提供絕佳的新方式來存取 Xamarin iOS 應用程式內的資訊和功能。 使用新的應用程式搜尋 Api，應用程式內容可透過焦點和 Safari 搜尋結果、交付和 Siri 提醒和建議來進行搜尋。 這可讓使用者快速存取應用程式內的活動和資訊。

此外，新的搜尋 Api 可讓您更輕鬆地在應用程式中整合搜尋，而不需要事先搜尋執行體驗。 因此，Apple 宣稱通常需要數小時的時間，才能使用應用程式搜尋來全面搜尋 iOS 9 應用程式的內容。

[![使用應用程式搜尋進行通用搜尋的 iOS 9 應用程式內容範例](images/intro01.png)](images/intro01.png#lightbox)

應用程式搜尋是由三個不同的 Api 所組成：

1. [**NSUserActivity**](nsuseractivity.md) -這是 Apple 在 iOS 8 中發行之遞交 API 的延伸模組。 它是用來讓使用者公開和私下) 可搜尋的應用程式互動歷程記錄。

2. [**核心焦點**](corespotlight.md) -可讓應用程式將其內容編制索引，以顯示在搜尋結果中。 它的運作方式就像是可新增和移除專案的資料庫 API，這是在應用程式中為私用內容編制索引的最佳方式。

3. [**WebMarkup**](web-markup.md) -適用于透過 web 介面提供其內容存取權的應用程式， (不只是從應用程式) 內。 Web 內容可以使用將由 Apple 編目的特殊連結來標記，並在使用者的 iOS 9 裝置上提供應用程式的深層連結。

## <a name="selecting-an-app-search-approach"></a>選取應用程式搜尋方法

決定要執行哪一種方法取決於您的應用程式所提供的互動類型，以及它所提供的內容類型。

請使用下列指引：

- [**NSUserActivity**](nsuseractivity.md) –使用此架構可為公開和私用內容提供搜尋能力，同時也可搜尋能力應用程式內的導覽點。

- [**核心焦點**](corespotlight.md) -使用此架構可針對儲存在裝置上的私用資料提供搜尋能力。

- [**Web 標記**](web-markup.md) –使用此架構可為應用程式提供搜尋能力，不僅能從應用程式內呈現其內容，也可從應用程式的網站提供。

每個應用程式搜尋方法都是不同的，而且可以個別使用，但 Apple 設計為可一起使用。 使用一個以上的方法來為特定專案編制索引時，請確定您在每個方法上都使用相同的 **專案識別碼** ，讓個別的連結能夠一起運作。

使用一個以上的方法不僅能確保使用者可以找到您的內容，還能協助改善專案在搜尋中的排名。

雖然對開發人員而言，排名程式大多是透明的，但使用者與指定專案的互動會大幅依據此等級進行權衡 (例如使用者點選連結) 。
藉由提供豐富、資訊豐富的專案，您可以確保使用者會 enticed 以與您的內容互動，進而提高其排名。

## <a name="what-content-to-index"></a>要編制索引的內容

Apple 針對在您的應用程式中提供搜尋索引的內容和動作提供下列建議：

- 使用者在您的應用程式中所查看、建立或策劃的任何內容。
- 應用程式內的導覽點和功能。
- 您的應用程式所顯示的新訊息、內容或其他類型的專案，最近已下載至裝置。

## <a name="app-search-enhancements"></a>應用程式搜尋增強功能

IOS 10 中的核心焦點提供數個應用程式搜尋增強功能，例如：

- **使用差異隱私權) 集結群眾力量深層連結熱門程度 (** -提供在搜尋結果中推廣深層連結應用程式內容的方式。
- **應用程式內搜尋** -使用新的 `CSSearchQuery` 類別來提供應用程式內焦點搜尋功能，類似于 Mail、Messages 和 Notes 應用程式的運作方式。
- **搜尋接續** -可讓使用者在焦點或 Safari 中開始搜尋，然後開啟應用程式並繼續進行搜尋。
- **驗證結果** 的視覺效果-Apple 的 [應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool) 現在會在 preforming 測試時，顯示網站標記和深層連結的視覺標記法。
- **訊息應用程式映射共用** -讓提供的熱門應用程式內映射可透過訊息應用程式延伸模組進行共用 (，) 出現在焦點搜尋中。

若要深入瞭解，請參閱我們的 [應用程式搜尋增強功能](~/ios/platform/search/app-search-enhancements.md) 指南。

### <a name="proactive-suggestions"></a>主動式建議

iOS 10 提供新的方式來推動應用程式的互動，方法是讓系統在適當的時間，自動向使用者呈現有用的資訊。 就像 iOS 9 一樣，使用焦點、遞交與 Siri 建議將深層搜尋新增至應用程式，應用程式可以在下列位置公開可由系統向使用者呈現的功能：

- 應用程式切換器
- 鎖定畫面
- CarPlay
- 地圖
- Siri 互動
- QuickType 建議 

應用程式會使用一系列的技術（例如 [NSUserActivity](xref:Foundation.NSUserActivity)、web 標記、核心焦點、MapKit、媒體播放機和 UIKit），將這項功能公開給系統。

若要深入瞭解，請參閱我們的 [主動式建議](~/ios/platform/search/proactive-suggestions.md) 指南。

## <a name="summary"></a>摘要

本文涵蓋了 iOS 9 針對 Xamarin iOS 應用程式所提供的新搜尋 API 功能。 它涵蓋 [NSUserActivity](nsuseractivity.md)、 [核心焦點](corespotlight.md) 以及用於編制內容索引的 [Web 標記](web-markup.md) 方法。 完成時，請簡短討論何時應使用指定的搜尋方法，以及應編制索引的內容類型。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [適用于開發人員的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)