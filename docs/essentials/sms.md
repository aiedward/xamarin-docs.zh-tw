---
title: Xamarin.Essentials：SMS
description: Xamarin.Essentials 中的 Sms 類別可讓應用程式使用傳送到收件者的指定訊息來開啟預設 SMS 應用程式。
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: a7b52bac0e9e2061cf9ff277db044ab232b1e9e5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "61347905"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials：SMS

**Sms** 類別可讓應用程式使用傳送到收件者的指定訊息來開啟預設 SMS 應用程式。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-sms"></a>使用 Sms

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

SMS 功能的運作方是呼叫 `SmsMessage` 上的 `ComposeAsync` 方法，此方法包含訊息的收件者與訊息的內文 (兩者皆為選擇性)。

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string recipient)
    {
        try
        {
            var message = new SmsMessage(messageText, new []{ recipient });
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

此外，您可以傳入多個收件者到 `SmsMessage`：

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string[] recipients)
    {
        try
        {
            var message = new SmsMessage(messageText, recipients);
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

- [Sms 始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [Sms API 文件](xref:Xamarin.Essentials.Sms)
