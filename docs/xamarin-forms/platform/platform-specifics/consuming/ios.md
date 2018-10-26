---
title: iOS 平台特性
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章會示範如何使用 iOS 平台特性 Xamarin.Forms 內建。
ms.prod: xamarin
ms.assetid: C0837996-A1E8-47F9-B3A8-98EE43B4A675
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: 69f754db0fd9661fb317f43c7cda546b0b510265
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119399"
---
# <a name="ios-platform-specifics"></a>iOS 平台特性

_平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。這篇文章會示範如何使用 iOS 平台特性 Xamarin.Forms 內建。_

## <a name="visualelements"></a>VisualElements

在 iOS 上，下列平台特有的功能被供 Xamarin.Forms 檢視、 頁面和配置而定：

- 模糊的任何支援[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱 <<c0> [ 套用模糊](#blur)。
- 停用上支援的舊版的色彩模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱 <<c0> [ 停用舊版色彩模式](#legacy-color-mode)。
- 在啟用下拉式陰影[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱 <<c0> [ 啟用陰影](#drop-shadow)。

<a name="blur" />

### <a name="applying-blur"></a>套用模糊

此平台專屬用以模糊分層在其下的內容，而由在 XAML 中設定[ `VisualElement.BlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty)附加屬性的值[ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle)列舉型別：

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

`BoxView.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `VisualElement.UseBlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，用來與套用模糊效果[ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle)提供四個列舉值： [ `None` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None)， [ `ExtraLight` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight)， [ `Light` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light)，以及[ `Dark` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark)。

結果是，指定[ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle)會套用至[ `BoxView` ](xref:Xamarin.Forms.BoxView)執行個體，哪些模糊[ `Image` ](xref:Xamarin.Forms.Image)分層其下方：

![](ios-images/blur-effect.png "模糊效果平台專屬")

> [!NOTE]
> 新增要柔邊效果時[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)，觸控事件將仍會收到`VisualElement`。

<a name="legacy-color-mode" />

### <a name="disabling-legacy-color-mode"></a>停用舊版色彩模式

Xamarin.Forms 檢視的一些功能的舊版的色彩模式。 在此模式中，當[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)檢視的屬性設定為`false`，檢視將會覆寫設定的使用者停用狀態的預設原生色彩的色彩。 針對回溯相容性，這個舊版的色彩模式仍受支援的檢視表的預設行為。

此平台專屬停用此舊版的色彩模式，以便即使檢視已停用，仍能由使用者在檢視上所設定的色彩。 它由在 XAML 中設定[ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty) ; 附加屬性`false`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                ios:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可用來控制是否已停用舊版的色彩模式。 颾魤 ㄛ [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}))方法可以用來傳回是否要停用舊版的色彩模式。

結果是，就可以停用舊版的色彩模式，以便使用者在檢視上所設定的色彩甚至保持停用檢視時：

![](ios-images/legacy-color-mode-disabled.png "已停用舊版的色彩模式")

> [!NOTE]
> 設定時[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)檢視上的舊版的色彩模式會完全忽略。 如需有關視覺狀態的詳細資訊，請參閱[Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)。

<a name="drop-shadow" />

### <a name="enabling-a-drop-shadow"></a>啟用下拉式陰影

此平台專屬來啟用下拉式陰影[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 它由在 XAML 中設定[ `VisualElement.IsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsShadowEnabledProperty) ; 附加屬性`true`，以及數個其他選擇性附加控制陰影的屬性：

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

或者，它可以取用從 C# 使用 fluent API:

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

`VisualElement.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `VisualElement.SetIsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsShadowEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可用來控制是否已啟用下拉式陰影`VisualElement`。 此外，下列方法可以叫用來控制陰影：

- [`SetShadowColor`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.Color)) – 設定延伸陰影的色彩。 預設色彩是[ `Color.Default` ](xref:Xamarin.Forms.Color.Default*)。
- [`SetShadowOffset`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.Size)) – 設定延伸陰影的位移。 位移會變更陰影轉換，並指定為方向[ `Size` ](xref:Xamarin.Forms.Size)值。 `Size`結構的值會以裝置獨立單位，與正在向左 （負值） 或向右 （正值），距離的第一個值和第二個值在上述的距離 （負值） 或下方 （正值）. 這個屬性的預設值是 （0.0，0.0），這會導致陰影正在轉換周圍每一端`VisualElement`。
- [`SetShadowOpacity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowOpacity(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Double)) – 會出現在範圍 0.0 （透明） 到 1.0 （不透明） 的值設定延伸陰影的不透明度。 預設不透明度值為 0.5。
- [`SetShadowRadius`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowRadius(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Double)) – 設定柔化半徑，用來呈現下拉式陰影。 預設半徑值為 10.0。

> [!NOTE]
> 可查詢下拉式陰影的狀態，藉由呼叫[ `GetIsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsShadowEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}))， [ `GetShadowColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}))， [ `GetShadowOffset` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}))， [ `GetShadowOpacity` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowOpacity(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}))，並[ `GetShadowRadius` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowRadius(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}))方法。

結果是，您可以上啟用下拉式陰影[ `VisualElement` ](xref:Xamarin.Forms.VisualElement):

![](ios-images/drop-shadow.png "已啟用下拉式陰影")

## <a name="views"></a>檢視

在 iOS 上，為 Xamarin.Forms 檢視提供了下列平台特有的功能：

- 確保所輸入文字融入[ `Entry` ](xref:Xamarin.Forms.Entry)藉由調整字型大小。 如需詳細資訊，請參閱 <<c0> [ 調整項目的字型大小](#adjust_font_size)。
- 在 設定資料指標色彩[ `Entry` ](xref:Xamarin.Forms.Entry)。 如需詳細資訊，請參閱 <<c0> [ 設定項目資料指標色彩](#entry-cursorcolor)。
- 設定分隔符號樣式[ `ListView` ](xref:Xamarin.Forms.ListView)。 如需詳細資訊，請參閱 < [ListView 上設定分隔符號樣式](#listview-separatorstyle)。
- 控制中的項目選取項目發生時[ `Picker` ](xref:Xamarin.Forms.Picker)。 如需詳細資訊，請參閱 <<c0> [ 控制選擇器項目選取](#picker_update_mode)。
- 啟用[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)點選位置上設定上的屬性[ `Slider` ](xref:Xamarin.Forms.Slider)列，而不是由拖曳`Slider`捲動方塊。 如需詳細資訊，請參閱 <<c0> [ 啟用在點選移動滑動軸捲動方塊](#slider-updateontap)。

<a name="adjust_font_size" />

### <a name="adjusting-the-font-size-of-an-entry"></a>調整項目的字型大小

此平台專屬用來調整字型大小[ `Entry` ](xref:Xamarin.Forms.Entry)以確保在控制項中，以符合不經的文字。 它由在 XAML 中設定[ `Entry.AdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty) ; 附加屬性`boolean`值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    <StackLayout Margin="20">
        <Entry x:Name="entry"
               Placeholder="Enter text here to see the font size change"
               FontSize="22"
               ios:Entry.AdjustsFontSizeToFitWidth="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

`Entry.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `Entry.EnableAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，用來調整以確保它符合不經文字的字型大小[ `Entry` ](xref:Xamarin.Forms.Entry). 颾魤 ㄛ [ `Entry` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry)類別`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空間也有[ `DisableAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}))方法，以停用此平台特定，並[ `SetAdjustsFontSizeToFitWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry},System.Boolean))方法可用來切換藉由呼叫調整字型大小[ `AdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}))方法：

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

