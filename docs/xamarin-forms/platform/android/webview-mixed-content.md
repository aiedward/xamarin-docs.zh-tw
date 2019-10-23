---
title: Android 上的 Web 操作混合內容
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，以在以 API 21 或更新版本為目標的應用程式中，顯示 Web 工作中的混合內容。
ms.prod: xamarin
ms.assetid: 68F908F3-04C5-4B91-B6E5-B7E8357B4154
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: f9b4a12d3049cea37235cc04805a7411a9bdb236
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696494"
---
# <a name="webview-mixed-content-on-android"></a>Android 上的 Web 操作混合內容

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定控制[`WebView`](xref:Xamarin.Forms.WebView)是否可以在以 API 21 或更新版本為目標的應用程式中顯示混合內容。 混合內容是一開始透過 HTTPS 連線載入的內容，但會透過 HTTP 連線載入資源（例如影像、音訊、影片、樣式表單、腳本）。 它會在 XAML 中使用，方法是將[`WebView.MixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty)附加屬性設定為[`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)列舉的值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

或者，也可以C#使用 Fluent API 來取用：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

@No__t_0 方法指定此平臺特定的只會在 Android 上執行。 [@No__t_3](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間中的[`WebView.SetMixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling))方法是用來控制是否可以顯示混合內容，而[`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)列舉提供三個可能的值：

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) –表示[`WebView`](xref:Xamarin.Forms.WebView)會允許 HTTPS 原始來源從 HTTP 來源載入內容。
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) –表示[`WebView`](xref:Xamarin.Forms.WebView)不允許 HTTPS 來源從 HTTP 來源載入內容。
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) –表示[`WebView`](xref:Xamarin.Forms.WebView)會嘗試與最新裝置網頁瀏覽器的方法相容。 某些 HTTP 內容可能允許由 HTTPS 來源載入，而其他類型的內容則會遭到封鎖。 每個作業系統版本可能會變更已封鎖或允許的內容類型。

結果是指定的[`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)值會套用至[`WebView`](xref:Xamarin.Forms.WebView)，以控制是否可以顯示混合內容：

[![Web 操作混合內容處理平臺特定](webview-mixed-content-images/webview-mixedcontent.png "Web 操作混合內容處理平臺特定")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "Web 操作混合內容處理平臺特定")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
