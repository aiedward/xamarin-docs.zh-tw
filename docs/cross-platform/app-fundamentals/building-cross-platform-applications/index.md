---
title: 建置跨平台應用程式
description: 本章節討論，摘要加上六個組件、 如何建置使用 Xamarin 開發平台 – 從了解 Xamarin 來設計行動應用程式，然後測試和部署到各種不同的應用程式存放區的運作方式的應用程式。
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
author: asb3993
ms.author: amburns
ms.date: 01/28/2016
ms.openlocfilehash: 683400e24844308769f0562552641216d45e7d11
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61276330"
---
# <a name="building-cross-platform-applications"></a>建置跨平台應用程式

有兩個選項可跨平台行動應用程式間共用程式碼：資產專案或可攜式類別程式庫共用。 這些選項[此處所討論](~/cross-platform/app-fundamentals/code-sharing.md); 更多有關[可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)並[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)也會提供。

<a name="Sections" />

 [概觀](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [第 1 篇 – 了解 Xamarin 行動裝置平台](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [第 2 篇-架構](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [第 3-設定 Xamarin 跨平台解決方案](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [第 4 – 部分處理多個平台](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [部分 5-實用的程式碼共用策略](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [部分 6 - 測試和 App Store 核准](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies" />

## <a name="case-studies"></a>案例研究

本文件中所述的準則會放在範例應用程式的作法*Tasky*，以及[預先建置的應用程式](https://xamarin.com/prebuilt)像[Xamarin CRM](https://xamarin.com/prebuilt/#xamarincrm)。

 <a name="Tasky" />

### <a name="tasky"></a>Tasky

Tasky 是適用於 iOS、 Android 和 Windows Phone 的簡易待辦事項清單應用程式。
它示範如何建立使用 Xamarin 的跨平台應用程式的基本概念，並使用本機 SQLite 資料庫。

 [![tasky 清單](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [ ![tasky 清單](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

讀取[Tasky 案例研究](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)。

## <a name="summary"></a>總結

本節介紹如何使用 Xamarin 的應用程式開發工具，並討論如何建置多個行動平台為目標的應用程式。

它會跨多個平台上涵蓋多層式的架構及重複使用該結構程式碼，並說明可用於該架構的不同軟體模式。

常見的應用程式函式 （例如檔案和網路作業） 和它們如何建置跨平台的方式將提供範例。

最後，它簡短討論測試，並提供個案研究，以將這些原則放入動作的參考。

## <a name="related-links"></a>相關連結

- [共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)
- [案例研究：Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Tasky 範例應用程式 (github)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Xamarin 行動裝置應用程式開發：跨平台C#和 Xamarin.Forms 基本概念 (Amazon)](http://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/)
- [使用行動裝置開發C#由 Greg Shackles (O'Reilly)](http://shop.oreilly.com/product/0636920024002.do)
- [專業的跨平台行動開發在C#Scott Olson、 John Hunter、 Ben Horgen，Kenny 常客 (Wrox)](http://www.wrox.com/WileyCDA/WroxTitle/Professional-Cross-Platform-Mobile-Development-in-C-.productCd-1118157702.html)
