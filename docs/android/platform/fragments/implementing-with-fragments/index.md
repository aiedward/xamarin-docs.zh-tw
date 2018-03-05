---
title: "實作以片段"
description: "Android 3.0 引進了片段。 片段是獨立的模組元件，可針對在不同大小螢幕上執行的應用程式，協助處理撰寫此類應用程式時所面臨的複雜性。 本文逐步解說如何使用片段來開發 Xamarin.Android 應用程式，以及如何預先 Android 3.0 版的裝置上支援片段。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: ebb53398edba64e255f1a534556836df8734ba6f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="implementing-with-fragments"></a>實作以片段

_Android 3.0 引進了片段。片段是獨立的模組元件，可針對在不同大小螢幕上執行的應用程式，協助處理撰寫此類應用程式時所面臨的複雜性。本文逐步解說如何使用片段來開發 Xamarin.Android 應用程式，以及如何預先 Android 3.0 版的裝置上支援片段。_

<a name="Overview" />

## <a name="overview"></a>總覽

本節中，我們將逐步解說如何建立應用程式將會顯示一份的所扮演的每個選取的播放引號。 我們的應用程式會利用片段，以便我們可以定義在一個地方，我們的 UI 元件，但使用不同的尺寸上。 例如，下列螢幕擷取畫面顯示電話以及 10"平板電腦上執行的應用程式：

[![平板電腦和手機上執行的範例應用程式的螢幕擷取畫面](images/intro-screenshot-sml.png)](images/intro-screenshot.png)

本節將討論下列主題：

- **建立片段**&ndash;示範如何建立要顯示的播放清單片段和其他片段，以顯示每個遊戲的引號。

- **支援不同的螢幕大小**&ndash;示範如何配置應用程式以充分利用較大的螢幕大小。

- **使用 Android 的支援封裝**&ndash;實作 Android 的支援套件，則一些微幅變更對應用程式，允許它在舊版的 Android 上執行的活動。

<a name="Requirements" />

## <a name="requirements"></a>需求

本逐步解說需要 Xamarin.Android 4.0 或更新版本。 它也會安裝 Android 支援封裝中，所需的片段文件中所述。

<a name="Introduction" />

## <a name="introduction"></a>簡介

在本節中，我們將建立的範例中，活動不會包含邏輯載入清單、 回應使用者選取或顯示選取的播放引號。 此邏輯存在於個別的片段。
將這個邏輯放在本身的片段中，我們可以分割應用程式有一個活動或小型螢幕與多個活動支援較大的螢幕，而不需要撰寫不同的邏輯，為每個活動的工作流程。 平板電腦，在兩個片段為一個活動中。 在電話上，片段會裝載於不同的活動。

此應用程式包含下列部分：

 **MainActivity** – 顯示其中一個或兩個片段，螢幕大小而定。 這是啟動活動。

 **TitlesFragment** – 顯示的使用者可以從中選取的播放清單。

 **DetailsFragment** – 顯示選取的播放引號。

 **DetailsActivity** – 裝載，並顯示 DetailsFragment。
此活動會使用小型螢幕，例如手機的裝置。



## <a name="related-links"></a>相關連結

- [FragmentsWalkthrough （範例）](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [設計工具概觀](~/android/user-interface/android-designer/index.md)
- [Xamarin.Android 範例： Honeycomb 組件庫](https://developer.xamarin.com/samples/HoneycombGallery/)
- [實作片段](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支援封裝](http://developer.android.com/sdk/compatibility-library.html)
