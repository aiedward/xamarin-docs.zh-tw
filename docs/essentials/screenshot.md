---
title: Xamarin.Essentials：螢幕擷取畫面
description: 本檔說明中的螢幕擷取畫面類別 Xamarin.Essentials ，可讓您捕捉應用程式目前顯示的畫面。
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 085da722aa2e893f97efb1c89f20b03da330ac3e
ms.sourcegitcommit: 744f977b0595f489c592e29c8a3ba548fde02b6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91414759"
---
# <a name="no-locxamarinessentials-screenshot"></a>Xamarin.Essentials：螢幕擷取畫面

**螢幕擷取畫面**類別可讓您捕捉應用程式目前顯示的畫面。

![發行前 API](~/media/shared/preview.png)


## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-screenshot"></a>使用螢幕擷取畫面

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

然後呼叫 `CaptureAsync` 以取得正在執行之應用程式目前畫面的螢幕擷取畫面。 這會傳回 `ScreenshotResult` ，它可以用來取得所 `Width` `Height` 拍攝螢幕擷取畫面的、和 `Stream` 。


```csharp
async Task CaptureScreenshot()
{
    var screenshot = await Screenshot.CaptureAsync();
    var stream = await screenshot.OpenReadAsync();

    Image = ImageSource.FromStream(() => stream);
}
```


## <a name="api"></a>API

- [螢幕擷取畫面來源程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Screenshot)
- [螢幕擷取畫面 API 檔](xref:Xamarin.Essentials.Screenshot)
