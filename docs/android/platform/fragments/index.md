---
title: Fragments
description: Android 3.0 引進了片段, 示範如何針對在手機和平板電腦上找到的各種不同螢幕大小, 支援更具彈性的設計。 本文將說明如何使用片段來開發 Xamarin Android 應用程式, 以及如何支援 Android 前 3.0 (API 層級 11) 裝置上的片段。
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: b04ecf0685e78b73346ea5af815ed46f98b5da0f
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524352"
---
# <a name="fragments"></a>Fragments

_Android 3.0 引進了片段, 示範如何針對在手機和平板電腦上找到的各種不同螢幕大小, 支援更具彈性的設計。本文將說明如何使用片段來開發 Xamarin Android 應用程式, 以及如何支援 Android 前 3.0 (API 層級 11) 裝置上的片段。_

## <a name="fragments-overview"></a>片段總覽

在大部分的平板電腦上找到較大的螢幕大小, 對 Android 開發新增了額外的複雜性層級-針對小型螢幕設計的版面配置不一定適用于較大的螢幕, 反之亦然。 為了減少這種引進的複雜性, Android 3.0 新增了兩項新功能:*片段*和*支援套件*。

可以將片段視為使用者介面模組。 他們可讓開發人員將使用者介面劃分成隔離、可重複使用的元件, 以便在不同的活動中執行。 在執行時間, 活動本身會決定要使用的片段。

支援套件原本稱為*相容性程式庫*, 以及在 android 3.0 之前執行 android 版本的裝置上允許使用的片段 (API 層級 11)。

例如, 下圖說明單一應用程式如何跨不同的裝置外型規格使用片段。

[![在平板電腦和手機中如何使用片段的圖表](images/00.png)](images/00.png#lightbox)

*片段 A*包含清單, 而*片段 B*包含該清單中所選取專案的詳細資料。 在平板電腦上執行應用程式時, 可以在相同的活動上顯示這兩個片段。 在手機上執行相同的應用程式時 (具有較小的螢幕大小), 這些片段會裝載于兩個不同的活動中。 片段 A 和片段 B 在兩種外型規格上都相同, 但裝載它們的活動則不同。

為了協助活動座標和管理所有這些片段, Android 引進了一個稱為*FragmentManager*的新類別。 每個活動都有自己的實例`FragmentManager` , 用於加入、刪除和尋找裝載的片段。 下圖說明片段和活動之間的關聯性:

[![說明活動、片段管理員和片段之間關聯性的圖表](images/01.png)](images/01.png#lightbox)

在某些方面, 可以將片段視為複合控制項或迷你活動。 它們會將 UI 的幾個部分組合成可重複使用的模組, 讓開發人員可獨立用於活動中。 片段具有視圖階層, 就像活動一樣, 但與活動不同的是, 它可以在畫面之間共用。 不同于片段的視圖會有自己的生命週期;views 不會。

雖然活動是一個或多個片段的主機, 但它並不直接知道片段本身。 同樣地, 片段並不會直接知道裝載活動中的其他片段。 不過, 片段和活動會感知其活動`FragmentManager`中的。 藉由使用`FragmentManager`, 活動或片段可以取得特定片段實例的參考, 然後在該實例上呼叫方法。 如此一來, 活動或片段就可以與其他片段通訊並與之互動。

本指南包含如何使用片段的完整涵蓋範圍, 包括:

- **建立片段**–如何建立必須執行的基本片段和金鑰方法。
- **片段管理和交易**–如何在執行時間操作片段。
- **Android 支援套件**–如何使用允許片段在舊版 Android 上使用的程式庫。


## <a name="requirements"></a>需求

從 API 層級 11 (Android 3.0) 開始, Android SDK 提供片段, 如下列螢幕擷取畫面所示:

[![在 Android SDK 管理員中選取 API 層級](images/02.png)](images/02.png#lightbox)

在 Xamarin. Android 4.0 和更新版本中可取得片段。 Xamarin Android 應用程式必須以至少 API 層級 11 (Android 3.0) 或更高版本為目標, 才能使用片段。 目標 Framework 可能會在專案屬性中設定, 如下所示:

[![在專案選項中設定目標 Framework API 層級](images/03-sml.png)](images/03.png#lightbox)

您可以使用 Android 支援套件和 Xamarin. Android 4.2 或更高版本, 在舊版 Android 中使用片段。 本節的檔會更詳細地說明如何執行這項操作。


## <a name="related-links"></a>相關連結

- [Honeycomb 資源庫 (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/honeycombgallery)
- [片段](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支援封裝](https://developer.android.com/sdk/compatibility-library.html)
- [MOTODEV 網路研討會:片段簡介](http://motodev.adobeconnect.com/p9h1aqk3ttn/)
