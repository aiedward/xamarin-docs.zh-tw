---
title: 在 Xamarin 中提供 Haptic 的意見反應
description: 本檔說明如何在 Xamarin iOS 應用程式中提供 haptic 的意見反應。 其中討論 UIImpactFeedbackGenerator、UINotificationFeedbackGenerator 和 UISelectionFeedbackGenerator。
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 156af7a5336ac091c0202e38a3a59a32846e281a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003352"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>在 Xamarin 中提供 Haptic 的意見反應

<a name="Overview" />

## <a name="overview"></a>總覽

在 iPhone 7 和 iPhone 7 Plus 上，Apple 已包含新的 haptic 回應，可提供其他方式來實際與使用者互動。 Haptic 意見反應（通常簡稱為 Haptics）會使用使用者介面設計中的觸控方式（透過強制、vibrations 或動作）。 使用這些新的觸覺意見反應選項，以取得使用者的注意並強化其動作。

將會詳細介紹下列主題：

- [關於 Haptic 意見反應](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>關於 Haptic 意見反應

數個內建的 UI 元素已提供 haptic 的意見反應，例如選擇器、交換器和滑杆。 iOS 10 現在新增了以程式設計方式使用 `UIFeedbackGenerator` 類別的具體子類別來觸發 haptics 的功能。

開發人員可以使用下列其中一個 `UIFeedbackGenerator` 子類別，以程式設計方式觸發 haptic 的意見反應：

- `UIImpactFeedbackGenerator`-使用此意見反應產生器來補充動作或工作，例如，當視圖滑入位置或兩個螢幕物件衝突時呈現「thud」。
- `UINotificationFeedbackGenerator`-使用此意見反應產生器進行通知，例如動作完成、失敗或其他任何類型的警告。
- `UISelectionFeedbackGenerator`-使用此意見產生器，讓選取範圍主動變更，例如從清單中挑選項目。

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

使用此意見反應產生器來補充動作或工作，例如，當視圖滑入位置或兩個螢幕物件衝突時，呈現「thud」。

使用下列程式碼觸發影響意見反應：

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

當開發人員建立 `UIImpactFeedbackGenerator` 類別的新實例時，他們會提供 `UIImpactFeedbackStyle` 將意見反應的強度指定為：

- `Heavy`
- `Medium`
- `Light`

呼叫 `UIImpactFeedbackGenerator` 的 `Prepare` 方法，以通知系統 haptic 意見反應即將發生，使其可以將延遲降到最低。

然後，`ImpactOccurred` 方法會觸發 haptic 的意見反應。

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

使用此意見反應產生器來進行通知，例如動作完成、失敗或其他任何類型的警告。

使用下列程式碼來觸發通知意見反應：

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

建立 `UINotificationFeedbackGenerator` 類別的新實例，並呼叫它的 `Prepare` 方法來通知系統 haptic 意見反應即將發生，使其可以將延遲降到最低。

呼叫 `NotificationOccurred` 以觸發具有給定 `UINotificationFeedbackType` 的 haptic 意見反應：

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

使用此意見產生器，讓選取範圍主動變更，例如從清單中挑選項目。

使用下列程式碼來觸發選取意見反應：

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

建立 `UISelectionFeedbackGenerator` 類別的新實例，並呼叫它的 `Prepare` 方法來通知系統 haptic 意見反應即將發生，使其可以將延遲降到最低。

然後，`SelectionChanged` 方法會觸發 haptic 的意見反應。

## <a name="summary"></a>總結

本文涵蓋了 iOS 10 中提供的新 haptic 意見反應，以及如何在 Xamarin 中執行。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
