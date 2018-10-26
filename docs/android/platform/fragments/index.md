---
title: 片段
description: Android 3.0 導入片段，示範如何在手機和平板電腦上找到的許多不同的螢幕大小支援更有彈性的設計。 本文將介紹如何使用片段來開發 Xamarin.Android 應用程式，以及如何預先 Android 3.0 (API 層級 11) 裝置上支援片段。
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: bc4441c7ee0c36af990297bad1b0c2f0e77123f3
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113283"
---
# <a name="fragments"></a>片段

_Android 3.0 導入片段，示範如何在手機和平板電腦上找到的許多不同的螢幕大小支援更有彈性的設計。本文將介紹如何使用片段來開發 Xamarin.Android 應用程式，以及如何預先 Android 3.0 (API 層級 11) 裝置上支援片段。_

## <a name="fragments-overview"></a>片段概觀

在大部分平板電腦上找到的較大螢幕大小新增至 Android 開發的一層額外的複雜性，專為在小型螢幕不一定也無法運作的較大的螢幕和反向所設計的版面配置。 若要減少這導入的複雜性，Android 3.0 新增兩項新功能，*片段*並*支援套件*。

片段可以視為使用者介面的模組。 它們可讓開發人員劃分成可以在不同的活動執行的隔離、 可重複使用的組件的使用者介面。 在執行階段，活動本身會決定要使用哪一個片段。

支援封裝已初次呼叫*相容性文件庫*並允許在執行版本的 Android 3.0 (API 層級 11) 之前的 Android 裝置上使用的片段。

例如下, 圖說明單一應用程式跨裝置外型規格所使用的片段。

[![片段中平板電腦和話筒的使用方式的圖表](images/00.png)](images/00.png#lightbox)

*片段 A*包含清單中，雖然*片段 B*包含該清單中選取之項目的詳細資料。 在平板電腦上執行應用程式時，它可以顯示這兩個片段，在相同的活動。 當話筒 （具有較小的螢幕大小） 上執行相同的應用程式時，就會將片段裝載於兩個不同的活動。 片段的和片段 B 上是相同的兩個尺寸，但裝載它們的活動不同。

為了協助協調和管理所有這些片段的活動，Android 引進了新的類別，稱為*FragmentManager*。 每個活動都有自己的執行個體`FragmentManager`新增、 刪除和尋找裝載片段。 下圖說明片段與活動之間的關聯性：

[![說明活動、 片段管理員 中，與片段之間的關聯性圖表](images/01.png)](images/01.png#lightbox)

在某些方面，片段可以想像為複合控制項或迷你活動。 這些組合在一起的 UI 項目可用獨立開發人員在活動中的可重複使用模組。 片段沒有檢視階層，就像活動，但不同於活動，它可以在共用到螢幕。 檢視與不同的片段片段具有自己的生命週期中;檢視不會。

一或多個片段的主機活動時，它並不直接知道本身的片段。 同樣地，片段也不會直接察覺其他片段中裝載的活動。 不過，片段和活動會留意`FragmentManager`在他們的活動。 使用`FragmentManager`，就可以取得片段，特定執行個體的參考，並接著呼叫方法，該執行個體上的 活動或片段。 如此一來，活動或片段可以通訊，並與其他片段互動。

本指南包含如何使用片段，其中包括完整的涵蓋範圍：

-   **建立片段**– 如何建立基本的片段和必須實作的主要方法。
-   **片段管理及交易**– 如何在執行階段操作片段。
-   **Android 支援封裝**– 如何使用程式庫可讓片段在較舊版本的 Android 上使用。


## <a name="requirements"></a>需求

片段是可在 Android SDK API 層級 11 (Android 3.0)，從開始，如下列螢幕擷取畫面所示：

[![選取 Android SDK Manager 中的 API 層級](images/02.png)](images/02.png#lightbox)

片段是可在 Xamarin.Android 4.0 和更新版本。 Xamarin.Android 應用程式必須至少為目標 API 層級 11 (Android 3.0) 或更高版本，才能使用片段。 目標 Framework 可能會設定在專案屬性，如下所示：

[![在 專案選項中設定的目標 Framework API 層級](images/03-sml.png)](images/03.png#lightbox)

可以使用舊版 Android 使用 Android 的支援封裝和 Xamarin.Android 4.2 或更新版本中的片段。 在本節中的文件中詳細涵蓋如何執行這項操作。


## <a name="related-links"></a>相關連結

- [Honeycomb 資源庫 （範例）](https://developer.xamarin.com/samples/monodroid/HoneycombGallery)
- [片段](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支援封裝](http://developer.android.com/sdk/compatibility-library.html)
- [MOTODEV 網路研討會： 簡介片段](http://motodev.adobeconnect.com/p9h1aqk3ttn/)
