---
title: Xamarin.Essentials:Email
description: Xamarin.Essentials 中的電子郵件類別使應用程式能夠開啟包含主題、本文和收件者 (收件者、副本、密件副本) 等指定資訊的預設電子郵件應用程式。
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: cccbe5f539e2807e749433623e938438e67965e8
ms.sourcegitcommit: 9a46ee759ec4a738da348e8f8904d0f482ef0f25
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060089"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials:Email

**電子郵件**類別使應用程式能夠開啟包含主題、本文和收件者 (收件者、副本、密件副本) 等指定資訊的預設電子郵件應用程式。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

> [!TIP]
> 若要在 iOS 上使用電子郵件 API，您必須在實體裝置上執行它，否則系統將會擲回例外狀況。

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

## <a name="file-attachments"></a>檔案附件

這項功能讓您可以在應用程式中，以裝置的電子郵件用戶端寄送檔案。 Xamarin.Essentials 會自動偵測檔案類型 (MIME)，並要求將檔案新增為附件。 每個電子郵件用戶端都不同，可能只支援特定的副檔名，也可能完全都不支援。

下列範例示範如何將文字寫入磁碟，並將它新增為電子郵件附件：

```csharp
var message = new EmailMessage
{
    Subject = "Hello",
    Body = "World",
};

var fn = "Attachment.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

message.Attachments.Add(new EmailAttachment(file));

await Email.ComposeAsync(message);
```

## <a name="platform-differences"></a>平台差異

# <a name="androidtabandroid"></a>[Android](#tab/android)

並非所有 Android 電子郵件用戶端都支援 `Html`，原因是完全無法偵測該項目，建議在傳送電子郵件時使用 `PlainText`。

# <a name="iostabios"></a>[iOS](#tab/ios)

無平台差異。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

因為嘗試傳送 `Html` 的 `BodyFormat` 會擲回 `FeatureNotSupportedException`，所以僅支援 `PlainText`。

並非所有電子郵件用戶端都支援傳送附件。 如需詳細資訊，請參閱[文件](https://docs.microsoft.com/windows/uwp/contacts-and-calendar/sending-email)。

-----

## <a name="api"></a>API

- [電子郵件原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [電子郵件 API 文件](xref:Xamarin.Essentials.Email)