結果是字型大小[ `Entry` ](xref:Xamarin.Forms.Entry)會縮放以確保在控制項中，以符合不經的文字：

![](ios-images/entry-font-size.png "調整項目大小的平台特定字型")

<a name="entry-cursorcolor" />

### <a name="setting-the-entry-cursor-color"></a>設定項目資料指標色彩

此平台特定設定中的資料指標色彩[ `Entry` ](xref:Xamarin.Forms.Entry)為指定的色彩。 它由在 XAML 中設定[ `Entry.CursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.CursorColorProperty)可繫結的屬性，以[ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry ... ios:Entry.CursorColor="LimeGreen" />
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var entry = new Xamarin.Forms.Entry();
entry.On<iOS>().SetCursorColor(Color.LimeGreen);
```

`Entry.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `Entry.SetCursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetCursorColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry},Xamarin.Forms.Color))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，設定與指定的資料指標色彩[ `Color` ](xref:Xamarin.Forms.Color)。 颾魤 ㄛ [ `Entry.GetCursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.GetCursorColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}))方法可用來擷取目前的資料指標色彩。

結果是中的資料指標色彩[ `Entry` ](xref:Xamarin.Forms.Entry)可以設定為特定[ `Color` ](xref:Xamarin.Forms.Color):

![](ios-images/entry-cursorcolor.png "項目資料指標色彩")

<a name="listview-separatorstyle" />

### <a name="setting-the-separator-style-on-a-listview"></a>設定分隔符號樣式的 ListView

此平台特定的控制項是否在儲存格之間的分隔符號[ `ListView` ](xref:Xamarin.Forms.ListView)使用完整的寬度`ListView`。 它由在 XAML 中設定[ `ListView.SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty)附加屬性的值[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)列舉型別：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.SeparatorStyle="FullWidth">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

