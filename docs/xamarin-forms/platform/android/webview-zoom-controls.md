---
title: Android 上的 Web 視圖縮放
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的來啟用 Web 工作的顯示方式。
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 2142882add91d613263d11fa4c1e6d7ad142c7c7
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "68655987"
---
# <a name="webview-zoom-on-android"></a>Android 上的 Web 視圖縮放

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定會啟用[`WebView`](xref:Xamarin.Forms.WebView)的縮小縮放和縮放控制項。 它會在 XAML 中使用，方法是將 `WebView.EnableZoomControls` 和 `WebView.DisplayZoomControls` 可系結屬性設定為 `boolean` 值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

[@No__t_0 可系結] 屬性會控制是否在[`WebView`](xref:Xamarin.Forms.WebView)上啟用縮小至縮放比例，而 [`WebView.DisplayZoomControls` 可系結] 屬性控制是否在 `WebView` 上重迭縮放控制項。

或者，您也可以C#使用 Fluent API 來取用平臺特定：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

@No__t_0 方法指定此平臺特定的只會在 Android 上執行。 [@No__t_2](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間中的 `WebView.EnableZoomControls` 方法是用來控制是否在[`WebView`](xref:Xamarin.Forms.WebView)上啟用縮小至縮放比例。 相同命名空間中的 `WebView.DisplayZoomControls` 方法是用來控制縮放控制項是否會在 `WebView` 上重迭。 此外，`WebView.ZoomControlsEnabled` 和 `WebView.ZoomControlsDisplayed` 方法都可以用來傳回是否分別啟用縮小至縮放和縮放控制項。

結果是可以在[`WebView`](xref:Xamarin.Forms.WebView)上啟用縮小至縮放比例，縮放控制項可以在 `WebView` 上重迭：

[![Android 上縮放的 Web 操作螢幕擷取畫面](webview-zoom-controls-images/webview-zoom.png "縮放的 Web 視圖")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "縮放的 Web 視圖")

> [!IMPORTANT]
> 縮放控制項必須透過個別的可系結屬性或方法來啟用和顯示，才會在[`WebView`](xref:Xamarin.Forms.WebView)上重迭。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
