---
title: Android 上的 Web 操作混合內容
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 本文說明如何使用 Android 平臺特定的, 以在以 API 21 或更新版本為目標的應用程式中, 以 Web 工作的方式顯示混合內容。
ms.prod: xamarin
ms.assetid: 68F908F3-04C5-4B91-B6E5-B7E8357B4154
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 286a7dceead327d727110d4ebbcecbc2341345b3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656063"
---
# <a name="webview-mixed-content-on-android"></a>Android 上的 Web 操作混合內容

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定控制是否可以在[`WebView`](xref:Xamarin.Forms.WebView)以 API 21 或更新版本為目標的應用程式中顯示混合內容。 混合的內容是透過 HTTPS 連線，一開始載入的但透過 HTTP 連線載入資源 （例如影像、 音訊、 視訊、 樣式表、 指令碼） 的內容。 它由在 XAML 中設定[ `WebView.MixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty)附加屬性的值[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)列舉型別：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

`WebView.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，可用來控制是否可以顯示混合的內容，與[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)提供三個可能值的列舉型別：

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) – 表示[ `WebView` ](xref:Xamarin.Forms.WebView)將允許 HTTPS origin 從 HTTP 來源載入內容。
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) – 表示[ `WebView` ](xref:Xamarin.Forms.WebView)將不允許從 HTTP 來源載入內容 HTTPS origin。
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) – 表示[ `WebView` ](xref:Xamarin.Forms.WebView)會嘗試將相容的最新的裝置網頁瀏覽器的方法。 部分 HTTP 內容可能會允許 HTTPS origin 所應載入和其他類型的內容將會遭到封鎖。 每個作業系統版本，可能會變更的是被封鎖或允許的內容類型。

結果是，指定[ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)值套用至[ `WebView` ](xref:Xamarin.Forms.WebView)，這會控制是否可以顯示混合的內容：

[![混合內容處理特定的平台的 WebView](webview-mixed-content-images/webview-mixedcontent.png "混合內容處理特定的平台的 WebView")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "WebView 混合內容處理特定的平台")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
