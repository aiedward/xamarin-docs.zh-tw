---
title: Xamarin.Essentials SMS
description: Sms 類別可讓應用程式以使用指定的訊息傳送給收件者開啟預設 SMS 應用程式。
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 5baeee03626ba659ac7e5c06be40039476a67e08
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials SMS

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**Sms**類別可讓應用程式以使用指定的訊息傳送給收件者開啟預設 SMS 應用程式。

## <a name="using-sms"></a>使用 Sms

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

SMS 功能的運作方式是呼叫`ComposeAsync`方法`SmsMessage`包含訊息的收件者和訊息，兩者都是選擇性的本文。

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string recipient)
    {
        try
        {
            var message = new SmsMessage(messageText, recipient);
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Sms 來源的程式碼](https://github.com/xamarin/Essentials/tree/master/Essentials/Sms)
- [Sms API 文件](xref:Xamarin.Essentials.Sms)
