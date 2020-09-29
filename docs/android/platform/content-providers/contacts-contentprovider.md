---
title: 使用連絡人 ContentProvider
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/22/2018
ms.openlocfilehash: 8c3608837132e221bec12b2c00fb2dd0b1730e76
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454074"
---
# <a name="using-the-contacts-contentprovider"></a>使用連絡人 ContentProvider

使用公開之存取資料的程式碼 `ContentProvider` 完全不需要類別的參考 `ContentProvider` 。 相反地，Uri 是用來在公開的資料上建立資料指標 `ContentProvider` 。 Android 會使用 Uri 來搜尋系統中的應用程式，該應用程式會 `ContentProvider` 使用該識別碼公開。 Uri 是字串，通常是以反向 DNS 格式（例如） `com.android.contacts/data` 。

Android *Contacts* 提供者不會讓開發人員記住這個字串，而是在類別中公開其中繼資料 `android.provider.ContactsContract` 。 這個類別是用來判斷的 Uri，以及 `ContentProvider` 可以查詢之資料表和資料行的名稱。

某些資料類型也需要特殊許可權才能存取。 內建的連絡人清單需要AndroidManifest.xml檔案 `android.permission.READ_CONTACTS` 中的許可權**AndroidManifest.xml** 。

從 Uri 建立資料指標的方法有三種：

