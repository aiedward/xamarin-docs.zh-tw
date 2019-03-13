---
title: 在 iOS 上的同時移動瀏覽軌跡辨識
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 iOS 平台特定，可讓同時移動瀏覽至應用程式中使用的筆勢辨識。
ms.prod: xamarin
ms.assetid: 883D89DA-F8FF-4B97-9C3F-2DD05C96A495
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 32c3651734fcc94dd75372f0c47f0ffb22b4a4ee
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209703"
---
# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>在 iOS 上的同時移動瀏覽軌跡辨識

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

當[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)附加到在捲動檢視中，所有筆勢會擷取藉由移動瀏覽檢視`PanGestureRecognizer`並不會傳遞給捲動檢視。 因此，將不會再捲動捲動檢視。

可讓此 iOS 平台專屬`PanGestureRecognizer`捲動檢視來擷取，並分享捲動檢視中的移動瀏覽軌跡中。 它由在 XAML 中設定[ `Application.PanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) ; 附加屬性`true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

`Application.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.SetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可用來控制是否在捲動檢視中的移動瀏覽筆勢辨識器會擷取為移動瀏覽鍵筆勢，或擷取並共用取景位置調整手勢與捲動檢視。 颾魤 ㄛ [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.GetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application}))方法可以用來傳回包含捲動檢視是否為共用取景位置調整筆勢[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)。

因此，使用已啟用，當此平台專屬[ `ListView` ](xref:Xamarin.Forms.ListView)包含[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)，這兩個`ListView`而`PanGestureRecognizer`會收到取景位置調整筆勢和處理它。 不過，若使用時停用此平台專屬`ListView`包含`PanGestureRecognizer`，則`PanGestureRecognizer`會擷取取景位置調整動作並加以處理，而`ListView`就不會收到移動瀏覽軌跡。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
