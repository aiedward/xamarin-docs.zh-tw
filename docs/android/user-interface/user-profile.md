---
title: 使用者設定檔
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/22/2018
ms.openlocfilehash: 1b7202a199315bf0be1664bf34725c26d12a66c8
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458068"
---
# <a name="user-profile"></a>使用者設定檔

自 API 層級5起，Android 支援列舉 [ContactsContract](xref:Android.Provider.ContactsContract) 提供者的連絡人。 例如，列出連絡人就像使用 ContactContracts 之類的 [contacts](xref:Android.Provider.ContactsContract.Contacts) 類別一樣簡單，如下列程式碼範例所示：

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

從 Android 4 開始 (API 層級 14) ， [ContactsContact 設定檔](xref:Android.Provider.ContactsContract.Profile) 類別可透過 `ContactsContract` 提供者取得。 `ContactsContact.Profile`提供裝置擁有者個人設定檔的存取權，包括連絡人資料，例如裝置擁有者的名稱和電話號碼。

## <a name="required-permissions"></a>必要權限

若要讀取和寫入連絡人資料，應用程式必須 `READ_CONTACTS` 分別要求和 `WRITE_CONTACTS` 許可權。
此外，若要讀取和編輯使用者設定檔，應用程式必須要求 `READ_PROFILE` 和 `WRITE_PROFILE` 許可權。

## <a name="updating-profile-data"></a>更新設定檔資料

設定好這些許可權之後，應用程式就可以使用一般的 Android 技術來與使用者設定檔的資料互動。 例如，若要更新設定檔的顯示名稱，請使用透過 ContactsContract ContentRawContactsUri 屬性抓取的[ContentResolver](xref:Android.Content.ContentResolver.Update*)來呼叫 `Uri` ，如下所示： [ContactsContract.Profile.ContentRawContactsUri](xref:Android.Provider.ContactsContract.Profile.ContentRawContactsUri)

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>讀取設定檔資料

將查詢發出至 ContactsContact 時， [ContentUri](xref:Android.Provider.ContactsContract.Profile.ContentUri) 會讀回設定檔資料。 例如，下列程式碼會讀取使用者設定檔的顯示名稱：

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

最後，若要流覽至使用者設定檔，請使用動作來建立意圖， `ActionView` `ContactsContract.Profile.ContentUri` 然後將它傳遞給 `StartActivity` 方法，如下所示：

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);
StartActivity (intent);
```

執行上述程式碼時，會顯示使用者設定檔，如下列螢幕擷取畫面所示：

[![顯示 John Doe 使用者設定檔之設定檔的螢幕擷取畫面](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

使用使用者設定檔類似于與 Android 中的其他資料互動，並提供額外層級的裝置個人化。

## <a name="related-links"></a>相關連結

- [ContactsProviderDemo (範例) ](/samples/xamarin/monodroid-samples/contactsproviderdemo)