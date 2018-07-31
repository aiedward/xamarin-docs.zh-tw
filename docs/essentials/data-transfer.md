---
title: Xamarin.Essentials： 資料傳輸
description: Xamarin.Essentials DataTransfer 類別可讓應用程式共用資料，例如在裝置上的其他應用程式的文字和 web 連結。
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 31e27556a6681b144084d2177cf3fde8fe8e5459
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353515"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials： 資料傳輸

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**DataTransfer**類別可讓應用程式共用資料，例如在裝置上的其他應用程式的文字和 web 連結。

## <a name="using-data-transfer"></a>使用資料傳輸

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

資料傳輸功能的運作方式是呼叫`RequestAsync`方法具有資料的要求承載，其中包含要共用其他應用程式的資訊。 可以混合文字和 Uri，以及每個平台將會處理內容為基礎的篩選。

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

要共用外部的應用程式提出要求時所顯示的使用者介面：

![資料傳輸](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>平台差異

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `Subject` 屬性用於訊息的所需的主旨。

# <a name="iostabios"></a>[iOS](#tab/ios)

* `Subject` 不使用。
* `Title` 不使用。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* `Title` 將預設為應用程式名稱，如果未設定。
* `Subject` 不使用。

-----

## <a name="api"></a>API

- [資料傳輸原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [資料傳輸的 API 文件](xref:Xamarin.Essentials.DataTransfer)
