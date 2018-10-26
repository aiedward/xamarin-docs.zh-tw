---
title: 實作片段位逐步解說
description: 本文逐步解說如何使用片段來開發 Xamarin.Android 應用程式。
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: b068169ee3f44932f8ee13d2546804f7b2d2a645
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103058"
---
# <a name="implementing-fragments---walkthrough"></a>實作片段位逐步解說

_片段是獨立的模組化元件，可協助您解決各種螢幕大小，以裝置為目標的 Android 應用程式的複雜度。本文逐步解說如何建立及開發 Xamarin.Android 應用程式時，使用片段。_

## <a name="overview"></a>總覽

在本節中，您將逐步解說如何建立和使用 Xamarin.Android 應用程式中的片段。 此應用程式將清單中，以顯示 William Shakespeare 的數個所扮演的標題。 當使用者點選婧矔菛的標題時，應用程式會在個別的活動顯示該紙牌引述：

[![在 Android 手機在直向模式中執行的應用程式](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

當電話會旋轉成橫向模式中時，將會變更應用程式的外觀： 播放和引號括住的清單會出現在相同的活動。 選取播放時，引號將會顯示相同的活動：

[![在 Android 手機中以橫向模式上執行的應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

最後，如果在平板電腦上執行應用程式：

[![在 Android 平板電腦上執行的應用程式](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

此範例應用程式可以輕鬆地適應不同的外型規格和方向變更最少的程式碼使用片段並[替代配置](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources)。

應用程式的資料會存在於兩個字串的陣列是硬式編碼在應用程式做為C#的字串陣列。 每個陣列將做為資料來源有一個片段。  一個陣列將會保留由 Shakespeare，某些所扮演的名稱，另一個陣列會保存該紙牌報價。 當應用程式啟動時，它會顯示中的 play 名稱`ListFragment`。 當使用者按一下中婧矔菛`ListFragment`，應用程式，將會啟動另一個活動將會顯示報價。

應用程式的使用者介面將包含兩個配置、 一個用於直向和橫向模式的其中一個。 在執行階段，Android 會決定載入何種版面配置根據裝置的方向，並將呈現活動提供該配置。 所有回應使用者按下滑鼠，並將資料顯示的邏輯會包含在片段中。 應用程式中的活動只存在於將裝載片段的容器。

本逐步解說會分成兩個指南。 [第一部份](./walkthrough.md)將著重在應用程式的核心部分。 將建立一組 （適用於直向模式） 的版面配置，以及兩個片段和兩個活動：

1. `MainActivity` &nbsp; 這是為應用程式的啟動活動。
1. `TitlesFragment` &nbsp; 這個片段會顯示一份播放 William Shakespeare 所寫入的標題。 它會由`MainActivity`。
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` 將會啟動`PlayQuoteActivity`回應使用者選取在婧矔菛`TitlesFragment`。
1. `PlayQuoteFragment` &nbsp; 這個片段顯示 William 莎士比亞婧矔菛引號。 它會由`PlayQuoteActivity`。

[第二個部分，本逐步解說的](./walkthrough-landscape.md)將討論新增替代的版面配置 （適用於橫向模式） 會在螢幕顯示兩個片段。 此外，一些次要的程式碼會進行變更的程式碼，讓應用程式將會使其行為會同時顯示在螢幕的片段數目。

## <a name="related-links"></a>相關連結

- [FragmentsWalkthrough （範例）](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [設計工具概觀](~/android/user-interface/android-designer/index.md)
- [實作片段](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支援封裝](http://developer.android.com/sdk/compatibility-library.html)
