---
title: Xamarin.Essentials:Email
description: Xamarin.Essentials 中的電子郵件類別使應用程式能夠開啟包含主題、本文和收件者 (收件者、副本、密件副本) 等指定資訊的預設電子郵件應用程式。
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.openlocfilehash: 06b4f4b612d0cb44e467a9da6dbee3194338027d
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2019
ms.locfileid: "58869958"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials:Email

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

# [<a name="android"></a>Android](#tab/android)

並非所有 Android 電子郵件用戶端都支援 `Html`，原因是完全無法偵測該項目，建議在傳送電子郵件時使用 `PlainText`。

# [<a name="ios"></a>iOS](#tab/ios)

無平台差異。

# [<a name="uwp"></a>UWP](#tab/uwp)

因為嘗試傳送 `Html` 的 `BodyFormat` 會擲回 `FeatureNotSupportedException`，所以僅支援 `PlainText`。

-----

## <a name="file-attachments"></a>檔案附件

![預覽功能](~/media/shared/preview.png)

以電子郵件寄送檔案在 Xamarin.Essentials 1.1.0 版為實驗性預覽功能。 這項功能讓您可以在應用程式中，以裝置的電子郵件用戶端寄送檔案。 若要啟用這項功能，請在您應用程式的啟動程式碼中設定下列屬性：

```csharp
ExperimentalFeatures.Enable(ExperimentalFeatures.EmailAttachments);
```

啟用此功能之後，任何檔案都可透過電子郵件來寄送。 Xamarin.Essentials 會自動偵測檔案類型 (MIME)，並要求將檔案新增為附件。 每個電子郵件用戶端都不同，可能只支援特定的副檔名，也可能完全都不支援。

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

## <a name="api"></a>API

- [電子郵件原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [電子郵件 API 文件](xref:Xamarin.Essentials.Email)
