---
title: 建立自訂的 ContentProvider
description: 上一節示範如何使用內建 ContentProvider 執行的資料。 本節將說明如何建立自訂 ContentProvider，然後使用其資料。
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 7583cfcb4f1a22167b95370f1376aa70d61188a0
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454048"
---
# <a name="creating-a-custom-contentprovider"></a>建立自訂的 ContentProvider

_上一節示範如何使用內建 ContentProvider 執行的資料。本節將說明如何建立自訂 ContentProvider，然後使用其資料。_

## <a name="about-contentproviders"></a>關於 ContentProviders

內容提供者類別必須繼承自 `ContentProvider` 。 它應該包含用來回應查詢的內部資料存放區，而且應該將 Uri 和 MIME 類型公開為常數，以協助取用程式碼對資料提出有效的要求。

### <a name="uri-authority"></a>URI (授權單位) 

`ContentProviders` 在 Android 中使用 Uri 存取。 公開的應用程式 `ContentProvider` 會在其 **AndroidManifest.xml** 檔案中，設定要回應的 uri。 安裝應用程式時，會註冊這些 Uri，讓其他應用程式可以存取這些 Uri。

在 Mono for Android 中，內容提供者類別應該具有 `[ContentProvider]` 屬性，以指定應新增至 **AndroidManifest.xml**的 Uri (或 uri) 。

### <a name="mime-type"></a>Mime 類型

MIME 類型的典型格式是由兩個部分所組成。 Android `ContentProviders` 通常會使用這兩個字串作為 MIME 類型的第一個部分：

1. `vnd.android.cursor.item`&ndash;若要表示單一資料列，請 `ContentResolver.CursorItemBaseType` 在程式碼中使用常數。

1. `vnd.android.cursor.dir`若 &ndash; 為多個資料列，請 `ContentResolver.CursorDirBaseType` 在程式碼中使用常數。

MIME 類型的第二個部分是您的應用程式特有的部分，而且應該使用具有前置詞的反向 DNS 標準 `vnd.` 。 範例程式碼會使用 `vnd.com.xamarin.sample.Vegetables` 。

### <a name="data-model-metadata"></a>資料模型中繼資料

使用應用程式需要建立 Uri 查詢來存取不同類型的資料。 基底 Uri 可以擴充以參考特定資料表，也可能包含參數來篩選結果。 與產生的資料指標一起使用的資料行和子句也必須宣告。

為了確保只會建立有效的 Uri 查詢，建議您提供有效的字串做為常數值。 這可讓您更輕鬆地存取， `ContentProvider` 因為它可透過程式碼自動完成來搜尋值，並避免字串出現錯誤。

在先前的範例中， `android.provider.ContactsContract` 類別公開了連絡人資料的中繼資料。 針對我們的自訂， `ContentProvider` 我們只會公開類別本身的常數。

## <a name="implementation"></a>實作

建立和使用自訂的步驟有三個 `ContentProvider` ：

1. **建立資料庫類別** &ndash; 實行 `SQLiteOpenHelper` 。

2. **建立 `ContentProvider` 類別** &ndash;`ContentProvider`使用資料庫的實例、公開為常數值的中繼資料，以及用來存取資料的方法來執行。

3. ** `ContentProvider` 經由其 Uri 存取** &ndash;`CursorAdapter`使用可透過 Uri 存取的來填入 `ContentProvider` 。

如先前所討論， `ContentProviders` 可以從其定義所在以外的應用程式使用。 在此範例中，資料會在相同的應用程式中取用，但請記住，如果其他應用程式知道 Uri 和 (架構的相關資訊（通常會公開為) 的常數值），則也可以存取該資料。

## <a name="create-a-database"></a>建立資料庫

大部分 `ContentProvider` 的執行都是以資料庫為基礎 `SQLite` 。 **SimpleContentProvider/VegetableDatabase**中的範例資料庫程式碼會建立一個非常簡單的兩個數據行資料庫，如下所示：

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

資料庫執行本身不需要任何特殊的考慮，只要將資料系結 `ContentProvider` 至控制項，但如果您想要將資料系結 `ContentProvider's` 至 `ListView` 控制項，則名為的唯一整數資料行 `_id` 必須是結果集的一部分。 如需使用控制項的詳細資訊，請參閱 [listview 和介面卡](~/android/user-interface/layouts/list-view/index.md) 檔 `ListView` 。

## <a name="create-the-contentprovider"></a>建立 ContentProvider

本節的其餘部分提供有關如何建立 **SimpleContentProvider/VegetableProvider .cs** 範例類別的逐步指示。

### <a name="initialize-the-database"></a>初始化資料庫

第一個步驟是子類別 `ContentProvider` ，並加入將使用的資料庫。

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

