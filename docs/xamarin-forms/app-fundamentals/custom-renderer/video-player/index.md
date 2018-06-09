---
title: 實作影片播放器
description: 本文說明如何實作影片播放器應用程式使用 Xamarin.Forms。
ms.prod: xamarin
ms.assetid: 0CE9BEE7-4F81-4A00-B9B3-5E2535CD3050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 00697ca0adf3a34abec90c2f96d9fd9c273d06bb
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35239780"
---
# <a name="implementing-a-video-player"></a>實作影片播放器

它有時會播放視訊檔案，在 Xamarin.Forms 應用程式。 這一系列的文章會討論如何撰寫用於 iOS、 Android 和 Xamarin.Forms 類別的通用 Windows 平台 (UWP) 的自訂轉譯器`VideoPlayer`。

在[ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)取樣，請實作和支援的所有檔案`VideoPlayer`名為的資料夾中`FormsVideoLibrary`，而且與命名空間識別`FormsVideoLibrary`或命名空間開始`FormsVideoLibrary`。 此組織和命名應該讓您輕鬆將視訊播放程式檔案複製到您自己的 Xamarin.Forms 方案。

`VideoPlayer` 可以播放視訊檔案的來源的三種類型：

- 使用 URL 的網際網路
- 平台應用程式中嵌入的資源
- 裝置的視訊媒體櫃

需要視訊播放程式*傳輸控制*，這是按鈕，以播放並暫停視訊，和定位的列，顯示視訊的進度，並且可讓使用者快速跳到不同的位置。 `VideoPlayer` 可以使用傳輸控制及定位列提供的平台 （如下所示），或者您可以提供自訂傳輸控制項和定位列。 以下是程式執行 iOS、 Android 和通用 Windows 平台：

[![播放網頁的視訊](web-videos-images/playwebvideo-small.png "播放的視訊 Web")](web-videos-images/playwebvideo-large.png#lightbox "播放網頁的視訊")

當然，您可以將電話轉向側邊，以放大。

更趨精密完美的視訊播放程式會有一些額外的功能，例如音量控制、 中斷視訊電話通話時，透過一種機制，以及在播放期間，讓畫面保持作用中的方法。

下面一系列文章漸進式示範如何建置平台轉譯器和支援的類別：

## <a name="creating-the-platform-video-playersplayer-creationmd"></a>[建立平台的視訊播放程式](player-creation.md)

每個平台需要`VideoPlayerRenderer`類別，建立並維護平台支援的視訊播放程式控制項。 本文將說明結構的轉譯器類別，以及如何建立交易夥伴。

## <a name="playing-a-web-videoweb-videosmd"></a>[播放網頁視訊](web-videos.md)

可能的視訊的視訊播放程式最常見的來源是網際網路。 本文說明如何能參考和當做來源使用的視訊播放程式的 Web 視訊。

## <a name="binding-video-sources-to-the-playersource-bindingsmd"></a>[繫結到播放程式的視訊來源](source-bindings.md)

本文使用`ListView`呈現播放視訊的集合。 一種程式會顯示程式碼後置檔案可以設定視訊的視訊播放程式中，來源的方式，但第二個程式會顯示如何使用資料繫結之間`ListView`和視訊播放程式。

## <a name="loading-application-resource-videosloading-resourcesmd"></a>[正在載入應用程式資源影片](loading-resources.md)

視訊可以當做資源內嵌在平台專案。 本文將說明如何儲存這些資源，然後稍後再將它們載入至視訊播放程式播放程式。

## <a name="accessing-the-devices-video-libraryaccessing-librarymd"></a>[存取裝置的視訊媒體櫃](accessing-library.md)

使用裝置的相機建立視訊時，視訊檔會儲存在裝置的映像庫中。 本文示範如何存取裝置的映像選擇器，以選取的視訊，再加以使用影片播放器播放。

## <a name="custom-video-transport-controlscustom-transportmd"></a>[自訂的視訊傳輸控制](custom-transport.md)

雖然每個平台上的視訊播放程式會提供自己的傳輸控制按鈕的形式**播放**和**暫停**，您可以隱藏這些按鈕的顯示，並提供您自己。 這篇文章會說明方法。

## <a name="custom-video-positioningcustom-positioningmd"></a>[自訂視訊定位](custom-positioning.md)

每個平台的視訊播放器有位置列顯示視訊的進度，並可讓您跳超前或回至特定位置。 本文示範如何與自訂控制項取代該位置列。





## <a name="related-links"></a>相關連結

- [視訊播放程式示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
