---
title: 建立自訂的 ContentProvider
description: 上一節示範了如何從內建的 ContentProvider 執行中取用資料。 本節將說明如何建立自訂 ContentProvider，然後使用其資料。
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3e57e0cd2fa87db8035fa68995b69f231151fa09
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020531"
---
# <a name="creating-a-custom-contentprovider"></a>建立自訂的 ContentProvider

_上一節示範了如何從內建的 ContentProvider 執行中取用資料。本節將說明如何建立自訂 ContentProvider，然後使用其資料。_

## <a name="about-contentproviders"></a>關於 ContentProviders

內容提供者類別必須繼承自 `ContentProvider`。 它應該包含用來回應查詢的內部資料存放區，而且它應該公開 Uri 和 MIME 類型做為常數，以協助取用程式碼對資料發出有效的要求。

### <a name="uri-authority"></a>URI （授權單位）

在 Android 中使用 Uri 存取 `ContentProviders`。 公開 `ContentProvider` 的應用程式會在其**androidmanifest.xml**中設定要回應的 uri。 安裝應用程式時，會註冊這些 Uri，讓其他應用程式可以存取它們。

在適用于 Android 的 Mono 中，內容提供者類別應具有 `[ContentProvider]` 屬性，以指定應新增至**androidmanifest.xml**的 uri （或 uri）。

### <a name="mime-type"></a>Mime 類型

MIME 類型的一般格式是由兩個部分所組成。 Android `ContentProviders` 通常會針對 MIME 類型的第一個部分使用這兩個字串：

1. `vnd.android.cursor.item` &ndash; 若要表示單一資料列，請在程式碼中使用 `ContentResolver.CursorItemBaseType` 常數。

1. `vnd.android.cursor.dir` &ndash; 多個資料列，請在程式碼中使用 `ContentResolver.CursorDirBaseType` 常數。

MIME 類型的第二個部分是您的應用程式所特有，而且應該使用具有 `vnd.` 前置詞的反向 DNS 標準。 範例程式碼會使用 `vnd.com.xamarin.sample.Vegetables`。

### <a name="data-model-metadata"></a>資料模型中繼資料

使用應用程式需要建立 Uri 查詢來存取不同類型的資料。 基底 Uri 可以擴充以參考特定資料表，也可能包含用來篩選結果的參數。 搭配產生的資料指標使用的資料行和子句也必須宣告。

為了確保只會建立有效的 Uri 查詢，建議您提供有效的字串做為常數值。 這可讓您更輕鬆地存取 `ContentProvider`，因為它可讓您透過程式碼自動完成來探索值，並避免字串中出現錯誤。

在上述範例中，`android.provider.ContactsContract` 類別公開了連絡人資料的中繼資料。 針對我們的自訂 `ContentProvider`，我們只會在類別本身公開常數。

## <a name="implementation"></a>實作

建立和使用自訂 `ContentProvider`有三個步驟：

1. **建立資料庫類別**&ndash; 執行 `SQLiteOpenHelper`。

2. **建立 `ContentProvider` 類別**&ndash; 使用資料庫的實例來執行 `ContentProvider`、公開為常數值的中繼資料，以及存取資料的方法。

3. 透過**其 Uri 存取 `ContentProvider`** &ndash; 使用 `ContentProvider`來填入 `CursorAdapter`，並透過其 uri 來存取。

如先前所討論，`ContentProviders` 可以從其定義所在的應用程式取用。 在此範例中，資料會在相同的應用程式中取用，但請記住，其他應用程式也可以存取它，只要它們知道架構的 Uri 和相關資訊（通常會公開為常數值）。

## <a name="create-a-database"></a>建立資料庫

大部分的 `ContentProvider` 的執行都是以 `SQLite` 資料庫為基礎。 **SimpleContentProvider/VegetableDatabase**中的範例資料庫程式碼會建立一個非常簡單的兩個數據行資料庫，如下所示：

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

資料庫本身不需要任何特殊考慮，就能以 `ContentProvider`公開，不過，如果您想要將 `ContentProvider's` 資料系結至 `ListView` 控制項，則名為 `_id` 的唯一整數資料行必須是結果集的一部分。 如需使用 `ListView` 控制項的詳細資訊，請參閱[listview 和介面卡](~/android/user-interface/layouts/list-view/index.md)檔。

## <a name="create-the-contentprovider"></a>建立 ContentProvider

本節的其餘部分提供逐步指示，說明如何建立**SimpleContentProvider/VegetableProvider**範例類別。

### <a name="initialize-the-database"></a>初始化資料庫

第一個步驟是將 `ContentProvider` 子類別化，並加入它將使用的資料庫。

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

