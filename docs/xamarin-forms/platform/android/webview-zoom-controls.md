---
title: 在 Android 上的 WebView 縮放
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 Android 平台特定，可讓 web 檢視的 顯示比例。
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 0e180ca5019bd4e5d1351cfb2f7a8c28ade2afe2
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65971640"
---
# <a name="webview-zoom-on-android"></a>在 Android 上的 WebView 縮放

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此 Android 平台特定啟用縮小-放大和縮放控制項上[ `WebView` ](xref:Xamarin.Forms.WebView)。 它由在 XAML 中設定`WebView.EnableZoomControls`並`WebView.DisplayZoomControls`可繫結的屬性，以`boolean`值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

`WebView.EnableZoomControls`可繫結的屬性會控制上是否已啟用縮小-放大[ `WebView` ](xref:Xamarin.Forms.WebView)，而`WebView.DisplayZoomControls`可繫結的屬性會控制是否在上重疊縮放控制項`WebView`。

或者，使用特定平台，從C#使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

`WebView.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 `WebView.EnableZoomControls`方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，可用來控制是否啟用捏合以縮放[ `WebView` ](xref:Xamarin.Forms.WebView)。 `WebView.DisplayZoomControls`方法，在相同的命名空間，來控制是否在上重疊縮放控制項`WebView`。 颾魤 ㄛ`WebView.ZoomControlsEnabled`和`WebView.ZoomControlsDisplayed`方法可以用來傳回是否縮小-放大和縮放控制項已啟用，分別。

結果是可在啟用該捏合縮放[ `WebView` ](xref:Xamarin.Forms.WebView)，並會縮放控制項可以顯示在`WebView`:

[![在 Android 上的縮放 WebView 的螢幕擷取畫面](webview-zoom-controls-images/webview-zoom.png "放大 WebView")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "放大的 web 檢視")

> [!IMPORTANT]
> 縮放控制項必須同時啟用並顯示，請透過各自可繫結的屬性或方法，以便在重疊[ `WebView` ](xref:Xamarin.Forms.WebView)。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
