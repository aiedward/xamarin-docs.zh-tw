---
title: Xamarin.Essentials：共用
description: 中的共用類別可 Xamarin.Essentials 讓應用程式將資料（例如文字和 web 連結）共用至裝置上的其他應用程式。
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 01/04/2021
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 63d7c8e0494a9b8da65a1604a2ab8bf6c0a58f6b
ms.sourcegitcommit: 2d52346fa1407358e57c339a130a2330bad8e5b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2021
ms.locfileid: "102446459"
---
# <a name="xamarinessentials-share"></a>Xamarin.Essentials：共用

**Share** 類別能讓應用程式將資料 (例如文字和 Web 連結) 共用於裝置上的其他應用程式。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-share"></a>使用 Share

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

Share 功能的運作方式是呼叫具有資料要求承載的 `RequestAsync` 方法，該承載包含要與其他應用程式共用的資訊。 文字和 URI 可以混合使用，每個平台都將根據內容來處理篩選。

```csharp

public class ShareTest
{
    public async Task ShareText(string text)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

提出要求時，會顯示要與外部應用程式共用的使用者介面：

![與外部應用程式 UI 共用](images/share.png)

## <a name="file"></a>檔案

這項功能可讓應用程式與裝置上的其他應用程式共用檔案。 Xamarin.Essentials 會自動偵測檔案類型 (MIME) 和要求共用。 每個平台可能只支援特定的副檔名。

下列範例示範如何將文字寫入磁碟，並共用到其他應用程式：

```csharp
var fn =  "Attachment.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

await Share.RequestAsync(new ShareFileRequest
{
    Title = Title,
    File = new ShareFile(file)
});
```

## <a name="multiple-files"></a>多個檔案

共用多個檔案的使用方式與單一檔案不同，只是一次傳送數個檔案的能力：

```csharp
var file1 = Path.Combine(FileSystem.CacheDirectory, "Attachment1.txt");
File.WriteAllText(file, "Content 1");
var file2 = Path.Combine(FileSystem.CacheDirectory, "Attachment2.txt");
File.WriteAllText(file, "Content 2");

await Share.RequestAsync(new ShareMultipleFilesRequest
{
    Title = ShareFilesTitle,
    Files = new ShareFile[] { new ShareFile(file1), new ShareFile(file2) }
});
```

## <a name="presentation-location"></a>展示位置

[!include[](~/essentials/includes/ios-PresentationSourceBounds.md)]

## <a name="platform-differences"></a>平台差異

# <a name="android"></a>[Android](#tab/android)

- `Subject` 屬性用於所需的訊息主旨。

# <a name="ios"></a>[iOS](#tab/ios)

- `Subject` 未使用。

# <a name="uwp"></a>[UWP](#tab/uwp)

- `Title` 將預設為應用程式名稱，如果未設定。
- `Subject` 未使用。

-----

## <a name="api"></a>API

- [Share 原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Share)
- [Share API 文件](xref:Xamarin.Essentials.Share)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Share-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
