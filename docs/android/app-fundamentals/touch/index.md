---
title: Xamarin 中的觸控和手勢
description: 現今許多裝置上的觸控式螢幕可讓使用者以自然且直覺的方式，快速且有效率地與裝置互動。 這種互動並不只限于簡單的觸控式偵測，也可以使用手勢。 例如，縮小到縮放手勢是一個非常常見的範例，其方式是使用兩個手指來捏合螢幕的一部分，讓使用者可以放大或縮小。本指南探討 Android 中的觸控和手勢。
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 54f036b2a845b7d2f6603b6b9f54472ad2af8bcd
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754711"
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>Xamarin 中的觸控和手勢

_現今許多裝置上的觸控式螢幕可讓使用者以自然且直覺的方式，快速且有效率地與裝置互動。這種互動並不只限于簡單的觸控式偵測，也可以使用手勢。例如，縮小到縮放手勢是一個非常常見的範例，其方式是使用兩個手指來捏合螢幕的一部分，讓使用者可以放大或縮小。本指南探討 Android 中的觸控和手勢。_

## <a name="touch-overview"></a>觸控總覽

iOS 和 Android 在處理觸控的方式上很類似。 兩者都可以在螢幕和複雜手勢上支援多點觸控的連絡人。 本指南介紹一些概念中的相似之處，以及在兩個平臺上執行觸控和手勢的 particularities。

Android 使用`MotionEvent`物件來封裝觸控資料，而 View 物件上的方法則用來接聽觸控。

除了捕捉觸控資料，iOS 和 Android 也提供將觸控模式解讀為手勢的方法。 這些手勢辨識器可以輪流用來解讀應用程式特定的命令，例如影像的旋轉或頁面的回合。 Android 提供一些支援的手勢和資源，讓您可以輕鬆地新增複雜的自訂手勢。

無論您是在 Android 或 iOS 上工作，在觸控和手勢辨識器之間的選擇都可能有點令人困惑。 本指南建議一般喜好設定應給予手勢辨識器。 筆勢辨識器會實作為離散類別，以提供更好的顧慮分隔和更好的封裝。 這可讓您輕鬆地在不同的視圖之間共用邏輯，並將寫入的程式碼數量降至最低。

本指南會針對每個作業系統遵循類似的格式：首先會引進並說明平臺的觸控 Api，因為它們是建立觸控互動的基礎。 然後，我們會深入探討手勢辨識器的世界–先探索一些常見的手勢，然後完成建立應用程式的自訂手勢。 最後，您將瞭解如何使用低層級的觸控追蹤來追蹤個別手指，以建立手指繪製程式。

## <a name="sections"></a>章節

- [Android 中的觸控](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
- [逐步解說：在 Android 中使用觸控](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
- [多點觸控追蹤](touch-tracking.md)

## <a name="summary"></a>總結

在本指南中，我們已檢查 Android 的觸控。 針對這兩種作業系統，我們已瞭解如何啟用觸控，以及如何回應觸控事件。 接下來，我們已瞭解 Android 和 iOS 提供的手勢和一些手勢辨識器，以處理一些較常見的案例。 我們已檢查如何建立自訂手勢，並在應用程式中加以執行。 本逐步解說示範了每個作業系統運作的概念和 Api，同時也看到了如何追蹤個別手指。

## <a name="related-links"></a>相關連結

- [Android Touch 開始（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch 最終（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
- [FingerPaint （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)
