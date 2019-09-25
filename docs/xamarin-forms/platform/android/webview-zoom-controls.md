---
title: Android 上的 Web 視圖縮放
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 本文說明如何使用 Android 平臺特定的來啟用 Web 工作的顯示方式。
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 2142882add91d613263d11fa4c1e6d7ad142c7c7
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "68655987"
---
# <a name="webview-zoom-on-android"></a>Android 上的 Web 視圖縮放

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定可在上[`WebView`](xref:Xamarin.Forms.WebView)啟用縮小縮放和縮放控制項。 它會在 XAML 中使用，方法`WebView.EnableZoomControls`是`WebView.DisplayZoomControls`將和可`boolean`系結屬性設定為值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

可系結屬性會控制是否[`WebView`](xref:Xamarin.Forms.WebView)在上啟用縮小至縮放比例，而`WebView.DisplayZoomControls`可系結屬性則`WebView`控制是否在上重迭縮放控制項。 `WebView.EnableZoomControls`

或者，您也可以C#使用 Fluent API 來取用平臺特定：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

`WebView.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 命名空間`WebView.EnableZoomControls`中的方法是用來控制是否在上啟用[`WebView`](xref:Xamarin.Forms.WebView)縮小至縮放比例。 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 相同`WebView.DisplayZoomControls`命名空間中的方法可用來控制縮放控制項是否會`WebView`在上重迭。 此外， `WebView.ZoomControlsEnabled`您還可以`WebView.ZoomControlsDisplayed`使用和方法，分別傳回是否要啟用縮小縮放比例控制項。

結果是可以在上[`WebView`](xref:Xamarin.Forms.WebView)啟用縮小到縮放比例，縮放控制項可以在`WebView`上重迭：

[![Android 上縮放的 web 操作螢幕擷取畫面](webview-zoom-controls-images/webview-zoom.png "縮放的 Web 視圖")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "縮放的 Web 視圖")

> [!IMPORTANT]
> 縮放控制項必須透過個別的可系結屬性或方法來啟用和顯示，以在上[`WebView`](xref:Xamarin.Forms.WebView)重迭。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
