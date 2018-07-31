---
title: Xamarin.Essentials： 電子郵件
description: 電子郵件中的類別 Xamarin.Essentials 可讓應用程式以使用指定的資訊，包括主旨、 主體和 TO、 CC （BCC） 收件者開啟預設的電子郵件應用程式。
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: f113cebfebf4238fd4b75ad8ab248e2abf61efea
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353902"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials： 電子郵件

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**電子郵件**類別可讓您指定的資訊，包括主旨、 主體和 TO、 CC （BCC） 收件者開啟預設的電子郵件應用程式的應用程式。

## <a name="using-email"></a>使用電子郵件

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

電子郵件功能的運作方式是呼叫`ComposeAsync`方法`EmailMessage`，包含關於電子郵件的資訊：

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

## <a name="api"></a>API

- [電子郵件的原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [電子郵件 API 文件](xref:Xamarin.Essentials.Email)
