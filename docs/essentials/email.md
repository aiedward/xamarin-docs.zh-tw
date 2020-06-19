---
title: Xamarin.Essentials：電子郵件
description: 中的 [電子郵件] 類別 Xamarin.Essentials 可讓應用程式以指定的資訊開啟預設的電子郵件應用程式，包括主旨、本文和收件者（收件者、副本、密件副本）。
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 08/20/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: eba2b6decc74c63e6b2790287842e6cc9b237bd2
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802378"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials：電子郵件

**電子郵件**類別使應用程式能夠開啟包含主題、本文和收件者 (收件者、副本、密件副本) 等指定資訊的預設電子郵件應用程式。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

> [!TIP]
> 若要在 iOS 上使用電子郵件 API，您必須在實體裝置上執行它，否則系統將會擲回例外狀況。

## <a name="using-email"></a>使用電子郵件

Xamarin.Essentials在您的類別中新增的參考：

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

這項功能可讓應用程式在裝置上的電子郵件客戶程式中以電子郵件傳送檔案。 Xamarin.Essentials會自動偵測檔案類型（MIME），並要求將檔案新增為附件。 每個電子郵件用戶端都不同，而且可能只支援特定的副檔名，或完全沒有。

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

# <a name="android"></a>[Android](#tab/android)

並非所有 Android 電子郵件用戶端都支援 `Html`，原因是完全無法偵測該項目，建議在傳送電子郵件時使用 `PlainText`。

# <a name="ios"></a>[iOS](#tab/ios)

無平台差異。

# <a name="uwp"></a>[UWP](#tab/uwp)

因為嘗試傳送 `Html` 的 `BodyFormat` 會擲回 `FeatureNotSupportedException`，所以僅支援 `PlainText`。

並非所有電子郵件用戶端都支援傳送附件。 如需詳細資訊，請參閱[文件](https://docs.microsoft.com/windows/uwp/contacts-and-calendar/sending-email)。

-----

## <a name="api"></a>API

- [電子郵件原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Email)
- [電子郵件 API 文件](xref:Xamarin.Essentials.Email)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Email-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