其餘的程式碼會形成實際的內容提供者實作為，讓您能夠探索和查詢資料。

## <a name="add-metadata-for-consumers"></a>新增取用者的中繼資料

有四種不同類型的中繼資料會在類別上公開 `ContentProvider` 。 只有授權單位是必要的，其餘部分則依照慣例完成。

- **授權** &ndash; 單位 `ContentProvider` 屬性 *必須* 加入至類別，才能在安裝應用程式時向 Android 註冊。

- **Uri** &ndash;`CONTENT_URI`會公開為常數，方便在程式碼中使用。 它應該符合授權單位，但包含配置和基底路徑。

- **MIME 類型** &ndash; 結果清單和單一結果會被視為不同的內容類型，因此我們會定義兩個 MIME 類型來代表它們。

- **InterfaceConsts** &ndash; 為每個資料行名稱提供常數值，如此一來，使用程式碼就可以輕鬆地探索並參考它們，而不會有風險的打字錯誤。

這段程式碼會顯示每個專案的實作為方式，從上一個步驟新增至資料庫定義：

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

## <a name="implement-the-uri-parsing-helper"></a>執行 URI 剖析協助程式

因為取用程式碼會使用 Uri 來提出要求 `ContentProvider` ，所以我們必須能夠剖析這些要求，以判斷要傳回哪些資料。 `UriMatcher`類別可在使用支援的 Uri 模式初始化時，協助剖析 uri `ContentProvider` 。

`UriMatcher`範例中的會使用兩個 uri 進行初始化：

1. *VegetableProvider/蔬菜* &ndash; 的 "要求傳回蔬菜的完整清單。

2. *VegetableProvider/蔬菜/ \# "* ， &ndash; 其中 \# 是數值參數的預留位置 (`_id` 資料庫) 中資料列的位置。 星號預留位置 ( " \* " ) 也可以用來比對文字參數。

在程式碼中，我們使用常數來參考中繼資料值，例如授權單位和基底 \_ 路徑。 傳回碼將用於進行 Uri 剖析的方法，以決定要傳回的資料。

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

此程式碼是類別的私用程式碼 `ContentProvider` 。 如需詳細資訊，請參閱 [Google 的 UriMatcher 檔](xref:Android.Content.UriMatcher) 。

## <a name="implement-the-querymethod"></a>執行 QueryMethod

要執行的最簡單 `ContentProvider` 方法是 `Query` 方法。 下列的執行會使用 `UriMatcher` 來剖析 `uri` 參數，並呼叫正確的資料庫方法。 如果 `uri` 包含 ID 參數，則會使用) 將整數剖析 (`LastPathSegment` ，並在資料庫查詢中使用。

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

`GetType`也必須覆寫方法。 您可以呼叫這個方法來判斷將針對指定 Uri 傳回的內容類型。
這可能會告訴使用中的應用程式如何處理該資料。

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

## <a name="implement-the-other-overrides"></a>執行其他覆寫

我們的簡單範例不允許編輯或刪除資料，但必須實作為 Insert、Update 和 Delete 方法，以便在沒有執行的情況下新增這些方法：

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

這會完成基本的 `ContentProvider` 執行。 一旦安裝應用程式之後，它所公開的資料就會在應用程式內，也可以在任何其他知道 Uri 參考它的應用程式中使用。

## <a name="access-the-contentprovider"></a>存取 ContentProvider

一旦 `VegetableProvider` 執行後，存取它的方式與本檔開頭的連絡人提供者相同：使用指定的 Uri 取得資料指標，然後使用介面卡來存取資料。

## <a name="bind-a-listview-to-a-contentprovider"></a>將 ListView 系結至 ContentProvider

若要在中填入 `ListView` 資料，我們使用對應到未篩選之蔬菜清單的 Uri。 在程式碼中，我們會使用 `VegetableProvider.CONTENT_URI` 我們已知解析成的常數值 `com.xamarin.sample.vegetableprovider/vegetables` 。 我們的 `VegetableProvider.Query` 實會傳回可系結至的資料指標 `ListView` 。

中的 `SimpleContentProvider/HomeScreen.cs` 程式碼會顯示顯示資料的簡單程度 `ContentProvider` ：

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

產生的應用程式如下所示：

[![列出蔬菜、水果、花卉 Buds、Legumes、燈泡、Tubers 的應用程式螢幕擷取畫面](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)

## <a name="retrieve-a-single-item-from-a-contentprovider"></a>從 ContentProvider 中取出單一專案

取用應用程式可能也會想要存取單一資料列，這可以藉由建立參考特定資料列的不同 Uri 來完成，例如) 的 (。

使用 `ContentResolver` 必要的建立 Uri，直接使用存取單一專案 `Id` 。

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

- [SimpleContentProvider (範例) ](/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)