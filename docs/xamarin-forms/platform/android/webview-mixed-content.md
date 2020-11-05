---
title: 在 Android 上將混合內容進行 Web 操作
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，在以 API 21 或更高版本為目標的應用程式中，于 Web 程式中顯示混合內容。
ms.prod: xamarin
ms.assetid: 68F908F3-04C5-4B91-B6E5-B7E8357B4154
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cfcdd52187fe8e3af258aedb25e8c0df85cbea4e
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375482"
---
# <a name="webview-mixed-content-on-android"></a>在 Android 上將混合內容進行 Web 操作

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定的控制是否 [`WebView`](xref:Xamarin.Forms.WebView) 可以在以 API 21 或更新版本為目標的應用程式中顯示混合內容。 混合內容是一開始透過 HTTPS 連線載入的內容，但是會將資源 (例如影像、音訊、影片、樣式表單、腳本) 透過 HTTP 連接來載入。 它是在 XAML 中使用，方法是將 [`WebView.MixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) 附加屬性設定為 [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) 列舉值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

`WebView.On<Android>`方法指定此平臺特定的只會在 Android 上執行。 [ `WebView.SetMixedContentMode` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific SetMixedContentMode (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。Web 視圖}、 Xamarin.Forms 。命名空間中的 PlatformConfiguration. AndroidSpecific. MixedContentHandling) # A3 方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 可用來控制是否可以顯示混合內容，並 [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) 提供三個可能值的列舉：

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) –表示 [`WebView`](xref:Xamarin.Forms.WebView) 將允許 HTTPS 來源從 HTTP 來源載入內容。
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) –表示 [`WebView`](xref:Xamarin.Forms.WebView) 將不允許 HTTPS 來源從 HTTP 來源載入內容。
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) –表示 [`WebView`](xref:Xamarin.Forms.WebView) 將嘗試與最新裝置網頁瀏覽器的方法相容。 某些 HTTP 內容可能會被 HTTPS 來源載入，而其他類型的內容將會遭到封鎖。 封鎖或允許的內容類型可能會隨著每個作業系統版本而變更。

結果會將指定的 [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) 值套用至 [`WebView`](xref:Xamarin.Forms.WebView) ，以控制是否可以顯示混合內容：

[![Web 架構混合內容處理平臺特定](webview-mixed-content-images/webview-mixedcontent.png "Web 架構混合內容處理平臺特定")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "Web 架構混合內容處理平臺特定")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)