---
title: 使用 SQLite.NET 搭配 Xamarin. iOS
description: SQLite.NET PCL NuGet 程式庫提供適用于 Xamarin iOS 應用程式的簡單資料存取機制。 本檔提供如何使用此程式庫的總覽。
ms.prod: xamarin
ms.assetid: 79813B09-42D7-47DD-AE71-A605E6B9EF24
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/18/2018
ms.openlocfilehash: fb0981fea906a474d39834a52f0a8bfdf496ca1e
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488409"
---
# <a name="using-sqlitenet-with-xamarinios"></a>使用 SQLite.NET 搭配 Xamarin. iOS

Xamarin 建議的 SQLite.NET 程式庫是基本的 ORM，可讓您在 iOS 裝置上儲存和抓取本機 SQLite 資料庫中的物件。
ORM 代表物件關聯式對應–此 API 可讓您從資料庫儲存和抓取「物件」，而不需要撰寫 SQL 語句。

<a name="Usage"/>

## <a name="usage"></a>使用

若要在 Xamarin 應用程式中包含 SQLite.NET 程式庫，請將下列 NuGet 套件新增至您的專案：

- **封裝名稱：** sqlite-net-pcl
- **作者：** Frank Krueger
- **識別碼：** sqlite-net-pcl
- **Url：** [nuget.org/packages/sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

[![SQLite.NET NuGet 套件](using-sqlite-orm-images/image1a-sml.png "SQLite.NET NuGet 套件")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> 有許多不同的 SQLite 套件可供使用，請務必選擇正確的，這可能不是搜尋中的最上層結果。

當您有可用的 SQLite.NET 程式庫之後，請遵循下列三個步驟來使用它來存取資料庫：

1. **新增 using 語句**-將下列語句新增至需要資料C#存取的檔案：

    ```csharp
    using SQLite;
    ```

1. **建立空白資料庫**-資料庫參考可以藉由將檔案路徑傳遞至 SQLiteConnection 類別的函式來建立。 您不需要檢查檔案是否已經存在–它會在必要時自動建立，否則將會開啟現有的資料庫檔案。

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

    DbPath 變數應根據本檔稍早所討論的規則來決定。

1. **儲存資料**-一旦您建立了 SQLiteConnection 物件，就可以呼叫其方法來執行資料庫命令，例如 CreateTable 和 Insert，如下所示：

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

1. **取出資料**-若要取出物件（或物件清單），請使用下列語法：

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>基本資料存取範例

在 iOS 上執行時，此檔的*DataAccess_Basic*範例程式碼看起來像這樣。 此程式碼說明如何執行簡單的 SQLite.NET 作業，並在應用程式的主視窗中將結果顯示為文字。

**iOS**

 [![iOS SQLite.NET 範例](using-sqlite-orm-images/image2-sml.png)](using-sqlite-orm-images/image2-sml.png#lightbox)

下列程式碼範例顯示使用 SQLite.NET 程式庫來封裝基礎資料庫存取的整個資料庫互動。 它會顯示：

1. 建立資料庫檔案
1. 藉由建立物件並加以儲存來插入一些資料
1. 查詢資料

您必須包含下列命名空間：

```csharp
using SQLite; // from the github SQLite.cs class
```

這需要您將 SQLite 新增至您的專案，如[這裡](#Usage)所強調。 請注意，SQLite 資料庫資料表的定義方式是將屬性新增至類別（`Stock` 類別），而不是 CREATE TABLE 命令。

```csharp
[Table("Items")]
public class Stock {
    [PrimaryKey, AutoIncrement, Column("_id")]
    public int Id { get; set; }
    [MaxLength(8)]
    public string Symbol { get; set; }
}
public static void DoSomeDataAccess () {
       Console.WriteLine ("Creating database, if it doesn't already exist");
   string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "ormdemo.db3");
   var db = new SQLiteConnection (dbPath);
   db.CreateTable<Stock> ();
   if (db.Table<Stock> ().Count() == 0) {
        // only insert the data if it doesn't already exist
        var newStock = new Stock ();
        newStock.Symbol = "AAPL";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "GOOG";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "MSFT";
        db.Insert (newStock);
    }
    Console.WriteLine("Reading data");
    var table = db.Table<Stock> ();
    foreach (var s in table) {
        Console.WriteLine (s.Id + " " + s.Symbol);
    }
}
```

使用 `[Table]` 屬性而不指定資料表名稱參數，會導致基礎資料庫資料表具有與類別相同的名稱（在此案例中為「庫存」）。 如果您直接針對資料庫撰寫 SQL 查詢，而不是使用 ORM 資料存取方法，則實際的資料表名稱很重要。 同樣地，`[Column("_id")]` 屬性是選擇性的，而且如果不存在，就會將資料行加入至與類別中的屬性同名的資料表。

## <a name="sqlite-attributes"></a>SQLite 屬性

您可以套用至類別的一般屬性，以控制如何將它們儲存在基礎資料庫中，包括：

- **[PrimaryKey]** –此屬性可以套用至整數屬性，以強制它成為基礎資料表的主鍵。 不支援複合主鍵。
- **[自動遞增]** –此屬性會針對每個插入資料庫的新物件，使整數屬性的值成為自動遞增
- **[Column （name）]** &ndash; `name` 參數會設定基礎資料庫資料行的名稱。
- **[資料表（名稱）]** –將類別標示為能夠儲存在具有指定名稱的基礎 SQLite 資料表中。
- **[MaxLength （值）]** –在嘗試資料庫插入時，限制 text 屬性的長度。 使用程式碼應該在插入物件之前先驗證這個屬性，因為在嘗試執行資料庫插入或更新作業時，這個屬性只會「核取」。
- **[忽略]** –讓 SQLite.NET 忽略此屬性。 這對於類型不能儲存在資料庫中的屬性而言特別有用，或是無法自動解析為 SQLite 的模型集合的屬性。
- **[Unique]** –確保基礎資料庫資料行中的值是唯一的。

這些屬性大部分都是選擇性的。 您應該一律指定整數主鍵，以便在您的資料上有效率地執行選取和刪除查詢。

## <a name="more-complex-queries"></a>更複雜的查詢

`SQLiteConnection` 上的下列方法可以用來執行其他資料作業：

- **Insert** –將新的物件加入至資料庫。
- **取得\<t >** -嘗試使用主鍵來抓取物件。
- **資料表\<t >** –傳回資料表中的所有物件。
- **Delete** –使用其主要索引鍵刪除物件。
- **查詢\<t >** -執行會傳回多個資料列的 SQL 查詢（做為物件）。
- **執行**–當您不想要從 SQL 傳回的資料列（例如 INSERT、UPDATE 和 DELETE 指示）時，請使用這個方法（而不是 `Query`）。

### <a name="getting-an-object-by-the-primary-key"></a>依主要金鑰取得物件

SQLite.Net 提供 Get 方法，以根據其主要索引鍵來抓取單一物件。

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>使用 Linq 選取物件

傳回集合的方法支援 IEnumerable\<T >，因此您可以使用 Linq 來查詢或排序資料表的內容。 下列程式碼顯示使用 Linq 篩選出以字母 "A" 開頭之所有專案的範例：

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>使用 SQL 選取物件

雖然 SQLite.Net 可以為您的資料提供以物件為基礎的存取權，但有時候您可能需要執行比 Linq 允許更複雜的查詢（或者您可能需要更快的效能）。 您可以使用 SQL 命令搭配查詢方法，如下所示：

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!IMPORTANT]
> 直接撰寫 SQL 語句時，您會針對資料庫中資料表和資料行的名稱建立相依性，這些是您的類別及其屬性所產生的。 如果您在程式碼中變更這些名稱，您必須記得更新任何手動撰寫的 SQL 語句。

### <a name="deleting-an-object"></a>刪除物件

主要金鑰用來刪除資料列，如下所示：

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

您可以檢查 `rowcount` 來確認受影響的資料列數目（在此案例中為 deleted）。

## <a name="using-sqlitenet-with-multiple-threads"></a>使用具有多個執行緒的 SQLite.NET

SQLite 支援三種不同的執行緒模式：*單一執行緒*、*多執行緒*和*序列化*。 如果您想要從多個執行緒存取資料庫，而不會有任何限制，您可以設定 SQLite 使用**序列化**執行緒模式。 請務必及早在應用程式中設定這個模式（例如，在 `OnCreate` 方法的開頭）。

若要變更執行緒模式，請呼叫 `Mono.Data.Sqlite` 命名空間中的 `SqliteConnection.SetConfig`。 例如，這行程式碼會針對**序列化**模式設定 SQLite：

```csharp
using Mono.Data.Sqlite;
...
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

## <a name="related-links"></a>相關連結

- [DataAccess 基本（範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Xamarin. 表單資料存取](~/xamarin-forms/data-cloud/data/databases.md)