`ListView.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `ListView.SetSeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SetSeparatorStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可用來控制是否中儲存格之間的分隔符號[ `ListView` ](xref:Xamarin.Forms.ListView)使用完整寬度`ListView`，以[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)提供兩個可能值的列舉型別：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default) – 表示預設 iOS 分隔符號行為。 這是在 Xamarin.Forms 中的預設行為。
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth) – 表示分隔符號會取自的某一邊`ListView`之間。

結果是，指定[ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)值套用至[ `ListView` ](xref:Xamarin.Forms.ListView)，這會控制的儲存格之間的分隔符號寬度：

![](ios-images/listview-separatorstyle.png "ListView SeparatorStyle 平台專屬")

> [!NOTE]
> 分隔符號樣式已設定為一旦`FullWidth`，就無法變更回`Default`在執行階段。

<a name="picker_update_mode" />

### <a name="controlling-picker-item-selection"></a>控制選擇器的項目選取

此平台特定的控制項中的項目選取項目發生時[ `Picker` ](xref:Xamarin.Forms.Picker)，讓使用者指定的項目選取瀏覽控制項，項目時，或只要**完成**已按下。 它由在 XAML 中設定`Picker.UpdateMode`附加屬性的值`UpdateMode`列舉型別：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

`Picker.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Picker.SetUpdateMode`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，用來控制何時發生的項目選取，請使用`UpdateMode`提供兩個可能值的列舉型別：

- `Immediately` – 使用者瀏覽中的項目，就會發生的項目選取[ `Picker` ](xref:Xamarin.Forms.Picker)。 這是在 Xamarin.Forms 中的預設行為。
- `WhenFinished` – 使用者已按下之後，才會發生的項目選取**完成**按鈕[ `Picker` ](xref:Xamarin.Forms.Picker)。

颾魤 ㄛ`SetUpdateMode`方法可用來切換列舉值，藉由呼叫`UpdateMode`方法，以傳回目前`UpdateMode`:

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

結果是，指定`UpdateMode`套用至[ `Picker` ](xref:Xamarin.Forms.Picker)，這會控制當項目選取項目，就會發生：

