---
標題：「適用于 iOS 的滑杆點」描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺專屬的，讓您可以在滑動軸上按下滑杆來設定 [值] 屬性。
assetid： D0915D37-9A59-4728-BB6A-FE094A661275 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：10/24/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="slider-thumb-tap-on-ios"></a>IOS 上的滑杆

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定可讓您透過 [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) 在橫條上的位置上點擊 [`Slider`](xref:Xamarin.Forms.Slider) ，而不是拖曳捲動方塊來設定屬性 `Slider` 。 將可系結屬性設定為，即可在 XAML 中使用它 [`Slider.UpdateOnTap`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) `true` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

`Slider.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 [ `Slider.SetUpdateOnTap` ] （X： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SetUpdateOnTap （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。滑杆}、[system.string]）方法（在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間中）是用來控制橫條上的點按是否要 `Slider` 設定 [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) 屬性。 此外，[ `Slider.GetUpdateOnTap` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. GetUpdateOnTap （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。滑杆}））方法，可以用來傳回是否要在橫條上 `Slider` 設定 `Slider.Value` 屬性。

結果就是，您 [`Slider`](xref:Xamarin.Forms.Slider) 可以在橫條上移動捲動方塊 `Slider` 並設定 [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) 屬性：

![](slider-thumb-images/slider-updateontap.png "Slider Update on Tap enabled")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
