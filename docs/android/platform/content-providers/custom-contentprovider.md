---
title: 建立自訂的 ContentProvider
description: 上一節演示了如何使用來自內置內容提供程序實現的數據。 本節將說明如何構建自定義內容提供程式,然後使用其數據。
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3e57e0cd2fa87db8035fa68995b69f231151fa09
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020531"
---
# <a name="creating-a-custom-contentprovider"></a>建立自訂的 ContentProvider

_上一節演示了如何使用來自內置內容提供程序實現的數據。本節將說明如何構建自定義內容提供程式,然後使用其數據。_

## <a name="about-contentproviders"></a>關於內容提供者

內容提供程式類必須從`ContentProvider`繼承。 它應由用於回應查詢的內部數據儲存組成,並且應該將 Uris 和 MIME 類型公開為常量,以説明使用代碼對數據發出有效的請求。

### <a name="uri-authority"></a>URI(授權)

`ContentProviders`使用Uri在Android中訪問。 公開`ContentProvider`將回應的 Uris 的應用程式在其**AndroidManifest.xml**檔中。 安裝應用程式時,將註冊這些 Uris,以便其他應用程式可以訪問它們。

在 Android 的 Mono 中`[ContentProvider]`,內容提供程式 類應具有指定應添加到**AndroidManifest.xml**的 Uri(或 Uris) 的屬性。

### <a name="mime-type"></a>米米類型

MIME 類型的典型格式由兩部分組成。 Android`ContentProviders`通常用於 MIME 類型的第一部分這兩個字串:

1. `vnd.android.cursor.item`&ndash;表示單個行,請使用代碼中的`ContentResolver.CursorItemBaseType`常量。

1. `vnd.android.cursor.dir`&ndash;對於多行,請使用代碼`ContentResolver.CursorDirBaseType`中的常量。

MIME 類型的第二部分特定於您的應用程式,應使用`vnd.`具有 首碼的反向 DNS 標準。 範例代碼使用`vnd.com.xamarin.sample.Vegetables`。

### <a name="data-model-metadata"></a>資料模型中繼資料

使用應用程式需要建構Uri查詢才能存取不同類型的資料。 可以擴展基Uri以引用特定數據表,還可以包括用於篩選結果的參數。 還必須聲明與生成的游標一起使用以顯示數據的列和子句。

為了確保僅構造有效的 Uri 查詢,通常將有效字串作為常量值提供。 這使得存取變得更容易`ContentProvider`, 因為它使這些值可以透過程式碼完成來發現,並防止字串中的拼寫錯誤。

在前面的範例中,`android.provider.ContactsContract`類公開了聯繫人數據的元數據。 對於我們的自定義`ContentProvider`,我們將只公開類本身上的常量。

## <a name="implementation"></a>實作

建立與使用自訂`ContentProvider`有三個步驟:

1. **建立資料庫類別**&ndash;`SQLiteOpenHelper`。

2. **使用資料庫`ContentProvider`** 的實例`ContentProvider`創建&ndash;類 實現,元數據公開為常量值和訪問數據的方法。

3. **`ContentProvider`透過其 Uri**&ndash;填`CursorAdapter`充`ContentProvider`存取, 透過其 Uri 存取 。

如前所述,`ContentProviders`可以從定義它們的位置以外的應用程式使用。 在此示例中,數據在同一應用程式中使用,但請記住,其他應用程式也可以訪問它,只要他們知道 Uri 和有關架構的資訊(通常作為常量值公開)。

## <a name="create-a-database"></a>建立資料庫

大`ContentProvider`部份可建立資料庫`SQLite`。 **簡單內容提供者/蔬菜資料庫.cs**中的範例資料庫代碼建立一個非常簡單的兩列資料庫,如下所示:

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

資料庫實現本身不需要任何特殊注意事項來用`ContentProvider`公開,但是,如果`ContentProvider's`要將數據綁定到控件`ListView`,`_id`則名為的唯一整數列必須是結果集的一部分。 有關使用`ListView`控制項的更多詳細資訊,請參閱[ListViews 和適配器](~/android/user-interface/layouts/list-view/index.md)文件。

## <a name="create-the-contentprovider"></a>建立內容提供者

本節的其餘部分提供有關**如何構建簡單內容提供程式/蔬菜提供者.cs**範例類的分步說明。

### <a name="initialize-the-database"></a>初始化資料庫

第一步是子類`ContentProvider`並添加要使用的資料庫。

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

