---
title: 處理在 Xamarin.iOS 應用程式的觸控
description: 本文件所連結說明如何使用觸控介面、 多點觸控、 手勢，與在 Xamarin.iOS 應用程式的 3D 觸控的指南。
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/23/2017
ms.openlocfilehash: 5aabc3a3c2ffbcffc0e12379989f7eb43b03a902
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399287"
---
# <a name="handling-touch-in-xamarinios-apps"></a>處理在 Xamarin.iOS 應用程式的觸控

像其他行動平台，iOS 會有數種方式來處理觸控。 它可以支援多點觸控 — 許多在螢幕上的連絡點 — 和複雜的筆勢。 本指南介紹一些概念，以及實作在 iOS 上的觸控和筆勢的任用。

iOS 封裝中的觸控資料`UITouch`類別，可透過一系列的應用程式`UIResponder`方法。 應用程式可以覆寫這些方法中的子`UIView`並`UIViewController`，這兩者都繼承自`UIResponder`。

除了擷取觸控資料，iOS 會提供方法，用於解譯成筆勢的風格的模式。 這些筆勢辨識器接著可用來解譯應用程式特有的命令，例如影像的旋轉或上下的頁面。 iOS 提供豐富的類別，來處理與最小新增的程式碼的常見筆勢集合。

觸控和筆勢辨識器之間的選擇可能是令人困惑。 本指南建議，在一般情況下，喜好設定，應該會提供筆勢辨識器。 筆勢辨識器會實作為離散類別，以提供更高的關注點分離更佳的封裝。 這讓您更容易共用的程式碼數量降到最低的不同檢視之間的邏輯。

不過，有一些當您需要使用低層級的觸控式處理，並甚至建立 finger-paint 程式追蹤多個根手指，比方說。

## <a name="sections"></a>章節

-  [在 iOS 中觸控](touch-in-ios.md)
-  [逐步解說：在 iOS 中使用觸控](ios-touch-walkthrough.md)
-  [多點觸控追蹤](touch-tracking.md)

本指南可在 iOS 中觸控簡介。 如需有關如何在 iOS 中使用 3D 觸控和 Haptic 意見反應的詳細資訊，其出現於 iOS 9 和 10 分別，請參閱特定的下列指南：

* [3D Touch](~/ios/platform/3d-touch.md)
* [提供 Haptic 意見反應](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>相關連結

- [iOS 觸控開始 （範例）](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS 觸控最終 （範例）](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
- [FingerPaint （範例）](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
