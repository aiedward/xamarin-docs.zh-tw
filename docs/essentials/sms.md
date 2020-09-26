---
title: Xamarin.Essentials： SMS
description: 中的 Sms 類別可 Xamarin.Essentials 讓應用程式使用指定的訊息來開啟預設 Sms 應用程式，以傳送給收件者。
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d981a7ed2bffbbff12cf69ee4d0cda27ce319040
ms.sourcegitcommit: 3a15d9b29d65139b18dcf0871fe00cffb2a56357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91353378"
---
# <a name="no-locxamarinessentials-sms"></a>Xamarin.Essentials： SMS

**Sms** 類別可讓應用程式使用傳送到收件者的指定訊息來開啟預設 SMS 應用程式。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取 **Sms** 功能，需要下列平臺特定設定。

# <a name="android"></a>[Android](#tab/android)

如果您專案的目標 Android 版本設為 **android 11 (R API 30) ** 您必須使用與新的 [封裝可見度需求](https://developer.android.com/preview/privacy/package-visibility)搭配使用的查詢來更新 android 資訊清單。

開啟 [Properties]**** 資料夾下的 **AndroidManifest.xml** 檔案並在 [manifest]**** 節點內新增下列內容：

```xml
<queries>
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <data android:scheme="smsto"/>
  </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

不需要進行額外設定。

# <a name="uwp"></a>[UWP](#tab/uwp)

無平台差異。

-----

## <a name="using-sms"></a>使用 Sms

Xamarin.Essentials在您的類別中新增的參考：

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

- [Sms 始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Sms)
- [Sms API 文件](xref:Xamarin.Essentials.Sms)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/SMS-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
