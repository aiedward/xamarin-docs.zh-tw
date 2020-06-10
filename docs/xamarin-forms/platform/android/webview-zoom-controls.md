---
標題：「在 Android 上進行 Web 工作的放大」描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的來啟用 Web 工作的顯示功能。」
assetid： DC1A3762-6A42-4298-929C-445F416C3E60 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：05/09/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="webview-zoom-on-android"></a>Android 上的 Web 視圖縮放

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定可在上啟用縮小縮放和縮放控制項 [`WebView`](xref:Xamarin.Forms.WebView) 。 它會在 XAML 中使用，方法是將 `WebView.EnableZoomControls` 和可系結 `WebView.DisplayZoomControls` 屬性設定為 `boolean` 值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

可系結 `WebView.EnableZoomControls` 屬性會控制是否在上啟用縮小至縮放比例 [`WebView`](xref:Xamarin.Forms.WebView) ，而可系結屬性則 `WebView.DisplayZoomControls` 控制是否在上重迭縮放控制項 `WebView` 。

或者，您可以使用 Fluent API，從 c # 取用平臺特定的：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

`WebView.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 `WebView.EnableZoomControls`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 是用來控制是否在上啟用縮小至縮放比例 [`WebView`](xref:Xamarin.Forms.WebView) 。 `WebView.DisplayZoomControls`相同命名空間中的方法可用來控制縮放控制項是否會在上重迭 `WebView` 。 此外，您還 `WebView.ZoomControlsEnabled` `WebView.ZoomControlsDisplayed` 可以使用和方法，分別傳回是否要啟用縮小縮放比例控制項。

結果是可以在上啟用縮小到縮放比例 [`WebView`](xref:Xamarin.Forms.WebView) ，縮放控制項可以在上重迭 `WebView` ：

[![Android 上縮放的 Web 操作螢幕擷取畫面](webview-zoom-controls-images/webview-zoom.png "縮放的 Web 視圖")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "縮放的 Web 視圖")

> [!IMPORTANT]
> 縮放控制項必須透過個別的可系結屬性或方法來啟用和顯示，以在上重迭 [`WebView`](xref:Xamarin.Forms.WebView) 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
