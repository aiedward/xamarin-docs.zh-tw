---
title： "VisualElement 模糊 on iOS" 描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用將模糊套用至 VisualElement 的 iOS 平臺特定。
assetid： 2DE3B65E-B96E-4ECD-92DF-AA42D5205C44 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：10/24/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="visualelement-blur-on-ios"></a>IOS 上的 VisualElement 模糊

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定是用來模糊其底下的內容，並可套用至任何 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 它會在 XAML 中使用，方法是將 [`VisualElement.BlurEffect`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty) 附加屬性設為列舉的值 [`BlurEffectStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) ：

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

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

`BoxView.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 [ `VisualElement.UseBlurEffect` ] （X： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. UseBlurEffect （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。VisualElement}、 Xamarin.Forms 。PlatformConfiguration. iOSSpecific. BlurEffectStyle））方法（在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間中）是用來套用模糊效果， [`BlurEffectStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) 列舉提供四個值： [`None`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None) 、 [`ExtraLight`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight) 、 [`Light`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light) 和 [`Dark`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark) 。

結果是指定的會套用 [`BlurEffectStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) 至 [`BoxView`](xref:Xamarin.Forms.BoxView) 實例，這會模糊 [`Image`](xref:Xamarin.Forms.Image) 其底下的分層：

![](applying-blur-images/blur-effect.png "Blur Effect Platform-Specific")

> [!NOTE]
> 將模糊效果加入至時 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ，仍會收到觸控事件 `VisualElement` 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
