---
title: Xamarin.Essentials 開啟瀏覽器
description: 瀏覽器類別可讓應用程式來最佳化的系統慣用的瀏覽器或外部瀏覽器中開啟網頁連結。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: e8e1a6973e7928032b2aa8669d36325b93671624
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials 瀏覽器

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**瀏覽器**類別可讓應用程式來最佳化的系統慣用的瀏覽器或外部瀏覽器中開啟網頁連結。

## <a name="using-browser"></a>使用瀏覽器

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

瀏覽器功能的運作方式是呼叫`OpenAsync`方法`Uri`和`BrowserLaunchType`。

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.RequestAsync(uri, BrowserLaunchType.SystemPreferred);
    }
}
```

## <a name="platform-implementation-specifics"></a>平台實作的特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

啟動類型會決定如何啟動瀏覽器：

## <a name="system-preferred"></a>慣用的系統

[Chrome 自訂索引標籤](https://developer.chrome.com/multidevice/android/customtabs)會嘗試使用載入的 Uri，並保留瀏覽感知。

## <a name="external"></a>外部

`Intent`將用來要求 Uri 會透過系統一般的瀏覽器中開啟。

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>慣用的系統

[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)用於到載入 Uri，並保留瀏覽感知。

## <a name="external"></a>外部

標準`OpenUrl`在主應用程式會用來啟動應用程式之外的預設瀏覽器。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

不論，一律啟動使用者的預設瀏覽器`BrowserLaunchType`。

--------------

## <a name="api"></a>API

- [瀏覽器的原始碼](https://github.com/xamarin/Essentials/tree/master/Essentials/Browser)
- [瀏覽器應用程式開發介面文件](xref:Xamarin.Essentials.Browser)
