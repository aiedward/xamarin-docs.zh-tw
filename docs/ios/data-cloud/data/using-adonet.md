---
title: 使用 ADO.NET 搭配 Xamarin. iOS
description: 本檔說明如何使用 ADO.NET 做為在 Xamarin iOS 應用程式中存取 SQLite 的方法。 其中討論群組件參考、Mono. Sqlite 和 BasicDataAccess 範例。
ms.prod: xamarin
ms.assetid: 79078A4D-2D24-44F3-9543-B50418A7A000
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: d5830fcc4eab2feb5002253a519d72099d6bcdde
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86929983"
---
# <a name="using-adonet-with-xamarinios"></a>使用 ADO.NET 搭配 Xamarin. iOS

Xamarin 具有可在 iOS 上取得之 SQLite 資料庫的內建支援，並使用類似 ADO.NET 的語法來公開。 使用這些 Api 需要您撰寫由 SQLite 處理的 SQL 語句，例如 `CREATE TABLE` 、 `INSERT` 和 `SELECT` 語句。

## <a name="assembly-references"></a>組件參考

若要透過 ADO.NET 使用存取 SQLite，您必須新增 `System.Data` 並 `Mono.Data.Sqlite` 參考您的 iOS 專案，如下所示（如 Visual Studio for Mac 和 Visual Studio 中的範例）：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

 ![Visual Studio for Mac 中的元件參考](using-adonet-images/image4.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

  ![Visual Studio 中的元件參考](using-adonet-images/image6.png)

-----

以滑鼠右鍵按一下 [**參考] > [編輯參考 ...** ]，然後按一下以選取必要的元件。

## <a name="about-monodatasqlite"></a>關於 Mono

我們會使用 `Mono.Data.Sqlite.SqliteConnection` 類別來建立空白的資料庫檔案，然後再具現化 `SqliteCommand` 物件，讓我們可用來對資料庫執行 SQL 指示。

1. **建立空白資料庫**- `CreateFile` 使用有效的（可寫入的）檔案路徑來呼叫方法。 在呼叫這個方法之前，您應該先檢查檔案是否已經存在，否則將會在舊檔案的頂端建立新的（空白）資料庫，而舊檔案中的資料將會遺失：

    `Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`

    > [!NOTE]
    > `dbPath`應該根據本檔稍早所討論的規則來判斷變數。

2. **建立資料庫連接**-建立 SQLite 資料庫檔案之後，您可以建立連線物件來存取資料。 連接是使用格式為的連接字串所建立 `Data Source=file_path` ，如下所示：

    ```csharp
    var connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open();
    // do stuff
    connection.Close();
    ```

    如先前所述，不應在不同的執行緒之間重複使用連接。 如果有疑問，請視需要建立連線，並在完成時將它關閉;但要注意的是，這項作業的執行頻率高於需求。

3. **建立和執行資料庫命令**-一旦有了連接，我們就可以對它執行任意 SQL 命令。 下列程式碼顯示正在執行的 CREATE TABLE 語句。

    ```csharp
    using (var command = connection.CreateCommand ()) {
        command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
        var rowcount = command.ExecuteNonQuery ();
    }
    ```

直接針對資料庫執行 SQL 時，您應該採取一般預防措施，不要提出不正確要求，例如嘗試建立已經存在的資料表。 追蹤資料庫的結構，讓您不會造成 SqliteException，例如「SQLite 錯誤資料表 [專案] 已經存在」。

## <a name="basic-data-access"></a>基本資料存取

在 iOS 上執行時，此檔的*DataAccess_Basic*範例程式碼看起來像這樣：

 ![iOS ADO.NET 範例](using-adonet-images/image9.png)

下列程式碼說明如何執行簡單的 SQLite 作業，並在應用程式的主視窗中將結果顯示為文字。

您必須包含下列命名空間：

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

下列程式碼範例顯示整個資料庫互動：

1. 建立資料庫檔案
2. 插入一些資料
3. 查詢資料

這些作業通常會出現在整個程式碼的多個位置，例如，您可以在應用程式第一次啟動時建立資料庫檔案和資料表，並在應用程式的個別畫面中執行資料讀取和寫入。 在下列範例中，已將此範例中的單一方法分組：

```csharp
public static SqliteConnection connection;
public static string DoSomeDataAccess ()
{
    // determine the path for the database file
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "adodemo.db3");

    bool exists = File.Exists (dbPath);

    if (!exists) {
        Console.WriteLine("Creating database");
        // Need to create the database before seeding it with some data
        Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);
        connection = new SqliteConnection ("Data Source=" + dbPath);

        var commands = new[] {
            "CREATE TABLE [Items] (_id ntext, Symbol ntext);",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'AAPL')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('2', 'GOOG')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('3', 'MSFT')"
        };
        // Open the database connection and create table with data
        connection.Open ();
        foreach (var command in commands) {
            using (var c = connection.CreateCommand ()) {
                c.CommandText = command;
                var rowcount = c.ExecuteNonQuery ();
                Console.WriteLine("\tExecuted " + command);
            }
        }
    } else {
        Console.WriteLine("Database already exists");
        // Open connection to existing database file
        connection = new SqliteConnection ("Data Source=" + dbPath);
        connection.Open ();
    }

    // query the database to prove data was inserted!
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT [_id], [Symbol] from [Items]";
        var r = contents.ExecuteReader ();
        Console.WriteLine("Reading data");
        while (r.Read ())
            Console.WriteLine("\tKey={0}; Value={1}",
                              r ["_id"].ToString (),
                              r ["Symbol"].ToString ());
    }
    connection.Close ();
}
```

## <a name="more-complex-queries"></a>更複雜的查詢

因為 SQLite 允許對資料執行任意的 SQL 命令，所以您可以執行您想要的任何 CREATE、INSERT、UPDATE、DELETE 或 SELECT 語句。 您可以在 Sqlite 網站上閱讀 SQLite 所支援的 SQL 命令。 SQL 語句是在 SqliteCommand 物件上使用三種方法的其中一種來執行：

- **ExecuteNonQuery** –通常用於建立資料表或插入資料。 某些作業的傳回值是受影響的資料列數目，否則為-1。
- **ExecuteReader** –當資料列集合應當做傳回時使用 `SqlDataReader` 。
- **ExecuteScalar** –抓取單一值（例如匯總）。

### <a name="executenonquery"></a>EXECUTENONQUERY

INSERT、UPDATE 和 DELETE 子句將會傳回受影響的資料列數目。 所有其他 SQL 語句都會傳回-1。

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

下列方法會顯示 SELECT 語句中的 WHERE 子句。 因為程式碼會製作完整的 SQL 語句，所以必須小心將保留的字元（例如字串前後的引號（'））轉義。

```csharp
public static string MoreComplexQuery ()
{
    var output = "";
    output += "\nComplex query example: ";
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal), "ormdemo.db3");

    connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open ();
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT * FROM [Items] WHERE Symbol = 'MSFT'";
        var r = contents.ExecuteReader ();
        output += "\nReading data";
        while (r.Read ())
            output += String.Format ("\n\tKey={0}; Value={1}",
                    r ["_id"].ToString (),
                    r ["Symbol"].ToString ());
    }
    connection.Close ();

    return output;
}
```

ExecuteReader 方法會傳回 SqliteDataReader 物件。 除了範例中所示的 Read 方法以外，其他實用的屬性包括：

- **RowsAffected** –受查詢影響的資料列計數。
- **HasRows** –是否傳回任何資料列。

### <a name="executescalar"></a>EXECUTESCALAR

這適用于傳回單一值的 SELECT 語句（例如匯總）。

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

`ExecuteScalar`方法的傳回型別是 `object` –您應該根據資料庫查詢來轉換結果。 結果可能是來自計數查詢的整數，或是來自單一資料行 SELECT 查詢的字串。 請注意，這不同于傳回 reader 物件的其他 Execute 方法，或受影響的資料列數目計數。

## <a name="microsoftdatasqlite"></a>Microsoft.Data.Sqlite

還有另一個程式庫 `Microsoft.Data.Sqlite` ，可以[從 NuGet 安裝](https://www.nuget.org/packages/Microsoft.Data.Sqlite)，它在功能上相當於， `Mono.Data.Sqlite` 並允許相同類型的查詢。

[這兩個程式庫](https://docs.microsoft.com/dotnet/standard/data/sqlite/compare)和一些[Xamarin 特定的詳細資料](https://docs.microsoft.com/dotnet/standard/data/sqlite/xamarin)有比較。 最重要的是，對於 Xamarin iOS 應用程式，您必須包含初始化呼叫：

```csharp
// required for Xamarin.iOS
SQLitePCL.Batteries_V2.Init();
```

## <a name="related-links"></a>相關連結

- [DataAccess 基本（範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin. 表單資料存取](~/xamarin-forms/data-cloud/data/databases.md)
