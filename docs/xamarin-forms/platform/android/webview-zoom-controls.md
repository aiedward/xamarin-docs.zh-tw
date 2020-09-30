---
title: Android 上的 Web 功能縮放
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用可在 Web 上啟用 zoom 的 Android 平臺特定。
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1e41efd4bf100205f20b8f4e02a74fad193134a4
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556421"
---
# <a name="webview-zoom-on-android"></a>Android 上的 Web 功能縮放

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定會啟用的縮小至縮放和縮放控制 [`WebView`](xref:Xamarin.Forms.WebView) 。 它是在 XAML 中使用，方法是將 `WebView.EnableZoomControls` 和可系結 `WebView.DisplayZoomControls` 屬性設定為 `boolean` 值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

可系結 `WebView.EnableZoomControls` 屬性會控制是否在上啟用縮小至縮放，而且可系結 [`WebView`](xref:Xamarin.Forms.WebView) `WebView.DisplayZoomControls` 屬性會控制縮放控制項是否在上重迭 `WebView` 。

或者，您可以使用流暢的 API，從 c # 取用平臺特定：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

`WebView.On<Android>`方法指定此平臺特定的只會在 Android 上執行。 `WebView.EnableZoomControls`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 是用來控制是否在上啟用縮小至縮放 [`WebView`](xref:Xamarin.Forms.WebView) 。 在 `WebView.DisplayZoomControls` 相同的命名空間中，方法是用來控制是否要將縮放控制項重迭在上 `WebView` 。 此外， `WebView.ZoomControlsEnabled` 和 `WebView.ZoomControlsDisplayed` 方法可以用來傳回是否分別啟用放大縮放和縮放控制項。

結果是可以在上啟用縮小至縮放比例 [`WebView`](xref:Xamarin.Forms.WebView) ，縮放控制項可以在上重迭 `WebView` ：

[![Android 上放大的 Web 程式的螢幕擷取畫面](webview-zoom-controls-images/webview-zoom.png "放大的 Web 視圖")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "放大的 Web 視圖")

> [!IMPORTANT]
> 縮放控制項必須同時啟用和顯示（透過個別的可系結屬性或方法），以在上重迭 [`WebView`](xref:Xamarin.Forms.WebView) 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)