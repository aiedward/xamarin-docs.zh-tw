---
title: 執行片段-逐步解說
description: 本文逐步解說如何使用片段來開發 Xamarin Android 應用程式。
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: e5a09c216f0def71efb1c3ddc0ed18672663bdfe
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643610"
---
# <a name="implementing-fragments---walkthrough"></a>執行片段-逐步解說

_片段是獨立的模組化元件, 可協助處理以各種螢幕大小為目標裝置之 Android 應用程式的複雜性。本文逐步解說如何在開發 Xamarin Android 應用程式時建立和使用片段。_

## <a name="overview"></a>總覽

在本節中, 您將逐步解說如何在 Xamarin Android 應用程式中建立和使用片段。 此應用程式將會在清單中 William Shakespeare, 以顯示數個播放的標題。 當使用者按下播放的標題時, 應用程式將會顯示在不同活動中播放的報價:

[![在 Android 手機上以直向模式執行的應用程式](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

當電話旋轉為橫向模式時, 應用程式的外觀將會變更: 播放和報價的清單會出現在相同的活動中。 選取 [播放] 時, 報價會顯示在相同的活動中:

[![以橫向模式在 Android 手機上執行的應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

最後, 如果應用程式正在平板電腦上執行:

[![在 Android 平板電腦上執行的應用程式](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

這個範例應用程式可以使用片段和[替代版面](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources)配置, 輕鬆地配合不同的外型規格和方向, 以最少的程式碼變更來調整。

應用程式的資料會存在於應用程式中硬式編碼為C#字串陣列的兩個字串陣列中。 每個陣列都會作為一個片段的資料來源。  一個陣列會保存 Shakespeare 的一些名稱, 另一個陣列則會保存該播放的報價。 當應用程式啟動時, 它會在中`ListFragment`顯示播放名稱。 當使用者按一下中的 [ `ListFragment`播放] 時, 應用程式將會啟動另一個活動來顯示報價。

應用程式的使用者介面會包含兩個配置, 一個用於直向, 另一個用於橫向模式。 在執行時間, Android 會根據裝置的方向決定要載入的版面配置, 並將該配置提供給要轉譯的活動。 回應使用者點擊和顯示資料的所有邏輯都會包含在片段中。 應用程式中的活動只會以裝載片段的容器形式存在。

本逐步解說將分為兩個指南。 [第一個部分](./walkthrough.md)將著重在應用程式的核心部分。 隨即會建立一組版面配置 (針對直向模式優化), 以及兩個片段和兩個活動:

1. `MainActivity`&nbsp;這是應用程式的啟動活動。
1. `TitlesFragment`&nbsp;此片段會顯示 William Shakespeare 所撰寫的播放標題清單。 它將由`MainActivity`主控。
1. `PlayQuoteActivity`會啟動,`PlayQuoteActivity` 以`TitlesFragment`回應選取 [播放] 的使用者。 &nbsp; `TitlesFragment`
1. `PlayQuoteFragment`&nbsp;此片段會顯示由 William Shakespeare 播放的報價。 它將由`PlayQuoteActivity`主控。

[本逐步解說的第二個部分](./walkthrough-landscape.md)將討論如何新增替代配置 (針對橫向模式優化), 這會在螢幕上顯示這兩個片段。 此外, 程式碼也會進行一些次要程式碼變更, 讓應用程式能夠將其行為調整成同時顯示在螢幕上的片段數目。

## <a name="related-links"></a>相關連結

- [FragmentsWalkthrough (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fragmentswalkthrough)
- [設計師總覽](~/android/user-interface/android-designer/index.md)
- [執行片段](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支援封裝](https://developer.android.com/sdk/compatibility-library.html)
