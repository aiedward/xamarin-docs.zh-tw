---
title: Xamarin.Essentials 開啟瀏覽器
description: 瀏覽器中的類別 Xamarin.Essentials 讓應用程式最佳化的系統慣用的瀏覽器或外部瀏覽器中開啟網頁連結。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 563d3899cffb80c0215d90e8e4392046c4635256
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815702"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials： 瀏覽器

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**瀏覽器**類別可讓應用程式最佳化的系統慣用的瀏覽器或外部瀏覽器中開啟網頁連結。

## <a name="using-browser"></a>使用瀏覽器

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

瀏覽器功能的運作方式是呼叫`OpenAsync`方法`Uri`和`BrowserLaunchType`。

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.OpenAsync(uri, BrowserLaunchType.SystemPreferred);
    }
}
```

## <a name="platform-implementation-specifics"></a>平台實作的特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

啟動類型會決定瀏覽器啟動的方式：

## <a name="system-preferred"></a>慣用的系統

[Chrome 自訂索引標籤](https://developer.chrome.com/multidevice/android/customtabs)會嘗試使用載入的 Uri，並保留瀏覽感知。

## <a name="external"></a>外部

`Intent`將用來要求 Uri 會透過系統一般的瀏覽器開啟。

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>慣用的系統

[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/)用於載入 Uri，並保留瀏覽感知。

## <a name="external"></a>外部

標準`OpenUrl`上主要的應用程式用來啟動預設瀏覽器外應用程式。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

使用者的預設瀏覽器將一律啟動不論`BrowserLaunchType`。

--------------

## <a name="api"></a>API

- [瀏覽器的原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [瀏覽器 API 文件](xref:Xamarin.Essentials.Browser)
