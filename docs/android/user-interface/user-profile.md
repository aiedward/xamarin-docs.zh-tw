---
title: 使用者設定檔
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/22/2018
ms.openlocfilehash: 1eaae86ab9eacf007eca792d96e889db6f367922
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765502"
---
# <a name="user-profile"></a>使用者設定檔

Android 已支援的列舉連絡人[ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/)自應用程式開發介面層級 5 提供者。 例如，列出連絡人很簡單，只使用[ContactContracts.Contacts](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract+Contacts/)類別，如下列程式碼範例所示：

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

從 Android 4 (API 層級 14) [ContactsContact.Profile](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract+Profile/)類別是可透過`ContactsContract`提供者。 `ContactsContact.Profile`可存取個人的設定檔之裝置，包括裝置擁有者的名稱和電話號碼等連絡資料擁有者。


## <a name="required-permissions"></a>必要的權限

若要讀取和寫入連絡資料，應用程式必須要求`READ_CONTACTS`和`WRITE_CONTACTS`權限，分別。
此外，若要閱讀和編輯使用者設定檔，應用程式必須要求`READ_PROFILE`和`WRITE_PROFILE`權限。


## <a name="updating-profile-data"></a>更新設定檔資料

一旦已設定這些權限，應用程式可以使用 Android 的一般技術與使用者設定檔的資料互動。 例如，若要更新的設定檔的顯示名稱，請呼叫[ContentResolver.Update](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.Update)與`Uri`透過擷取[ContactsContract.Profile.ContentRawContactsUri](https://developer.xamarin.com/api/property/Android.Provider.ContactsContract+Profile.ContentRawContactsUri/)屬性，如下所示如下：

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>讀取設定檔資料

發出查詢，以便[ContactsContact.Profile.ContentUri](https://developer.xamarin.com/api/property/Android.Provider.ContactsContract+Profile.ContentUri/)讀取備份設定檔資料。 例如，下列程式碼會讀取使用者設定檔的顯示名稱：

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

## <a name="navigating-to-the-user-profile"></a>瀏覽至 使用者設定檔

最後，巡覽至 使用者設定檔，建立與意圖`ActionView`動作和`ContactsContract.Profile.ContentUri`然後將它傳遞給`StartActivity`方法如下：

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);           
StartActivity (intent);
```

執行上述程式碼，當使用者設定檔會顯示如下列螢幕擷取畫面所示：

[![螢幕擷取畫面顯示 John Doe 的使用者設定檔的設定檔](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

使用使用者設定檔是類似於與 Android 中的其他資料互動，並且提供一層額外的裝置個人化。



## <a name="related-links"></a>相關連結

- [ContactsProviderDemo (sample)](https://developer.xamarin.com/samples/monodroid/ContactsProviderDemo/)
- [介紹的冰淇淋三明治](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