1. **ManagedQuery ( # B1 ** &ndash; Android 2.3 (API 層級 10) 和更早版本中的慣用方法 `ManagedQuery` 會傳回資料指標，並自動管理重新整理資料和關閉資料指標。 此方法在 Android 3.0 (API Level 11) 中已淘汰。

1. **ContentResolver。查詢 ( # B1 ** &ndash; 傳回非受控資料指標，表示它必須在程式碼中明確地重新整理和關閉。

1. **CursorLoader ( # A1。LoadInBackground ( # B3 ** &ndash; 在 Android 3.0 (API Level 11) 中引進， `CursorLoader` 現在是使用的慣用方式 `ContentProvider` 。 `CursorLoader``ContentResolver`在背景執行緒上查詢，因此不會封鎖 UI。
   您可以使用 v4 相容性程式庫，在較舊版本的 Android 中存取這個類別。

上述每個方法都有相同的基本輸入集合：

- **Uri** &ndash; 的完整名稱  `ContentProvider` 。
- **投射** &ndash; 針對資料指標選取哪些資料行的規格。
- **選取專案** &ndash; 類似于 SQL  `WHERE` 子句。
- **SelectionArgs** &ndash; 要在選取專案中取代的參數。
- **SortOrder** &ndash; 排序依據的資料行。

## <a name="creating-inputs-for-a-query"></a>建立查詢的輸入

`ContactsProvider`範例程式碼會針對 Android 的內建連絡人提供者執行非常簡單的查詢。 您不需要知道實際的 Uri 或資料行名稱，查詢連絡人所需的所有資訊 `ContentProvider` 都可做為類別所公開的常數 `ContactsContract` 。

無論使用哪一種方法來取得資料指標，都會使用這些相同的物件做為參數，如 *ContactsProvider/ContactsAdapter .cs* 檔案所示：

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

在此範例中， `selection` `selectionArgs` 會將和 `sortOrder` 設定為，以忽略和 `null` 。

## <a name="creating-a-cursor-from-a-content-provider-uri"></a>從內容提供者 Uri 建立資料指標

一旦建立參數物件之後，就可以使用下列三種方式之一：

### <a name="using-a-managed-query"></a>使用 Managed 查詢

以 Android 2.3 (API 層級 10) 或更早版本為目標的應用程式應該使用此方法：

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

這個資料指標會由 Android 管理，因此您不需要關閉它。

### <a name="using-contentresolver"></a>使用 ContentResolver

`ContentResolver`直接存取以取得的資料指標 `ContentProvider` 可如下所示：

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

這個資料指標不受管理，因此必須在不再需要時關閉。
確定程式碼會關閉已開啟的資料指標，否則會發生錯誤。

```csharp
cursor.Close();
```

或者，您可以呼叫 `StartManagingCursor()` 和 `StopManagingCursor()` 來「管理」資料指標。 停止和重新開機活動時，會自動停用並重新查詢 Managed 資料指標。

### <a name="using-cursorloader"></a>使用 CursorLoader

針對 Android 3.0 (API 層級 11) 或更新版本所建立的應用程式應該使用此方法：

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

`CursorLoader`可確保所有資料指標作業都是在背景執行緒上完成，而且可以在重新開機活動時，以智慧方式在活動實例之間重複使用現有的資料指標 (例如，因為設定變更) 而不是重新載入資料。

較舊的 Android 版本也可以使用 `CursorLoader` [v4 支援程式庫](https://developer.android.com/tools/support-library/index.html)來使用類別。

## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>使用自訂介面卡顯示資料指標資料

為了顯示連絡人影像，我們將使用自訂介面卡，讓我們可以手動解析 `PhotoId` 影像檔案路徑的參考。

若要使用自訂介面卡來顯示資料，此範例會使用 `CursorLoader` 從**ContactsProvider/ContactsAdapter**的**FillContacts**方法中，將所有連絡人資料取出至本機集合：

```csharp
void FillContacts ()
{
   var uri = ContactsContract.Contacts.ContentUri;
   string[] projection = {
       ContactsContract.Contacts.InterfaceConsts.Id,
       ContactsContract.Contacts.InterfaceConsts.DisplayName,
       ContactsContract.Contacts.InterfaceConsts.PhotoId
  };
   // CursorLoader introduced in Honeycomb (3.0, API11)
   var loader = new CursorLoader(activity, uri, projection, null, null, null);
   var cursor = (ICursor)loader.LoadInBackground();
   contactList = new List<Contact> ();
   if (cursor.MoveToFirst ()) {
      do {
          contactList.Add (new Contact{
              Id = cursor.GetLong (cursor.GetColumnIndex (projection [0])),
              DisplayName = cursor.GetString (cursor.GetColumnIndex (projection [1])),
              PhotoId = cursor.GetString (cursor.GetColumnIndex (projection [2]))
          });
       } while (cursor.MoveToNext());
   }
}
```

然後使用集合來執行 BaseAdapter 的方法 `contactList` 。 介面卡的執行方式就如同使用任何其他集合一樣， &ndash; 因為資料是源自 `ContentProvider` ：

```csharp
Activity activity;
public ContactsAdapter (Activity activity)
{
   this.activity = activity;
   FillContacts ();
}
public override int Count {
   get { return contactList.Count; }
}
public override Java.Lang.Object GetItem (int position)
{
  return null; // could wrap a Contact in a Java.Lang.Object to return it here if needed
}
public override long GetItemId (int position)
{
   return contactList [position].Id;
}
public override View GetView (int position, View convertView, ViewGroup parent)
{
   var view = convertView ?? activity.LayoutInflater.Inflate (Resource.Layout.ContactListItem, parent, false);
   var contactName = view.FindViewById<TextView> (Resource.Id.ContactName);
   var contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
   contactName.Text = contactList [position].DisplayName;
   if (contactList [position].PhotoId == null) {
       contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
       contactImage.SetImageResource (Resource.Drawable.ContactImage);
   } else {
       var contactUri = ContentUris.WithAppendedId (ContactsContract.Contacts.ContentUri, contactList [position].Id);
       var contactPhotoUri = Android.Net.Uri.WithAppendedPath (contactUri, Contacts.Photos.ContentDirectory);
       contactImage.SetImageURI (contactPhotoUri);
   }
   return view;
}
```

如果映射存在) 使用裝置上影像檔案的 Uri，則會顯示該影像 (。 應用程式看起來像這樣：

[![顯示 ListView 中連絡人的應用程式螢幕擷取畫面;影像會顯示在一個專案的左側](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

使用類似的程式碼模式，您的應用程式可以存取各種不同的系統資料，包括使用者的相片、影片和音樂。
某些資料類型需要在專案的 **AndroidManifest.xml**中要求特殊許可權。

## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>使用 SimpleCursorAdapter 顯示資料指標資料

您也可以使用 (來顯示游標 `SimpleCursorAdapter` ，不過只會顯示名稱，而不是相片) 。 此程式碼示範如何搭配使用 `ContentProvider` `SimpleCursorAdapter` (此程式碼不會出現在範例) 中：

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName
};
var loader = new CursorLoader (this, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
var fromColumns = new string[] {ContactsContract.Contacts.InterfaceConsts.DisplayName};
var toControlIds = new int[] {Android.Resource.Id.Text1};
adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlsIds);
listView.Adapter = adapter;
```

如需有關實施的詳細資訊，請參閱 [listview 和介面卡](~/android/user-interface/layouts/list-view/index.md) `SimpleCursorAdapter` 。

## <a name="related-links"></a>相關連結

- [ContactsAdapter 示範 (範例) ](/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)