代碼的其餘部分將形成實際的內容提供程序實現,允許發現和查詢數據。

## <a name="add-metadata-for-consumers"></a>為消費者新增中繼資料

我們將在`ContentProvider`類上公開四種不同類型的元數據。 只有權力是必需的,其餘的是按慣例完成的。

- **權限**&ndash;`ContentProvider`*必須*將屬性添加到類中,以便在安裝應用程式時將其註冊到 Android。

- **Uri** &ndash; Uri `CONTENT_URI` A 作為常量公開,因此在代碼中易於使用。 它應該與管理局匹配,但包括方案和基本路徑。

- **MIME 類型**&ndash;結果清單和單個結果被視為不同的內容類型,因此我們定義兩個 MIME 類型來表示它們。

- **介面Const**&ndash;為每個資料列名稱提供常量值,以便使用代碼可以輕鬆地發現和引用它們,而不會冒印刷錯誤的風險。

此代碼顯示每個項目的實用方式,從上一步新增到資料庫定義中:

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

## <a name="implement-the-uri-parsing-helper"></a>從 URI 解析說明程式

由於使用代碼使用 Uris`ContentProvider`來發出 請求,我們需要能夠分析這些請求,以確定要返回的數據。 類`UriMatcher`可以説明解析 Uris,一旦它已初始`ContentProvider`化為 支援的 Uri 模式。

範例中`UriMatcher`的將初始化為兩個 Uri:

1. *"com.xamarin.sample.蔬菜供應商/蔬菜"*&ndash;請求返回蔬菜的完整清單。

2. *"com.xamarin.sample.蔬菜供應商/\#蔬菜/",*&ndash;其中\#是數值參數(`_id`資料庫中行 的佔位符)。 星號占位符("")\*也可用於匹配文本參數。

在代碼中,我們使用常量來引用元資料值,如授權和 BASE\_PATH。 返回代碼將用於執行Uri解析的方法,以確定要返回的數據。

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

此代碼是類別的專用程式`ContentProvider`碼 。 有關詳細資訊,請參閱[Google 的 UriMatcher 文件](xref:Android.Content.UriMatcher)。

## <a name="implement-the-querymethod"></a>實作查詢方法

實作最`ContentProvider`簡單的方法是該`Query`方法 。 下面的實現使用`UriMatcher``uri`解析 參數並調用正確的資料庫方法。 如果`uri`包含 ID 參數,則整數將解析`LastPathSegment`(使用 ),並在資料庫查詢中使用。

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

還必須`GetType`重寫該方法。 可以調用此方法來確定將為給定 Uri 返回的內容類型。
這可能告訴使用的應用程式如何處理該數據。

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

## <a name="implement-the-other-overrides"></a>實現其他覆寫

我們的簡單示例不允許編輯或刪除資料,但必須實現「插入」、「更新」和「刪除」方法,以便在不實現的情況下添加它們:

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

這完成了基本`ContentProvider`實現。 安裝應用程式後,它公開的數據將在應用程式內部可用,也可以提供給知道 Uri 引用它的任何其他應用程式。

## <a name="access-the-contentprovider"></a>存取內容提供者

`VegetableProvider`實現 後,訪問它的方式與本文檔開頭的"連絡人"提供程式相同:使用指定的Uri獲取游標,然後使用適配器訪問數據。

## <a name="bind-a-listview-to-a-contentprovider"></a>將清單檢視繫結為內容提供者

為了使用數據`ListView`填充 a,我們使用 Uri 對應於未篩選的蔬菜清單。 在代碼中,我們使用常量值`VegetableProvider.CONTENT_URI`,我們知道該值解析`com.xamarin.sample.vegetableprovider/vegetables`為 。 我們的`VegetableProvider.Query`實現將返回一個游標,然後可以綁定`ListView`到 。

中`SimpleContentProvider/HomeScreen.cs`的代碼顯示顯示到資料是多麼簡單`ContentProvider`:

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

產生的應用程式如下所示:

[![應用程式清單的螢幕截圖,蔬菜, 水果, 花蕾, 豆類, 燈泡, 塊莖](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)

## <a name="retrieve-a-single-item-from-a-contentprovider"></a>從內容提供者檢索單項目

使用的應用程式可能還希望訪問單行數據,這可以通過構造引用特定行(例如)的不同Uri來完成。

直接`ContentResolver`使用 存取單項目,透過使用`Id`所需的 Uri 構建 Uri

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

完整方法如下所示:

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

- [簡單內容提供者 (例如範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
