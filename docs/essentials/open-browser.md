---
title: Xamarin.Essentials 開啟瀏覽器
description: 中的瀏覽器類別 Xamarin.Essentials 可讓應用程式在優化系統偏好瀏覽器或外部瀏覽器中開啟網頁連結。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 09/24/2020
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0c38949e9c8c0a957a7afa37206683588ffbb4cf
ms.sourcegitcommit: 3a15d9b29d65139b18dcf0871fe00cffb2a56357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91353404"
---
# <a name="no-locxamarinessentials-browser"></a>Xamarin.Essentials：瀏覽器

**Browser** 類別可讓應用程式在最佳化系統偏好瀏覽器或外部瀏覽器中開啟網頁連結。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取 **瀏覽器** 功能，需要下列平臺特定設定。

# <a name="android"></a>[Android](#tab/android)

如果您專案的目標 Android 版本設為 **android 11 (R API 30) ** 您必須使用與新的 [封裝可見度需求](https://developer.android.com/preview/privacy/package-visibility)搭配使用的查詢來更新 android 資訊清單。

開啟 [Properties]**** 資料夾下的 **AndroidManifest.xml** 檔案並在 [manifest]**** 節點內新增下列內容：

```xml
<queries>
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <data android:scheme="http"/>
  </intent>
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <data android:scheme="https"/>
  </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

不需要進行額外設定。

# <a name="uwp"></a>[UWP](#tab/uwp)

無平台差異。

-----

## <a name="using-browser"></a>使用 Browser

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

Browser 功能的運作方式是使用 `Uri` 與 `BrowserLaunchMode` 呼叫 `OpenAsync` 方法。

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.OpenAsync(uri, BrowserLaunchMode.SystemPreferred);
    }
}
```

當瀏覽器已由使用者「啟動」__ 但未必由其「關閉」__ 後，會傳回此方法。  `bool` 結果會指出是否啟動成功。

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

# <a name="android"></a>[Android](#tab/android)

「啟動模式」決定瀏覽器的啟動方式：

## <a name="system-preferred"></a>系統偏好

[自訂](https://developer.chrome.com/multidevice/android/customtabs) 索引標籤將會嘗試用來載入 Uri 並保持流覽感知。

## <a name="external"></a>外部

將使用 `Intent` 來要求透過系統一般瀏覽器來開啟 Uri。

# <a name="ios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>系統偏好

會使用 [SFSafariViewController](xref:SafariServices.SFSafariViewController) 來載入 Uri 並維持瀏覽感知。

## <a name="external"></a>外部

將使用主應用程式上的標準 `OpenUrl` 來在應用程式之外啟動預設瀏覽器。

# <a name="uwp"></a>[UWP](#tab/uwp)

一律會啟動使用者的預設瀏覽器，不論 `BrowserLaunchMode` 為何。

--------------

## <a name="api"></a>API

- [Browser 原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Browser)
- [Browser API 文件](xref:Xamarin.Essentials.Browser)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Open-Browser-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
