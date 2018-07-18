---
title: 'Xamarin.Essentials: SMS'
description: Xamarin.Essentials Sms 類別可讓應用程式以使用指定的訊息傳送給收件者開啟預設 SMS 應用程式。
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a93a67b83ea8f435a5e3ad5d26e1d6cbbb7092f7
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815593"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**Sms**類別可讓應用程式以使用指定的訊息傳送給收件者開啟預設 SMS 應用程式。

## <a name="using-sms"></a>使用 Sms

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

SMS 功能的運作方式是呼叫`ComposeAsync`方法`SmsMessage`，其中包含訊息的收件者和訊息，這兩者都是選擇性的主體。

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

- [Sms 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [Sms API 文件](xref:Xamarin.Essentials.Sms)
