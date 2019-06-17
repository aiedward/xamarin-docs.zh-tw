---
title: 觸控和筆勢在 Xamarin.Android 中
description: 在許多現今的裝置上的觸控式螢幕可讓使用者快速且有效率地與裝置互動自然且直覺的方式。 此互動不是只限於簡單觸控偵測-就能夠使用也筆勢。 比方說，捏合以縮放筆勢是一個非常常見的範例進行捏合以使用者可以放大或縮小的兩個手指在螢幕的一部分。本指南會檢查觸控，並在 Android 中的筆勢。
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7f957c9ff5a0e7c3a0821978703860ed2f723a92
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013117"
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>觸控和筆勢在 Xamarin.Android 中

_在許多現今的裝置上的觸控式螢幕可讓使用者快速且有效率地與裝置互動自然且直覺的方式。此互動不是只限於簡單觸控偵測-就能夠使用也筆勢。比方說，捏合以縮放筆勢是一個非常常見的範例進行捏合以使用者可以放大或縮小的兩個手指在螢幕的一部分。本指南會檢查觸控，並在 Android 中的筆勢。_

## <a name="touch-overview"></a>觸控式概觀

iOS 和 Android 會在它們處理觸控的方式類似。 兩者都可以支援多點觸控-在螢幕上的連絡人的許多點-和複雜的筆勢。 本指南介紹一些相似之處的概念，以及實作觸控的任用和筆勢兩種平台。

Android 使用`MotionEvent`物件封裝觸控資料，以及接聽修飾的檢視物件上的方法。

除了擷取觸控資料，iOS 和 Android 會提供方法，以便將解譯成筆勢的風格的模式。 這些筆勢辨識器接著可用來解譯應用程式特有的命令，例如影像的旋轉或上下的頁面。 Android 提供少數幾個支援的筆勢，以及要加入複雜的自訂軌跡簡單的資源。

不論您使用 Android 或 iOS 上，選擇觸控和筆勢辨識器可以是令人困惑的其中一個。 本指南建議，在一般情況下，喜好設定，應該會提供筆勢辨識器。 筆勢辨識器會實作為離散類別，以提供更高的關注點分離更佳的封裝。 這可讓您輕鬆地共用的程式碼數量降到最低的不同檢視之間的邏輯。

本指南會遵循類似的格式，針對每個作業系統： 首先，Api 會引入，並說明，因為它們是在哪一個觸控式互動是的建置的平台的觸控。 然後，我們探討世界的筆勢辨識器 – 第一次瀏覽一些常用的筆勢，並完成建立自訂的軌跡，應用程式。 最後，您會看到如何追蹤個人的手指來建立 finger-paint 程式使用低層級的觸控追蹤。

## <a name="sections"></a>章節

-  [Android 中的觸控](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [逐步解說：在 Android 中使用觸控](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [多點觸控追蹤](touch-tracking.md)

## <a name="summary"></a>總結

本指南中，我們會檢查在 Android 中的觸控。 這兩個作業系統中，我們已了解如何啟用觸控式以及如何回應觸控事件。 接下來，我們已了解筆勢和筆勢辨識器的一些 Android 和 iOS 提供處理一些較常見的案例。 我們會檢驗如何建立自訂的筆勢和應用程式中實作它們。 逐步解說示範的概念和 Api 在動作中，每個作業系統，您也看到了如何追蹤個人的手指。



## <a name="related-links"></a>相關連結

- [Android 觸控開始 （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android 觸控最終 （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
- [FingerPaint （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
