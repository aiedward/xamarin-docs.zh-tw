---
title: 建立自訂的 ContentProvider
description: 上一節示範如何使用內建的 ContentProvider 實作的資料。 本章節將說明如何建置自訂的 ContentProvider，然後取用其資料。
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: da8aacac1f282fefb6b8d0e84cae168cf3a7148b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953355"
---
# <a name="creating-a-custom-contentprovider"></a>建立自訂的 ContentProvider

_上一節示範如何使用內建的 ContentProvider 實作的資料。本章節將說明如何建置自訂的 ContentProvider，然後取用其資料。_

## <a name="about-contentproviders"></a>關於 ContentProviders

內容提供者類別必須繼承自`ContentProvider`。 它應該包含用來回應查詢的內部資料存放區，它應該公開常數，以協助使用程式碼進行資料的有效要求 Uri 和 MIME 類型。

### <a name="uri-authority"></a>URI （授權）

`ContentProviders` 存取在 Android 中使用的 Uri。 應用程式來公開`ContentProvider`設定中，它會回應的 Uri 其**AndroidManifest.xml**檔案。 安裝應用程式時，這些 Uri 會註冊，讓其他應用程式可以存取它們。

在適用於 Android 的 Mono，內容提供者類別應該要有`[ContentProvider]`屬性來指定的 Uri （或 Uri），應新增至**AndroidManifest.xml**。


### <a name="mime-type"></a>Mime 類型

MIME 類型的一般格式是由兩個部分所組成。 Android`ContentProviders`通常使用這兩個字串來進行的第一個部分的 MIME 類型：

1. `vnd.android.cursor.item` &ndash; 若要表示的單一資料列，使用`ContentResolver.CursorItemBaseType`常數中程式碼。

1. `vnd.android.cursor.dir` &ndash; 多個資料列，使用`ContentResolver.CursorDirBaseType`常數中程式碼。

MIME 類型的第二個部分是您的應用程式特定，而且應該使用反向 DNS 標準`vnd.`前置詞。 範例程式碼使用`vnd.com.xamarin.sample.Vegetables`。


### <a name="data-model-metadata"></a>資料模型中繼資料

取用端應用程式需要建構 Uri 的查詢，以存取不同類型的資料。 基底 Uri，可以展開，以參考特定資料表的資料，而且也可以包含參數來篩選結果。 也必須宣告的資料行和子句搭配產生的資料指標，以顯示資料。

若要確保只有有效的 Uri 查詢建構而成，它是慣用提供有效的字串，做為常數的值。 這可讓您更輕鬆地存取`ContentProvider`因為它可透過程式碼完成功能，可探索的值，並可防止在字串中的錯字。

在上述範例中`android.provider.ContactsContract`類別公開的連絡人資料的中繼資料。 我們的自訂`ContentProvider`我們只會公開類別本身的常數。


## <a name="implementation"></a>實作

有三個步驟來建立及使用自訂`ContentProvider`:

1. **建立資料庫類別**&ndash;實作`SQLiteOpenHelper`。

2. **建立`ContentProvider`類別**&ndash;實作`ContentProvider`資料庫執行個體，請使用中繼資料公開為常值和方法來存取資料。

3. **存取權`ContentProvider`透過其 Uri** &ndash;填入`CursorAdapter`使用`ContentProvider`、 存取方式為透過其 Uri。

如先前所討論，`ContentProviders`可以取用從以外定義所在的應用程式。 在此範例中使用的資料相同的應用程式，但請記住，其他應用程式也可以存取它，只要他們知道將 Uri 和結構描述 （這通常公開為常數值） 的相關資訊。


## <a name="create-a-database"></a>建立資料庫

大部分`ContentProvider`實作會根據`SQLite`資料庫。 中的範例資料庫程式碼**SimpleContentProvider/VegetableDatabase.cs**建立非常簡單的資料行的兩個資料庫，如所示：

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
  const string create_table_sql =
    "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
  const string DatabaseName = "vegetables.db";
  const int DatabaseVersion = 1;

  public VegetableDatabase(Context context) : base(context, DatabaseName, null, DatabaseVersion) { }
  public override void OnCreate(SQLiteDatabase db)
  {
    db.ExecSQL(create_table_sql);
    // seed with data
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Vegetables')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Fruits')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Flower Buds')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Legumes')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Bulbs')");
    db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Tubers')");
  }
  public override void OnUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
  {
    throw new NotImplementedException();
  }
}
```

資料庫實作本身不需要任何特殊的考量，會公開`ContentProvider`，但如果您想要繫結`ContentProvider's`資料`ListView`控制權接著唯一的整數資料行名為`_id`必須屬於結果集。 請參閱[Listview 和 Adapter](~/android/user-interface/layouts/list-view/index.md)如需使用詳細的文件`ListView`控制項。


