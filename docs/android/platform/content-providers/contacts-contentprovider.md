---
title: 使用連絡人 ContentProvider
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/22/2018
ms.openlocfilehash: e83b9a594bad5ee3d29800988eb94812600da8a6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643702"
---
# <a name="using-the-contacts-contentprovider"></a>使用連絡人 ContentProvider

使用所公開`ContentProvider`之存取資料的程式碼, 完全不需要`ContentProvider`類別的參考。 取而代之的是, Uri 是用來在所公開`ContentProvider`的資料上建立游標。 Android 會使用 Uri, 在系統中搜尋公開具有該識別碼之`ContentProvider`的應用程式。 Uri 是字串, 通常會以反向 DNS 格式 (例如`com.android.contacts/data`)。

Android*連絡人*提供者會在`android.provider.ContactsContract`類別中公開其中繼資料, 而不是讓開發人員記住此字串。 這個類別是用來判斷的 Uri `ContentProvider` , 以及可查詢之資料表和資料行的名稱。

某些資料類型也需要特殊許可權才能存取。 內建的 [連絡人] 清單需要`android.permission.READ_CONTACTS` **androidmanifest.xml**中的許可權。

有三種方式可從 Uri 建立資料指標:

1. **ManagedQuery ()** Android 2.3 (API 層級 10) 和更早版本中的慣用`ManagedQuery`方法, 會傳回資料指標, 並自動管理重新整理資料和關閉資料指標。 &ndash; 這個方法在 Android 3.0 (API 層級 11) 中已被取代。

1. **ContentResolver ()** &ndash;傳回非受控資料指標, 這表示它必須在程式碼中明確地重新整理和關閉。

1. **CursorLoader ()。LoadInBackground ()** &ndash;是在 Android 3.0 (API 層級 11) `CursorLoader`引進, 現在是使用`ContentProvider`的慣用方式。 `CursorLoader``ContentResolver`在背景執行緒上查詢, 因此不會封鎖 UI。
   您可以使用 v4 相容性程式庫, 在較舊版本的 Android 中存取此類別。


每一個方法都有相同的基本輸入集合:

-  **Uri**的完整名稱`ContentProvider`。 &ndash;
-  **投射**&ndash;指定資料指標所要選取之資料行的規格。
-  **選取範圍**類似于 SQL `WHERE`子句。 &ndash;
-  **SelectionArgs**&ndash;要在選取範圍中取代的參數。
-  **SortOrder**&ndash;排序依據的資料行。



## <a name="creating-inputs-for-a-query"></a>建立查詢的輸入

`ContactsProvider`範例程式碼會對 Android 的內建連絡人提供者執行非常簡單的查詢。 您不需要知道實際的 Uri 或資料行名稱-所有查詢連絡人`ContentProvider`所需的資訊, 都可以當做`ContactsContract`類別所公開的常數。

不論使用哪一個方法來抓取資料指標, 這些相同的物件都會當做參數使用, 如*ContactsProvider/ContactsAdapter*檔案中所示:

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

在`selection`此範例中, 會`selectionArgs`將`sortOrder`和設定為, 以`null`忽略和。



## <a name="creating-a-cursor-from-a-content-provider-uri"></a>從內容提供者 Uri 建立資料指標

一旦建立參數物件之後, 就可以使用下列三種方式的其中一種:



### <a name="using-a-managed-query"></a>使用 Managed 查詢

以 Android 2.3 (API 層級 10) 或更早版本為目標的應用程式應該使用下列方法:

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

此資料指標將由 Android 管理, 因此您不需要將它關閉。



### <a name="using-contentresolver"></a>使用 ContentResolver

直接`ContentResolver`存取以取得的`ContentProvider`資料指標, 可以如下所示:

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

這個資料指標不受管理, 因此必須在不再需要時關閉它。
請確認程式碼會關閉已開啟的資料指標, 否則會發生錯誤。

```csharp
cursor.Close();
```

或者, 您可以呼叫`StartManagingCursor()` , `StopManagingCursor()`並對資料指標進行「管理」。 停止並重新啟動活動時, 會自動停用並重新查詢 Managed 資料指標。



### <a name="using-cursorloader"></a>使用 CursorLoader

針對 Android 3.0 (API 層級 11) 或更新版本所建立的應用程式應該使用此方法:

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

`CursorLoader`可確保所有資料指標作業都是在背景執行緒上完成, 而且可以在活動重新開機時, 以智慧方式在活動實例之間重複使用現有的游標 (例如, 因為設定變更), 而不是重新載入資料。

較舊的 Android 版本也可以`CursorLoader`使用[v4 支援程式庫](https://developer.android.com/tools/support-library/index.html)來使用類別。



## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>使用自訂介面卡顯示游標資料

為了顯示連絡人影像, 我們將使用自訂介面卡, 讓我們可以手動解析圖像`PhotoId`檔案路徑的參考。

若要顯示具有自訂介面卡的資料, 此`CursorLoader`範例會使用, 從**ContactsProvider/ContactsAdapter**的**FillContacts**方法中, 將所有連絡人資料取出至本機集合:

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

然後使用`contactList`集合來執行 BaseAdapter 的方法。 介面卡的執行方式與任何其他集合&ndash;一樣, 因為資料`ContentProvider`來自于, 所以在這裡沒有特別的處理:

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

使用裝置上影像檔案的 Uri, 顯示影像 (如果有的話)。 應用程式看起來像這樣:

[![在 ListView 中顯示連絡人之應用程式的螢幕擷取畫面;影像會顯示在一個專案的左邊](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

您的應用程式可以使用類似的程式碼模式來存取各種不同的系統資料, 包括使用者的相片、影片和音樂。
某些資料類型需要在專案的**androidmanifest.xml**中要求特殊許可權。



## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>使用 SimpleCursorAdapter 顯示資料指標

游標也可以與一起`SimpleCursorAdapter`顯示 (雖然只會顯示名稱, 而不是相片)。 此程式碼示範如何搭配使用`ContentProvider` `SimpleCursorAdapter` (此程式碼不會出現在範例中):

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

如需有關實施`SimpleCursorAdapter`的進一步資訊, 請參閱[listview 和介面卡](~/android/user-interface/layouts/list-view/index.md)。


## <a name="related-links"></a>相關連結

- [ContactsAdapter 示範 (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
