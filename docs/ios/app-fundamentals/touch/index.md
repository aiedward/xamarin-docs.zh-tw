---
title: 在 Xamarin iOS 應用程式中處理觸控
description: 本檔連結的指南會說明如何在 Xamarin iOS 應用程式中使用觸控、多點觸控、筆勢和 3D Touch。
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/23/2017
ms.openlocfilehash: edf5f1c82857930a2647f306195a4c82f4338db6
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291489"
---
# <a name="handling-touch-in-xamarinios-apps"></a>在 Xamarin iOS 應用程式中處理觸控

就像其他行動平臺一樣，iOS 有數種方式可處理觸控。 它可以支援多點觸控，也就是螢幕上的許多連絡人，還有複雜的手勢。 本指南介紹一些概念，以及在 iOS 上執行觸控和手勢的 particularities。

iOS 會將觸控資料封裝在 `UITouch` 類別中，這可透過一系列的 `UIResponder` 方法提供給應用程式使用。 應用程式可以在 `UIView` 和 `UIViewController`的子類別中覆寫這些方法，這兩者都是繼承自 `UIResponder`。

除了捕捉觸控資料，iOS 也提供將觸控模式解讀為手勢的方法。 這些手勢辨識器可以輪流用來解讀應用程式特定的命令，例如影像的旋轉或頁面的回合。 iOS 提供了一組豐富的類別，可透過最小的加入程式碼來處理一般手勢。

觸控和手勢辨識器之間的選擇可能會令人混淆。 本指南建議一般喜好設定應給予手勢辨識器。 筆勢辨識器會實作為離散類別，以提供更好的顧慮分隔和更好的封裝。 這可讓您直接在不同的視圖之間共用邏輯，將寫入的程式碼量降到最低。

不過，有時候您需要使用低層級的觸控處理，甚至是追蹤多個手指，例如，建立手指繪製程式。

## <a name="sections"></a>章節

- [在 iOS 中觸控](touch-in-ios.md)
- [逐步解說：在 iOS 中使用觸控](ios-touch-walkthrough.md)
- [多點觸控追蹤](touch-tracking.md)

本指南可做為 iOS 的觸控簡介。 如需有關在 ios 9 和10中分別引進的 3D Touch 和 Haptic 意見反應的詳細資訊，請參閱以下的特定指南：

- [3D Touch](~/ios/platform/3d-touch.md)
- [提供 Haptic 意見反應](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>相關連結

- [iOS 觸控最終（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final)
- [FingerPaint （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)
