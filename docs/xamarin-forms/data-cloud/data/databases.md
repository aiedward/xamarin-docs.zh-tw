---
title: Xamarin.Forms 本機資料庫
description: Xamarin.Forms 支援使用 SQLite 資料庫引擎的資料庫導向應用程式，讓您可以在共用程式碼中載入和儲存物件。 本文描述 Xamarin.Forms 應用程式如何使用 SQLite.Net 來讀取和寫入資料至本機 SQLite 資料庫。
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 03/01/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a7dd5ea8963fed079c82ac6944d571176002486e
ms.sourcegitcommit: 322e7bcf9fb8c1ad52ab8e929bea95d45e280834
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751440"
---
# <a name="xamarinforms-local-databases"></a>Xamarin.Forms 本機資料庫

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/todo)

SQLite 資料庫引擎可讓 Xamarin.Forms 應用程式在共用程式碼中載入和儲存資料物件。 範例應用程式會使用 SQLite 資料庫資料表來儲存 todo 專案。 本文描述如何在共用程式碼中使用 SQLite.Net，以在本機資料庫中儲存和取出資訊。

[![IOS 和 Android 上 Todolist 應用程式的螢幕擷取畫面](databases-images/todo-list-sml.png)](databases-images/todo-list.png#lightbox "IOS 和 Android 上的 Todolist 應用程式")

遵循下列步驟將 SQLite.NET 整合到行動應用程式：

1. [安裝 NuGet 套件](#install-the-sqlite-nuget-package)。
1. [設定常數](#configure-app-constants)。
1. [建立資料庫存取類別](#create-a-database-access-class)。
1. [存取中的 Xamarin.Forms 資料](#access-data-in-xamarinforms)。
1. [Advanced configuration](#advanced-configuration)。

## <a name="install-the-sqlite-nuget-package"></a>安裝 SQLite NuGet 套件

使用 NuGet 套件管理員來搜尋 **sqlite-net-pcl** ，並將最新版本新增至共用程式碼專案。

有許多名稱類似的 NuGet 套件。 正確的套件有下列屬性：

- **識別碼：** sqlite-net-pcl
- **作者：** SQLite-net
- **擁有者：** praeclarum
- **NuGet 連結：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> 不論套件名稱為何，請使用 **sqlite-net-pcl** NuGet 套件，即使在專案中也一樣。

## <a name="configure-app-constants"></a>設定應用程式常數

範例專案包含可提供一般設定資料的 **Constants.cs** 檔案：

```csharp
public static class Constants
{
    public const string DatabaseFilename = "TodoSQLite.db3";

    public const SQLite.SQLiteOpenFlags Flags =
        // open the database in read/write mode
        SQLite.SQLiteOpenFlags.ReadWrite |
        // create the database if it doesn't exist
        SQLite.SQLiteOpenFlags.Create |
        // enable multi-threaded database access
        SQLite.SQLiteOpenFlags.SharedCache;

    public static string DatabasePath
    {
        get
        {
            var basePath = Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);
            return Path.Combine(basePath, DatabaseFilename);
        }
    }
}
```

常數檔案 `SQLiteOpenFlag` 會指定用來初始化資料庫連接的預設列舉值。 `SQLiteOpenFlag`列舉支援下列值：

- `Create`：連接將會自動建立資料庫檔案（如果不存在的話）。
- `FullMutex`：以序列化執行緒模式開啟連接。
- `NoMutex`：以多執行緒模式開啟連接。
- `PrivateCache`：即使已啟用，連接也不會參與共用快取。
- `ReadWrite`：連接可以讀取和寫入資料。
- `SharedCache`：連接將參與共用快取（若已啟用）。
- `ProtectionComplete`：當裝置鎖定時，檔案已加密且無法存取。
- `ProtectionCompleteUnlessOpen`：檔案在開啟之前會經過加密，但可供存取，即使使用者鎖定裝置也是如此。
- `ProtectionCompleteUntilFirstUserAuthentication`：檔案會經過加密，直到使用者已啟動並解除鎖定裝置為止。
- `ProtectionNone`：資料庫檔案未加密。

您可能需要根據資料庫的使用方式，指定不同的旗標。 如需的詳細資訊 `SQLiteOpenFlags` ，請參閱在 sqlite.org 上 [開啟新的資料庫連接](https://www.sqlite.org/c3ref/open.html) 。

## <a name="create-a-database-access-class"></a>建立資料庫存取類別

資料庫包裝函式類別會從應用程式的其餘部分抽象化資料存取層。 此類別會集中化查詢邏輯並簡化資料庫初始化的管理，讓您在應用程式成長時更輕鬆地重構或擴充資料作業。 Todo 應用程式會定義 `TodoItemDatabase` 此用途的類別。

### <a name="lazy-initialization"></a>延遲初始設定

`TodoItemDatabase`使用由自訂類別表示的非同步延遲初始化 `AsyncLazy<T>` ，來延遲資料庫的初始存取，直到第一次被存取為止：

```csharp
public class TodoItemDatabase
{
    static SQLiteAsyncConnection Database;

    public static readonly AsyncLazy<TodoItemDatabase> Instance = new AsyncLazy<TodoItemDatabase>(async () =>
    {
        var instance = new TodoItemDatabase();
        CreateTableResult result = await Database.CreateTableAsync<TodoItem>();
        return instance;
    });

    public TodoItemDatabase()
    {
        Database = new SQLiteAsyncConnection(Constants.DatabasePath, Constants.Flags);
    }

    //...
}
```

此 `Instance` 欄位是用來建立物件的資料庫資料表 `TodoItem` （如果尚未存在），並傳回 `TodoItemDatabase` 做為 singleton。 `Instance`型別的欄位 `AsyncLazy<TodoItemDatabase>` 是在第一次等候時所構成。 如果有多個執行緒同時嘗試存取欄位，它們都會使用單一結構。 然後，當結構完成時，所有 `await` 作業都會完成。 此外， `await` 結構之後的任何作業都會立即繼續，因為值可以使用。

> [!NOTE]
> 資料庫連接是靜態欄位，可確保在應用程式存留期內使用單一資料庫連接。 在單一應用程式會話期間，使用持續性靜態連接可提供比開啟和關閉連接更佳的效能。

### <a name="asynchronous-lazy-initialization"></a>非同步延遲初始化

若要啟動資料庫初始化，請避免封鎖執行，並有機會攔截例外狀況，範例應用程式會使用以類別表示的非同步延遲初始化 `AsyncLazy<T>` ：

```csharp
public class AsyncLazy<T> : Lazy<Task<T>>
{
    readonly Lazy<Task<T>> instance;

    public AsyncLazy(Func<T> factory)
    {
        instance = new Lazy<Task<T>>(() => Task.Run(factory));
    }

    public AsyncLazy(Func<Task<T>> factory)
    {
        instance = new Lazy<Task<T>>(() => Task.Run(factory));
    }

    public TaskAwaiter<T> GetAwaiter()
    {
        return instance.Value.GetAwaiter();
    }
}
```

`AsyncLazy`類別會合並 `Lazy<T>` 和類型， `Task<T>` 以建立延遲初始化的工作，以表示資源的初始化。 傳遞給函式的 factory 委派可以是同步或非同步。 Factory 委派將會線上程集區執行緒上執行，且不會執行一次以上 (即使多個執行緒嘗試同時啟動它們) 。 當 factory 委派完成時，會提供延遲初始化的值，而且任何等候該實例的方法 `AsyncLazy<T>` 都會收到值。 如需詳細資訊，請參閱 [改用 asynclazy<t>](https://devblogs.microsoft.com/pfxteam/asynclazyt/)。

### <a name="data-manipulation-methods"></a>資料操作方法

`TodoItemDatabase`類別包含四種資料操作類型的方法：建立、讀取、編輯和刪除。 SQLite.NET 程式庫提供簡單的物件關聯式對應 (ORM) ，可讓您在不需要撰寫 SQL 語句的情況下儲存和取出物件。

```csharp
public class TodoItemDatabase
{
    // ...
    public Task<List<TodoItem>> GetItemsAsync()
    {
        return Database.Table<TodoItem>().ToListAsync();
    }

    public Task<List<TodoItem>> GetItemsNotDoneAsync()
    {
        // SQL queries are also possible
        return Database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
    }

    public Task<TodoItem> GetItemAsync(int id)
    {
        return Database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
    }

    public Task<int> SaveItemAsync(TodoItem item)
    {
        if (item.ID != 0)
        {
            return Database.UpdateAsync(item);
        }
        else
        {
            return Database.InsertAsync(item);
        }
    }

    public Task<int> DeleteItemAsync(TodoItem item)
    {
        return Database.DeleteAsync(item);
    }
}
```

## <a name="access-data-in-xamarinforms"></a>存取中的資料 Xamarin.Forms

`TodoItemDatabase`類別 `Instance` 會公開欄位，可透過此欄位叫用類別中的資料存取作業 `TodoItemDatabase` ：

```csharp
async void OnSaveClicked(object sender, EventArgs e)
{
    var todoItem = (TodoItem)BindingContext;
    TodoItemDatabase database = await TodoItemDatabase.Instance;
    await database.SaveItemAsync(todoItem);

    // Navigate backwards
    await Navigation.PopAsync();
}
```

## <a name="advanced-configuration"></a>進階組態

SQLite 提供的強大 API 具有比本文和範例應用程式中涵蓋的功能更多的功能。 下列各節涵蓋擴充性重要的功能。

如需詳細資訊，請參閱 sqlite.org 上的 [SQLite 檔](https://www.sqlite.org/docs.html) 。

### <a name="write-ahead-logging"></a>預先寫入記錄

根據預設，SQLite 會使用傳統的復原日誌。 未變更資料庫內容的複本會寫入個別的復原檔案，然後這些變更會直接寫入資料庫檔案中。 刪除復原日誌時，就會發生認可。

Write-Ahead 記錄 (WAL) 會先將變更寫入個別的 WAL 檔。 在 WAL 模式中，認可是一種特殊記錄，會附加至 WAL 檔案，以允許在單一 WAL 檔中發生多筆交易。 WAL 檔案會在稱為 _檢查點_ 的特殊作業中，合併回資料庫檔案中。

本機資料庫的 WAL 可能會更快，因為讀取器和寫入器不會彼此封鎖，允許並行讀取和寫入作業。 但是，WAL 模式不允許變更 _頁面大小_、將其他檔案關聯加入至資料庫，以及加入額外的 _檢查點_ 作業。

若要在 SQLite.NET 中啟用 WAL，請 `EnableWriteAheadLoggingAsync` 在實例上呼叫方法 `SQLiteAsyncConnection` ：

```csharp
await Database.EnableWriteAheadLoggingAsync();
```

如需詳細資訊，請參閱 sqlite.org 上的 [SQLite Write-Ahead 記錄](https://www.sqlite.org/wal.html) 。

### <a name="copy-a-database"></a>複製資料庫

有幾種情況可能需要複製 SQLite 資料庫：

- 資料庫已隨附于您的應用程式，但必須複製或移至行動裝置上的可寫入儲存體。
- 您需要建立資料庫的備份或複本。
- 您需要為資料庫檔案進行版本、移動或重新命名。

一般而言，移動、重新命名或複製資料庫檔案的程式與其他任何檔案類型的程式相同，但有一些其他考慮：

- 在嘗試移動資料庫檔案之前，應先關閉所有資料庫連接。
- 如果您使用預先 [寫入記錄](#write-ahead-logging)，SQLite 將會建立 ( 的共用記憶體存取。具有下列 shm) 檔和 (預先寫入記錄)  (. wal) 檔。 確定您也將任何變更套用至這些檔案。

如需詳細資訊，請參閱[中 Xamarin.Forms ](~/xamarin-forms/data-cloud/data/files.md)的檔案處理。

## <a name="related-links"></a>相關連結

- [Todo 範例應用程式](/samples/xamarin/xamarin-forms-samples/todo)
- [SQLite.NET NuGet 套件](https://www.nuget.org/packages/sqlite-net-pcl/)
- [SQLite 檔](https://www.sqlite.org/docs.html)
- [搭配使用 SQLite 與 Android](~/android/data-cloud/data-access/using-sqlite-orm.md)
- [搭配使用 SQLite 與 iOS](~/ios/data-cloud/data/using-sqlite-orm.md)
- [改用 asynclazy<t>](https://devblogs.microsoft.com/pfxteam/asynclazyt/)
