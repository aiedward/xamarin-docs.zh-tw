---
title: 實作影片播放程式
description: 本文說明如何使用來執行影片播放程式應用程式 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 0CE9BEE7-4F81-4A00-B9B3-5E2535CD3050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a4b092d90b3afa2197c1db576545d233eb6e208d
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563506"
---
# <a name="implementing-a-video-player"></a>實作影片播放程式

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

有時候，在應用程式中播放影片檔案是很理想的做法 Xamarin.Forms 。 這一系列的文章會討論如何針對名為的類別，撰寫適用于 iOS、Android 和通用 Windows 平臺 (UWP) 的自訂轉譯器 Xamarin.Forms `VideoPlayer` 。

在 [**VideoPlayerDemos**](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) 範例中，所有實作並支援 `VideoPlayer` 的檔案都位於名為 `FormsVideoLibrary` 的資料夾，以 `FormsVideoLibrary` 命名空間或以 `FormsVideoLibrary` 開頭的命名空間識別。 此組織和命名應可讓您輕鬆地將影片播放程式檔案複製到您自己的 Xamarin.Forms 解決方案中。

`VideoPlayer` 可以播放三種來源類型的影片檔案：

- 使用 URL 的網際網路
- 平台應用程式中的內嵌資源
- 裝置的影片庫

影片播放程式需要*傳輸控制項*，即為播放和暫停影片的按鈕，以及可顯示影片進度並讓使用者快速跳至不同位置的定位列。 `VideoPlayer` 可以使用平台提供的傳輸控制項和定位列 (如下所示)，或者您也可以提供自訂傳輸控制項和定位列。 以下是在 iOS、Android 和通用 Windows 平台執行的程式：

[![播放 Web 影片](web-videos-images/playwebvideo-small.png "播放 Web 影片")](web-videos-images/playwebvideo-large.png#lightbox "播放 Web 影片")

當然，您可以將手機轉為橫向以放大檢視。

較複雜的影片播放程式具有某些額外功能，例如音量控制、通話時會中斷影片的機制，以及在播放期間保持螢幕開啟的方式。

下列一系列文章會逐步示範如何建置平台轉譯器和支援的類別：

## <a name="creating-the-platform-video-players"></a>[建立平臺影片播放機](player-creation.md)

每個平台需要 `VideoPlayerRenderer` 類別，旨在建立並維護受平台支援的影片播放程式控制項。 本文章說明轉譯器類別的結構，以及如何建立播放器。

## <a name="playing-a-web-video"></a>[播放 Web 影片](web-videos.md)

網際網路可能是影片播放程式中最常見的影片來源。 本文說明如何將 Web 影片用於參考，並將其用作影片播放程式的來源。

## <a name="binding-video-sources-to-the-player"></a>[將影片來源系結至播放機](source-bindings.md)

本文使用 `ListView` 呈現要播放的影片集合。 一個程式會顯示程式碼後置檔案可如何設定影片播放程式的影片來源，但第二個程式會顯示您可以如何使用 `ListView` 和影片播放程式之間的資料繫結。

## <a name="loading-application-resource-videos"></a>[正在載入應用程式資源影片](loading-resources.md)

影片可以在平台專案中內嵌為資源。 本文說明如何儲存這些資源，並稍後將其載入程式，由影片播放程式播放。

## <a name="accessing-the-devices-video-library"></a>[存取裝置的影片庫](accessing-library.md)

使用裝置的相機來建立影片時，影片檔案會儲存在裝置的影像庫中。 本文章會示範如何存取裝置的影像選擇器來選取影片，再使用影片播放程式來播放。

## <a name="custom-video-transport-controls"></a>[自訂影片傳輸控制項](custom-transport.md)

雖然每個平台上的影片播放程式會各自提供按鈕形式的 [播放]**** 和 [暫停]**** 傳輸控制項，您也可以隱藏這些按鈕並改用您自己的。 本文將說明如何做到。

## <a name="custom-video-positioning"></a>[自訂影片定位](custom-positioning.md)

每個平台的影片播放程式都有定位列，會顯示影片的進度，並可讓您快轉或倒轉至特定位置。 本文會示範如何使用自訂控制項取代該定位列。

## <a name="related-links"></a>相關連結

- [影片播放程式示範 (範例)](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)