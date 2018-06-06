---
title: Xamarin.Essentials： 電子郵件
description: 電子郵件中的類別 Xamarin.Essentials 讓應用程式開啟預設的電子郵件應用程式指定的資訊，包括主旨、 主體和收件者 （TO、 副本、 密件副本）。
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: aea2f429126180ae3d98bc665bed5574f416ea53
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782433"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials： 電子郵件

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**電子郵件**類別可讓應用程式來開啟預設的電子郵件應用程式指定的資訊，包括主旨、 主體和收件者名單 （TO、 副本、 密件副本）。

## <a name="using-email"></a>使用電子郵件

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

電子郵件功能的運作方式是呼叫`ComposeAsync`方法`EmailMessage`，其中包含電子郵件的相關資訊：

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
            // Some other exception occured
        }
    }
}
```

## <a name="api"></a>API

- [電子郵件的原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [電子郵件應用程式開發介面文件](xref:Xamarin.Essentials.Email)
