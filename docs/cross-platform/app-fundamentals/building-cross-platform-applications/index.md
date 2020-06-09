---
title: 建置跨平台應用程式
description: 本節將討論如何使用 Xamarin 開發平臺來建立應用程式，包括瞭解 Xamarin 如何運作以設計行動應用程式，然後測試和部署至各種應用程式存放區。
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
author: davidortinau
ms.author: daortin
ms.date: 01/28/2016
ms.openlocfilehash: 2f7d09405f90ac9fc4c3ce80181baafa447df637
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571232"
---
# <a name="building-cross-platform-applications"></a>建置跨平台應用程式

有兩個選項可在跨平臺行動應用程式之間共用程式碼：共用資產專案和可移植的類別庫。 [這裡會討論](~/cross-platform/app-fundamentals/code-sharing.md)這些選項;也提供[可移植類別庫](~/cross-platform/app-fundamentals/pcl.md)和[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)的詳細資訊。

<a name="Sections"></a>

 [概觀](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [第1部分–瞭解 Xamarin Mobile 平臺](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [第2部分-架構](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [第3部分–設定 Xamarin 跨平臺解決方案](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [第4部分–處理多個平臺](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [第5部分–實用的程式碼共用策略](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [第6部分-測試和 App Store 核准](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies"></a>

## <a name="case-studies"></a>個案研究

本檔中所述的原則會在範例應用程式*Tasky*和[Xamarin CRM](https://xamarin.com/prebuilt/#xamarincrm)等[預先建立的應用程式](https://xamarin.com/prebuilt)中進行練習。

 <a name="Tasky"></a>

### <a name="tasky"></a>Tasky

Tasky 是適用于 iOS、Android 和 Windows Phone 的簡單待辦事項清單應用程式。
其中示範使用 Xamarin 建立跨平臺應用程式的基本概念，並使用本機 SQLite 資料庫。

 [ ![ tasky 清單](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [ ![ tasky 清單](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

閱讀[Tasky 案例研究](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)。

## <a name="summary"></a>總結

本節介紹 Xamarin 的應用程式開發工具，並討論如何建立以多個行動平臺為目標的應用程式。

它涵蓋了一種分層架構，可跨多個平臺重複使用程式碼，並說明可在該架構內使用的不同軟體模式。

範例會提供一般應用程式函式（如檔案和網路作業），以及如何以跨平臺方式建立它們。

最後，它會簡短討論測試，並提供案例研究的參考，將這些準則納入動作中。

## <a name="related-links"></a>相關連結

- [共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)
- [案例研究：Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Tasky 範例應用程式（github）](https://docs.microsoft.com/samples/xamarin/mobile-samples/taskyportable/)
- [Xamarin 行動應用程式開發：跨平臺 c # 和 Xamarin. 表單基本概念（Amazon）](https://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/)
- [透過 Greg Shackles 的 c # 進行行動開發（O'Reilly）](https://shop.oreilly.com/product/0636920024002.do)
