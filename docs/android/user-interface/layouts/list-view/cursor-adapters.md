---
title: "使用 CursorAdapters"
ms.topic: article
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/25/2017
ms.openlocfilehash: 43d1ef53933ca7867b834dbf118ec730ccbf71ac
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="using-cursoradapters"></a>使用 CursorAdapters

<a name="overview" />

## <a name="overview"></a>總覽

Android 提供特別以顯示來自 SQLite 資料庫查詢資料的配接器類別：

 **SimpleCursorAdapter** -類似至`ArrayAdapter`因為子類別化不使用它。 直接建構函式中提供必要的參數 （例如，資料指標和配置的資訊），並將指派給`ListView`。

 **CursorAdapter** – 您可以繼承自當您需要更充分掌控資料繫結的基底類別值來配置控制項 （例如，隱藏/顯示控制項或變更其內容）。

資料指標的配接器提供高效能用來在捲動 SQLite 中儲存資料的詳細清單。 使用的程式碼必須定義在 SQL 查詢`Cursor`物件，並接著說明如何建立和填入每個資料列的檢視。

<a name="Creating_an_SQLite_Database" />

## <a name="creating-an-sqlite-database"></a>建立 SQLite 資料庫

若要示範游標配接器需要簡單的 SQLite 資料庫實作。 中的程式碼**SimpleCursorTableAdapter/VegetableDatabase.cs**包含的程式碼和 SQL 來建立資料表並填入一些資料。
完整`VegetableDatabase`類別如下所示：

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
   public static readonly string create_table_sql =
       "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
   public static readonly string DatabaseName = "vegetables.db";
   public static readonly int DatabaseVersion = 1;
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
   {   // not required until second version :)
       throw new NotImplementedException();
   }
}
```

`VegetableDatabase`類別會在具現化`OnCreate`方法`HomeScreen`活動。 `SQLiteOpenHelper`基底類別管理資料庫檔案的安裝程式，並可確保在 SQL 其`OnCreate`方法只能執行一次。 這個類別用在下列兩個範例中為`SimpleCursorAdapter`和`CursorAdapter`。

資料指標查詢*必須*有整數資料行`_id`如`CursorAdapter`運作。 如果基礎資料表沒有名為整數資料行`_id`然後使用另一個唯一的整數，在資料行別名`RawQuery`組成資料指標。 請參閱[Android 文件](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)以取得詳細資訊。

<a name="Creating_the_Cursor" />

### <a name="creating-the-cursor"></a>建立資料指標

這些範例使用`RawQuery`開啟 SQL 查詢至`Cursor`物件。 傳回從資料指標的資料行清單會定義可供資料指標配接器中顯示的資料行。 建立資料庫中的程式碼**SimpleCursorTableAdapter/HomeScreen.cs** `OnCreate`方法如下所示：

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

呼叫任何程式碼`StartManagingCursor`也應該呼叫`StopManagingCursor`。 這些範例使用`OnCreate`若要開始，和`OnDestroy`來關閉資料指標。 `OnDestroy`方法包含此程式碼：

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

一旦應用程式有 SQLite 資料庫可用，且已建立的游標物件，如下所示，它可以利用 `SimpleCursorAdapter`或的子類別`CusorAdapter`顯示中的資料列`ListView`。

<a name="Using_SimpleCursorAdapter" />

## <a name="using-simplecursoradapter"></a>使用 SimpleCursorAdapter

`SimpleCursorAdapter` 就像`ArrayAdapter`，但所特製化的 SQLite 搭配使用。 其不需要子類別化 – 建立物件時，只要將一些簡單的參數，然後再將它指派給`ListView`的`Adapter`屬性。

SimpleCursorAdapter 建構函式的參數如下：

 **內容**– 包含的活動的參考。

 **版面配置**– 若要使用的資料列檢視的資源識別碼。

 **ICursor** – 包含 SQLite 查詢以顯示資料的資料指標。

 **從**字串陣列 – 對應至資料指標中的資料行名稱的字串陣列。

 **若要**整數陣列 – 對應至資料列版面配置中控制項的版面配置 Id 的陣列。 在指定的資料行值`from`陣列會繫結至相同的索引在此陣列中指定 ControlID。

`from`和`to`陣列必須有相同數目的項目，因為它們形成版面配置控制項的對應資料來源檢視中。

**SimpleCursorTableAdapter/HomeScreen.cs**向上範例程式碼線路`SimpleCursorAdapter`如下所示：

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` 是既快速又簡單的方式，來顯示 SQLite 資料`ListView`。 主要限制是，它只能繫結控制項顯示的資料行值，不允許您變更資料列配置 （例如，顯示/隱藏控制項或變更屬性） 的其他層面。

<a name="Subclassing_CursorAdapter" />

## <a name="subclassing-cursoradapter"></a>子類別化 CursorAdapter

A`CursorAdapter`子類別具有相同的效能優點，做為`SimpleCursorAdapter`針對顯示資料的 SQLite，但它也可讓您完整控制建立和每個資料列檢視的版面配置。 `CursorAdapter`實作是非常不同的子類別化`BaseAdapter`因為它不會覆寫`GetView`， `GetItemId`，`Count`或`this[]`索引子。

指定可運作的 SQLite 資料庫中，您只需要兩種方法可以建立覆寫`CursorAdapter`子類別：

- **BindView** – 提供檢視，進行更新以顯示提供資料指標中的資料。

- **NewView** – 時呼叫`ListView`需要一個新的檢視來顯示。 `CursorAdapter`會負責的回收檢視 (不同於`GetView`一般配接器上的方法)。

配接器子類別，先前範例中的方法傳回的資料列數目，以及擷取目前的項目 –`CursorAdapter`不需要這些方法，因為該資訊可以證明資料指標本身。 藉由分割的建立和擴展這兩種方法，每個檢視`CursorAdapter`會強制檢視重複使用。 這是相較之下一般配接器就可以略過`convertView`參數`BaseAdapter.GetView`方法。

<a name="Implementing_the_CursorAdapter" />

### <a name="implementing-the-cursoradapter"></a>實作 CursorAdapter

中的程式碼**CursorTableAdapter/HomeScreenCursorAdapter.cs**包含`CursorAdapter`子類別。 它會儲存內容的參考傳遞至建構函式，使其可以存取`LayoutInflater`中`NewView`方法。 完整的類別看起來像這樣：

```csharp
public class HomeScreenCursorAdapter : CursorAdapter {
   Activity context;
   public HomeScreenCursorAdapter(Activity context, ICursor c)
       : base(context, c)
   {
       this.context = context;
   }
   public override void BindView(View view, Context context, ICursor cursor)
   {
       var textView = view.FindViewById<TextView>(Android.Resource.Id.Text1);
       textView.Text = cursor.GetString(1); // 'name' is column 1 in the cursor query
   }
   public override View NewView(Context context, ICursor cursor, ViewGroup parent)
   {
       return this.context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, parent, false);
   }
}
```

<a name="Assigning_the_CursorAdapter" />

### <a name="assigning-the-cursoradapter"></a>指派 CursorAdapter

在`Activity`將顯示`ListView`，建立資料指標和`CursorAdapter`然後將它指派給 [清單] 檢視。

執行此動作的程式碼**CursorTableAdapter/HomeScreen.cs** `OnCreate`方法如下所示：

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

`OnDestroy`方法包含`StopManagingCursor`先前所述的方法呼叫。



## <a name="related-links"></a>相關連結

- [SimpleCursorTableAdapter （範例）](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter （範例）](https://developer.xamarin.com/samples/CursorTableAdapter/)
