---
title: IOS 上的同時移動流覽手勢辨識
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用可讓您在應用程式中使用同時平移手勢辨識的 iOS 平臺特定。
ms.prod: xamarin
ms.assetid: 883D89DA-F8FF-4B97-9C3F-2DD05C96A495
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f1baf5d71cc25a1f84ff683e9d0072f34715c0da
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373233"
---
# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>IOS 上的同時移動流覽手勢辨識

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

當 [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) 連接到滾動圖內的視圖時，所有的平移手勢都是由所捕捉， `PanGestureRecognizer` 而且不會傳遞到捲軸。 因此，[滾動] 視圖將不再滾動。

此 iOS 平臺特定可讓 `PanGestureRecognizer` 在滾動視圖中使用捲軸來捕捉和共用平移手勢。 它是在 XAML 中使用，方法是將 [`Application.PanGestureRecognizerShouldRecognizeSimultaneously`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) 附加屬性設定為 `true` ：

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

`Application.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SetPanGestureRecognizerShouldRecognizeSimultaneously (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Application}、system.string) # A3 方法（在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間中）是用來控制捲軸中的平移手勢辨識器是否會捕捉移動流覽手勢，或使用捲軸來捕捉和共用平移手勢。 此外，[ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. GetPanGestureRecognizerShouldRecognizeSimultaneously (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Application} ) # A3 方法可以用來傳回是否與包含的滾動視圖共用平移手勢 [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) 。

因此，在此平臺特定啟用的情況下，當包含時， [`ListView`](xref:Xamarin.Forms.ListView) [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) `ListView` 和 `PanGestureRecognizer` 都會接收平移手勢並進行處理。 不過，使用此平臺特定的停用時，當包含時， `ListView` `PanGestureRecognizer` 會捕捉移動流覽 `PanGestureRecognizer` 手勢並加以處理，而不會接收移動流覽 `ListView` 手勢。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)