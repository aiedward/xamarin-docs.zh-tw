---
title: 提供在 Xamarin.iOS Haptic 意見反應
description: 本文件說明如何提供 haptic Xamarin.iOS 應用程式中的意見反應。 它討論 UIImpactFeedbackGenerator、 UINotificationFeedbackGenerator 和 UISelectionFeedbackGenerator。
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d0dae6d6f50423474fbfebad5d630000e2160f6a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790184"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>提供在 Xamarin.iOS Haptic 意見反應

<a name="Overview" />

## <a name="overview"></a>總覽

在 iPhone 7 和 iPhone 上 7 加上、 Apple 已包含了提供其他方式來實際連絡使用者的新 haptic 回覆。 Haptic 意見反應 （通常稱為 Haptics） 會在使用者介面設計使用觸控 （透過強制、 振動的情況下或影片） 的意義。 使用這些新的 tactile 意見反應選項來取得使用者注意並強化其動作。

將會詳細介紹下列主題：

- [Haptic 意見反應的相關](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>Haptic 意見反應的相關

數個內建的 UI 項目已提供 haptic 回饋，例如選擇器、 交換器和滑桿。 iOS 10 現在增加的能力以程式設計方式觸發程序使用的具體子類別的 haptics`UIFeedbackGenerator`類別。

開發人員可以使用下列其中一種`UIFeedbackGenerator`子類別以程式設計的方式是觸發程序 haptic 的意見反應：

- `UIImpactFeedbackGenerator` -使用此意見反應產生器來補充某個動作或工作，例如呈現"thud 」 檢視投影片位置時，或兩個螢幕上的物件相衝突。
- `UINotificationFeedbackGenerator` -使用此意見反應產生器的通知，例如動作完成、 失敗或任何其他類型的警告。
- `UISelectionFeedbackGenerator` -在主動例如挑選清單中的項目變更選取範圍中使用此意見反應產生器。

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

使用此意見反應產生器來補充某個動作或工作，例如呈現"thud 」 檢視投影片位置時，或兩個螢幕上的物件相衝突。

使用下列程式碼，觸發程序影響的意見反應：

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

當開發人員建立的新執行個體`UIImpactFeedbackGenerator`類別提供`UIImpactFeedbackStyle`指定做為意見的強度：

- `Heavy`
- `Medium`
- `Light`

`Prepare`方法`UIImpactFeedbackGenerator`呼叫以通知系統 haptic 意見反應即將，好讓它可以延遲降至最低。

`ImpactOccurred`方法然後觸發 haptic 意見反應。

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

使用此意見反應產生器，例如動作完成、 失敗或任何其他類型的警告通知。

使用下列程式碼，觸發程序通知意見反應：

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

新執行個體`UINotificationFeedbackGenerator`建立類別和其`Prepare`呼叫方法來通知系統 haptic 意見反應即將，好讓它可以延遲降至最低。

`NotificationOccurred`稱為觸發 haptic 意見反應與給定`UINotificationFeedbackType`的：

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

使用此意見反應產生器選項，例如挑選清單中的項目正在變更。

使用下列程式碼，觸發程序選取回應：

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

新執行個體`UISelectionFeedbackGenerator`建立類別和其`Prepare`呼叫方法來通知系統 haptic 意見反應即將，好讓它可以延遲降至最低。

`SelectionChanged`方法然後觸發 haptic 意見反應。

## <a name="summary"></a>總結

這篇文章已涵蓋 haptic 意見反應 iOS 10 以及如何實作它們 Xamarin.iOS 中可用的新的類型。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
