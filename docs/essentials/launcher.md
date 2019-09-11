---
title: Xamarin.Essentials Launcher
description: Xamarin.Essentials 中的 Launcher 類別可讓應用程式依系統開啟 URI。
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: 3e49e12faac1fc93816d9a51646f639046c200c5
ms.sourcegitcommit: ab51d32f4ea0e0d4701f0bf2f1465c9323cd070b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70887367"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials:啟動器

**Launcher** 類別可讓應用程式依系統開啟 URI。 當深層連結到其他應用程式的自訂 URI 配置時，通常會使用它。 若您要開啟瀏覽器並瀏覽網站，您應該參考 **[瀏覽器](open-browser.md)** API。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-launcher"></a>使用 Launcher

在類別中新增對 Xamarin.Essentials 的參考：

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

## <a name="files"></a>檔案

此功能可讓應用程式要求其他應用程式開啟及檢視檔案。 Xamarin.Essentials 會自動偵測檔案類型 (MIME)，並要求開啟檔案。

以下範例說明如何將文字寫入磁片，並要求將它開啟：

```csharp
var fn = "File.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

await Launcher.OpenAsync(new OpenFileRequest
{
    File = new ReadOnlyFile(file)
});
```

## <a name="platform-differences"></a>平台差異

# <a name="androidtabandroid"></a>[Android](#tab/android)

從 `CanOpenAsync` 傳回的工作會立即完成。

# <a name="iostabios"></a>[iOS](#tab/ios)

若此裝置上的目的地應用程式先前從未由 `OpenAsync` 從您的應用程式開啟，iOS 將會提示一次使用者允許您的應用程式開啟它。

從 `CanOpenAsync` 傳回的工作會立即完成。

您可以在[這裡](xref:UIKit.UIApplication.CanOpenUrl*)找到有關 iOS 實作的詳細資訊

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

無平台差異。

-----

## <a name="api"></a>API

- [Launcher 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Launcher API 文件](xref:Xamarin.Essentials.Launcher)
