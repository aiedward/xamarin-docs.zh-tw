---
標題：「 Xamarin.Forms 本機資料庫」描述：「 Xamarin.Forms 使用 SQLite 資料庫引擎來支援資料庫驅動的應用程式，讓您可以在共用程式碼中載入和儲存物件。 本文說明 Xamarin.Forms 應用程式如何使用 SQLite.Net 來讀取和寫入資料至本機 SQLite 資料庫。
assetid： F687B24B-7DF0-4F8E-A21A-A9BB507480EB ms. 技術： xamarin-表單作者： profexorgeek ms. author： jusjohns ms. 日期：12/05/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-local-databases"></a>Xamarin.Forms本機資料庫

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)

SQLite 資料庫引擎可讓 Xamarin.Forms 應用程式將資料物件載入和儲存在共用程式碼中。 範例應用程式會使用 SQLite 資料庫資料表來儲存 todo 專案。 本文說明如何在共用程式碼中使用 SQLite.Net，在本機資料庫中儲存和取出資訊。

[![IOS 和 Android 上 Todolist 應用程式的螢幕擷取畫面](databases-images/todo-list-sml.png)](databases-images/todo-list.png#lightbox "IOS 和 Android 上的 Todolist 應用程式")

遵循下列步驟，將 SQLite.NET 整合到行動應用程式：

1. [安裝 NuGet 套件](#install-the-sqlite-nuget-package)。
1. [設定常數](#configure-app-constants)。
1. [建立資料庫存取類別](#create-a-database-access-class)。
1. [存取中的 Xamarin.Forms 資料](#access-data-in-xamarinforms)。
1. [Advanced configuration](#advanced-configuration)。

## <a name="install-the-sqlite-nuget-package"></a>安裝 SQLite NuGet 套件

使用 NuGet 套件管理員來搜尋**sqlite-net-pcl** ，並將最新版本加入至共用程式碼專案。

有許多名稱類似的 NuGet 套件。 正確的套件有下列屬性：

- **建立者：** Frank A. Krueger (praeclarum)
- **識別碼：** sqlite-net-pcl
- **NuGet 連結：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> 不論套件名稱為何，請使用 **sqlite-net-pcl** NuGet 套件，即使在專案中也一樣。

## <a name="configure-app-constants"></a>設定應用程式常數

範例專案包含提供一般設定資料的**Constants.cs**檔案：

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

- `Create`：如果資料庫檔案不存在，連接就會自動加以建立。
- `FullMutex`：連接會以序列化執行緒模式開啟。
- `NoMutex`：連接會在多執行緒模式中開啟。
- `PrivateCache`：即使已啟用，連接也不會參與共用快取。
- `ReadWrite`：連接可以讀取和寫入資料。
- `SharedCache`：如果已啟用，連接將會參與共用快取。
- `ProtectionComplete`：當裝置鎖定時，檔案已加密且無法存取。
- `ProtectionCompleteUnlessOpen`：檔案會經過加密後才會開啟，但是即使使用者鎖定裝置，仍可存取該檔案。
- `ProtectionCompleteUntilFirstUserAuthentication`：檔案會經過加密，直到使用者已啟動並解除鎖定裝置為止。
- `ProtectionNone`：資料庫檔案未加密。

視您的資料庫使用方式而定，您可能需要指定不同的旗標。 如需的詳細資訊 `SQLiteOpenFlags` ，請參閱在 sqlite.org 上[開啟新的資料庫連接](https://www.sqlite.org/c3ref/open.html)。

## <a name="create-a-database-access-class"></a>建立資料庫存取類別

資料庫包裝函式類別會將資料存取層從應用程式的其餘部分抽象化。 這個類別會集中查詢邏輯，並簡化資料庫初始化的管理，讓您在應用程式成長時更輕鬆地重構或擴充資料作業。 Todo 應用程式會 `TodoItemDatabase` 針對此用途定義類別。

### <a name="lazy-initialization"></a>延遲初始設定

`TodoItemDatabase`會使用 .net `Lazy` 類別來延遲資料庫的初始化，直到第一次存取為止。 使用延遲初始化可防止資料庫載入進程延遲啟動應用程式。 如需詳細資訊，請參閱[Lazy &lt; T &gt; Class](xref:System.Lazy`1)。

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
                initialized = true;
            }
        }
    }

    //...
}
```

資料庫連接是靜態欄位，可確保在應用程式的生命週期中使用單一資料庫連接。 使用持續性的靜態連線，可提供比在單一應用程式會話期間多次開啟和關閉連接更好的效能。

`InitializeAsync`方法負責檢查是否已有資料表儲存 `TodoItem` 物件。 如果資料表不存在，這個方法會自動建立。

### <a name="the-safefireandforget-extension-method"></a>SafeFireAndForget 擴充方法

當 `TodoItemDatabase` 類別具現化時，它必須初始化資料庫連接，這是非同步進程。 但是：

- 類別的構造函式不能是非同步。
- 未等待的非同步方法不會擲回例外狀況。
- 使用 `Wait` 方法會封鎖執行緒_和_抑制例外狀況。

為了啟動非同步初始化、避免封鎖執行，而且有機會攔截例外狀況，範例應用程式會使用名為的擴充方法 `SafeFireAndForget` 。 `SafeFireAndForget`擴充方法會為類別提供額外的功能 `Task` ：

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

`SafeFireAndForget`方法會等候所提供物件的非同步執行 `Task` ，並可讓您附加 `Action` 呼叫（如果擲回例外狀況）。

如需詳細資訊，請參閱以工作為[基礎的非同步模式（](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)點一下）。

### <a name="data-manipulation-methods"></a>資料操作方法

`TodoItemDatabase`類別包含四種資料操作類型的方法： [建立]、[讀取]、[編輯] 和 [刪除]。 SQLite.NET 程式庫提供簡單的物件關聯式對應（ORM），可讓您儲存和抓取物件，而不需要撰寫 SQL 語句。

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

## <a name="access-data-in-xamarinforms"></a>存取資料Xamarin.Forms

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

這個屬性可讓 Xamarin.Forms 元件呼叫實例上的資料抓取和操作方法，以 `Database` 回應使用者互動。 例如：

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

SQLite 提供健全的 API，其功能比本文和範例應用程式中所涵蓋的更多功能。 下列各節涵蓋擴充性的重要功能。

如需詳細資訊，請參閱 sqlite.org 上的[SQLite 檔](https://www.sqlite.org/docs.html)。

### <a name="write-ahead-logging"></a>預先寫入記錄

根據預設，SQLite 會使用傳統的回復日誌。 未變更的資料庫內容複本會寫入個別的復原檔案中，然後這些變更會直接寫入資料庫檔案中。 刪除復原日誌時，就會發生認可。

預先寫入記錄（WAL）會先將變更寫入另一個 WAL 檔案。 在 WAL 模式中，認可是特殊記錄，附加至 WAL 檔案，可讓單一 WAL 檔案中發生多個交易。 在稱為「_檢查點_」的特殊作業中，WAL 檔案會合並回資料庫檔案中。

本機資料庫的 WAL 可能會比較快，因為讀取器和寫入器不會彼此封鎖，讓讀寫作業得以並行處理。 不過，WAL 模式不允許變更_頁面大小_、將其他檔案關聯新增至資料庫，以及加入額外的_檢查點_作業。

若要在 SQLite.NET 中啟用 WAL，請 `EnableWriteAheadLoggingAsync` 在實例上呼叫方法 `SQLiteAsyncConnection` ：

```csharp
await Database.EnableWriteAheadLoggingAsync();
```

如需詳細資訊，請參閱 sqlite.org 上的[SQLite 預先寫入記錄](https://www.sqlite.org/wal.html)。

### <a name="copying-a-database"></a>複製資料庫

在幾種情況下，可能必須複製 SQLite 資料庫：

- 資料庫隨附于您的應用程式，但必須複製或移至行動裝置上可寫入的儲存體。
- 您需要建立資料庫的備份或複本。
- 您需要為資料庫檔案進行版本、移動或重新命名。

一般來說，移動、重新命名或複製資料庫檔案與其他任何檔案類型相同，還有一些其他考慮：

- 在嘗試移動資料庫檔案之前，應該先關閉所有資料庫連接。
- 如果您使用預先[寫入記錄](#write-ahead-logging)，SQLite 會建立共用記憶體存取（. 具有下列 shm）檔案和（寫入預先記錄）（. wal）檔案。 請確定您也將任何變更套用至這些檔案。

如需詳細資訊，請參閱[中 Xamarin.Forms ](~/xamarin-forms/data-cloud/data/files.md)的檔案處理。

## <a name="related-links"></a>相關連結

- [Todo 範例應用程式](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)
- [SQLite.NET NuGet 套件](https://www.nuget.org/packages/sqlite-net-pcl/)
- [SQLite 檔](https://www.sqlite.org/docs.html)
- [搭配使用 SQLite 與 Android](~/android/data-cloud/data-access/using-sqlite-orm.md)
- [搭配使用 SQLite 與 iOS](~/ios/data-cloud/data/using-sqlite-orm.md)
- [以工作為基礎的非同步模式（點一下）](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)
- [Lazy &lt; T &gt; 類別](xref:System.Lazy`1)
