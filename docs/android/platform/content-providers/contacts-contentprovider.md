---
title: 使用連絡人 ContentProvider
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/22/2018
ms.openlocfilehash: fca57b7af34ae2b28dda9bf20a95183138cbc641
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020537"
---
# <a name="using-the-contacts-contentprovider"></a>使用連絡人 ContentProvider

使用`ContentProvider`透過存取的資料的代碼根本不需要參考類別`ContentProvider`。 相反,Uri 用於在公開的資料上建立`ContentProvider`游標 。 Android 使用Uri 搜索系統,查找使用該標識`ContentProvider`符公開 的 應用程式。 Uri 是一個字串,通常採用反向 DNS`com.android.contacts/data`格式, 如 。

Android*聯繫人*提供程式不是讓開發人員記住此字串,而是`android.provider.ContactsContract`在類中公開其元數據。 此類用於確定的`ContentProvider`Uri以及可查詢的表和列的名稱。

某些數據類型還需要特殊的訪問許可權。 內建連絡人清單`android.permission.READ_CONTACTS`需要**AndroidManifest.xml**檔中的許可權。

從 Uri 建立游標有三種方法:

1. **託管查詢()** &ndash; Android 2.3(API 級別 10)和更早版本`ManagedQuery`的首選 方法返回游標,並自動管理刷新數據和關閉游標。 此方法在 Android 3.0(API 級別 11)中被棄用。

1. **ContentResolver.query()**&ndash;傳回非託管游標,這意味著必須在代碼中顯式刷新和關閉它。

1. **游標載入器()。在**&ndash;Android 3.0(API 級別 11)中引入`CursorLoader`的 LoadIn背景()`ContentProvider`現在是使用 的首選方式。 `CursorLoader`查詢後台線程`ContentResolver`上的 a,以便不會阻止 UI。
   此類可以使用 v4 相容性庫在舊版本的 Android 中訪問。

這些方法中的每一個都有相同的基本輸入集:

- **Uri**&ndash;的完全限定`ContentProvider`名稱 。
- **要**&ndash;為游標選擇哪些列的投影規範。
- **選擇**&ndash;類似於 SQL`WHERE`子句。
- **要**&ndash;選擇中取代的選擇參數。
- **排序按**&ndash;排序的列。

## <a name="creating-inputs-for-a-query"></a>建立查詢建立輸入

示例`ContactsProvider`代碼對 Android 的內建聯繫人提供程式執行非常簡單的查詢。 您不需要知道實際的 Uri 或列名稱 -`ContentProvider`查詢聯絡人`ContactsContract`所需的所有資訊都作為 類公開的常量提供。

無論使用哪種方法檢索游標,這些相同的物件都用作參數,如*聯繫人提供程式/聯繫人適配器.cs*檔中所示:

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

這個範例中`selection`, 將透過`selectionArgs``sortOrder``null`設定為來忽略 。

## <a name="creating-a-cursor-from-a-content-provider-uri"></a>從內容提供者 Uri 建立游標

建立參數物件後,可透過以下三種方式之一使用它們:

### <a name="using-a-managed-query"></a>使用託管查詢

適合 Android 2.3(API 等級 10)或更早版本的應用程式應使用此方法:

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

此游標將由 Android 管理,因此您無需關閉它。

### <a name="using-contentresolver"></a>使用內容解析器

`ContentResolver`直接訪問以獲取針對`ContentProvider`的遊標可以執行,如下所示:

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

此遊標是非託管的,因此在不再需要時必須關閉它。
確保代碼關閉打開的游標,否則將發生錯誤。

```csharp
cursor.Close();
```

或者,您可以調用`StartManagingCursor()``StopManagingCursor()`並 「管理」游標。 當活動停止並重新啟動時,託管游標將自動停用並重新查詢。

### <a name="using-cursorloader"></a>使用游標載入器

為 Android 3.0(API 級別 11)或較新的應用程式應使用此方法:

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

`CursorLoader`確保所有遊標操作都在後台線程上完成,並且可以在活動重新啟動時(例如由於配置更改)在活動實例中智慧重用現有游標,而不是重新載入數據。

早期的 Android 版本`CursorLoader`也可以使用[v4 支援函式庫](https://developer.android.com/tools/support-library/index.html)。

## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>使用自訂介面

要顯示連絡人映像,我們將使用自定義適配器,以便我們可以手動解析`PhotoId`對 圖像檔路徑的引用。

若要使用自訂介面卡顯示資料,該範例使用 從`CursorLoader`**聯絡人提供程式/連絡人適配器.cs**的**FillContact**方法中檢索所有聯絡人資料到本地集合中:

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

然後使用`contactList`集合實現 BaseAdapter 的方法。 適配器的實現與任何其他集合&ndash;一樣,這裡沒有特殊處理,因為資料來自`ContentProvider`:

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

使用Uri顯示圖像(如果存在)。 應用程式如下所示:

[![在 ListView 中顯示聯繫人的應用屏幕截圖;影像顯示在一個條目的左側](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

使用類似的代碼模式,應用程式可以訪問各種系統數據,包括使用者的照片、視頻和音樂。
某些資料類型要求在專案的**AndroidManifest.xml**中請求特殊許可權。

## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>使用 SimpleCursor 配接器顯示游標資料

游標也可以用`SimpleCursorAdapter`顯示 (儘管只顯示名稱,而不是照片)。 此程式碼展示如何`ContentProvider`使用`SimpleCursorAdapter`與 (此代碼不顯示在範例中):

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

有關實`SimpleCursorAdapter`作的詳細資訊[,請參閱清單檢視和適接器](~/android/user-interface/layouts/list-view/index.md)。

## <a name="related-links"></a>相關連結

- [連絡人配接器示範(範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
