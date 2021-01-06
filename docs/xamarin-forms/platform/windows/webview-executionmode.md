---
title: Windows 上的 Web 上執行模式
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的，以設定在 Web 上裝載其內容的執行緒。
ms.prod: xamarin
ms.assetid: 4D3C4112-0FF6-47F8-BC22-579D92032807
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 314851c4697e95ef8662710c986d0b175249fad6
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940712"
---
# <a name="webview-execution-mode-on-windows"></a>Windows 上的 Web 上執行模式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此平臺特定會設定 [`WebView`](xref:Xamarin.Forms.WebView) 裝載其內容的執行緒。 它是在 XAML 中使用，方法是將可系結 `WebView.ExecutionMode` 屬性設定為 `WebViewExecutionMode` 列舉值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.ExecutionMode="SeparateThread" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

WebView webView = new Xamarin.Forms.WebView();
webView.On<Windows>().SetExecutionMode(WebViewExecutionMode.SeparateThread);
```

`WebView.On<Windows>`方法指定此平臺特定只會在通用 Windows 平臺上執行。 `WebView.SetExecutionMode`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 是用來設定 `WebView` 裝載其內容的執行緒，且 `WebViewExecutionMode` 列舉提供三個可能的值：

- `SameThread` 指出內容是在 UI 執行緒上託管。 這是 Windows 上的預設值 `WebView` 。
- `SeparateThread` 指出內容是在背景執行緒上主控。
- `SeparateProcess` 指出內容會在應用程式進程以外的個別進程上裝載。 每個 Web 程式實例都沒有個別的進程，因此應用程式的所有 Web 程式共用實例都共用相同的個別進程。

此外， `GetExecutionMode` 方法可以用來傳回的目前 `WebViewExecutionMode` `WebView` 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
