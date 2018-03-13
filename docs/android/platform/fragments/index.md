---
title: "片段"
description: "Android 3.0 引進了片段，示範如何以更具彈性的設計支援在手機和平板電腦上找到許多不同的螢幕大小。 本文將討論如何使用片段來開發 Xamarin.Android 應用程式，以及如何預先 Android 3.0 (API 層級 11) 裝置上支援片段。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 8595ecb63e49a4768120e98f41826b74c2dd43e4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="fragments"></a>片段

_Android 3.0 引進了片段，示範如何以更具彈性的設計支援在手機和平板電腦上找到許多不同的螢幕大小。本文將討論如何使用片段來開發 Xamarin.Android 應用程式，以及如何預先 Android 3.0 (API 層級 11) 裝置上支援片段。_

## <a name="fragments-overview"></a>片段概觀

在大部分平板電腦上找到較大的螢幕大小加入 Android 開發一層額外的複雜性 — 配置設計主要用於小型螢幕不一定也無法運作的較大的螢幕，反之亦然。 若要減少此導入的複雜性，Android 3.0 加入兩個新功能，*片段*和*支援封裝*。

片段可視為使用者介面模組。 它們可讓開發人員劃分成可以在不同的活動執行的外掛式、 可重複使用部分的使用者介面。 在執行階段，活動本身會決定要使用哪一個片段。

支援封裝所初次呼叫*相容性文件庫*和允許的片段，以在執行 Android Android 3.0 (API 層級 11) 之前版本的裝置上使用。

例如下, 圖說明單一應用程式如何跨不同裝置的表單係數使用片段。

[![片段平板電腦和話筒中的使用方式的圖表](images/00.png)](images/00.png#lightbox)

*片段的*包含清單，而*片段 B*包含該清單中選取之項目的詳細資料。 平板電腦上執行應用程式時，它可以顯示兩個片段上相同的活動。 話筒 （具有較小的螢幕大小） 上執行相同的應用程式時，片段會裝載於兩個不同的活動。 片段 A 和 B 片段上是相同的這兩種尺寸，但不同裝載它們的活動。

為了幫助協調和管理所有這些片段的活動，Android 導入了新的類別稱為*FragmentManager*。 每個活動都有自己的執行個體`FragmentManager`新增、 刪除和尋找裝載片段。 下圖說明片段與活動之間的關聯性：

[![說明活動、 片段管理員 中，與片段之間的關聯性的圖表](images/01.png)](images/01.png#lightbox)

在某些方面，片段可以想像為複合控制項或迷你活動。 這些組合向上 UI 段為可重複使用的模組可以使用獨立開發人員在活動中。 片段沒有檢視階層架構，就像活動 — 但不同於活動，它可以共用跨螢幕。 檢視不同片段，片段會有自己的週期。檢視則否。

一個或多個片段主機活動時，它並不直接知道本身的片段。 同樣地，片段不會直接察覺其他片段中裝載的活動。 不過，片段和活動會感知`FragmentManager`在他們的活動。 使用`FragmentManager`，很可能要取得的片段，特定執行個體的參考，然後在該執行個體上呼叫方法的活動或片段。 如此一來，活動或片段可以通訊，並與其他片段互動。

本指南包含如何使用片段，包括完整涵蓋範圍：

-   **建立片段**– 如何建立基本的片段和索引鍵必須實作的方法。
-   **片段管理與交易**– 如何在執行階段操作片段。
-   **Android 支援封裝**– 如何使用程式庫可讓片段用於 Android 的舊版本。


## <a name="requirements"></a>需求

片段是可在 Android SDK API 層級 11 (Android 3.0) 為開頭，如下列螢幕擷取畫面所示：

[![Android SDK Manager 中選取的應用程式開發介面層級](images/02.png)](images/02.png#lightbox)

片段是 Xamarin.Android 4.0 和更新版本。 Xamarin.Android 應用程式必須至少為目標 API 層級 11 (Android 3.0) 或更高版本，才能使用片段。 目標 Framework 可能會設定專案選項 中，如下所示：

[![專案選項中設定的目標 Framework 應用程式開發介面層級](images/03.png)](images/03.png#lightbox)

很可能在使用的 Android 支援封裝和 Xamarin.Android 4.2 Android 或更高版本的舊版本中使用片段。 在這一節的文件中的更詳細地討論如何執行此動作。


## <a name="related-links"></a>相關連結

- [Honeycomb 圖庫 （範例）](https://developer.xamarin.com/samples/monodroid/HoneycombGallery)
- [片段](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支援封裝](http://developer.android.com/sdk/compatibility-library.html)
- [MOTODEV 網路研討會： 導入片段](http://motodev.adobeconnect.com/p9h1aqk3ttn/)
