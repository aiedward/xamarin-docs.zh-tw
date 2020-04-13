---
title: 片段
description: Android 3.0 引入了片段,演示如何支援手機和平板電腦上許多不同的螢幕尺寸的更靈活的設計。 本文將介紹如何使用片段來開發 Xamarin.Android 應用程式,以及如何在 Android 前 3.0(API 11 級)設備上支援片段。
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 5d243429fe4f61768568a634b205055c1ad94297
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020256"
---
# <a name="fragments"></a>片段

_Android 3.0 引入了片段,演示如何支援手機和平板電腦上許多不同的螢幕尺寸的更靈活的設計。本文將介紹如何使用片段來開發 Xamarin.Android 應用程式,以及如何在 Android 前 3.0(API 11 級)設備上支援片段。_

## <a name="fragments-overview"></a>片段概述

大多數平板電腦上更大的螢幕尺寸為 Android 開發增加了額外的複雜性層-專為小螢幕設計的佈局不一定適用於更大的螢幕,反之亦然。 為了減少引入的併發症數量,Android 3.0 添加了兩個新功能,*片段**和支援包*。

片段可以被視為使用者介面模組。 它們允許開發人員將使用者介面劃分為可獨立運行的隔離、可重用部分。 在運行時,活動本身將決定要使用的片段。

支援包最初稱為*相容性庫*,允許在 Android 3.0(API 級別 11)之前運行 Android 版本的設備上使用片段。

例如,下圖說明瞭單個應用程式如何使用跨不同設備外形規格的片段。

[![磁碟在平板電腦和手機中的使用方式圖](images/00.png)](images/00.png#lightbox)

*片段 A*包含清單,而*片段 B*包含該清單中選取的項目的詳細資訊。 當應用程式在平板電腦上運行時,它可以在同一活動上顯示兩個片段。 當同一應用程式在聽筒上運行時(螢幕大小較小),片段將託管在兩個單獨的活動中。 片段 A 和片段 B 在這兩個外形規格上都相同,但承載它們的活動是不同的。

為了幫助活動協調和管理所有這些片段,Android 引入了一個名為*片段管理器*的新類。 每個活動都有自己的實體,`FragmentManager`用於新增、刪除和尋找託管片段。 下圖說明了片段與活動之間的關係:

[![說明活動、片段管理員與片段之間關聯的圖表](images/01.png)](images/01.png#lightbox)

在某些情況下,片段可以被視為複合控制件或小型活動。 它們將 UI 部分捆綁到可重用的模組中,然後由活動中的開發人員獨立使用。 片段確實具有檢視層次結構(與活動類似),但與活動不同,它可以跨螢幕共用。 視圖與片段不同,因為片段有自己的生命週期;視圖不。

雖然活動是一個或多個片段的主機,但它並不直接知道片段本身。 同樣,片段並不直接知道託管活動中的其他片段。 但是,片段和活動知道其活動中的`FragmentManager`。 通過使用`FragmentManager`, 活動或片段可以獲取對片段的特定實例的引用,然後調用該實例上的方法。 這樣,活動或片段可以與其他片段進行通信和交互。

本指南包含有關如何使用片段的全面介紹,包括:

- **建立片段**– 如何建立必須實現的基本片段和關鍵方法。
- **片段管理和事務**– 如何在運行時操作片段。
- **Android 支援包**– 如何使用允許片段在舊版本的 Android 上使用的庫。

## <a name="requirements"></a>需求

從 API 級別 11 (Android 3.0) 開始的 Android SDK 中提供了片段,如下圖所示:

[![在 Android SDK 管理員中選擇 API 等級](images/02.png)](images/02.png#lightbox)

片段有 Xamarin.Android 4.0 及更高版本。 Xamarin.Android 應用程式必須針對至少 API 級別 11(Android 3.0)或更高版本才能使用片段。 目標框架可以在項目屬性中設置,如下所示:

[![在項目選項中設定目標框架 API 等級](images/03-sml.png)](images/03.png#lightbox)

通過使用 Android 支援包和 Xamarin.Android 4.2 或更高版本,可以在舊版本的 Android 中使用片段。 本節的文檔中詳細介紹了如何執行此操作。

## <a name="related-links"></a>相關連結

- [蜂巢畫廊(範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/honeycombgallery)
- [片段](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支援包](https://developer.android.com/sdk/compatibility-library.html)
