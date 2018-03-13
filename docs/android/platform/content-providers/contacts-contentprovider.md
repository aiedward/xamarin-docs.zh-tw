---
title: "使用連絡人 ContentProvider"
ms.topic: article
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/22/2018
ms.openlocfilehash: 730cc1f815641d79350784790e3b33b743d1aebe
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="using-the-contacts-contentprovider"></a>使用連絡人 ContentProvider

使用存取所公開的資料的程式碼`ContentProvider`不需要的參考`ContentProvider`完全類別。 相反地，會使用 Uri 來建立資料指標所公開的資料`ContentProvider`。 Android 用來搜尋系統是否有公開的應用程式的 Uri`ContentProvider`具有該識別碼。 Uri 是通常在反向 DNS 格式的字串，例如`com.android.contacts/data`。

而不是執行記住這個字串，在 Android 開發人員*連絡人*提供者會公開它的中繼資料中`android.provider.ContactsContract`類別。 這個類別用來判斷的 Uri`ContentProvider`以及資料表和可查詢的資料行的名稱。

某些資料類型也需要存取特殊權限。 內建的連絡人清單需要`android.permission.READ_CONTACTS`中的權限**AndroidManifest.xml**檔案。

有三種方式可建立資料指標從 Uri:

1. **ManagedQuery()** &ndash;慣用的方法在 Android 2.3 (API 層級 10) 和舊版中，`ManagedQuery`傳回一個資料指標，也會自動管理資料重新整理和關閉資料指標。 在 Android 3.0 (API 層級 11)，這個方法已被取代。

1. **ContentResolver.Query()** &ndash;傳回 unmanaged 資料指標，這表示必須重新整理及程式碼中明確地關閉。

1. **CursorLoader()。LoadInBackground()** &ndash; Android 3.0 (API 層級 11) 中引進`CursorLoader`現在是較好的方法耗用`ContentProvider`。 `CursorLoader` 查詢`ContentResolver`背景執行緒，因此不封鎖 UI。
   這個類別可以存取舊版的 Android 使用 v4 相容性文件庫中。


每一種方法具有相同的基本組輸入：

-  **Uri** &ndash;完整限定的名稱`ContentProvider`。
-  **投影**&ndash;要選取的資料指標的資料行規格。
-  **選取項目**&ndash;類似於 SQL`WHERE`子句。
-  **SelectionArgs** &ndash;用來取代選取範圍中的參數。
-  **SortOrder** &ndash;排序所依據的資料行。



## <a name="creating-inputs-for-a-query"></a>建立查詢的輸入

`ContactsProvider`範例程式碼執行 Android 的內建連絡人的提供者非常簡單的查詢。 您不需要知道的實際 Uri 或資料行的名稱-查詢連絡人所需的所有資訊`ContentProvider`可做為所公開的常數`ContactsContract`類別。

不論哪一種方法用來擷取資料指標，這些相同的物件會當做參數中所示*ContactsProvider/ContactsAdapter.cs*檔案：

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

例如， `selection`，`selectionArgs`和`sortOrder`將設定為忽略`null`。



## <a name="creating-a-cursor-from-a-content-provider-uri"></a>建立資料指標從內容提供者 Uri

一旦建立參數物件，就可以使用下列三種方式之一：



### <a name="using-a-managed-query"></a>使用受管理的查詢

為 Android 2.3 (API 層級 10) 的目標應用程式或之前應該使用這個方法：

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

這個資料指標會受 Android，因此您不需要將它關閉。



### <a name="using-contentresolver"></a>使用 ContentResolver

存取`ContentResolver`直接以取得針對游標`ContentProvider`可以完成這種方式：

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

這個資料指標是 unmanaged，，因此必須在不再需要時關閉。
確定程式碼會關閉已開啟的資料指標，否則會發生錯誤。

```csharp
cursor.Close();
```

或者，您可以呼叫`StartManagingCursor()`和`StopManagingCursor()`來管理資料指標。 受管理的資料指標會自動停用並重新查詢活動會停止並重新啟動時。



### <a name="using-cursorloader"></a>使用 CursorLoader

針對 Android 3.0 (API 層級 11) 建置應用程式或更新版本應該使用這個方法：

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

`CursorLoader`可確保所有的資料指標作業都是在背景執行緒，而且可以有智慧地重複使用現有的資料指標跨活動執行個體活動重新啟動時 （例如，由於組態變更） 而不是，再重新載入資料。

也可以使用 Android 舊版`CursorLoader`類別使用[v4 支援程式庫](http://developer.android.com/tools/support-library/index.html)。



## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>顯示自訂配接器的資料指標資料

若要顯示連絡人的映像我們將使用自訂配接器，以便我們可以手動解決`PhotoId`參考了映像的檔案路徑。

若要自訂的配接器與顯示資料，此範例會使用`CursorLoader`成為本機集合中擷取所有連絡人資料**FillContacts**方法從**ContactsProvider/ContactsAdapter.cs**:

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

然後實作的 BaseAdapter 方法使用`contactList`集合。 就如同它可以與任何其他集合，實作配接器&ndash;會不做任何特殊處理，因為資料來自`ContentProvider`:

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

顯示的影像 （如果有） 使用裝置上的映像檔案的 Uri。 應用程式看起來像這樣：

[![螢幕擷取畫面顯示連絡人 ListView; 中的應用程式左邊的一個項目會顯示影像](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

您的應用程式可以使用類似的程式碼模式，來存取各種不同的系統資料，包括使用者的相片、 影片和音樂。
某些資料類型需要特殊權限可要求在專案的**AndroidManifest.xml**。



## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>顯示資料指標資料與 SimpleCursorAdapter

資料指標也會顯示與`SimpleCursorAdapter`(雖然只有名稱將會顯示，但不相片)。 此程式碼示範如何使用`ContentProvider`與`SimpleCursorAdapter`（此程式碼未出現在此範例）：

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

請參閱[Listview 和配接器](~/android/user-interface/layouts/list-view/index.md)專注於實作`SimpleCursorAdapter`。


## <a name="related-links"></a>相關連結

- [ContactsAdapter 示範 （範例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
