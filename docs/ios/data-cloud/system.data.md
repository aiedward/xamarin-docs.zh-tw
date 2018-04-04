---
title: System.Data
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b141dfac49e2cfa2dc80b7c0e4ca3a93968590a6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="systemdata"></a>System.Data

Xamarin.iOS 8.10 新增支援[System.Data](https://developer.xamarin.com/api/namespace/System.Data/)，包括`Mono.Data.Sqlite.dll`ADO.NET 提供者。 支援包括下列新增[組件](~/cross-platform/internals/available-assemblies.md):

-  `System.Data.dll`
-  `System.Data.Service.Client.dll`
-  `System.Transactions.dll`
-  `Mono.Data.Tds.dll`
-  `Mono.Data.Sqlite.dll`


<a name="Example" />

## <a name="example"></a>範例

下列程式會建立在資料庫`Documents/mydb.db3`，如果資料庫先前沒有它，並會填入範例資料。 然後查詢資料庫，並將輸出寫入`stderr`。

### <a name="add-references"></a>加入參考

首先，以滑鼠右鍵按一下**參考**節點，然後選擇 **編輯參考...**然後選取`System.Data`和`Mono.Data.Sqlite`:

[![](system.data-images/edit-references-sml.png "加入新的參考")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>程式碼範例

下列程式碼顯示建立資料表並使用內嵌的 SQL 命令插入資料列的簡單範例：

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;

class Demo {
    static void Main (string [] args)
    {
        var connection = GetConnection ();
        using (var cmd = connection.CreateCommand ()) {
            connection.Open ();
            cmd.CommandText = "SELECT * FROM People";
            using (var reader = cmd.ExecuteReader ()) {
                while (reader.Read ()) {
                    Console.Error.Write ("(Row ");
                    Write (reader, 0);
                    for (nint i = 1; i < reader.FieldCount; ++i) {
                        Console.Error.Write(" ");
                        Write (reader, i);
                    }
                    Console.Error.WriteLine(")");
                }
            }
            connection.Close ();
        }
    }

    static SqliteConnection GetConnection()
    {
        var documents = Environment.GetFolderPath (
                Environment.SpecialFolder.Personal);
        string db = Path.Combine (documents, "mydb.db3");
        bool exists = File.Exists (db);
        if (!exists)
            SqliteConnection.CreateFile (db);
        var conn = new SqliteConnection("Data Source=" + db);
        if (!exists) {
            var commands = new[] {
            "CREATE TABLE People (PersonID INTEGER NOT NULL, FirstName ntext, LastName ntext)",
            // WARNING: never insert user-entered data with embedded parameter values
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (1, 'First', 'Last')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (2, 'Dewey', 'Cheatem')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (3, 'And', 'How')",
            };
            conn.Open ();
            foreach (var cmd in commands) {
                using (var c = conn.CreateCommand()) {
                    c.CommandText = cmd;
                    c.CommandType = CommandType.Text;
                    c.ExecuteNonQuery ();
                }
            }
            conn.Close ();
        }
        return conn;
    }

    static void Write(SqliteDataReader reader, int index)
    {
        Console.Error.Write("({0} '{1}')",
                reader.GetName(index),
                reader [index]);
    }
}
```

> [!IMPORTANT]
> 如上述程式碼範例中所述，SQL 命令中內嵌字串，因為它會讓您的程式碼容易遭到不正確的作法是[SQL 資料隱碼](http://en.wikipedia.org/wiki/SQL_injection)。


### <a name="using-command-parameters"></a>使用命令參數

下列程式碼會示範如何使用命令參數，將使用者輸入的文字安全地插入資料庫 （即使文字包含單一所有格符號之類的特殊 SQL 字元）：

```csharp
// user input from Textbox control
var fname = fnameTextbox.Text;
var lname = lnameTextbox.Text;
// use command parameters to safely insert into database
using (var addCmd = conn.CreateCommand ()) {
    addCmd.CommandText = "INSERT INTO [People] (PersonID, FirstName, LastName) VALUES (@COL1, @COL2, @COL3)";
    addCmd.CommandType = System.Data.CommandType.Text;
    addCmd.AddParameterWithValue ("@COL1", 1);
    addCmd.AddParameterWithValue ("@COL2", fname);
    addCmd.AddParameterWithValue ("@COL3", lname);
    addCmd.ExecuteNonQuery ();
}
```

<a name="Missing_Functionality" />

## <a name="missing-functionality"></a>遺漏的功能

同時**System.Data**和**Mono.Data.Sqlite**遺失某些功能。

<a name="System.Data" />

### <a name="systemdata"></a>System.Data

功能中遺漏**System.Data.dll**所組成：

-  任何項目需要[System.CodeDom](https://developer.xamarin.com/api/namespace/System.CodeDom/) （例如： [System.Data.TypedDataSetGenerator](https://developer.xamarin.com/api/type/System.Data.TypedDataSetGenerator/) )
-  XML 設定檔支援 （例如： [System.Data.Common.DbProviderConfigurationHandler](https://developer.xamarin.com/api/type/System.Data.Common.DbProviderConfigurationHandler/) )
-   [System.Data.Common.DbProviderFactories](https://developer.xamarin.com/api/type/System.Data.Common.DbProviderFactories/) （取決於 XML 組態檔支援）
-   [System.Data.OleDb](https://developer.xamarin.com/api/namespace/System.Data.OleDb/)
-   [System.Data.Odbc](https://developer.xamarin.com/api/namespace/System.Data.Odbc/)
-  `System.EnterpriseServices.dll`的相依性為*移除*從`System.Data.dll`，並產生移除[SqlConnection.EnlistDistributedTransaction(ITransaction)](https://developer.xamarin.com/api/member/System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction/(System.EnterpriseServices.ITransaction))方法。


<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono.Data.Sqlite

同時， **Mono.Data.Sqlite.dll**不發生任何來源的程式碼變更時，但可能會改為主機的數目*執行階段*發出自`Mono.Data.Sqlite.dll`SQLite 3.5 會繫結。 iOS 8，同時，隨附於 SQLite 3.8.5。 部分就說，某些作業的兩個版本之間已經變更的。

較舊 iOS 版本隨附的 SQLite 下列版本：

- **iOS 7** -3.7.13 版本。
- **iOS 6** -3.7.13 版本。
- **iOS 5** -3.7.7 版本。
- **iOS 4** -3.6.22 版本。

最常見的問題會顯示有關資料庫結構描述查詢，例如判斷在執行階段，資料行存在於指定的資料表，例如`Mono.Data.Sqlite.SqliteConnection.GetSchema`(覆寫[DbConnection.GetSchema](https://developer.xamarin.com/api/member/System.Data.Common.DbConnection.GetSchema/)) 和`Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable`(覆寫[DbDataReader.GetSchemaTable](https://developer.xamarin.com/api/member/System.Data.Common.DbDataReader.GetSchemaTable/))。 簡單地說，它看起來也任何項目使用[DataTable](https://developer.xamarin.com/api/type/System.Data.DataTable/)太運作。

<a name="Data_Binding" />

## <a name="data-binding"></a>資料繫結

在此階段不支援資料繫結。