[![](ios-images/picker-updatemode.png "選擇器 UpdateMode 平台專屬")](ios-images/picker-updatemode-large.png#lightbox "Picker UpdateMode Plaform-Specific")

<a name="slider-updateontap" />

### <a name="enabling-a-slider-thumb-to-move-on-tap"></a>啟用在點選移動滑動軸捲動方塊

啟用此平台專屬[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)點選位置上設定上的屬性[ `Slider` ](xref:Xamarin.Forms.Slider)列，而不是由拖曳`Slider`捲動方塊。 它由在 XAML 中設定[ `Slider.UpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty)可繫結的屬性，以`true`:

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

![](ios-images/slider-updateontap.png "在已啟用點選滑桿更新")

## <a name="pages"></a>頁面

在 iOS 上，為 Xamarin.Forms 頁面提供了下列平台特有的功能：

- 在上隱藏巡覽列分隔符號[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 如需詳細資訊，請參閱 <<c0> [ 隱藏巡覽列上的分隔符號 NavigationPage](#navigationpage-hideseparatorbar)。
- 控制是否半透明的導覽列。 如需詳細資訊，請參閱 <<c0> [ 進行導覽列半透明](#translucent_navigation_bar)。
- 控制是否狀態列文字的色彩上[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)調整成符合要求的導覽列的明暗度。 如需詳細資訊，請參閱 <<c0> [ 調整狀態列文字的色彩模式](#status_bar_color_mode)。
- 控制是否要將頁面標題顯示為頁面巡覽列中的大型標題。 如需詳細資訊，請參閱 <<c0> [ 顯示的大型標題](#large_title)。
- 設定狀態軸可見度[ `Page` ](xref:Xamarin.Forms.Page)。 如需詳細資訊，請參閱 <<c0> [ 頁面上設定狀態列可見性](#set_status_bar_visibility)。
- 確保內容該頁面位於而言是安全的所有 iOS 裝置的螢幕區域。 如需詳細資訊，請參閱 <<c0> [ 啟用安全區域版面配置輔助線](#safe_area_layout)。
- 在 iPad 上設定強制回應頁面的呈現樣式。 如需詳細資訊，請參閱 < [iPad 上設定強制回應頁面呈現樣式](#modal-page-presentation-style)。

<a name="navigationpage-hideseparatorbar" />

### <a name="hiding-the-navigation-bar-separator-on-a-navigationpage"></a>隱藏巡覽列上 NavigationPage 分隔符號

此平台特定隱藏的分隔線和位於底部的瀏覽列的陰影[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 它由在 XAML 中設定[ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty)可繫結的屬性，以`false`:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ios:NavigationPage.HideNavigationBarSeparator="true">

</NavigationPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;

public class iOSTitleViewNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public iOSTitleViewNavigationPageCS()
    {
        On<iOS>().SetHideNavigationBarSeparator(true);
    }
}
```

`NavigationPage.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `NavigationPage.SetHideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetHideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可用來控制是否會隱藏巡覽列分隔符號。 颾魤 ㄛ [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}))方法可以用來傳回是否要隱藏導覽列分隔符號。

結果是瀏覽列分隔符號[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)可以隱藏：

![](ios-images/navigationpage-hideseparatorbar.png "隱藏 NavigationPage 導覽列")

<a name="translucent_navigation_bar" />

### <a name="making-the-navigation-bar-translucent"></a>進行瀏覽列半透明

此平台專屬用來變更巡覽列的透明度，且由在 XAML 中設定[ `NavigationPage.IsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty) ; 附加屬性`boolean`值：

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

`NavigationPage.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `NavigationPage.EnableTranslucentNavigationBar` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.EnableTranslucentNavigationBar(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，用來進行瀏覽列半透明。 颾魤 ㄛ [ `NavigationPage` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage)類別`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空間也有[ `DisableTranslucentNavigationBar` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.DisableTranslucentNavigationBar(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}))還原成預設狀態的導覽列的方法和[ `SetIsNavigationBarTranslucent`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetIsNavigationBarTranslucent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},System.Boolean))方法可用來切換瀏覽列透明度，藉由呼叫[ `IsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}))方法：

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

