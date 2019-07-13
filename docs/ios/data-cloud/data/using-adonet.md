---
title: 與 Xamarin.iOS 中使用 ADO.NET
description: 本文件說明如何使用 ADO.NET 做為方法，來存取 SQLite 中的 Xamarin.iOS 應用程式。 它討論的組件參考、 Mono.Data.Sqlite 和 BasicDataAccess 範例。
ms.prod: xamarin
ms.assetid: 79078A4D-2D24-44F3-9543-B50418A7A000
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 293ccc66395edbe18399717caf632292ff760b0b
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650195"
---
# <a name="using-adonet-with-xamarinios"></a>與 Xamarin.iOS 中使用 ADO.NET

Xamarin 的內建支援可在 iOS，使用熟悉的 ADO.NET 類似的語法所公開之 SQLite 資料庫。 使用這些 Api 會要求您撰寫這類處理的 SQLite，SQL 陳述式`CREATE TABLE`，`INSERT`和`SELECT`陳述式。

## <a name="assembly-references"></a>組件參考

若要使用存取透過 ADO.NET，您必須新增 SQLite`System.Data`和`Mono.Data.Sqlite`參考您的 iOS 專案，如下所示 （適用於 Visual Studio for Mac 和 Visual Studio 中的範例）：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 ![](using-adonet-images/image4.png "Visual Studio for Mac 中的組件參考")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  ![](using-adonet-images/image6.png "Visual Studio 中的組件參考")

-----

以滑鼠右鍵按一下**參考 > 編輯參考...** 然後按一下以選取必要的組件。

## <a name="about-monodatasqlite"></a>關於 Mono.Data.Sqlite

我們將使用`Mono.Data.Sqlite.SqliteConnection`類別來建立空白的資料庫檔案，然後具現化`SqliteCommand`物件，我們可以使用來執行對資料庫的 SQL 指令。


1. **建立空白的資料庫**-呼叫`CreateFile`使用有效的方法 (亦即。 可寫入) 的檔案路徑。 您應該檢查是否檔案已經存在之前呼叫這個方法，否則將舊的 replicaset，之上建立新的 （空白） 資料庫和舊的檔案中的資料將會遺失：

    `Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`

    > [!NOTE]
    > `dbPath`變數應該根據本文件稍早所述的規則決定。

2. **建立資料庫連接**-SQLite 資料庫檔案建立之後，您可以建立連接物件來存取資料。 連接的連接字串，其使用的格式建構`Data Source=file_path`，如下所示：

    ```csharp
    var connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open();
    // do stuff
    connection.Close();
    ```

    如先前所述，連線應該永遠不會是重複使用多個不同的執行緒。 如果有疑問，建立所需的連接，並關閉它，當您完成時，但請注意，執行此超過通常太必要。
    
3. **建立和執行資料庫命令**-一旦我們擁有我們可以執行任意的 SQL 命令，對它的連線。 下列程式碼會顯示正在執行 CREATE TABLE 陳述式。

    ```csharp
    using (var command = connection.CreateCommand ()) {
        command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
        var rowcount = command.ExecuteNonQuery ();
    }
    ```

直接對資料庫執行 SQL 時，您應該採取一般的預防措施，不以提出無效的項目，例如嘗試建立已經存在的資料表。 因此，例如 「 SQLite 錯誤資料表 [項目] 已經存在 」，不會造成 SqliteException，持續追蹤的資料庫的結構。

## <a name="basic-data-access"></a>基本資料存取

*DataAccess_Basic*時在 iOS 上執行這份文件的範例程式碼看起來像這樣：

 ![](using-adonet-images/image9.png "iOS ADO.NET 範例")

下列程式碼說明如何執行簡單的 SQLite 作業，並為應用程式的主視窗中的文字顯示結果。

您必須加入這些命名空間：

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

下列程式碼範例會顯示整個資料庫互動：

1.  建立資料庫檔案
2.  插入一些資料
3.  查詢資料

這些作業會通常會出現在多個位置，在整個程式碼，例如您可能建立的資料庫檔案和資料表，您的應用程式初次啟動時，並在您的應用程式中執行資料讀取和寫入個別的畫面中。 在下列範例中均已分組為此範例中的單一方法：

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

由於 SQLite 允許任意的 SQL 命令，以對資料執行，您可以執行任何建立、 插入、 更新、 刪除或選取您要的陳述式。 您可以閱讀 SQLite 支援 Sqlite 網站上的 SQL 命令。 使用三種方法之一 SqliteCommand 物件上執行的 SQL 陳述式：

-  **ExecuteNonQuery** ： 通常用於資料表建立或資料插入。 某些作業的傳回值會受到影響的資料列數目，否則就是-1。
-  **ExecuteReader** – 使用時應該傳回的資料列的集合，做為`SqlDataReader`。
-  **ExecuteScalar** – 擷取單一值 （例如彙總）。


### <a name="executenonquery"></a>EXECUTENONQUERY

INSERT、 UPDATE 和 DELETE 陳述式會傳回受影響的資料列數目。 所有其他 SQL 陳述式會傳回-1。

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

下列方法會顯示 SELECT 陳述式中 WHERE 子句。 因為程式碼會製作完整的 SQL 陳述式，所以它必須謹慎地逸出保留的字元，例如字串周圍的引號 （'）。

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

ExecuteReader 方法會傳回 SqliteDataReader 物件。 Read 方法的範例所示的情況下，除了其他有用的屬性包括：

-  **RowsAffected** – 查詢所影響的資料列計數。
-  **HasRows** – 是否傳回任何資料列。


### <a name="executescalar"></a>EXECUTESCALAR

您可以使用此傳回單一值 （例如彙總） 的 SELECT 陳述式。

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

`ExecuteScalar`方法的傳回型別是`object`– 您應該根據資料庫查詢結果轉換。 COUNT 查詢的整數或字串，以從單一資料行的 SELECT 查詢，可能是結果。 請注意，這不同於其他 Execute 方法傳回的讀取器物件或受影響的資料列數目的計數。


## <a name="related-links"></a>相關連結

- [DataAccess Basic （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 進階 （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin.Forms 資料存取](~/xamarin-forms/data-cloud/data/databases.md)
