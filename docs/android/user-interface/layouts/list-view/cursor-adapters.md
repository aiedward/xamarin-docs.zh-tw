---
title: 使用 CursorAdapters
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/25/2017
ms.openlocfilehash: d0b5845036ab2981a4aa06d2a01ed6b13d094bef
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028917"
---
# <a name="using-cursoradapters-with-xamarinandroid"></a>使用 CursorAdapters 搭配 Xamarin. Android

Android 會特別提供介面卡類別，以顯示 SQLite 資料庫查詢的資料：

 **SimpleCursorAdapter** –類似于 `ArrayAdapter`，因為它可以在不具子類別化的情況下使用。 只需在函式中提供必要的參數（例如資料指標和配置資訊），然後指派給 `ListView`。

 **CursorAdapter** –當您需要更充分掌控資料值與版面配置控制項（例如隱藏/顯示控制項或變更其屬性）的系結時，您可以繼承自的基類。

資料指標介面卡可讓您以高效能的方式，來流覽儲存于 SQLite 中的長清單資料。 取用程式碼必須在 `Cursor` 物件中定義 SQL 查詢，然後描述如何建立並填入每個資料列的視圖。

## <a name="creating-an-sqlite-database"></a>建立 SQLite 資料庫

若要示範游標介面卡，需要簡單的 SQLite 資料庫執行。 **SimpleCursorTableAdapter/VegetableDatabase**中的程式碼包含用來建立資料表的程式碼和 SQL，並在其中填入一些資料。
完整的 `VegetableDatabase` 類別如下所示：

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

`VegetableDatabase` 類別將會在 `HomeScreen` 活動的 `OnCreate` 方法中具現化。 `SQLiteOpenHelper` 基類會管理資料庫檔案的設定，並確保其 `OnCreate` 方法中的 SQL 只會執行一次。 這個類別用於 `SimpleCursorAdapter` 和 `CursorAdapter`的下列兩個範例中。

資料指標查詢*必須*有整數資料行 `_id`，`CursorAdapter` 才能正常執行。 如果基礎資料表沒有名為 `_id` 的整數資料行，則在組成資料指標的 `RawQuery` 中，使用另一個唯一整數的資料行別名。 如需詳細資訊，請參閱[Android](xref:Android.Widget.CursorAdapter)檔。

### <a name="creating-the-cursor"></a>建立資料指標

這些範例會使用 `RawQuery`，將 SQL 查詢轉換成 `Cursor` 物件。 從游標處傳回的資料行清單，會定義可在游標介面卡中顯示的資料行。 在**SimpleCursorTableAdapter/HomeScreen** `OnCreate` 方法中建立資料庫的程式碼如下所示：

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

任何呼叫 `StartManagingCursor` 的程式碼也應該呼叫 `StopManagingCursor`。 這些範例會使用 `OnCreate` 來啟動，並 `OnDestroy` 來關閉資料指標。 `OnDestroy` 的方法包含此程式碼：

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

一旦應用程式有可用的 SQLite 資料庫，並已建立如下所示的資料指標物件，它就可以利用 `SimpleCursorAdapter` 或 `CusorAdapter` 的子類別，在 `ListView`中顯示資料列。

## <a name="using-simplecursoradapter"></a>使用 SimpleCursorAdapter

`SimpleCursorAdapter` 類似 `ArrayAdapter`，但專門用來搭配 SQLite 使用。 它不需要子類別化-只要在建立物件時設定一些簡單的參數，然後將它指派給 `ListView`的 `Adapter` 屬性即可。

SimpleCursorAdapter 函數的參數如下：

 **CoNtext** –包含活動的參考。

 **版面**配置-要使用之資料列視圖的資源識別碼。

 **ICursor** –包含要顯示之資料的 SQLite 查詢的游標。

 **從**字串陣列–對應于資料指標中資料行名稱的字串陣列。

 **至**整數陣列–對應至資料列配置中控制項的版面配置識別碼陣列。 `from` 陣列中所指定的資料行值將會系結到在相同索引的這個陣列中指定的 ControlID。

`from` 和 `to` 陣列必須有相同數目的專案，因為它們會形成從資料來源到視圖中版面配置控制項的對應。

**SimpleCursorTableAdapter/HomeScreen .cs**範例程式碼會 `SimpleCursorAdapter`，如下所示：

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` 是在 `ListView`中顯示 SQLite 資料快速又簡單的方式。 主要的限制是它只能系結資料行值來顯示控制項，而不允許您變更資料列配置的其他層面（例如，顯示/隱藏控制項或變更屬性）。

## <a name="subclassing-cursoradapter"></a>子類別化 CursorAdapter

`CursorAdapter` 子類別與從 SQLite 顯示資料的 `SimpleCursorAdapter` 具有相同的效能優勢，但它也可讓您完整控制每個資料列視圖的建立和配置。 `CursorAdapter` 的執行與子類別化 `BaseAdapter` 非常不同，因為它不會覆寫 `GetView`、`GetItemId`、`Count` 或 `this[]` 索引子。

假設有一個可運作的 SQLite 資料庫，您只需要覆寫兩個方法來建立 `CursorAdapter` 子類別：

- **BindView** –如果有一個視圖，請更新它，以在提供的資料指標中顯示資料。

- **NewView** –當 `ListView` 需要顯示新的視圖時呼叫。 `CursorAdapter` 會負責回收的查看（與一般介面卡上的 `GetView` 方法不同）。

先前範例中的介面卡子類別有方法可以傳回資料列數目，並取得目前的專案– `CursorAdapter` 不需要這些方法，因為該資訊可以從資料指標本身以往累積所有。 藉由將每個視圖的建立和擴展分割成這兩個方法，`CursorAdapter` 會強制使用 view 重複使用。 這與一般介面卡相反，可以忽略 `BaseAdapter.GetView` 方法的 `convertView` 參數。

### <a name="implementing-the-cursoradapter"></a>執行 CursorAdapter

**CursorTableAdapter/HomeScreenCursorAdapter**中的程式碼包含 `CursorAdapter` 子類別。 它會儲存傳遞至此函式的內容參考，讓它可以存取 `NewView` 方法中的 `LayoutInflater`。 完整的類別看起來像這樣：

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

### <a name="assigning-the-cursoradapter"></a>指派 CursorAdapter

在將顯示 `ListView`的 `Activity` 中，建立游標並 `CursorAdapter`，然後將它指派給清單視圖。

在**CursorTableAdapter/HomeScreen** `OnCreate` 方法中執行此動作的程式碼如下所示：

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

`OnDestroy` 方法包含先前所述的 `StopManagingCursor` 方法呼叫。

## <a name="related-links"></a>相關連結

- [SimpleCursorTableAdapter （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/simplecursortableadapter)
- [CursorTableAdapter （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/cursortableadapter)