結果是，可以變更導覽列的透明度：

![](ios-images/translucent-navigation-bar.png "平台專屬的半透明的導覽列")

<a name="status_bar_color_mode" />

### <a name="adjusting-the-status-bar-text-color-mode"></a>調整文字的色彩模式狀態列

此平台特定的控制項上是否色彩的狀態列文字[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)調整成符合要求的導覽列的明暗度。 它由在 XAML 中設定[ `NavigationPage.StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty)附加屬性的值[ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)列舉型別：

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <MasterDetailPage.Master>
        <ContentPage Title="Master Page Title" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>

```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var mdp = sender as MasterDetailPage;
    if (mdp.IsPresented)
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

`NavigationPage.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `NavigationPage.SetStatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間中，控制項上是否色彩的狀態列文字[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)調整以符合亮度的導覽列中，使用[ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)提供兩個可能值的列舉型別：

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust) – 表示不應該調整的狀態列文字色彩。
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity) – 表示狀態列的文字色彩應符合的導覽列的明暗度。

颾魤 ㄛ [ `GetStatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}))方法可用來擷取目前的值[ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)套用至列舉型別[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage).

結果是在狀態列上的文字色彩[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)可以調整以符合導覽列的明暗度。 在此範例中，狀態列的文字色彩變更為使用者之間的切換[ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)和[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)頁面[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

![](ios-images/status-bar-text-color-mode.png "狀態列文字的色彩模式平台專屬")

<a name="large_title" />

### <a name="displaying-large-titles"></a>顯示大型標題

此平台專屬用來顯示為導覽列中，使用 iOS 11 或更新版本的裝置上的大型標題的頁面標題。 大型標題靠左對齊，而使用較大的字型，且會轉換成標準的標題使用者一開始捲動的內容，以便有效率地使用螢幕面積。 但是，在橫向模式下，標題將返回到導航欄的中心以優化內容佈局。 它由在 XAML 中設定`NavigationPage.PrefersLargeTitles`附加屬性`boolean`值：

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

或者可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

`NavigationPage.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `NavigationPage.SetPrefersLargeTitle`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可讓您控制是否要啟用大型標題。

前提是大型的項目上啟用[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)，在巡覽堆疊中的所有頁面會都顯示大型的標題。 此行為可覆寫頁面上設定`Page.LargeTitleDisplay`附加屬性的值`LargeTitleDisplayMode`列舉型別：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

從 C# 使用 fluent API 或者，覆寫頁面行為：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

`Page.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Page.SetLargeTitleDisplay`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間上控制項的大型標題行為[ `Page` ](xref:Xamarin.Forms.Page)，與`LargeTitleDisplayMode`提供三個可能的列舉型別值：

- `Always` – 強制字型與導覽列中使用大型的格式大小。
- `Automatic` – 為上一個項目，在巡覽堆疊中使用相同的樣式 （大型或小型）。
- `Never` -強制執行規則、 小型格式導覽列的使用。

颾魤 ㄛ`SetLargeTitleDisplay`方法可用來切換列舉值，藉由呼叫`LargeTitleDisplay`方法，以傳回目前`LargeTitleDisplayMode`:

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

結果是，指定`LargeTitleDisplayMode`套用至[ `Page` ](xref:Xamarin.Forms.Page)，大型標題行為：

![](ios-images/large-title.png "模糊效果平台專屬")

<a name="set_status_bar_visibility" />

### <a name="setting-the-status-bar-visibility-on-a-page"></a>設定 [狀態] 列在頁面上的可見性

此平台專屬用來設定 [狀態] 列的可見性[ `Page` ](xref:Xamarin.Forms.Page)，其中包含能夠控制狀態列如何進入或離開`Page`。 它由在 XAML 中設定`Page.PrefersStatusBarHidden`附加屬性的值`StatusBarHiddenMode`列舉型別，並選擇性地`Page.PreferredStatusBarUpdateAnimation`附加屬性的值`UIStatusBarAnimation`列舉型別：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

`Page.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Page.SetPrefersStatusBarHidden`方法，請在`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空間，用來設定 [狀態] 列的可見性[ `Page` ](xref:Xamarin.Forms.Page)藉由指定的其中一個`StatusBarHiddenMode`列舉值： `Default`， `True`或`False`。 `StatusBarHiddenMode.True`並`StatusBarHiddenMode.False`值設定狀態 列可見性，不論裝置方向和`StatusBarHiddenMode.Default`值會隱藏垂直 compact 的環境中的 狀態 列。

結果是在 [狀態] 列的可見性[ `Page` ](xref:Xamarin.Forms.Page)可以設定：

![](ios-images/hide-status-bar.png "狀態列可見性平台專屬")

> [!NOTE]
> 在  [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)，指定`StatusBarHiddenMode`列舉值也會更新所有的子頁面上的 [狀態] 列。 所有其他[ `Page` ](xref:Xamarin.Forms.Page)-衍生型別，指定的`StatusBarHiddenMode`列舉值只會更新目前頁面上的 [狀態] 列。

`Page.SetPreferredStatusBarUpdateAnimation`方法用來設定 [狀態] 列如何進入或離開[ `Page` ](xref:Xamarin.Forms.Page)藉由指定的其中一個`UIStatusBarAnimation`列舉值： `None`， `Fade`，或`Slide`。 如果`Fade`或是`Slide`指定列舉值，0.25 的第二個動畫執行狀態列進入或離開`Page`。

<a name="safe_area_layout" />

### <a name="enabling-the-safe-area-layout-guide"></a>啟用安全區域版面配置輔助線

此平台專屬用來確保頁面內容位於而言是安全的所有使用 iOS 11 和更新版本裝置的螢幕區域。 具體來說，它會幫助，以確定該內容不由裝置圓角邊角、 家用的指標或在 iPhone X 上的感應器外罩裁剪。它由在 XAML 中設定`Page.UseSafeArea`附加屬性`boolean`值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

`Page.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Page.SetUseSafeArea`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可讓您控制是否要啟用安全區域版面配置輔助線。

結果是螢幕的網頁內容可以位於安全適用於所有 Iphone 區域：

[![](ios-images/safe-area-layout.png "安全區域版面配置輔助線")](ios-images/safe-area-layout-large.png#lightbox "安全區域版面配置輔助線")

> [!NOTE]
> Apple 所定義的安全區域設定時，會在 Xamarin.Forms [ `Page.Padding` ](xref:Xamarin.Forms.Page.Padding)屬性，而且會覆寫這個屬性的所有已設定的舊值。

安全區域可以藉由擷取自訂其[ `Thickness` ](xref:Xamarin.Forms.Thickness)具有值`Page.SafeAreaInsets`方法，從[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間。 然後可以修改為所需和重新指派給`Padding`頁面的建構函式中的屬性或[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)覆寫：

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

<a name="modal-page-presentation-style" />

### <a name="setting-the-modal-page-presentation-style-on-an-ipad"></a>在 iPad 上設定強制回應頁面呈現樣式

此平台專屬用來在 iPad 上設定強制回應頁面的呈現樣式。 它由在 XAML 中設定`Page.ModalPresentationStyle`可繫結的屬性，以`UIModalPresentationStyle`列舉值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
    ...
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSModalFormSheetPageCS : ContentPage
{
    public iOSModalFormSheetPageCS()
    {
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.FormSheet);
        ...
    }
}
```

`Page.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Page.SetModalPresentationStyle`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，用來設定強制回應的展示樣式[ `Page` ](xref:Xamarin.Forms.Page)藉由指定下列其中一種`UIModalPresentationStyle`列舉值：

- `FullScreen`可設定以涵蓋整個畫面的強制回應的呈現樣式。 根據預設，強制回應頁面會顯示使用此簡報樣式。
- `FormSheet`其中設定上置中並小於螢幕的強制回應的呈現樣式。

颾魤 ㄛ`GetModalPresentationStyle`方法可用來擷取目前的值`UIModalPresentationStyle`套用至列舉型別[ `Page` ](xref:Xamarin.Forms.Page)。

結果是強制回應的展示樣式[ `Page` ](xref:Xamarin.Forms.Page)可以設定：

[![](ios-images/modal-presentation-style-small.png "在 iPad 上的強制回應的展示樣式")](ios-images/modal-presentation-style-large.png#lightbox "iPad 上的強制回應的展示樣式")

> [!NOTE]
> 使用此平台特定設定的強制回應的展示樣式的網頁都必須使用強制回應導覽。 如需詳細資訊，請參閱 < [Xamarin.Forms 強制回應頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)。

## <a name="layouts"></a>版面配置

在 iOS 上，為 Xamarin.Forms 版面配置提供了下列平台特有的功能：

- 控制是否[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)處理觸控筆勢，或將它傳遞給它的內容。 如需詳細資訊，請參閱 < [ScrollView 中延遲內容修飾](#delay_content_touches)。

<a name="delay_content_touches" />

### <a name="delaying-content-touches-in-a-scrollview"></a>ScrollView 中的延遲內容修飾

隱含的計時器會在觸控筆勢開始時觸發[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)在 iOS 上和`ScrollView`決定，是否應該處理筆勢，或將它傳遞給其內容為基礎的計時器範圍內中的使用者動作。 根據預設，iOS`ScrollView`延遲內容的風格，但這可能會導致問題，在某些情況下使用`ScrollView`時應該不 winning 筆勢的內容。 因此，此平台特定的控制項是否`ScrollView`處理觸控筆勢，或將它傳遞給它的內容。 它由在 XAML 中設定`ScrollView.ShouldDelayContentTouches`附加屬性`boolean`值：

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

`ScrollView.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `ScrollView.SetShouldDelayContentTouches`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，是用來控制[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)處理觸控筆勢，或將它傳遞給它的內容。 颾魤 ㄛ`SetShouldDelayContentTouches`方法可用來切換藉由呼叫延遲內容修飾`ShouldDelayContentTouches`傳回是否延遲內容修飾的方法：

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

結果是[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)可以停用延遲接收內容的風格，因此，在此案例[ `Slider` ](xref:Xamarin.Forms.Slider)接收筆勢而不是[ `Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)頁面的[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

[![](ios-images/scrollview-delay-content-touches.png "ScrollView 延遲內容觸及特定平台")](ios-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView Delay Content Touches Plaform-Specific")

## <a name="application"></a>應用程式

在 iOS 上，下列平台特有的功能提供適用於 Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application)類別：

- 啟用[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)捲動檢視來擷取，並分享捲動檢視中的移動瀏覽軌跡中。 如需詳細資訊，請參閱 <<c0> [ 啟用同時移動瀏覽軌跡辨識](#simultaneous-pan-gesture)。

<a name="simultaneous-pan-gesture" />

### <a name="enabling-simultaneous-pan-gesture-recognition"></a>啟用同時移動瀏覽軌跡辨識

當[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)附加到在捲動檢視中，所有筆勢會擷取藉由移動瀏覽檢視`PanGestureRecognizer`並不會傳遞給捲動檢視。 因此，將不會再捲動捲動檢視。

啟用此平台專屬`PanGestureRecognizer`捲動檢視來擷取，並分享捲動檢視中的移動瀏覽軌跡中。 它由在 XAML 中設定[ `Application.PanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) ; 附加屬性`true`:

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

## <a name="summary"></a>總結

這篇文章會示範如何使用 iOS 平台特性 Xamarin.Forms 內建。 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。

## <a name="related-links"></a>相關連結

- [建立平台特性](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
