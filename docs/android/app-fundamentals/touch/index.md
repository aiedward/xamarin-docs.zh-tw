---
title: 觸控與手勢 Xamarin.Android 中
description: 在許多現今的裝置上的觸控式螢幕讓使用者快速且有效地與裝置互動以自然且直覺式的方式。 這種互動不只限於簡單觸控偵測-可能使用筆勢以及。 例如，縮小-放大手勢是螢幕的一個非常常見的範例就與使用者都可以放大或縮小的兩指部分。本指南檢查觸控與手勢都在 Android。
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 3700f9c73fb58fefcdba7987c9931e385cd52d38
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>觸控與手勢 Xamarin.Android 中

_在許多現今的裝置上的觸控式螢幕讓使用者快速且有效地與裝置互動以自然且直覺式的方式。這種互動不只限於簡單觸控偵測-可能使用筆勢以及。例如，縮小-放大手勢是螢幕的一個非常常見的範例就與使用者都可以放大或縮小的兩指部分。本指南檢查觸控與手勢都在 Android。_

## <a name="touch-overview"></a>觸控概觀

iOS 和 Android 的很類似的方式處理觸控。 兩者都可以支援多點觸控-連絡人在螢幕上的多點-和複雜的筆勢。 本指南介紹一些相似之處的概念，以及實作觸控任用和手勢都在這兩個平台上。

Android 使用`MotionEvent`物件來封裝觸控資料和接聽修飾的檢視物件上的方法。

除了擷取觸控資料，iOS 和 Android 會提供方法，以便將解譯成筆勢修飾的模式。 這些筆勢辨識器接著可用來解譯應用程式特定的命令，例如將影像的旋轉或開啟的頁面。 Android 提供少數幾個支援放軌跡，以及資源來進行加入複雜的自訂軌跡輕鬆。

無論您是在 Android 或 iOS，觸控和筆勢辨識器之間的選擇可能是令人混淆。 本指南建議的一般情況下，喜好設定應該授予筆勢辨識器。 筆勢辨識器會實作做為離散類別，提供更大的分隔考量和更好的封裝。 這可讓您輕易地共用不同的檢視，減少撰寫的程式碼之間的邏輯。

本指南會遵循類似的格式，針對每個作業系統： 首先，應用程式開發介面引進，而且所說明，因為它們是建立互動的觸控的基礎平台的觸控。 然後，我們探討世界的筆勢辨識器 – 第一次瀏覽一些常見的筆勢，及完成建立應用程式的自訂軌跡。 最後，您會看到如何追蹤個別指建立 finger-paint 程式使用觸控低層級追蹤。

## <a name="sections"></a>章節

-  [Android 中的觸控](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [逐步解說： 在 Android 中使用觸控](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [多點觸控追蹤](touch-tracking.md)

## <a name="summary"></a>總結

本指南中，我們會檢查觸控在 Android。 這兩個作業系統中，我們學到如何啟用觸控以及如何回應觸控事件。 接下來，我們發現了筆勢和的筆勢辨識器的一些 Android 和 iOS 提供處理一些較常見的案例。 我們會檢查如何建立自訂的筆勢和應用程式中實作它們。 逐步解說示範的概念和 Api 在動作中，每個作業系統，您也可了解如何追蹤個別手指。



## <a name="related-links"></a>相關連結

- [Android 觸控開始 （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android 觸控最終 （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
- [FingerPaint （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
