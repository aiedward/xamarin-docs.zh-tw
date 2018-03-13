---
title: "建置跨平台應用程式"
description: "本章節討論，摘要加上六個組件，如何建置使用 Xamarin 開發平台-從了解 Xamarin 的方式設計行動應用程式，然後測試並部署到不同的應用程式市集的應用程式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/28/2016
ms.openlocfilehash: 7934738a546a266036573b81e15ef9b2fa28d7b4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="sharing-code-options"></a>共用程式碼選項

有兩個選項的跨平台行動應用程式之間共用程式碼： 共用資產專案和可攜式類別庫。 這些選項[這裡所討論](~/cross-platform/app-fundamentals/code-sharing.md); 上的詳細資訊[可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)和[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)也會提供。

<a name="Sections" />

## <a name="building-cross-platform-mobile-apps"></a>建置跨平台行動應用程式

 [概觀](~/cross-platform/app-fundamentals/building-cross-platform-applications/part-0-overview.md)

 [第 1 篇 – 了解 Xamarin 的行動裝置平台](~/cross-platform/app-fundamentals/building-cross-platform-applications/part-1-understanding-the-xamarin-mobile-platform.md)

 [第 2 篇-架構](~/cross-platform/app-fundamentals/building-cross-platform-applications/part-2-architecture.md)

 [第 3 – 設定 Xamarin 的跨平台解決方案](~/cross-platform/app-fundamentals/building-cross-platform-applications/part-3-setting-up-a-xamarin-cross-platform-solution.md)

 [第 4 – 部分處理多個平台](~/cross-platform/app-fundamentals/building-cross-platform-applications/part-4-platform-divergence-abstraction-divergent-implementation.md)

 [第 5 – 實用的程式碼共用策略](~/cross-platform/app-fundamentals/building-cross-platform-applications/part-5-practical-code-sharing-strategies.md)

 [部分 6 - 測試和 App Store 核准](~/cross-platform/app-fundamentals/building-cross-platform-applications/part-6-testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies" />


## <a name="case-studies"></a>案例研究

這份文件中所述的原則會放在範例應用程式的作法是*Tasky*，以及[預先建置的應用程式](https://xamarin.com/prebuilt)像[Xamarin CRM](https://xamarin.com/prebuilt/#xamarincrm)。

 <a name="Tasky" />


### <a name="tasky"></a>Tasky

Tasky 是簡單的待辦事項清單應用程式進行 iOS、 Android 和 Windows Phone。
它會示範建立使用 Xamarin 的跨平台應用程式的基本概念，並使用本機的 SQLite 資料庫。

 [![tasky 清單](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [ ![tasky 清單](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

讀取[Tasky 案例研究](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)。


## <a name="summary"></a>總結

本節介紹 Xamarin 的應用程式開發工具，並討論如何建置多個行動平台為目標的應用程式。

它涵蓋多層式的架構跨多平台，供重複使用該結構程式碼，並描述可用於該架構的不同軟體模式。

常見的應用程式函式 （例如檔案和網路作業） 和它們如何建置跨平台方式將提供範例。

最後，它簡要討論測試，並提供個案研究，以將這些原則放入動作的參考。



## <a name="related-links"></a>相關連結

- [共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)
- [案例研究：Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Tasky 範例應用程式 (github)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Xamarin 的行動裝置應用程式開發： 跨平台 C# 和 Xamarin.Forms 的基本概念](http://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/))
- [使用 C# 所 Greg 行動應用程式開發 Shackles (O'Reilly)](http://shop.oreilly.com/product/0636920024002.do)
- [以 C# Scott Olson、 John 獵人、 Ben Horgen，Kenny 常客 (Wrox) 專業人員跨平台行動開發](http://www.wiley.com/WileyCDA/WileyTitle/productCd-1118157702.html)
