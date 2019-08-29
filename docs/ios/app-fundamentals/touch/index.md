---
title: 在 Xamarin iOS 應用程式中處理觸控
description: 本檔連結的指南會說明如何在 Xamarin iOS 應用程式中使用觸控、多點觸控、筆勢和 3D Touch。
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/23/2017
ms.openlocfilehash: 60bd87adb56e311407280ba98f48d8c3e2160836
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121010"
---
# <a name="handling-touch-in-xamarinios-apps"></a>在 Xamarin iOS 應用程式中處理觸控

就像其他行動平臺一樣, iOS 有數種方式可處理觸控。 它可以支援多點觸控, 也就是螢幕上的許多連絡人, 還有複雜的手勢。 本指南介紹一些概念, 以及在 iOS 上執行觸控和手勢的 particularities。

iOS 會將觸控資料封裝`UITouch`在類別中, 透過一`UIResponder`系列的方法可供應用程式使用。 應用程式可以在`UIView`和`UIViewController`的子類別中覆寫這些方法, 這`UIResponder`兩者都是繼承自。

除了捕捉觸控資料, iOS 也提供將觸控模式解讀為手勢的方法。 這些手勢辨識器可以輪流用來解讀應用程式特定的命令, 例如影像的旋轉或頁面的回合。 iOS 提供了一組豐富的類別, 可透過最小的加入程式碼來處理一般手勢。

觸控和手勢辨識器之間的選擇可能會令人混淆。 本指南建議一般喜好設定應給予手勢辨識器。 筆勢辨識器會實作為離散類別, 以提供更好的顧慮分隔和更好的封裝。 這可讓您直接在不同的視圖之間共用邏輯, 將寫入的程式碼量降到最低。

不過, 有時候您需要使用低層級的觸控處理, 甚至是追蹤多個手指, 例如, 建立手指繪製程式。

## <a name="sections"></a>章節

- [在 iOS 中觸控](touch-in-ios.md)
- [逐步解說：在 iOS 中使用觸控](ios-touch-walkthrough.md)
- [多點觸控追蹤](touch-tracking.md)

本指南可做為 iOS 的觸控簡介。 如需有關在 ios 9 和10中分別引進的 3D Touch 和 Haptic 意見反應的詳細資訊, 請參閱以下的特定指南:

- [3D Touch](~/ios/platform/3d-touch.md)
- [提供 Haptic 意見反應](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>相關連結

- [iOS 觸控啟動 (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start)
- [iOS 觸控最終 (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final)
- [FingerPaint (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)
