---
title: 在 iOS 上 VisualElement 陰影
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用可在 VisualElement 上啟用陰影的 iOS 平臺特定。
ms.prod: xamarin
ms.assetid: 2147FD66-058E-4BE5-840A-369842B26EC4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2e54182ca5c1f2afbc107011aea6b84af864e78d
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562338"
---
# <a name="visualelement-drop-shadows-on-ios"></a>在 iOS 上 VisualElement 陰影

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定是用來啟用上的投影 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 它是在 XAML 中使用，方法是將 [`VisualElement.IsShadowEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsShadowEnabledProperty) 附加屬性設定為 `true` ，以及一些可控制陰影的其他選用附加屬性：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <BoxView ...
                 ios:VisualElement.IsShadowEnabled="true"
                 ios:VisualElement.ShadowColor="Purple"
                 ios:VisualElement.ShadowOpacity="0.7"
                 ios:VisualElement.ShadowRadius="12">
            <ios:VisualElement.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </ios:VisualElement.ShadowOffset>
         </BoxView>
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var boxView = new BoxView { Color = Color.Aqua, WidthRequest = 100, HeightRequest = 100 };
boxView.On<iOS>()
       .SetIsShadowEnabled(true)
       .SetShadowColor(Color.Purple)
       .SetShadowOffset(new Size(10,10))
       .SetShadowOpacity(0.7)
       .SetShadowRadius(12);
```

`VisualElement.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 [ `VisualElement.SetIsShadowEnabled` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetIsShadowEnabled (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} 是命名空間中的}、system.string) # A3 方法， [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 可用來控制是否啟用陰影 `VisualElement` 。 此外，也可以叫用下列方法來控制陰影：

- [ `SetShadowColor` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetShadowColor (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement}， Xamarin.Forms 。色彩) # A3 –設定陰影的色彩。 預設色彩為 [`Color.Default`](xref:Xamarin.Forms.Color.Default*) 。
- [ `SetShadowOffset` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetShadowOffset (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement}， Xamarin.Forms 。大小) # A3 –設定陰影的位移。 位移會變更陰影轉換的方向，並指定為 [`Size`](xref:Xamarin.Forms.Size) 值。 `Size`結構值是以與裝置無關的單位來表示，而第一個值是左邊的距離 (負數值) 或右 (正值) ，而第二個值則是超過 (負數值) 或低於 (正數值) 的距離。 這個屬性的預設值是 (0.0，0.0) ，這會導致在的每一側周圍轉換影子 `VisualElement` 。
- [ `SetShadowOpacity` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetShadowOpacity (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement}，system.string) # A3 –設定投影的不透明度，其值位於範圍 0.0 (透明) 至 1.0 (不透明) 。 預設的不透明度值為0.5。
- [ `SetShadowRadius` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetShadowRadius (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement}，system.string) # A3 –設定用來呈現投影的模糊半徑。 預設半徑值為10.0。

> [!NOTE]
> 您可以藉由呼叫 [ `GetIsShadowEnabled` ] (x：來查詢陰影的狀態 Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetIsShadowEnabled (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} ) # A3，[ `GetShadowColor` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetShadowColor (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} ) # A7，[ `GetShadowOffset` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetShadowOffset (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} ) # A11，[ `GetShadowOpacity` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetShadowOpacity (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} ) # A15 和 [ `GetShadowRadius` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetShadowRadius (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} ) # A19 方法。

結果是可以在上啟用陰影 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ：

![已啟用陰影](drop-shadow-images/drop-shadow.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)