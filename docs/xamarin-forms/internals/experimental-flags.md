---
title: Xamarin.Forms 實驗性旗標
description: Xamarin.Forms 實驗性旗標可讓工程小組更快速地將新功能交付給使用者，同時仍然能夠在移至穩定版本之前變更功能 Api。
ms.prod: xamarin
ms.assetid: AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d9f6f5e2d27f04dadfa2b830703bb36355072c44
ms.sourcegitcommit: c9f62ed4bab516be18606e87e89e01f57b4ad17e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98163487"
---
# <a name="no-locxamarinforms-experimental-flags"></a>Xamarin.Forms 實驗性旗標

當新 Xamarin.Forms 功能執行時，有時會置於實驗旗標後方。 如此一來，工程團隊就能更快為您提供新功能，同時仍能在移至穩定版本之前變更功能 Api。 一旦功能移至穩定的版本，就會移除實驗旗標。

Xamarin.Forms 包含下列實驗旗標：

- `Shell_UWP_Experimental`

您必須在應用程式中啟用旗標或旗標，才能使用在實驗旗標背後的功能。 有兩種方法可啟用實驗旗標：

- 在您的平臺專案中啟用實驗旗標。
- 在您的類別中啟用實驗旗標 `App` 。

> [!WARNING]
> 使用在實驗旗標背後的功能，而不啟用旗標，會導致您的應用程式擲回例外狀況，指出必須啟用的旗標。

## <a name="enable-flags-in-platform-projects"></a>啟用平臺專案中的旗標

`Xamarin.Forms.Forms.SetFlags`方法可以用來在您的平臺專案中啟用實驗旗標：

```csharp
Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

`SetFlags`您應該在 `AppDelegate` IOS、 `MainActivity` Android 上的類別和 UWP 的類別中叫用方法 `App` 。

> [!IMPORTANT]
> 在您的平臺專案中啟用實驗旗標必須在叫用方法之前發生 `Forms.Init` 。

`Xamarin.Forms.Forms.SetFlags`方法 `string` 會接受陣列引數，讓您可以在單一方法呼叫中啟用多個實驗旗標：

```csharp
Xamarin.Forms.Forms.SetFlags(new string[] { "Shell_UWP_Experimental", "AnotherFeature_Experimental" });
```

> [!WARNING]
> 請勿多次呼叫 `SetFlags` 方法，因為後續的呼叫將會覆寫先前呼叫的結果。

## <a name="enable-flags-in-your-app-class"></a>在您的應用程式類別中啟用旗標

`Device.SetFlags`方法可以用來在 `App` 您的共用程式碼專案的類別中啟用實驗旗標：

```csharp
Device.SetFlags(new string[]{ "Shell_UWP_Experimental" });
```

`Device.SetFlags`方法 `IReadOnlyList<string>` 會接受引數，讓您可以在單一方法呼叫中啟用多個實驗旗標：

```csharp
Device.SetFlags(new string[]{ "Shell_UWP_Experimental", "AnotherFeature_Experimental" });
```

> [!WARNING]
> 請勿多次呼叫 `SetFlags` 方法，因為後續的呼叫將會覆寫先前呼叫的結果。

## <a name="old-experimental-flags"></a>舊的實驗旗標

下表列出現在已正式運作之功能的實驗旗標，以及 Xamarin.Forms 已移除實驗旗標的版本：

| 旗標 | Xamarin.Forms 釋放 |
| ---- | --------------------- |
| `AppTheme_Experimental` | 4.8 |
| `Brush_Experimental` | 5.0 |
| `CarouselView_Experimental` | 5.0 |
| `CollectionView_Experimental` | 4.3 |
| `DragAndDrop_Experimental` | 5.0 |
| `FastRenderers_Experimental` | 4.0 |
| `IndicatorView_Experimental` | 4.7 |
| `Markup_Experimental` | 5.0 (移至 Xamarin 社區工具組)  |
| `MediaElement_Experimental` | 5.0 (移至 Xamarin 社區工具組)  |
| `RadioButton_Experimental` | 5.0 |
| `Shapes_Experimental` | 5.0 |
| `Shell_Experimental` | 4.0  |
| `StateTriggers_Experimental` | 4.7 |
| `SwipeView_Experimental` | 5.0 |
| `Visual_Experimental` | 3.6 |