## <a name="create-the-contentprovider"></a>建立 ContentProvider

本節的其餘部分會提供逐步指示如何**SimpleContentProvider/VegetableProvider.cs**範例類別所建立。


### <a name="initialize-the-database"></a>初始化資料庫

第一個步驟是子類別化`ContentProvider`並加入它將使用的資料庫。

```csharp
public class VegetableProvider : ContentProvider 
{
    VegetableDatabase vegeDB;
    public override bool OnCreate()
    {
       vegeDB = new VegetableDatabase(Context);
        return true;
    }
}
```

其餘的程式碼會形成實際的內容提供者實作，以允許進行探索並查詢資料。



## <a name="add-metadata-for-consumers"></a>新增取用者的中繼資料

有四種不同的類型，我們將在上公開的中繼資料的`ContentProvider`類別。 只需要將授權單位，其餘部分是由慣例。

- **授權單位** &ndash; `ContentProvider`屬性*必須*加入至類別，因此，它會向 Android 應用程式安裝。

- **Uri** &ndash; `CONTENT_URI`使其容易使用程式碼中公開為常數。 它應該符合授權單位，但包含配置和基底路徑。

- **MIME 類型**&ndash;的結果，以及單一的結果清單會被視為不同的內容類型，因此我們定義兩個 MIME 類型，代表它們。

- **InterfaceConsts** &ndash;每個資料行名稱 中，提供的常數值，以便使用程式碼輕鬆地可以探索，並可避免發生風險印刷錯誤參考。

此程式碼顯示這些項目實作的方式從上一個步驟新增至資料庫定義：

```csharp
[ContentProvider(new string[] { CursorTableAdapter.VegetableProvider.AUTHORITY })]
public class VegetableProvider : ContentProvider 
{
   public const string AUTHORITY = "com.xamarin.sample.VegetableProvider";
   static string BASE_PATH = "vegetables";
   public static readonly Android.Net.Uri CONTENT_URI = Android.Net.Uri.Parse("content://" + AUTHORITY + "/" + BASE_PATH);
   // MIME types used for getting a list, or a single vegetable
   public const string VEGETABLES_MIME_TYPE = ContentResolver.CursorDirBaseType + "/vnd.com.xamarin.sample.Vegetables";
   public const string VEGETABLE_MIME_TYPE = ContentResolver.CursorItemBaseType + "/vnd.com.xamarin.sample.Vegetables";
   // Column names
   public static class InterfaceConsts {
       public const string Id = "_id";
       public const string Name = "name";
   }
   VegetableDatabase vegeDB;
   public override bool OnCreate()
   {
       vegeDB = new VegetableDatabase(Context);
       return true;
   }
}
```


## <a name="implement-the-uri-parsing-helper"></a>實作 URI 剖析協助程式

因為使用程式碼會使用 Uri 來提出要求的`ContentProvider`，我們必須要能夠剖析這些要求，以判斷要傳回的資料。 `UriMatcher`類別可以幫助剖析 Uri，一旦已初始化與 Uri 模式`ContentProvider`支援。

`UriMatcher`在範例中將會初始化兩個 Uri:

1. *「 com.xamarin.sample.VegetableProvider/vegetables"* &ndash;要求傳回蔬菜的完整清單。

2. *「 com.xamarin.sample.VegetableProvider/vegetables/\#」* &ndash;何處\#是數字參數的預留位置 (`_id`資料庫中的資料列)。 星號預留位置 ("\*」) 也可用來比對文字參數。

在程式碼中使用常數來參考中繼資料值，例如授權單位和基底\_路徑。 傳回碼將用於方法來執行剖析，以判斷要傳回什麼資料的 Uri。

```csharp
const int GET_ALL = 0; // return code when list of Vegetables requested
const int GET_ONE = 1; // return code when a single Vegetable is requested by ID
static UriMatcher uriMatcher = BuildUriMatcher();
static UriMatcher BuildUriMatcher()
{
  var matcher = new UriMatcher(UriMatcher.NoMatch);
  // Uris to match, and the code to return when matched
  matcher.AddURI(AUTHORITY, BASE_PATH, GET_ALL); // all vegetables
  matcher.AddURI(AUTHORITY, BASE_PATH + "/#", GET_ONE); // specific vegetable by numeric ID
  return matcher;
}
```

此程式碼是所有私用`ContentProvider`類別。 請參閱[Google 的 UriMatcher 文件](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/)如需詳細資訊。


## <a name="implement-the-querymethod"></a>實作 QueryMethod

