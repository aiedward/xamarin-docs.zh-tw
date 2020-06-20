---
title: IOS 上的 VisualElement 投影
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，以在 VisualElement 上啟用陰影。
ms.prod: xamarin
ms.assetid: 2147FD66-058E-4BE5-840A-369842B26EC4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7410386e10f605fdeed452fe37755c1e48e6b9b9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136990"
---
# <a name="visualelement-drop-shadows-on-ios"></a>IOS 上的 VisualElement 投影

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定是用來啟用上的投影 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 它會在 XAML 中使用，方法是將 [`VisualElement.IsShadowEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsShadowEnabledProperty) 附加屬性設定為 `true` ，並使用一些額外的選擇性附加屬性來控制陰影：

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

或者，您也可以使用 Fluent API，從 c # 取用它：

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

`VisualElement.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 [ `VisualElement.SetIsShadowEnabled` ] （X： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetIsShadowEnabled （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。VisualElement}，system.string）方法，在命名空間中， [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來控制是否已在上啟用投影 `VisualElement` 。 此外，您可以叫用下列方法來控制陰影：

- [ `SetShadowColor` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetShadowColor （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。VisualElement}、 Xamarin.Forms 。色彩）–設定陰影的色彩。 預設色彩為 [`Color.Default`](xref:Xamarin.Forms.Color.Default*) 。
- [ `SetShadowOffset` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetShadowOffset （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。VisualElement}、 Xamarin.Forms 。[大小]）–設定陰影的位移。 位移會變更陰影的轉換方向，並指定為 [`Size`](xref:Xamarin.Forms.Size) 值。 `Size`結構值是以與裝置無關的單位來表示，第一個值是左邊的距離（負值）或右邊（正值），而第二個值是上方的距離（負值）或下方（正數值）。 這個屬性的預設值為（0.0，0.0），這會導致在的每一側轉換陰影 `VisualElement` 。
- [ `SetShadowOpacity` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetShadowOpacity （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。VisualElement}，system.string）–設定陰影的不透明度，其值在0.0 （透明）到1.0 （不透明）範圍內。 預設的不透明度值為0.5。
- [ `SetShadowRadius` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetShadowRadius （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。VisualElement}，System.object）–設定用來呈現陰影的模糊半徑。 預設半徑值為10.0。

> [!NOTE]
> 您可以藉由呼叫 [ `GetIsShadowEnabled` ] （x：，來查詢陰影的狀態 Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetIsShadowEnabled （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。VisualElement}）），[ `GetShadowColor` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetShadowColor （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。VisualElement}）），[ `GetShadowOffset` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetShadowOffset （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。VisualElement}）），[ `GetShadowOpacity` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetShadowOpacity （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。VisualElement}））和 [ `GetShadowRadius` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetShadowRadius （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。VisualElement}））方法。

結果是可以在上啟用投影 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ：

![](drop-shadow-images/drop-shadow.png "Drop shadow enabled")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
