---
title: 提供 Haptic 意見反應在 Xamarin.iOS 中
description: 本文件說明如何提供 haptic 意見反應的 Xamarin.iOS 應用程式中。 它討論 UIImpactFeedbackGenerator、 UINotificationFeedbackGenerator 和 UISelectionFeedbackGenerator。
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: b2c381c59ba1574e80babc2c7e68535a3deffe35
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384615"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>提供 Haptic 意見反應在 Xamarin.iOS 中

<a name="Overview" />

## <a name="overview"></a>總覽

IPhone 7 和 iPhone 上 7 Plus，Apple 已包含了提供實際接觸使用者的其他方式的新 haptic 回應。 Haptic 意見反應 （通常稱為 Haptics） 會在使用者介面設計使用觸控 （透過強制、 振動的情況下或動作） 的意義。 取得使用者的注意力，並強化其動作中使用這些新的 tactile 的意見反應選項。

將會詳細介紹下列主題：

- [關於 Haptic 意見反應](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>關於 Haptic 意見反應

數個內建的 UI 項目已提供 haptic 意見反應，例如選擇器、 切換和滑桿。 iOS 10 現在將能夠以程式設計方式觸發程序使用的具體子類別的 haptics`UIFeedbackGenerator`類別。

開發人員可以使用下列其中一種`UIFeedbackGenerator`子類別以程式設計方式觸發程序 haptic 意見反應：

- `UIImpactFeedbackGenerator` -使用此意見反應產生器來補充動作或工作，例如呈現"thud 」 檢視滑至位置時，或在螢幕上的兩個物件碰撞在一起。
- `UINotificationFeedbackGenerator` -使用此意見反應產生器的通知，例如動作完成、 失敗或任何其他類型的警告。
- `UISelectionFeedbackGenerator` -使用此意見反應產生器進行主動變更挑選清單中的項目，例如選取項目。

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

使用此意見反應產生器，來補充動作或工作，例如呈現"thud 」 檢視滑至位置時，或在螢幕上的兩個物件碰撞在一起。

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

`Prepare`方法的`UIImpactFeedbackGenerator`呼叫以通知系統 haptic 意見反應即將發生，以便它可以延遲降至最低。

`ImpactOccurred`方法接著觸發程序的 haptic 意見反應。

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

使用此意見反應產生器，例如警告動作完成、 失敗或任何其他類型的通知。

使用下列程式碼，觸發程序通知的意見反應：

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

新執行個體`UINotificationFeedbackGenerator`建立類別並將其`Prepare`呼叫方法來通知系統 haptic 意見反應即將發生，以便它可以延遲降至最低。

`NotificationOccurred` Haptic 意見反應與觸發程序會呼叫指定`UINotificationFeedbackType`的：

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

使用此意見反應產生器，主動變更挑選清單中的項目，例如選取範圍。

使用下列程式碼，觸發程序選項意見反應：

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

新執行個體`UISelectionFeedbackGenerator`建立類別並將其`Prepare`呼叫方法來通知系統 haptic 意見反應即將發生，以便它可以延遲降至最低。

`SelectionChanged`方法接著觸發程序的 haptic 意見反應。

## <a name="summary"></a>總結

這篇文章已涵蓋適用於 iOS 10 和如何在 Xamarin.iOS 中實作它們的 haptic 意見反應的新類型。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
