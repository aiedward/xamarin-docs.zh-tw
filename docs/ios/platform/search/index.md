---
title: 在 Xamarin 中搜尋 Api
description: 本文說明如何使用 iOS 9 提供的新應用程式搜尋 Api，以允許使用者在您的 Xamarin iOS 應用程式中搜尋資訊和功能。
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: ec63407189b635a9586b02e848647518aaf0170f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031555"
---
# <a name="search-apis-in-xamarinios"></a>在 Xamarin 中搜尋 Api

_本文說明如何使用 iOS 9 提供的應用程式搜尋 Api，以允許使用者在您的 Xamarin iOS 應用程式中搜尋資訊和功能。_

IOS 9 中的搜尋功能已擴充，可提供絕佳的新方式來存取 Xamarin iOS 應用程式中的資訊和功能。 藉由使用新的應用程式搜尋 Api，應用程式內容可透過焦點和 Safari 搜尋結果、遞交和 Siri 提醒與建議來進行搜尋。 這可讓使用者快速存取應用程式內的活動和資訊。

此外，新的搜尋 Api 可讓您更輕鬆地在應用程式中整合搜尋，而不需要預先搜尋的執行體驗。 因此，Apple 宣告通常需要幾個小時的時間，才能使用應用程式搜尋，讓 iOS 9 應用程式的內容可供通用搜尋。

[![](images/intro01.png "An example of iOS 9 app content universally searchable using App Search")](images/intro01.png#lightbox)

應用程式搜尋是由三個不同的 Api 所組成：

1. [**NSUserActivity**](nsuseractivity.md) -這是 Apple 在 iOS 8 中發行之遞交 API 的延伸模組。 這是用來讓使用者以公開和私下的方式來搜尋應用程式互動歷程記錄。

2. [**核心焦點**](corespotlight.md)-允許應用程式為其內容編制索引，以顯示在搜尋結果中。 它的運作方式就像是可新增和移除專案的資料庫 API，而這是在應用程式中為私人內容編制索引的最佳方式。

3. [**WebMarkup**](web-markup.md) -適用于透過 web 介面提供其內容存取權的應用程式（而不只是從應用程式內）。 Web 內容可以使用 Apple 會進行編目的特殊連結來標示，並在使用者的 iOS 9 裝置上提供應用程式的深層連結。

## <a name="selecting-an-app-search-approach"></a>選取應用程式搜尋方法

決定要執行哪一種方法，取決於您的應用程式所提供的互動類型，以及它所呈現的內容類型。

請使用下列指導方針：

- [**NSUserActivity**](nsuseractivity.md) –使用此架構可提供公開和私人內容的搜尋能力，以及在您的應用程式中搜尋能力導覽點。

- [**核心焦點**](corespotlight.md)–使用此架構可針對儲存在裝置上的私用資料提供搜尋能力。

- [**Web 標記**](web-markup.md)–您可以使用此架構來提供搜尋能力，讓應用程式不僅能從應用程式內呈現其內容，也能從應用程式的網站中取得。

每個應用程式搜尋方法都是相異的，而且可以個別使用，不過 Apple 會將其設計成搭配使用。 使用多個方法來為特定專案編制索引時，請確定您在每個方法上使用相同的**專案識別碼**，以便讓個別的連結一起工作。

使用多個方法不僅可確保使用者能找到您的內容，還能協助您改善專案在搜尋中的排名。

雖然對開發人員而言，排名程式大多是透明的，但使用者與指定專案的互動會大幅權衡此等級（例如，使用者點選連結）。
藉由提供豐富、具資訊性的專案，您可以確保使用者會 enticed 與您的內容互動，進而提高排名。

## <a name="what-content-to-index"></a>要編制索引的內容

Apple 針對要在應用程式中提供搜尋索引的內容和動作提供下列建議：

- 使用者從您的應用程式中查看、建立或策劃的任何內容。
- 應用程式內的導覽點和功能。
- 您的應用程式所顯示的新訊息、內容或其他類型的專案，最近已下載到裝置。

## <a name="app-search-enhancements"></a>應用程式搜尋增強功能

IOS 10 中的核心焦點為應用程式搜尋提供了數項增強功能，例如：

- **集結群眾力量深層連結的熱門程度（具有差異隱私權）** -提供在搜尋結果中升級深層連結應用程式內容的方法。
- **應用程式內搜尋**-使用新的 `CSSearchQuery` 類別來提供應用程式內焦點搜尋功能，類似于郵件、訊息和便箋應用程式的工作方式。
- **搜尋接續**-允許使用者在焦點或 Safari 中開始搜尋，然後開啟應用程式並繼續進行搜尋。
- **驗證結果的視覺效果**-Apple 的[應用程式搜尋 API 驗證工具](https://search.developer.apple.com/appsearch-validation-tool)現在會顯示網站標記的視覺標記法，以及 preforming 測試時的深層連結。
- **訊息應用程式映射共用**-允許提供常用的應用程式內映射，以在訊息中共用（透過訊息應用程式延伸模組），以顯示在焦點搜尋中。

若要深入瞭解，請參閱我們的[應用程式搜尋增強功能](~/ios/platform/search/app-search-enhancements.md)指南。

### <a name="proactive-suggestions"></a>主動式建議

iOS 10 提供新的方式來驅動應用程式，方法是允許系統在適當的時間內，自動向使用者顯示有用的資訊。 就像 iOS 9 能夠使用焦點、遞交和 Siri 建議，將深入搜尋新增至應用程式，使用 iOS 10，應用程式可以公開可由系統從下列位置呈現給使用者的功能：

- 應用程式切換器
- 鎖定畫面
- 了 carplay
- 地圖
- Siri 互動
- QuickType 建議 

應用程式會使用[NSUserActivity](xref:Foundation.NSUserActivity)、web 標記、核心焦點、MapKit、媒體播放機和 UIKit 等技術集合，將此功能公開給系統。

若要深入瞭解，請參閱我們的[主動式建議](~/ios/platform/search/proactive-suggestions.md)指南。

## <a name="summary"></a>總結

本文涵蓋 iOS 9 為 Xamarin iOS 應用程式提供的新搜尋 API 功能。 它涵蓋了[NSUserActivity](nsuseractivity.md)、[核心焦點](corespotlight.md)和[Web 標記](web-markup.md)方法，可為內容編制索引。 完成後，請簡短討論何時應使用指定的搜尋方法，以及應該編制索引的內容類型。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [應用程式搜尋程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
