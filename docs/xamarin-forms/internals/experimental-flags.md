---
title: Xamarin。表單實驗性旗標
description: '[Xamarin] 實驗旗標可讓工程小組更快速地將新功能交付給使用者，同時仍然能夠在移到穩定版本之前，先變更功能 Api。'
ms.prod: xamarin
ms.assetid: AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2020
ms.openlocfilehash: cebb996da992058616f9cf96ef3212c9ce27022a
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2020
ms.locfileid: "80112586"
---
# <a name="xamarinforms-experimental-flags"></a>Xamarin。表單實驗性旗標

當新的 [Xamarin] 表單功能執行時，有時會放在實驗旗標後方。 這可讓工程小組更快速地為您提供新功能，同時仍可在將功能 Api 移至穩定版本之前加以變更。 一旦將功能移至穩定版本之後，就會移除實驗旗標。

Xamarin 包含下列實驗旗標：

- `CarouselView_Experimental`
- `IndicatorView_Experimental`
- `Markup_Experimental`
- `MediaElement_Experimental`
- `Shell_UWP_Experimental`
- `StateTriggers_Experimental`
- `SwipeView_Experimental`

使用實驗旗標後方的功能時，您必須在應用程式中啟用旗標或旗標。 有兩種方法可以啟用實驗旗標：

- 在您的平臺專案中啟用實驗旗標或旗標。
- 在您的 `App` 類別中啟用實驗旗標或旗標。

> [!WARNING]
> 使用實驗性旗標後方的功能，而不啟用旗標，會導致應用程式擲回例外狀況，指出必須啟用的旗標。

## <a name="enable-flags-in-platform-projects"></a>啟用平臺專案中的旗標

您可以使用 `Xamarin.Forms.Forms.SetFlags` 方法，在您的平臺專案中啟用實驗旗標：

```csharp
Xamarin.Forms.Forms.SetFlags("CarouselView_Experimental");
```

在 iOS 上的 `AppDelegate` 類別、Android 上的 `MainActivity` 類別，以及 UWP 的 `App` 類別中，都應叫用 `SetFlags` 方法。

> [!IMPORTANT]
> 在您的平臺專案中啟用實驗旗標必須在叫用 `Forms.Init` 方法之前發生。

`Xamarin.Forms.Forms.SetFlags` 方法會接受 `string` 陣列引數，讓您可以在單一方法呼叫中啟用多個實驗旗標：

```csharp
Xamarin.Forms.Forms.SetFlags(new string[] { "CarouselView_Experimental", "IndicatorView_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> 請勿多次呼叫 `SetFlags` 方法，因為後續呼叫將會覆寫先前呼叫的結果。

## <a name="enable-flags-in-your-app-class"></a>啟用應用程式類別中的旗標

`Device.SetFlags` 方法可以用來在共用程式碼專案的 `App` 類別中啟用實驗旗標：

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

`Device.SetFlags` 方法會接受 `IReadOnlyList<string>` 引數，讓您可以在單一方法呼叫中啟用多個實驗旗標：

```csharp
Device.SetFlags(new string[]{ "CarouselView_Experimental", "MediaElement_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> 請勿多次呼叫 `SetFlags` 方法，因為後續呼叫將會覆寫先前呼叫的結果。
