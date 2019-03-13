---
title: Xamarin.Essentials 開啟瀏覽器
description: Xamarin.Essentials 中的 Browser 類別可讓應用程式在最佳化系統偏好瀏覽器或外部瀏覽器中開啟網頁連結。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: ea2a10c11a77fcb2b3ce142d176522ebf0310725
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898866"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials: Browser

**Browser** 類別可讓應用程式在最佳化系統偏好瀏覽器或外部瀏覽器中開啟網頁連結。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-browser"></a>使用 Browser

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

Browser 功能的運作方式是使用 `Uri` 與 `BrowserLaunchMode` 呼叫 `OpenAsync` 方法。

```csharp

public class BrowserTest
{
    public async Task<bool> OpenBrowser(Uri uri)
    {
        return await Browser.OpenAsync(uri, BrowserLaunchMode.SystemPreferred);
    }
}
```

當瀏覽器已由使用者「啟動」但未必由其「關閉」後，會傳回此方法。  `bool` 結果會指出是否啟動成功。

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

「啟動模式」決定瀏覽器的啟動方式：

## <a name="system-preferred"></a>系統偏好

將嘗試使用 [Chrome 自訂索引標籤](https://developer.chrome.com/multidevice/android/customtabs)來載入 Uri 並維持瀏覽感知。

## <a name="external"></a>外部

將使用 `Intent` 來要求透過系統一般瀏覽器來開啟 Uri。

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>系統偏好

會使用 [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) 來載入 Uri 並維持瀏覽感知。

## <a name="external"></a>外部

將使用主應用程式上的標準 `OpenUrl` 來在應用程式之外啟動預設瀏覽器。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

一律會啟動使用者的預設瀏覽器，不論 `BrowserLaunchMode` 為何。

--------------

## <a name="api"></a>API

- [Browser 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [Browser API 文件](xref:Xamarin.Essentials.Browser)
