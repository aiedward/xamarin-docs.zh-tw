---
title: 在 Windows 上進行 Web 操作 JavaScript 警示
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的，讓 Web 程式在 UWP 訊息對話方塊中顯示 JavaScript 警示。
ms.prod: xamarin
ms.assetid: 95A153A1-72A0-4C0B-A452-ACE966BB12CB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c7ff394fbe050fbd97c574c7edc32376e7d1b069
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557214"
---
# <a name="webview-javascript-alerts-on-windows"></a>在 Windows 上進行 Web 操作 JavaScript 警示

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個平臺特定的可讓在 [`WebView`](xref:Xamarin.Forms.WebView) UWP 訊息對話方塊中顯示 JavaScript 警示。 它是在 XAML 中使用，方法是將 [`WebView.IsJavaScriptAlertEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) 附加屬性設定為 `boolean` 值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var webView = new Xamarin.Forms.WebView
{
  Source = new HtmlWebViewSource
  {
    Html = @"<html><body><button onclick=""window.alert('Hello World from JavaScript');"">Click Me</button></body></html>"
  }
};
webView.On<Windows>().SetIsJavaScriptAlertEnabled(true);
```

`WebView.On<Windows>`方法指定此平臺特定只會在通用 Windows 平臺上執行。 [ `WebView.SetIsJavaScriptAlertEnabled` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific SetIsJavaScriptAlertEnabled (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Web 空間中的 Web 程式名稱}、system.string) # A3 方法， [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 可用來控制是否啟用 JavaScript 警示。 此外， `WebView.SetIsJavaScriptAlertEnabled` 方法可以藉由呼叫方法來切換 JavaScript 警示，以傳回 [`IsJavaScriptAlertEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*) 它們是否已啟用：

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

結果是 JavaScript 警示可以顯示在 UWP 訊息對話方塊中：

![Web 型 JavaScript 警示平臺特定](webview-javascript-alert-images/webview-javascript-alert.png "Web 型 JavaScript 警示平臺特定")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)