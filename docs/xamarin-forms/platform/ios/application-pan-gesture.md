---
標題：「iOS 上的同時移動流覽手勢辨識」描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，讓您能夠在應用程式中使用同時移動流覽手勢。」
assetid： 883D89DA-F8FF-4B97-9C3F-2DD05C96A495 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：10/24/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>IOS 上的同時移動流覽手勢識別

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

當 [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) 連接到捲軸內部的視圖時，所有的平移手勢都會由捕捉， `PanGestureRecognizer` 而且不會傳遞至 [捲軸] 視圖。 因此，捲軸不會再滾動。

這個 iOS 平臺特有的可讓 `PanGestureRecognizer` 在 [滾動] 視圖中，使用 [滾動] 視圖來捕捉和共用平移手勢。 它會在 XAML 中使用，方法是將 [`Application.PanGestureRecognizerShouldRecognizeSimultaneously`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) 附加屬性設定為 `true` ：

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

`Application.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ] （X： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SetPanGestureRecognizerShouldRecognizeSimultaneously （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。Application}，system.string）方法（在命名空間中） [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來控制捲軸中的移動流覽手勢辨識器是否將捕捉平移手勢，或使用捲軸來捕捉和共用平移手勢。 此外，[ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. GetPanGestureRecognizerShouldRecognizeSimultaneously （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。應用程式}））方法，可以用來傳回移動流覽筆勢是否與包含的捲軸共用 [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) 。

因此，在已啟用此平臺特定的情況下，當 [`ListView`](xref:Xamarin.Forms.ListView) 包含時 [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) ， `ListView` 和 `PanGestureRecognizer` 都會接收平移手勢並加以處理。 不過，在此平臺特定的停用情況下，當包含時， `ListView` `PanGestureRecognizer` `PanGestureRecognizer` 將會捕捉移動流覽手勢並加以處理，而且 `ListView` 不會接收平移手勢。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
