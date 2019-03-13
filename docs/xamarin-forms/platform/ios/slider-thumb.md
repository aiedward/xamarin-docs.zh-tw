---
title: 在 iOS 上點選 滑動軸捲動方塊
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 iOS 平台特定可設定滑動軸上點選 Slider.Value 屬性。
ms.prod: xamarin
ms.assetid: D0915D37-9A59-4728-BB6A-FE094A661275
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 43cc87f9d319295ce65d55488e1be032ae00a697
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208763"
---
# <a name="slider-thumb-tap-on-ios"></a>在 iOS 上點選 滑動軸捲動方塊

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

可讓此 iOS 平台專屬[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)點選位置上設定上的屬性[ `Slider` ](xref:Xamarin.Forms.Slider)列，而不是由拖曳`Slider`捲動方塊。 它由在 XAML 中設定[ `Slider.UpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty)可繫結的屬性，以`true`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

`Slider.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `Slider.SetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.SetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可用來控制是否在點選`Slider`列將會設定[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)屬性。 颾魤 ㄛ [ `Slider.GetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.GetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider}))方法可以用來傳回是否在點選`Slider`列將會設定`Slider.Value`屬性。

結果是在點選[ `Slider` ](xref:Xamarin.Forms.Slider)可移動列`Slider`thumb，並將[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)屬性：

![](slider-thumb-images/slider-updateontap.png "在已啟用點選滑桿更新")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
