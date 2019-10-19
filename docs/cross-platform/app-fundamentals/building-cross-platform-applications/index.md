---
title: 建置跨平台應用程式
description: 本節將討論如何使用 Xamarin 開發平臺來建立應用程式，包括瞭解 Xamarin 如何運作以設計行動應用程式，然後測試和部署至各種應用程式存放區。
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
author: conceptdev
ms.author: crdun
ms.date: 01/28/2016
ms.openlocfilehash: 2c630e58ff5a1221ec9e95099df4781041ecf2b4
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "70288048"
---
# <a name="building-cross-platform-applications"></a>建置跨平台應用程式

有兩個選項可在跨平臺行動應用程式之間共用程式碼：共用資產專案和可移植的類別庫。 [這裡會討論](~/cross-platform/app-fundamentals/code-sharing.md)這些選項;也提供[可移植類別庫](~/cross-platform/app-fundamentals/pcl.md)和[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)的詳細資訊。

<a name="Sections" />

 [概觀](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [第1部分–瞭解 Xamarin Mobile 平臺](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [第2部分-架構](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [第3部分–設定 Xamarin 跨平臺解決方案](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [第4部分–處理多個平臺](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [第5部分–實用的程式碼共用策略](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [部分 6 - 測試和 App Store 核准](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies" />

## <a name="case-studies"></a>個案研究

本檔中所述的原則會在範例應用程式*Tasky*和[Xamarin CRM](https://xamarin.com/prebuilt/#xamarincrm)等[預先建立的應用程式](https://xamarin.com/prebuilt)中進行練習。

 <a name="Tasky" />

### <a name="tasky"></a>Tasky

Tasky 是適用于 iOS、Android 和 Windows Phone 的簡單待辦事項清單應用程式。
其中示範使用 Xamarin 建立跨平臺應用程式的基本概念，並使用本機 SQLite 資料庫。

 [![tasky 清單](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [![tasky 清單](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

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
- [Xamarin 行動應用程式開發：跨平臺C#和 Xamarin。表單基本概念（Amazon）](http://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/)
- [以 Greg Shackles C# （O'Reilly）的行動裝置開發](http://shop.oreilly.com/product/0636920024002.do)
- [Scott Olson、John Hunter、Ben C# Horgen、Kenny Goers （Wrox）中的專業跨平臺行動裝置開發](http://www.wrox.com/WileyCDA/WroxTitle/Professional-Cross-Platform-Mobile-Development-in-C-.productCd-1118157702.html)
