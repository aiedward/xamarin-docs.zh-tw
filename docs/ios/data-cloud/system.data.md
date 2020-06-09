---
title: System.servicemodel 中的 system.object 資料
description: 本檔說明如何使用 system.string 和 Mono 來存取 Xamarin iOS 應用程式中的 SQLite 資料。
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: 736d70aebcf861b5557d5f076a42ff0a3dcfc043
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569945"
---
# <a name="systemdata-in-xamarinios"></a>System.servicemodel 中的 system.object 資料

Xamarin. iOS 8.10 新增了對[system.string 的支援，包括](xref:System.Data) `Mono.Data.Sqlite.dll` ADO.NET 提供者。 支援包括新增下列[元件](~/cross-platform/internals/available-assemblies.md)：

- `System.Data.dll`
- `System.Data.Service.Client.dll`
- `System.Transactions.dll`
- `Mono.Data.Tds.dll`
- `Mono.Data.Sqlite.dll`

<a name="Example"></a>

## <a name="example"></a>範例

下列程式會在中建立資料庫 `Documents/mydb.db3` ，如果資料庫先前不存在，則會填入範例資料。 接著會查詢資料庫，並將輸出寫入 `stderr` 。

### <a name="add-references"></a>新增參考

首先，以滑鼠右鍵按一下 [**參考**] 節點，然後選擇 [**編輯參考 ...** ]，然後選取 `System.Data` 和 `Mono.Data.Sqlite` ：

[![](system.data-images/edit-references-sml.png "Adding new references")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>範例程式碼

下列程式碼顯示使用內嵌 SQL 命令來建立資料表及插入資料列的簡單範例：

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
> 如上述程式碼範例中所述，在 SQL 命令中內嵌字串是不好的作法，因為它可讓您的程式碼容易受到[sql 插入](https://en.wikipedia.org/wiki/SQL_injection)式攻擊。

### <a name="using-command-parameters"></a>使用命令參數

下列程式碼示範如何使用命令參數，將使用者輸入的文字安全地插入資料庫中（即使文字包含特殊的 SQL 字元，例如單引號）：

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

<a name="Missing_Functionality"></a>

## <a name="missing-functionality"></a>缺少的功能

**System.web**和**Mono**都遺漏一些功能。

<a name="System.Data"></a>

### <a name="systemdata"></a>System.Data

**System.web**中遺漏的功能包含：

- 任何需要[system.object 的](xref:System.CodeDom)專案（例如 [TypedDataSetGenerator](xref:System.Data.TypedDataSetGenerator) ）
- XML 設定檔案支援（例如 [System.web. DbProviderConfigurationHandler](xref:System.Data.Common.DbProviderConfigurationHandler) ）
- [DbProviderFactories](xref:System.Data.Common.DbProviderFactories) （取決於 XML 設定檔案支援）
- [System.Data.OleDb](xref:System.Data.OleDb)
- [System.Data.Odbc](xref:System.Data.Odbc)
- 相依性 `System.EnterpriseServices.dll` 已從中*移除* `System.Data.dll` ，因此會移除[SqlConnection. EnlistDistributedTransaction （ITransaction）](xref:System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction*)方法。

<a name="Mono.Data.Sqlite"></a>

### <a name="monodatasqlite"></a>Mono. Data. Sqlite

同時， **Mono. Data. Sqlite**不會變更任何原始程式碼，而是會因為系結 Sqlite 3.5，而改為裝載一些*運行*時間問題 `Mono.Data.Sqlite.dll` 。 同時，iOS 8 隨附于 SQLite 3.8.5。 就夠了，這兩個版本之間有一些改變。

較舊版本的 iOS 隨附下列版本的 SQLite：

- **iOS 7** -版本3.7.13。
- **iOS 6** -版本3.7.13。
- **iOS 5** -版本3.7.7。
- **iOS 4** -版本3.6.22。

最常見的問題似乎與資料庫架構查詢有關，例如，在執行時間判斷特定資料表上有哪些資料行，例如（覆 `Mono.Data.Sqlite.SqliteConnection.GetSchema` 寫[DbConnection. GetSchema](xref:System.Data.Common.DbConnection.GetSchema)和 `Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable` （覆寫[DbDataReader. GetSchemaTable](xref:System.Data.Common.DbDataReader.GetSchemaTable)。 簡單地說，使用[DataTable](xref:System.Data.DataTable)的任何專案都不太可能會有作用。

<a name="Data_Binding"></a>

## <a name="data-binding"></a>資料繫結

目前不支援資料系結。
