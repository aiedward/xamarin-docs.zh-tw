---
title: Xamarin 中的觸控和手勢
description: 現今許多裝置上的觸控畫面可讓使用者以自然且直覺的方式快速且有效率地與裝置互動。 這種互動方式不只限於簡單的觸控式偵測，也可以使用手勢。 例如，您可以使用兩個手指（使用者可以放大或縮小）來捏合螢幕的一部分，來縮小至縮放手勢的範例。本指南探討 Android 中的觸控和手勢。
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: b4740b91b3d59a3c50696af06eec4ff82bbf9b1e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455192"
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>Xamarin 中的觸控和手勢

_現今許多裝置上的觸控畫面可讓使用者以自然且直覺的方式快速且有效率地與裝置互動。這種互動方式不只限於簡單的觸控式偵測，也可以使用手勢。例如，您可以使用兩個手指（使用者可以放大或縮小）來捏合螢幕的一部分，來縮小至縮放手勢的範例。本指南探討 Android 中的觸控和手勢。_

## <a name="touch-overview"></a>Touch 總覽

iOS 和 Android 的方式類似于處理觸控的方式。 兩者都可支援螢幕和複雜手勢上的多點觸控。 本指南介紹一些概念中的相似之處，以及在兩個平臺上執行觸控和手勢的 particularities。

Android 會使用 `MotionEvent` 物件來封裝觸控資料，並使用 View 物件上的方法來接聽觸控。

除了捕捉觸控資料，iOS 和 Android 也提供用來解讀觸控模式的方式。 這些手勢辨識器可以接著用來解讀應用程式特定的命令，例如影像的旋轉或頁面的旋轉。 Android 提供一些支援的手勢和資源，可讓您輕鬆地新增複雜的自訂手勢。

無論您是在 Android 或 iOS 上工作，在觸控和手勢辨識器之間的選擇，可能會造成混淆。 本指南建議您一般應該將喜好設定給予手勢辨識器。 手勢辨識器會實作為離散類別，以提供更好的考慮和更佳的封裝分離。 這可讓您輕鬆地在不同的視圖之間共用邏輯，將撰寫的程式碼數量降到最低。

本指南會針對每個作業系統遵循類似的格式：首先，會介紹並說明平臺的觸控 Api，因為它們是建立觸控互動的基礎。 然後，我們會深入探討手勢辨識器的世界–先探索一些常見的手勢，然後完成建立應用程式的自訂手勢。 最後，您將瞭解如何使用低層級觸控追蹤來追蹤個別的手指，以建立手指繪圖程式。

## <a name="sections"></a>章節

- [Android 中的觸控](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
- [逐步解說：在 Android 中使用觸控](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
- [多點觸控追蹤](touch-tracking.md)

## <a name="summary"></a>摘要

在本指南中，我們已在 Android 中檢查觸控。 針對這兩個作業系統，我們已瞭解如何啟用觸控，以及如何回應觸控事件。 接下來，我們瞭解 Android 和 iOS 提供來處理一些更常見案例的手勢和一些手勢辨識器。 我們已探討如何建立自訂筆勢，並在應用程式中加以執行。 本逐步解說示範各作業系統運作的概念和 Api，您也會看到如何追蹤個別的手指。

## <a name="related-links"></a>相關連結

- [Android Touch 開始 (範例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch 最終 (範例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
- [FingerPaint (範例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)