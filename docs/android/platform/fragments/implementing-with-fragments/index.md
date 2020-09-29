---
title: 執行片段-逐步解說
description: 本文將逐步解說如何使用片段來開發 Xamarin. Android 應用程式。
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: ab680020f62548eed4d1da0e4dbb13434a6d8ce7
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453944"
---
# <a name="implementing-fragments---walkthrough"></a>執行片段-逐步解說

_片段是獨立的模組化元件，可協助解決以各種螢幕大小為目標裝置的 Android 應用程式複雜度。本文將逐步解說如何在開發 Xamarin. Android 應用程式時建立和使用片段。_

## <a name="overview"></a>概觀

在本節中，您將逐步解說如何在 Xamarin Android 應用程式中建立和使用片段。 此應用程式會在清單中 William Shakespeare，以顯示數個播放的標題。 當使用者按下播放的標題時，應用程式將會在不同的活動中顯示該播放的報價：

[![在 Android 手機上以縱向模式執行的應用程式](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

當手機旋轉為橫向模式時，應用程式的外觀將會變更：播放清單和引號都會出現在相同的活動中。 選取 [播放] 時，會在相同的活動中顯示報價：

[![在 Android 手機上以橫向模式執行的應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

最後，如果應用程式正在平板電腦上執行：

[![在 Android 平板電腦上執行的應用程式](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

這個範例應用程式可以使用片段和 [替代版面](../../../app-fundamentals/resources-in-android/alternate-resources.md)配置，輕鬆地適應不同的外型規格和方向，並減少程式碼變更。

應用程式的資料會存在於兩個字串陣列中，以 c # 字串陣列的形式在應用程式中硬式編碼。 每個陣列將作為一個片段的資料來源。  其中一個陣列會依 Shakespeare 保存某些播放的名稱，而另一個陣列將會保留該播放的報價。 當應用程式啟動時，它會在中顯示播放名稱 `ListFragment` 。 當使用者按一下中的 play 時 `ListFragment` ，應用程式將會啟動另一個活動，以顯示報價。

應用程式的使用者介面將包含兩個配置，一個用於直向，另一個用於橫向模式。 在執行時間，Android 會根據裝置的方向來決定要載入的版面配置，並將該版面配置提供給要轉譯的活動。 所有回應使用者按一下和顯示資料的邏輯都會包含在片段中。 應用程式中的活動只會以裝載片段的容器的形式存在。

本逐步解說將分為兩個指南。 [第一個部分](./walkthrough.md)將著重于應用程式的核心元件。 系統會建立一組適用于直向模式)  (優化的版面配置，以及兩個片段和兩個活動：

1. `MainActivity`&nbsp;這是應用程式的啟動活動。
1. `TitlesFragment`&nbsp;此片段會顯示 William Shakespeare 所撰寫的播放標題清單。 它將由裝載 `MainActivity` 。
1. `PlayQuoteActivity`&nbsp; `TitlesFragment` 將會啟動， `PlayQuoteActivity` 以回應選取播放的使用者 `TitlesFragment` 。
1. `PlayQuoteFragment`&nbsp;此片段會顯示 William Shakespeare play 的報價。 它將由裝載 `PlayQuoteActivity` 。

[本逐步解說的第二個部分](./walkthrough-landscape.md)將討論如何新增 (針對橫向模式優化的替代版面配置) 這會在螢幕上顯示這兩個片段。 此外，也會對程式碼進行一些小程式代碼變更，讓應用程式能夠將其行為調整為同時顯示在畫面上的片段數目。

## <a name="related-links"></a>相關連結

- [FragmentsWalkthrough (範例) ](/samples/xamarin/monodroid-samples/fragmentswalkthrough)
- [設計工具總覽](~/android/user-interface/android-designer/index.md)
- [執行片段](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支援封裝](https://developer.android.com/sdk/compatibility-library.html)