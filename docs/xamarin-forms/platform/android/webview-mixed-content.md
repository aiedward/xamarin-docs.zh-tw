---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7269b0617be7199c365f350fc26ecd42256e28f3
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128449"
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
