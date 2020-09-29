---
title: 使用 CursorAdapters
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/25/2017
ms.openlocfilehash: 81ce3a31a151c8783e09bc42f2dcc2c64168a2e4
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457571"
---
# <a name="using-cursoradapters-with-xamarinandroid"></a>搭配使用 CursorAdapters 與 Xamarin. Android

Android 提供的介面卡類別專門用來顯示 SQLite 資料庫查詢中的資料：

 **SimpleCursorAdapter** –類似于， `ArrayAdapter` 因為它可以在不具子類別的情況下使用。 只要提供必要的參數 (例如，在函式中) 資料指標和配置資訊，然後將指派給 `ListView` 。

 **CursorAdapter** –當您需要更充分掌控資料值與配置控制項的系結時，可以繼承的基類 (例如，隱藏/顯示控制項或變更其屬性) 。

資料指標介面卡可提供高效能的方式，以在 SQLite 中儲存的長清單資料上進行滾動。 取用程式碼必須在物件中定義 SQL 查詢 `Cursor` ，然後說明如何建立和填入每個資料列的視圖。

## <a name="creating-an-sqlite-database"></a>建立 SQLite 資料庫

若要示範資料指標介面卡，需要簡單的 SQLite 資料庫執行。 **SimpleCursorTableAdapter/VegetableDatabase**中的程式碼包含程式碼和 SQL 以建立資料表，並填入一些資料。
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

此 `VegetableDatabase` 類別將會在活動的方法中具現化 `OnCreate` `HomeScreen` 。 `SQLiteOpenHelper`基類會管理資料庫檔案的設定，並確保其方法中的 SQL `OnCreate` 只會執行一次。 下列兩個範例會使用這個類別 `SimpleCursorAdapter` `CursorAdapter` 。

資料指標查詢 *必須* 有整數資料行， `_id` `CursorAdapter` 才能運作。 如果基礎資料表沒有名為的整數資料行 `_id` ，請在組成資料指標的中，使用另一個唯一整數的資料行別名 `RawQuery` 。 如需詳細資訊，請參閱 [Android](xref:Android.Widget.CursorAdapter) 檔。

### <a name="creating-the-cursor"></a>建立資料指標

這些範例會使用將 `RawQuery` SQL 查詢轉換成 `Cursor` 物件。 從資料指標傳回的資料行清單會定義可在資料指標介面卡中顯示的資料行。 在 **SimpleCursorTableAdapter/HomeScreen .cs**方法中建立資料庫的程式碼 `OnCreate` 如下所示：

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

任何呼叫的程式碼 `StartManagingCursor` 也應該呼叫 `StopManagingCursor` 。 這些範例會用 `OnCreate` 來啟動，以及 `OnDestroy` 關閉資料指標。 `OnDestroy`此方法包含下列程式碼：

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

一旦應用程式有 SQLite 資料庫可供使用，並已建立資料指標物件（如所示），就可以利用的 `SimpleCursorAdapter` 或子類別， `CusorAdapter` 在中顯示資料列 `ListView` 。

## <a name="using-simplecursoradapter"></a>使用 SimpleCursorAdapter

`SimpleCursorAdapter` 類似于 `ArrayAdapter` ，但專門用來搭配 SQLite。 它不需要子類別化–只要在建立物件時設定一些簡單參數，然後將它指派給 `ListView` 的 `Adapter` 屬性即可。

SimpleCursorAdapter 函式的參數如下：

 **CoNtext** –包含活動的參考。

 **版面** 配置-要使用之資料列視圖的資源識別碼。

 **ICursor** –包含要顯示之資料的 SQLite 查詢的資料指標。

 **從** 字串陣列–對應至資料指標中之資料行名稱的字串陣列。

 **至** 整數陣列–對應至資料列配置中控制項的版面配置識別碼陣列。 陣列中指定之資料行的值將會系結 `from` 至位於相同索引的這個陣列中所指定的 ControlID。

`from`和 `to` 陣列必須有相同數目的專案，因為它們會形成從資料來源到視圖中版面配置控制項的對應。

**SimpleCursorTableAdapter/HomeScreen**的範例程式碼會以 `SimpleCursorAdapter` 像這樣：

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` 是在中顯示 SQLite 資料的快速簡單方式 `ListView` 。 主要限制是，它只能將資料行值系結至顯示控制項，而不允許您變更資料列配置的其他方面 (例如，顯示/隱藏控制項或變更) 屬性。

## <a name="subclassing-cursoradapter"></a>子類別化 CursorAdapter

子 `CursorAdapter` 類別具有與 `SimpleCursorAdapter` 從 SQLite 顯示資料相同的效能優點，但也可讓您完整控制每個資料列視圖的建立和配置。 其實作與子類別化 `CursorAdapter` 非常不同 `BaseAdapter` ，因為它不會覆寫 `GetView` 、 `GetItemId` `Count` 或 `this[]` 索引子。

假設有一個可運作的 SQLite 資料庫，您只需要覆寫兩個方法來建立子 `CursorAdapter` 類別：

- **BindView** –有了一個視圖，請加以更新，以顯示所提供資料指標中的資料。

- **NewView** –在 `ListView` 需要新的視圖顯示時呼叫。 `CursorAdapter`會負責回收 (與 `GetView` 一般介面卡) 上的方法不同。

先前範例中的介面卡子類別具有傳回資料列數目的方法，以及抓取目前專案的方法– `CursorAdapter` 因為可以從資料指標本身以往累積該資訊，所以不需要這些方法。 藉由將每個視圖的建立和擴展分割成這兩種方法，就會 `CursorAdapter` 重複使用強制顯示。 這與一般介面卡相較之下，可以忽略 `convertView` 方法的參數 `BaseAdapter.GetView` 。

### <a name="implementing-the-cursoradapter"></a>執行 CursorAdapter

**CursorTableAdapter/HomeScreenCursorAdapter**中的程式碼包含子 `CursorAdapter` 類別。 它會儲存傳遞至函式的內容參考，以便 `LayoutInflater` 在方法中存取 `NewView` 。 完整的類別看起來像這樣：

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

在中， `Activity` 會顯示 `ListView` ，建立資料指標， `CursorAdapter` 然後將它指派給清單視圖。

在 **CursorTableAdapter/HomeScreen .cs**方法中執行此動作的程式碼 `OnCreate` 如下所示：

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

`OnDestroy`方法包含 `StopManagingCursor` 先前所述的方法呼叫。

## <a name="related-links"></a>相關連結

- [SimpleCursorTableAdapter (範例) ](/samples/xamarin/monodroid-samples/simplecursortableadapter)
- [CursorTableAdapter (範例) ](/samples/xamarin/monodroid-samples/cursortableadapter)