---
title: Xamarin.Forms 本機資料庫
description: Xamarin.Forms 支援使用 SQLite 資料庫引擎的資料庫導向應用程式，讓您可以在共用程式碼中載入和儲存物件。 本文描述 Xamarin.Forms 應用程式如何使用 SQLite.Net 來讀取和寫入資料至本機 SQLite 資料庫。
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 12/05/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: afa5ccf8f4d4485ae7a9a45bcbc745bddee20f5c
ms.sourcegitcommit: 1550019cd1e858d4d13a4ae6dfb4a5947702f24b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897477"
---
# <a name="no-locxamarinforms-local-databases"></a>Xamarin.Forms 本機資料庫

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)

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

在 `TodoItemDatabase` `Lazy` 第一次存取資料庫之前，會使用 .net 類別來延遲資料庫的初始化。 使用延遲初始化可防止資料庫載入進程延遲應用程式啟動。 如需詳細資訊，請參閱 [Lazy &lt; T &gt; 類別](xref:System.Lazy`1)。

```csharp
public class TodoItemDatabase
{
    static readonly Lazy<SQLiteAsyncConnection> lazyInitializer = new Lazy<SQLiteAsyncConnection>(() =>
    {
        return new SQLiteAsyncConnection(Constants.DatabasePath, Constants.Flags);
    });

    static SQLiteAsyncConnection Database => lazyInitializer.Value;
    static bool initialized = false;

    public TodoItemDatabase()
    {
        InitializeAsync().SafeFireAndForget(false);
    }

    async Task InitializeAsync()
    {
        if (!initialized)
        {
            if (!Database.TableMappings.Any(m => m.MappedType.Name == typeof(TodoItem).Name))
            {
                await Database.CreateTablesAsync(CreateFlags.None, typeof(TodoItem)).ConfigureAwait(false);
            }
            initialized = true;
        }
    }

    //...
}
```

資料庫連接是靜態欄位，可確保在應用程式存留期內使用單一資料庫連接。 在單一應用程式會話期間，使用持續性靜態連接可提供比開啟和關閉連接更佳的效能。

`InitializeAsync`方法負責檢查資料表是否已存在，以儲存 `TodoItem` 物件。 如果資料表不存在，這個方法會自動建立資料表。

### <a name="the-safefireandforget-extension-method"></a>SafeFireAndForget 擴充方法

當 `TodoItemDatabase` 類別具現化時，它必須初始化資料庫連接，這是非同步處理常式。 但是：

- 類別的函式不可以是非同步。
- 未等候的非同步方法不會擲回例外狀況。
- 使用 `Wait` 方法會封鎖執行緒 _並_ 抑制例外狀況。

為了開始非同步初始化，請避免封鎖執行，並有機會攔截例外狀況，範例應用程式會使用稱為的擴充方法 `SafeFireAndForget` 。 `SafeFireAndForget`擴充方法會提供類別的其他功能 `Task` ：

```csharp
public static class TaskExtensions
{
    // NOTE: Async void is intentional here. This provides a way
    // to call an async method from the constructor while
    // communicating intent to fire and forget, and allow
    // handling of exceptions
    public static async void SafeFireAndForget(this Task task,
        bool returnToCallingContext,
        Action<Exception> onException = null)
    {
        try
        {
            await task.ConfigureAwait(returnToCallingContext);
        }

        // if the provided action is not null, catch and
        // pass the thrown exception
        catch (Exception ex) when (onException != null)
        {
            onException(ex);
        }
    }
}
```

`SafeFireAndForget`方法會等候所提供物件的非同步執行 `Task` ，並可讓您附加在擲 `Action` 回例外狀況時呼叫的。

如需詳細資訊，請參閱以工作為 [基礎的非同步模式 (點擊) ](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)。

### <a name="data-manipulation-methods"></a>資料操作方法

`TodoItemDatabase`類別包含四種資料操作類型的方法：建立、讀取、編輯和刪除。 SQLite.NET 程式庫提供簡單的物件關聯式對應 (ORM) ，可讓您在不需要撰寫 SQL 語句的情況下儲存和取出物件。

```csharp
public class TodoItemDatabase {

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

## <a name="access-data-in-no-locxamarinforms"></a>存取中的資料 Xamarin.Forms

Xamarin.Forms `App` 類別會公開類別的實例 `TodoItemDatabase` ：

```csharp
static TodoItemDatabase database;
public static TodoItemDatabase Database
{
    get
    {
        if (database == null)
        {
            database = new TodoItemDatabase();
        }
        return database;
    }
}
```

這個屬性可讓 Xamarin.Forms 元件呼叫實例上的資料抓取和操作方法， `Database` 以回應使用者互動。 例如：

```csharp
var saveButton = new Button { Text = "Save" };
saveButton.Clicked += async (sender, e) =>
{
    var todoItem = (TodoItem)BindingContext;
    await App.Database.SaveItemAsync(todoItem);
    await Navigation.PopAsync();
};
```

## <a name="advanced-configuration"></a>進階組態

SQLite 提供的強大 API 具有比本文和範例應用程式中涵蓋的功能更多的功能。 下列各節涵蓋擴充性重要的功能。

如需詳細資訊，請參閱 sqlite.org 上的 [SQLite 檔](https://www.sqlite.org/docs.html) 。

### <a name="write-ahead-logging"></a>Write-Ahead 記錄

根據預設，SQLite 會使用傳統的復原日誌。 未變更資料庫內容的複本會寫入個別的復原檔案，然後這些變更會直接寫入資料庫檔案中。 刪除復原日誌時，就會發生認可。

Write-Ahead 記錄 (WAL) 會先將變更寫入個別的 WAL 檔。 在 WAL 模式中，認可是一種特殊記錄，會附加至 WAL 檔案，以允許在單一 WAL 檔中發生多筆交易。 WAL 檔案會在稱為 _檢查點_ 的特殊作業中，合併回資料庫檔案中。

本機資料庫的 WAL 可能會更快，因為讀取器和寫入器不會彼此封鎖，允許並行讀取和寫入作業。 但是，WAL 模式不允許變更 _頁面大小_ 、將其他檔案關聯加入至資料庫，以及加入額外的 _檢查點_ 作業。

若要在 SQLite.NET 中啟用 WAL，請 `EnableWriteAheadLoggingAsync` 在實例上呼叫方法 `SQLiteAsyncConnection` ：

```csharp
await Database.EnableWriteAheadLoggingAsync();
```

如需詳細資訊，請參閱 sqlite.org 上的 [SQLite Write-Ahead 記錄](https://www.sqlite.org/wal.html) 。

### <a name="copying-a-database"></a>複製資料庫

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
- [以工作為基礎的非同步模式 (點擊) ](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)
- [Lazy &lt; T &gt; 類別](xref:System.Lazy`1)
