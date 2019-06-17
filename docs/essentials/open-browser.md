---
title: Xamarin.Essentials 開啟瀏覽器
description: Xamarin.Essentials 中的 Browser 類別可讓應用程式在最佳化系統偏好瀏覽器或外部瀏覽器中開啟網頁連結。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
ms.openlocfilehash: 3f9e5a7a1e20b8e7d708ff80712e6262bae4baa7
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58869583"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials:瀏覽器

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

## <a name="customization"></a>自訂

使用系統慣用的瀏覽器時，有數種自訂選項可供 iOS 及 Android 使用。 這包含會顯示的 `TitleMode` (僅限 Android)，以及 `Toolbar` (iOS 及 Android) 和 `Controls` (僅限 iOS) 慣用色彩選項。 

呼叫 `OpenAsync` 時，會使用 `BrowserLaunchOptions` 指定這些選項。

```csharp
await Browser.OpenAsync(uri, new BrowserLaunchOptions
                {
                    LaunchMode = BrowserLaunchMode.SystemPreferred,
                    TitleMode = BrowserTitleMode.Show,
                    PreferredToolbarColor = Color.AliceBlue,
                    PreferredControlColor = Color.Violet
                });
```

![瀏覽器選項](images/browser-options.png)

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

「啟動模式」決定瀏覽器的啟動方式：

## <a name="system-preferred"></a>系統偏好

將嘗試使用 [Chrome 自訂索引標籤](https://developer.chrome.com/multidevice/android/customtabs)來載入 Uri 並維持瀏覽感知。

## <a name="external"></a>外部

將使用 `Intent` 來要求透過系統一般瀏覽器來開啟 Uri。

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>系統偏好

會使用 [SFSafariViewController](xref:SafariServices.SFSafariViewController) 來載入 Uri 並維持瀏覽感知。

## <a name="external"></a>外部

將使用主應用程式上的標準 `OpenUrl` 來在應用程式之外啟動預設瀏覽器。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

一律會啟動使用者的預設瀏覽器，不論 `BrowserLaunchMode` 為何。

--------------

## <a name="api"></a>API

- [Browser 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [Browser API 文件](xref:Xamarin.Essentials.Browser)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Open-Browser-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]