最簡單`ContentProvider`方法，以實作是`Query`方法。 實作會使用以下`UriMatcher`剖析`uri`參數並呼叫正確的資料庫的方法。 如果`uri`包含 ID 參數，則整數剖析出 (使用`LastPathSegment`) 和資料庫查詢中使用。

```csharp
public override Android.Database.ICursor Query(Android.Net.Uri uri, string[] projection, string selection, string[] selectionArgs, string sortOrder)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return GetFromDatabase();
  case GET_ONE:
    var id = uri.LastPathSegment;
    return GetFromDatabase(id); // the ID is the last part of the Uri
  default:
    throw new Java.Lang.IllegalArgumentException("Unknown Uri: " + uri);
  }
}
Android.Database.ICursor GetFromDatabase()
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables", null);
}
Android.Database.ICursor GetFromDatabase(string id)
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables WHERE _id = " + id, null);
}
```

`GetType`必須也會覆寫方法。 可能會呼叫這個方法，來判斷指定的 Uri 會傳回的內容類型。
這可為取用的應用程式如何處理該資料。

```csharp
public override String GetType(Android.Net.Uri uri)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return VEGETABLES_MIME_TYPE; // list
  case GET_ONE:
    return VEGETABLE_MIME_TYPE; // single item
  default:
    throw new Java.Lang.IllegalArgumentExceptoin ("Unknown Uri: " + uri);
   }
}
```


## <a name="implement-the-other-overrides"></a>實作其他的覆寫

我們的簡單範例不允許編輯或刪除資料，但必須實作 Insert、 Update 和 Delete 方法因此實作的情況下新增它們：

```csharp
public override int Delete(Android.Net.Uri uri, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override Android.Net.Uri Insert(Android.Net.Uri uri, ContentValues values)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override int Update(Android.Net.Uri uri, ContentValues values, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
```

完成基本`ContentProvider`實作。 一旦安裝應用程式之後，它會公開資料可供使用應用程式內同時也知道的 Uri 來參考它的任何其他應用程式。


## <a name="access-the-contentprovider"></a>存取 ContentProvider

一次`VegetableProvider`已實作，存取完成這份文件開頭的連絡人提供者相同的方式： 取得資料指標使用指定的 Uri，然後使用配接器存取的資料。


## <a name="bind-a-listview-to-a-contentprovider"></a>將 ListView 繫結至 ContentProvider

若要填入`ListView`資料，我們使用的 Uri，對應到蔬菜的未篩選的清單。 在程式碼中，我們使用的常數值`VegetableProvider.CONTENT_URI`，我們知道其解析後`com.xamarin.sample.vegetableprovider/vegetables`。 我們`VegetableProvider.Query`實作會傳回一個資料指標，則會繫結至`ListView`。

中的程式碼`SimpleContentProvider/HomeScreen.cs`顯示 顯示來自資料是如何簡單`ContentProvider`:

```csharp
listView = FindViewById<ListView>(Resource.Id.List);
string[] projection = new string[] { VegetableProvider.InterfaceConsts.Id, VegetableProvider.InterfaceConsts.Name} ;
string[] fromColumns = new string[] { VegetableProvider.InterfaceConsts.Name };
int[] toControlIds = new int[] { Android.Resource.Id.Text1 };

// CursorLoader introduced in Honeycomb (3.0, API_11)
var loader = new CursorLoader(this,
   VegetableProvider.CONTENT_URI, projection, null, null, null);
cursor = (ICursor)loader.LoadInBackground();

// Create a SimpleCursorAdapter
adapter = new SimpleCursorAdapter(this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlIds);
listView.Adapter = adapter;
```

產生的應用程式看起來像這樣：

[![應用程式清單蔬菜、 水果、 花花苞、 豆科植物、 燈泡、 Tubers 的螢幕擷取畫面](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)



## <a name="retrieve-a-single-item-from-a-contentprovider"></a>擷取單一項目從 ContentProvider

使用的應用程式也可以存取的資料，即可建構不同的 Uri （舉例來說） 是指特定資料列的單一資料列。

使用`ContentResolver`直接以存取單一項目，藉由建置具有必要的 Uri `Id`。

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

完整的方法看起來像這樣：

```csharp
protected void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
  var id = e.Id;
  string[] projection = new string[] { "name" };
  var uri = Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
  ICursor vegeCursor = ContentResolver.Query(uri, projection, null, new string[] { id.ToString() }, null);
  string text = "";
  if (vegeCursor.MoveToFirst()) {
    text = vegeCursor.GetInt(0) + " " + vegeCursor.GetString(1);
    Android.Widget.Toast.MakeText(this, text, Android.Widget.ToastLength.Short).Show();
  }
  vegeCursor.Close();
}
```


## <a name="related-links"></a>相關連結

- [SimpleContentProvider （範例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
