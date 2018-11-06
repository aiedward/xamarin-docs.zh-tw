---
title: Xamarin.Essentials：資料傳輸
description: Xamarin.Essentials 中的 DataTransfer 類別，能讓應用程式將資料 (例如文字和 Web 連結) 共用於裝置上的其他應用程式。
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 179d4327aa768e7aa2c81dbbffd694d078327400
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674830"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials：資料傳輸

![發行前的 NuGet](~/media/shared/pre-release.png)

**DataTransfer** 類別能讓應用程式將資料 (例如文字和 Web 連結) 共用於裝置上的其他應用程式。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-data-transfer"></a>使用資料傳輸

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

資料傳輸功能透過呼叫具有資料要求承載的 `RequestAsync` 方法來運作，該承載包含要與其他應用程式共用的資訊。 文字和 URI 可以混合使用，每個平台都將根據內容來處理篩選。

```csharp

public class DataTransferTest
{
    public async Task ShareText(string text)
    {
        await DataTransfer.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await DataTransfer.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

提出要求時，會顯示要與外部應用程式共用的使用者介面：

![資料傳輸](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>平台差異

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `Subject` 屬性用於所需的訊息主旨。

# <a name="iostabios"></a>[iOS](#tab/ios)

* `Subject` 未使用。
* `Title` 未使用。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* `Title` 將預設為應用程式名稱，如果未設定。
* `Subject` 未使用。

-----

## <a name="api"></a>API

- [資料傳輸原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [資料傳輸 API 文件](xref:Xamarin.Essentials.DataTransfer)
