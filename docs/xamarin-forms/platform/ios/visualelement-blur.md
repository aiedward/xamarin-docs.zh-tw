---
title: 在 iOS 上 VisualElement 模糊
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用將模糊套用至 VisualElement 的 iOS 平臺特定。
ms.prod: xamarin
ms.assetid: 2DE3B65E-B96E-4ECD-92DF-AA42D5205C44
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6a81e5a589e99a62aeaea58707228afd03016a8c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563935"
---
# <a name="visualelement-blur-on-ios"></a>在 iOS 上 VisualElement 模糊

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定用來模糊其下階層式內容，並可套用至任何內容 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 它是在 XAML 中使用，方法是將 [`VisualElement.BlurEffect`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty) 附加屬性設定為 [`BlurEffectStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) 列舉值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
  ...
  <AbsoluteLayout HorizontalOptions="Center">
      <Image Source="monkeyface.png" />
      <BoxView x:Name="boxView" ios:VisualElement.BlurEffect="ExtraLight" HeightRequest="300" WidthRequest="300" />
  </AbsoluteLayout>
  ...
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

`BoxView.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 [ `VisualElement.UseBlurEffect` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. UseBlurEffect (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement}， Xamarin.Forms 。命名空間中的 PlatformConfiguration. iOSSpecific. BlurEffectStyle) # A3 方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 可用來套用模糊效果，並 [`BlurEffectStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) 提供四個值的列舉： [`None`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None) 、 [`ExtraLight`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight) 、 [`Light`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light) 和 [`Dark`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark) 。

結果是將指定的套用 [`BlurEffectStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) 至 [`BoxView`](xref:Xamarin.Forms.BoxView) 實例，這會模糊 [`Image`](xref:Xamarin.Forms.Image) 其下的分層：

![模糊效果平臺特定](applying-blur-images/blur-effect.png)

> [!NOTE]
> 當將模糊效果加入至時 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ，仍會收到觸控事件 `VisualElement` 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)