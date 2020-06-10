---
title： "在 Android 上使用 Web 工作的混合內容" 描述：「平臺詳細資訊可讓您取用只能在特定平臺上使用的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，以在以 API 21 或更新版本為目標的應用程式中，顯示 Web 上的混合內容。
assetid： 68F908F3-04C5-4B91-B6E5-B7E8357B4154 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：07/10/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="webview-mixed-content-on-android"></a>Android 上的 Web 操作混合內容

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定控制是否 [`WebView`](xref:Xamarin.Forms.WebView) 可以在以 API 21 或更新版本為目標的應用程式中顯示混合內容。 混合內容是一開始透過 HTTPS 連線載入的內容，但會透過 HTTP 連線載入資源（例如影像、音訊、影片、樣式表單、腳本）。 它會在 XAML 中使用，方法是將 [`WebView.MixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) 附加屬性設為列舉的值 [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) ：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

`WebView.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 [ `WebView.SetMixedContentMode` ] （X： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. SetMixedContentMode （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。Web 視圖}、 Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. MixedContentHandling））方法，在 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 命名空間中，可用來控制是否可以顯示混合內容， [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) 列舉會提供三個可能的值：

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow)–表示 [`WebView`](xref:Xamarin.Forms.WebView) 將允許 HTTPS 來源從 HTTP 來源載入內容。
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow)–表示 [`WebView`](xref:Xamarin.Forms.WebView) 將不允許 HTTPS 來源從 HTTP 來源載入內容。
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode)–表示 [`WebView`](xref:Xamarin.Forms.WebView) 會嘗試與最新裝置網頁瀏覽器的方法相容。 某些 HTTP 內容可能允許由 HTTPS 來源載入，而其他類型的內容則會遭到封鎖。 每個作業系統版本可能會變更已封鎖或允許的內容類型。

結果是指定的 [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) 值會套用至 [`WebView`](xref:Xamarin.Forms.WebView) ，以控制是否可以顯示混合內容：

[![Web 操作混合內容處理平臺特定](webview-mixed-content-images/webview-mixedcontent.png "Web 操作混合內容處理平臺特定")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "Web 操作混合內容處理平臺特定")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
