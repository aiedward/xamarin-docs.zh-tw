---
title: 實作片段的逐步解說
description: 本文逐步解說如何使用片段來開發 Xamarin.Android 應用程式。
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: 92c68298d7abd2570efd89e12d7cfb6364e90972
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798352"
---
# <a name="implementing-fragments---walkthrough"></a>實作片段的逐步解說

_片段是獨立的模組化的元件，可協助解決與各種不同的螢幕大小的裝置為目標的 Android 應用程式的複雜性。本文逐步解說如何建立及開發 Xamarin.Android 應用程式時，使用片段。_

## <a name="overview"></a>總覽

在本節中，您將逐步如何建立及使用 Xamarin.Android 應用程式中的片段。 此應用程式會在清單中，以顯示威廉莎士比亞由數個所扮演的標題。 當使用者點選 play 標題時，應用程式會在不同的活動中顯示該遊戲的引號：

[![在 Android 手機在直向模式中執行的應用程式](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

當為橫向模式旋轉電話時，會變更應用程式的外觀： 清單播放和引號將會出現相同的活動。 選取的播放時，引號將會顯示相同的活動：

[![在 Android 手機中以橫向模式執行的應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

最後，如果在平板電腦上執行應用程式：

[![Android 平板電腦上執行應用程式](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

此範例應用程式可以輕鬆地適應不同的尺寸和方向，以最少的程式碼變更使用片段和[替代配置](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources)。

應用程式的資料將存在於兩個是做為 C# 的字串陣列的應用程式中的硬式編碼的字串陣列。 每個陣列做為一個片段的資料來源。  一個陣列會保留由莎士比亞，某些所扮演的名稱，另一個陣列會保留該遊戲的引號。 應用程式啟動時，它會顯示在播放名稱`ListFragment`。 當使用者按一下中的播放`ListFragment`，應用程式，將會啟動另一個活動將會顯示引號。

應用程式的使用者介面將會包含兩個配置，另一個適用於縱向，一個用於橫向模式。 在執行階段，Android 將會決定載入何種版面配置會根據裝置的方向，並將提供給活動來呈現的版面配置。 所有使用者所按的回應，並將資料顯示的邏輯會包含在片段中。 應用程式中的活動只存在於將裝載片段的容器。

本逐步解說會分成兩個指南。 [第一部分](./walkthrough.md)將焦點放在應用程式的核心部分。 將建立一組 （直向模式最佳化） 的配置，以及兩個片段和兩個活動：

1. `MainActivity` &nbsp; 這是為應用程式的啟動活動。
1. `TitlesFragment` &nbsp; 此片段會顯示一份撰寫威廉莎士比亞所扮演的標題。 它會由`MainActivity`。
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` 將啟動`PlayQuoteActivity`以回應使用者選取在婧矔菛`TitlesFragment`。
1. `PlayQuoteFragment` &nbsp; 此片段會顯示威廉莎士比亞婧矔菛引號。 它會由`PlayQuoteActivity`。

[本逐步解說的第二部分](./walkthrough-landscape.md)將討論新增替代的版面配置 （適合橫向模式） 會在螢幕顯示兩個片段。 此外，某些次要的程式碼會進行變更的程式碼，讓應用程式將會調整其行為，以同時顯示在畫面的片段數目。

## <a name="related-links"></a>相關連結

- [FragmentsWalkthrough （範例）](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [設計工具概觀](~/android/user-interface/android-designer/index.md)
- [實作片段](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支援封裝](http://developer.android.com/sdk/compatibility-library.html)
