---
title: Windows 上的 Web 視圖 JavaScript 警示
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的，讓 Web 程式在 UWP 訊息對話方塊中顯示 JavaScript 警示。
ms.prod: xamarin
ms.assetid: 95A153A1-72A0-4C0B-A452-ACE966BB12CB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b7d039d26895b50f937392941e42a92a6e51f322
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137484"
---
# <a name="webview-javascript-alerts-on-windows"></a>Windows 上的 Web 視圖 JavaScript 警示

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此平臺特定可讓在 [`WebView`](xref:Xamarin.Forms.WebView) UWP 訊息對話方塊中顯示 JavaScript 警示。 它會在 XAML 中使用，方法是將 [`WebView.IsJavaScriptAlertEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) 附加屬性設定為 `boolean` 值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

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

`WebView.On<Windows>`方法會指定此平臺特定只會在通用 Windows 平臺上執行。 [ `WebView.SetIsJavaScriptAlertEnabled` ] （X： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. SetIsJavaScriptAlertEnabled （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。[Web 視圖}，system.string）] 方法，在 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 命名空間中，可用來控制是否啟用 JavaScript 警示。 此外， `WebView.SetIsJavaScriptAlertEnabled` 方法可以藉由呼叫 [`IsJavaScriptAlertEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*) 方法來傳回是否已啟用來切換 JavaScript 警示：

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

結果是 JavaScript 警示可以顯示在 UWP 訊息對話方塊中：

![Web 視圖 JavaScript 警示平臺特定](webview-javascript-alert-images/webview-javascript-alert.png "Web 視圖 JavaScript 警示平臺特定")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