其餘的程式碼會形成實際的內容提供者實體系，以允許探索和查詢資料。

## <a name="add-metadata-for-consumers"></a>新增取用者的中繼資料

我們將在 `ContentProvider` 類別上公開四種不同類型的中繼資料。 只有授權單位是必要的，其餘部分則依照慣例完成。

- *必須*將 `ContentProvider` 屬性的**授權**單位 &ndash; 新增至類別，才能在安裝應用程式時向 Android 註冊。

- **Uri** &ndash; `CONTENT_URI` 會公開為常數，以便在程式碼中輕鬆使用。 它應該符合授權單位，但包含配置和基底路徑。

- **Mime 類型**&ndash; 結果清單和單一結果會被視為不同的內容類型，因此我們會定義兩個 mime 類型來代表它們。

- **InterfaceConsts** &ndash; 提供每個資料行名稱的常數值，讓取用程式碼可以輕鬆地探索並參考它們，而不會產生錯誤的發生風險。

此程式碼會顯示每個專案的執行方式，並新增到上一個步驟中的資料庫定義：

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

因為取用程式碼會使用 Uri 來提出 `ContentProvider`的要求，所以我們必須能夠剖析這些要求，以判斷要傳回的資料。 使用 `ContentProvider` 所支援的 Uri 模式初始化 uri 之後，`UriMatcher` 類別可以協助剖析 Uri。

範例中的 `UriMatcher` 將會使用兩個 Uri 進行初始化：

1. 「 *VegetableProvider/蔬菜*」 &ndash; 要求傳回蔬菜的完整清單。

2. 「 *VegetableProvider/蔬菜/\#* 」 &ndash; 其中 \# 是數值參數（資料庫中資料列的 `_id`）的預留位置。 星號預留位置（"\*"）也可以用來比對文字參數。

在程式碼中，我們使用常數來參考中繼資料值，例如授權單位和基底\_路徑。 傳回碼將用於執行 Uri 剖析的方法，以判斷要傳回的資料。

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

這段程式碼是 `ContentProvider` 類別的私用。 如需進一步資訊，請參閱[Google 的 UriMatcher 檔](xref:Android.Content.UriMatcher)。

## <a name="implement-the-querymethod"></a>執行 QueryMethod

要執行的最簡單 `ContentProvider` 方法是 `Query` 方法。 下列的執行會使用 `UriMatcher` 來剖析 `uri` 參數，並呼叫正確的資料庫方法。 如果 `uri` 包含 ID 參數，則會剖析整數（使用 `LastPathSegment`），並在資料庫查詢中使用。

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

也必須覆寫 `GetType` 方法。 您可以呼叫這個方法，以判斷將針對指定的 Uri 傳回的內容類型。
這可能會告訴取用應用程式如何處理該資料。

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

我們的簡單範例不允許編輯或刪除資料，但必須實作為 Insert、Update 和 Delete 方法，以便在沒有執行的情況下加以新增：

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

這會完成基本 `ContentProvider` 的執行。 一旦安裝應用程式之後，它所公開的資料就可以在應用程式內使用，也可用於任何其他知道 Uri 來參考它的應用程式。

## <a name="access-the-contentprovider"></a>存取 ContentProvider

一旦執行 `VegetableProvider` 之後，存取它的方式會與本檔開頭的連絡人提供者相同：使用指定的 Uri 取得游標，然後使用介面卡來存取資料。

## <a name="bind-a-listview-to-a-contentprovider"></a>將 ListView 系結至 ContentProvider

若要在 `ListView` 中填入資料，我們會使用對應于未篩選之蔬菜清單的 Uri。 在程式碼中，我們會使用常數值 `VegetableProvider.CONTENT_URI`，我們知道它會解析成 `com.xamarin.sample.vegetableprovider/vegetables`。 我們的 `VegetableProvider.Query` 的執行會傳回可接著系結至 `ListView`的資料指標。

`SimpleContentProvider/HomeScreen.cs` 中的程式碼顯示從 `ContentProvider`顯示資料有多簡單：

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

[![應用程式的螢幕擷取畫面，列出蔬菜、水果、花卉 Buds、Legumes、燈泡、Tubers](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)

## <a name="retrieve-a-single-item-from-a-contentprovider"></a>從 ContentProvider 中取出單一專案

取用應用程式可能也會想要存取單一資料列，這可以藉由建立參考特定資料列的不同 Uri 來完成（例如）。

藉由建立具有所需 `Id`的 Uri，直接使用 `ContentResolver` 來存取單一專案。

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

- [SimpleContentProvider （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
