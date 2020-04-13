---
title: 實現片段 ─ 演練
description: 本文將介紹如何使用片段來開發 Xamarin.Android 應用程式。
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: b601fc37cc75dcd43c3688de8d302f0a47a06b35
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027412"
---
# <a name="implementing-fragments---walkthrough"></a>實現片段 ─ 演練

_片段是自包含的模組化元件,可幫助解決針對具有各種螢幕尺寸的設備的 Android 應用的複雜性。本文介紹在開發 Xamarin.Android 應用程式時如何創建和使用片段。_

## <a name="overview"></a>概觀

在本節中,您將介紹如何在 Xamarin.Android 應用程式中創建和使用片段。 此應用程式將在清單中顯示威廉·莎士比亞的幾部戲劇的標題。 當使用者點擊播放的標題時,應用將在單獨的活動中顯示該播放的引號:

[![在縱向模式下在 Android 手機上執行的應用程式](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

當手機旋轉到橫向模式時,應用程式的外觀將發生變化:播放和引號清單將在同一活動中顯示。 選擇播放時,報價將在同一活動中顯示:

[![在橫向模式下在 Android 手機上執行的應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

最後,如果應用在平板電腦上運行:

[![在 Android 平板電腦上執行的應用程式](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

此示例應用程式可以使用片段和[備用佈局](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources),以最少的代碼更改輕鬆適應不同的外形規格和方向。

應用程式的數據將存在於應用程式中硬編碼為 C# 字串陣列的兩個字串陣列中。 每個陣列將用作一個片段的數據來源。  一個陣列將保存莎士比亞的一些戲劇的名稱,另一個陣列將包含該劇中的引號。 當應用啟動時,它將在中`ListFragment`顯示播放名稱。 當用戶按一下`ListFragment`播放時,應用將啟動另一個活動,顯示報價。

應用程式的用戶介面將由兩個布局組成,一個用於縱向,一個用於橫向模式。 在運行時,Android將根據設備的方向確定要載入的佈局,並將該佈局提供給要呈現的活動。 回應使用者按一下和顯示數據的所有邏輯都將包含在片段中。 應用中的活動僅作為承載片段的容器存在。

本演練將分為兩個指南。 第[一部分](./walkthrough.md)將側重於應用程式的核心部分。 將建立一組佈局(針對縱向模式進行優化),以及兩個片段和兩個活動:

1. `MainActivity`&nbsp;這是應用的啟動活動。
1. `TitlesFragment`&nbsp;這個片段將顯示威廉·莎士比亞創作的戲劇標題清單。 它會由承載`MainActivity`。
1. `PlayQuoteActivity`將啟動`PlayQuoteActivity`以`TitlesFragment`回應 使用者在中選擇&nbsp;`TitlesFragment`播放。
1. `PlayQuoteFragment`&nbsp;這個片段將顯示威廉·莎士比亞的戲劇引言。 它會由承載`PlayQuoteActivity`。

[本演練的第二部分](./walkthrough-landscape.md)將討論添加備用佈局(針對橫向模式進行優化),該佈局將在螢幕上顯示兩個片段。 此外,還將對代碼進行一些次要代碼更改,以便應用會將其行為適應同時顯示在螢幕上的片段數。

## <a name="related-links"></a>相關連結

- [片段瀏覽(範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fragmentswalkthrough)
- [設計工具概觀](~/android/user-interface/android-designer/index.md)
- [實作片段](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支援包](https://developer.android.com/sdk/compatibility-library.html)
