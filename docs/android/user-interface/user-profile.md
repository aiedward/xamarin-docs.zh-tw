---
title: 使用者設定檔
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/22/2018
ms.openlocfilehash: bed346b33ac92f6a1c73cdd3b29fb70ba17c5e91
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509663"
---
# <a name="user-profile"></a>使用者設定檔

Android 支援從 API 層級5開始列舉[ContactsContract](xref:Android.Provider.ContactsContract)提供者的連絡人。 例如, [列出連絡人] 就像使用 [ [ContactContracts](xref:Android.Provider.ContactsContract.Contacts) ] 類別一樣簡單, 如下列程式碼範例所示:

```csharp
// Get the URI for the user's contacts:
var uri = ContactsContract.Contacts.ContentUri;

// Setup the "projection" (columns we want) for only the ID and display name:
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.Id, 
    ContactsContract.Contacts.InterfaceConsts.DisplayName };

// Use a CursorLoader to retrieve the user's contacts data:
CursorLoader loader = new CursorLoader(this, uri, projection, null, null, null);
ICursor cursor = (ICursor)loader.LoadInBackground();

// Print the contact data to the console if reading back succeeds:
if (cursor != null)
{
    if (cursor.MoveToFirst())
    {
        do
        {
            Console.WriteLine("Contact ID: {0}, Contact Name: {1}",
                               cursor.GetString(cursor.GetColumnIndex(projection[0])),
                               cursor.GetString(cursor.GetColumnIndex(projection[1])));
        } while (cursor.MoveToNext());
    }
}
```

從 Android 4 (API 層級 14) 開始, 您可以透過`ContactsContract`提供者取得[ContactsContact 設定檔](xref:Android.Provider.ContactsContract.Profile)類別。 `ContactsContact.Profile`提供裝置擁有者個人設定檔的存取權, 包括連絡人資料, 例如裝置擁有者的名稱和電話號碼。

## <a name="required-permissions"></a>必要的使用權限

若要讀取和寫入連絡人資料, 應用程式必須`READ_CONTACTS`分別`WRITE_CONTACTS`要求和許可權。
此外, 若要讀取和編輯使用者設定檔, 應用程式必須`READ_PROFILE`要求`WRITE_PROFILE`和許可權。

## <a name="updating-profile-data"></a>更新設定檔資料

一旦設定這些許可權之後, 應用程式就可以使用一般的 Android 技術來與使用者設定檔的資料互動。 例如, 若要更新設定檔的顯示名稱, 請呼叫[ContentResolver](xref:Android.Content.ContentResolver.Update*) , 並`Uri`透過 ContactsContract 取得[ContentRawContactsUri](xref:Android.Provider.ContactsContract.Profile.ContentRawContactsUri)屬性, 如下所示:

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>讀取設定檔資料

對 ContactsContact 發出查詢。 [ContentUri](xref:Android.Provider.ContactsContract.Profile.ContentUri)會讀回設定檔資料。 例如, 下列程式碼會讀取使用者設定檔的顯示名稱:

```csharp
// Read the profile
var uri = ContactsContract.Profile.ContentUri;

// Setup the "projection" (column we want) for only the display name:
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.DisplayName };

// Use a CursorLoader to retrieve the data:
CursorLoader loader = new CursorLoader(this, uri, projection, null, null, null);
ICursor cursor = (ICursor)loader.LoadInBackground();
if (cursor != null)
{
    if (cursor.MoveToFirst ())
    {
        Console.WriteLine(cursor.GetString (cursor.GetColumnIndex (projection [0])));
    }
}
```

## <a name="navigating-to-the-user-profile"></a>流覽至使用者設定檔

最後, 若要流覽至使用者設定檔, 請使用`ActionView`動作建立意圖, 然後將`ContactsContract.Profile.ContentUri`它傳遞給`StartActivity`方法, 如下所示:

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);
StartActivity (intent);
```

執行上述程式碼時, 會顯示使用者設定檔, 如下列螢幕擷取畫面所示:

[![顯示 John Doe 使用者設定檔的設定檔螢幕擷取畫面](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

使用使用者設定檔類似于與 Android 中的其他資料互動, 並提供額外的裝置個人化層級。

## <a name="related-links"></a>相關連結

- [ContactsProviderDemo (範例)](https://developer.xamarin.com/samples/monodroid/ContactsProviderDemo/)
- [霜淇淋三明治簡介](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平臺](https://developer.android.com/sdk/android-4.0.html)
