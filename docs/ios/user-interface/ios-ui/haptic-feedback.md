---
title: 在 Xamarin 中提供 Haptic 意見反應
description: 本檔說明如何在 Xamarin iOS 應用程式中提供 haptic 意見反應。 其中討論 UIImpactFeedbackGenerator、UINotificationFeedbackGenerator 和 UISelectionFeedbackGenerator。
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: e6879db1631bc6b58a36142344ff1fba997ee4fb
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432047"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>在 Xamarin 中提供 Haptic 意見反應

<a name="Overview"></a>

## <a name="overview"></a>概觀

在 iPhone 7 和 iPhone 7 Plus 上，Apple 已包含新的 haptic 回應，可提供實際參與使用者的其他方式。 Haptic 意見 (通常稱為 Haptics) 透過消費者介面設計中的強制、振動或移動) ，使用觸控 (的意義。 您可以使用這些新的 tactile 意見反應選項，來取得使用者的注意力並強化其動作。

將會詳細介紹下列主題：

- [關於 Haptic 意見反應](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback"></a>

## <a name="about-haptic-feedback"></a>關於 Haptic 意見反應

數個內建的 UI 元素已提供 haptic 的意見反應，例如選擇器、開關和滑杆。 iOS 10 現在新增了以程式設計方式使用類別的具體子類別來觸發 haptics 的功能 `UIFeedbackGenerator` 。

開發人員可以使用下列其中一個子 `UIFeedbackGenerator` 類別，以程式設計方式觸發 haptic 意見反應：

- `UIImpactFeedbackGenerator` -使用此意見反應產生器來補充動作或工作，例如，當 View 滑入位置或兩個畫面上的物件衝突時，呈現「thud」。
- `UINotificationFeedbackGenerator` -將此意見反應產生器用於通知，例如動作完成、失敗或任何其他類型的警告。
- `UISelectionFeedbackGenerator` -將此意見反應產生器用於選取的活動變更，例如從清單挑選項目。

<a name="UIImpactFeedbackGenerator"></a>

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

您可以使用此意見反應產生器來補充動作或工作，例如，當視野滑入位置或兩個畫面上的物件衝突時，呈現「thud」。

使用下列程式碼來觸發影響意見反應：

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

當開發人員建立類別的新實例時， `UIImpactFeedbackGenerator` 會提供將 `UIImpactFeedbackStyle` 意見反應的強度指定為：

- `Heavy`
- `Medium`
- `Light`

的 `Prepare` 方法 `UIImpactFeedbackGenerator` 會呼叫，以通知系統 haptic 的意見反應即將發生，以便將延遲降至最低。

`ImpactOccurred`然後，方法會觸發 haptic 意見反應。

<a name="UINotificationFeedbackGenerator"></a>

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

使用此意見反應產生器來取得通知，例如動作完成、失敗或任何其他類型的警告。

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

`UINotificationFeedbackGenerator`系統會建立類別的新實例，並 `Prepare` 呼叫其方法來通知系統 haptic 的意見反應即將發生，以便將延遲降至最低。

`NotificationOccurred`呼叫以觸發具有下列指定的 haptic 意見反應 `UINotificationFeedbackType` ：

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator"></a>

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

將此意見反應產生器用於選取的活動變更，例如從清單挑選項目。

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

`UISelectionFeedbackGenerator`系統會建立類別的新實例，並 `Prepare` 呼叫其方法來通知系統 haptic 的意見反應即將發生，以便將延遲降至最低。

`SelectionChanged`然後，方法會觸發 haptic 意見反應。

## <a name="summary"></a>摘要

本文涵蓋了 iOS 10 提供的新 haptic 意見反應類型，以及如何在 Xamarin 中加以執行。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)