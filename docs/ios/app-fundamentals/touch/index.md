---
title: 處理 Xamarin iOS 應用程式中的觸控
description: 本檔連結的指南會說明如何在 Xamarin iOS 應用程式中使用觸控、多點觸控、手勢及 3D Touch。
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/23/2017
ms.openlocfilehash: db3e66920beb355e0b05df2118cd2645c602f0d5
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433923"
---
# <a name="handling-touch-in-xamarinios-apps"></a>處理 Xamarin iOS 應用程式中的觸控

IOS 和其他行動平臺一樣，有許多方法可以處理觸控。 它可支援多點觸控（螢幕上的許多連絡人點），以及複雜的手勢。 本指南介紹一些概念，以及在 iOS 上執行觸控和手勢的 particularities。

iOS 會將觸控資料封裝在 `UITouch` 類別中，並透過一系列的方法提供給應用程式使用 `UIResponder` 。 應用程式可以在和的子類別中覆寫這些方法，而這兩種方法 `UIView` `UIViewController` 都是繼承自 `UIResponder` 。

除了捕捉觸控資料，iOS 也提供用來解讀手勢模式的方法。 這些手勢辨識器可以接著用來解讀應用程式特定的命令，例如影像的旋轉或頁面的旋轉。 iOS 提供了一組豐富的類別，可使用最少的程式碼來處理一般手勢。

在觸控和手勢辨識器之間的選擇，可能會造成混淆。 本指南建議您一般應該將喜好設定給予手勢辨識器。 手勢辨識器會實作為離散類別，以提供更好的考慮和更佳的封裝分離。 這可讓您直接在不同的視圖之間共用邏輯，將撰寫的程式碼數量降到最低。

不過，有時候您需要使用低層級觸控處理，甚至是追蹤多個手指，例如建立手指繪圖程式。

## <a name="sections"></a>章節

- [在 iOS 中觸控](touch-in-ios.md)
- [逐步解說：在 iOS 中使用觸控](ios-touch-walkthrough.md)
- [多點觸控追蹤](touch-tracking.md)

本指南是 iOS 中觸控的簡介。 如需有關在 ios 中使用 3D Touch 和 Haptic 意見反應的詳細資訊，請參閱以下的特定指南：

- [3D Touch](~/ios/platform/3d-touch.md)
- [提供 Haptic 意見反應](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>相關連結

- [iOS 觸控最終 (範例) ](/samples/xamarin/ios-samples/applicationfundamentals-touch-final)
- [FingerPaint (範例) ](/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)