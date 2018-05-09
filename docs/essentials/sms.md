---
title: Xamarin.Essentials SMS
description: Sms 類別可讓應用程式以使用指定的訊息傳送給收件者開啟預設 SMS 應用程式。
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 460aea01381934f1862946c6e17e314560e889f2
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
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
