---
title: Xamarin.Essentials：電子郵件
description: Xamarin.Essentials 中的電子郵件類別使應用程式能夠開啟包含主題、本文和收件者 (收件者、副本、密件副本) 等指定資訊的預設電子郵件應用程式。
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c2958cc4572c2f87c46c9edc5fc194284658f24
ms.sourcegitcommit: 704d4cfd418c17b0e85a20c33a16d2419db0be71
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2018
ms.locfileid: "51691746"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials：電子郵件

![發行前的 NuGet](~/media/shared/pre-release.png)

**電子郵件**類別使應用程式能夠開啟包含主題、本文和收件者 (收件者、副本、密件副本) 等指定資訊的預設電子郵件應用程式。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-email"></a>使用電子郵件

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

電子郵件功能透過呼叫 `ComposeAsync` 方法和包含電子郵件相關資訊的 `EmailMessage` 來運作：

```csharp
public class EmailTest
{
    public async Task SendEmail(string subject, string body, List<string> recipients)
    {
        try
        {
            var message = new EmailMessage
            {
                Subject = subject,
                Body = body,
                To = recipients,
                //Cc = ccRecipients,
                //Bcc = bccRecipients
            };
            await Email.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException fbsEx)
        {
            // Email is not supported on this device
        }
        catch (Exception ex)
        {
            // Some other exception occurred
        }
    }
}
```


## <a name="platform-differences"></a>平台差異

# <a name="androidtabandroid"></a>[Android](#tab/android)

無平台差異。

# <a name="iostabios"></a>[iOS](#tab/ios)

無平台差異。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

因為嘗試傳送 `Html` 的 `BodyFormat` 會擲回 `FeatureNotSupportedException`，所以僅支援 `PlainText`。

-----

## <a name="api"></a>API

- [電子郵件原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [電子郵件 API 文件](xref:Xamarin.Essentials.Email)
