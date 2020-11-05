---
title: 在 iOS 上點按滑動軸
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，藉由在滑動軸上點擊，來設定 [值] 屬性。
ms.prod: xamarin
ms.assetid: D0915D37-9A59-4728-BB6A-FE094A661275
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 66fd7fcd5accbc1b6b1595fcb8ee7b1f8eb8d6d4
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370737"
---
# <a name="slider-thumb-tap-on-ios"></a>在 iOS 上點按滑動軸

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定可讓您透過在 [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) 橫條上的位置來設定屬性 [`Slider`](xref:Xamarin.Forms.Slider) ，而不需要拖曳 `Slider` thumb。 它是在 XAML 中使用，方法是將可系結 [`Slider.UpdateOnTap`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) 屬性設定為 `true` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

`Slider.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 [ `Slider.SetUpdateOnTap` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SetUpdateOnTap (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。滑杆}、system.string) # A3 方法（在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間中）是用來控制是否要按下橫條來 `Slider` 設定 [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) 屬性。 此外，[ `Slider.GetUpdateOnTap` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. GetUpdateOnTap (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。滑杆} ) # A3 方法可以用來傳回點擊 `Slider` 線是否會設定 `Slider.Value` 屬性。

結果是，按一下 [`Slider`](xref:Xamarin.Forms.Slider) 橫條可以移動 `Slider` thumb 並設定 [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) 屬性：

![啟用時滑動軸更新](slider-thumb-images/slider-updateontap.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)