---
title: Xamarin.Essentials：連絡人
description: 中的 Contact 類別 Xamarin.Essentials 可讓使用者挑選連絡人並取得其相關資訊。
ms.assetid: 02280c42-720a-44c3-979e-4818a20c9821
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bd239a8dcf192c0bdbc6265769208f4fc989bbbe
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434492"
---
# <a name="no-locxamarinessentials-contacts"></a>Xamarin.Essentials：連絡人

Contact **類別可** 讓使用者挑選連絡人並取得其相關資訊。

![發行前 API](~/media/shared/preview.png)

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取「 **連絡人** 」功能，需要下列平臺特定設定。

# <a name="android"></a>[Android](#tab/android)

需要 `ReadContacts` 權限，而且必須在 Android 專案中設定。 能以下列方式新增：

開啟 [Properties]**** 資料夾下的 **AssemblyInfo.cs** 檔案並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.ReadContacts)]
```

或更新 Android 資訊清單：

開啟 [ **Properties** ] 資料夾底下的**AndroidManifest.xml**檔案，並在**資訊清單**節點內新增下列內容。

```xml
<uses-permission android:name="android.permission.READ_CONTACTS" /> />
```

禍以滑鼠右鍵按一 Android 專案並開啟專案的屬性。 在 [ **Android 資訊清單** ] 下，尋找 [ **必要許可權：** ] 區域並檢查此許可權。 這將會自動更新 **AndroidManifest.xml** 檔案。

# <a name="ios"></a>[iOS](#tab/ios)

在中 `Info.plist` ，新增下列機碼：

```xml
<key>NSContactsUsageDescription</key>
<string>This app needs access to contacts to pick a contact and get info.</string>
```

請務必更新您的 `<string>` 應用程式特定的描述中的，因為它會向您的使用者顯示。

# <a name="uwp"></a>[UWP](#tab/uwp)

在 [ `Package.appxmanifest` **功能** ] 下，確定 `Contact` 已核取 [功能]。

-----

## <a name="picking-a-contact"></a>挑選連絡人

藉由呼叫 `Contacts.PickContactAsync()` contact 對話方塊將會出現，並允許使用者接收使用者的相關資訊。


```csharp
try
{
    var contact = await Contacts.PickContactAsync();

    if(contact == null)
        return;

    var name = contact.Name;
    var contactType = contact.ContactType; // Unknown, Personal, Work
    var numbers = contact.Numbers; // List of phone numbers
    var emails = contact.Emails; // List of email addresses 
    
}
catch (Exception ex)
{
    // Handle exception here.
}
```


## <a name="api"></a>API

- [連絡人原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Contacts)
- [連絡人 API 檔](xref:Xamarin.Essentials.Contacts)
