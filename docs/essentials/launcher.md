---
title: Xamarin.Essentials 發射
description: 中的啟動器類別 Xamarin.Essentials 可讓應用程式依系統開啟 URI。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 08/20/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1e68755778522fa61d593d25e763fae1569724cf
ms.sourcegitcommit: 2a7bbe9cbee3727ba20ee755c1713bcfdb4d8ecb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98950972"
---
# <a name="no-locxamarinessentials-launcher"></a>Xamarin.Essentials：啟動器

**Launcher** 類別可讓應用程式依系統開啟 URI。 當深層連結到其他應用程式的自訂 URI 配置時，通常會使用它。 若您要開啟瀏覽器並瀏覽網站，您應該參考 **[瀏覽器](open-browser.md)** API。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-launcher"></a>使用 Launcher

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

若要使用 Launcher 功能，請呼叫 `OpenAsync` 方法並傳入 `string` 或 `Uri` 以開啟。 (選擇性) `CanOpenAsync` 方法可用來檢查 URI 配置是否可由裝置上的應用程式處理。

```csharp
public class LauncherTest
{
    public async Task OpenRideShareAsync()
    {
        var supportsUri = await Launcher.CanOpenAsync("lyft://");
        if (supportsUri)
            await Launcher.OpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

這可以使用 `TryOpenAsync` 結合成單一呼叫，其會檢查參數是否可以開啟，如果可以，便加以開啟。

```csharp
public class LauncherTest
{
    public async Task<bool> OpenRideShareAsync()
    {
        return await Launcher.TryOpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

### <a name="additional-platform-setup"></a>其他平臺設定

# <a name="android"></a>[Android](#tab/android)

無額外設定。

# <a name="ios"></a>[iOS](#tab/ios)

在 iOS 9 及更新版本中，Apple 會強制執行應用程式可查詢的配置。 若要指定您要使用的配置，您必須在檔案 `LSApplicationQueriesSchemes` 中指定 `Info.plist` 。

```
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>lyft</string>  
    <string>fb</string>
</array>
```

# <a name="uwp"></a>[UWP](#tab/uwp)

無額外設定。

-----

## <a name="files"></a>檔案

此功能可讓應用程式要求其他應用程式開啟及檢視檔案。 Xamarin.Essentials 會自動偵測檔案類型 (MIME) 並要求開啟檔案。

以下是將文字寫入磁片並要求開啟的範例：

```csharp
var fn = "File.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

await Launcher.OpenAsync(new OpenFileRequest
{
    File = new ReadOnlyFile(file)
});
```

## <a name="presentation-location-when-opening-files"></a>開啟檔案時的展示位置

[!include[](~/essentials/includes/ios-PresentationSourceBounds.md)]

## <a name="platform-differences"></a>平台差異

# <a name="android"></a>[Android](#tab/android)

從 `CanOpenAsync` 傳回的工作會立即完成。

# <a name="ios"></a>[iOS](#tab/ios)

若此裝置上的目的地應用程式先前從未由 `OpenAsync` 從您的應用程式開啟，iOS 將會提示一次使用者允許您的應用程式開啟它。

從 `CanOpenAsync` 傳回的工作會立即完成。

您可以在[這裡](xref:UIKit.UIApplication.CanOpenUrl*)找到有關 iOS 實作的詳細資訊

# <a name="uwp"></a>[UWP](#tab/uwp)

無平台差異。

-----

## <a name="api"></a>API

- [Launcher 原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Launcher)
- [Launcher API 文件](xref:Xamarin.Essentials.Launcher)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Launcher-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
