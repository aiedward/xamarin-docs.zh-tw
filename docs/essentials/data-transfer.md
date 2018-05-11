---
title: Xamarin.Essentials 資料傳輸
description: DataTransfer 類別可讓共用資料，例如文字和網頁的連結，在裝置上的其他應用程式的應用程式。
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: baec3bdd89cb98d7595a524b6b9c4263ca18aa41
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials 資料傳輸

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**DataTransfer**類別可讓共用資料，例如文字和網頁的連結，在裝置上的其他應用程式的應用程式。

## <a name="using-data-transfer"></a>使用資料傳輸

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

資料傳輸的功能的運作方式是呼叫`RequestAsync`包含了可共用給其他應用程式資訊的資料要求承載的方法。 文字和 Uri 可以混合，每個平台將會處理內容為基礎的篩選。

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

外部應用程式會出現提出要求時，要共用的使用者介面：

![資料傳輸](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>平台差異

| 平台 | 差異 |
| --- | --- |
| Android | Subject 屬性適用於想要的郵件主旨。 |
| iOS | 未使用的主旨。 |
| iOS | 未使用的標題。 |
| UWP | 標題會預設為應用程式名稱如果未設定。 |
| UWP | 未使用的主旨。 |

## <a name="api"></a>API

- [資料傳輸的原始程式碼](https://github.com/xamarin/Essentials/tree/master/Essentials/DataTransfer)
- [資料傳輸的 API 文件](xref:Xamarin.Essentials.DataTransfer)
