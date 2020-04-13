---
title: Xamarin.Forms 本機資料庫
description: Xamarin.Forms 使用 SQLite 資料庫引擎來支援資料庫驅動型應用程式，讓您可以將物件載入和儲存至共用程式碼。 本文描述 Xamarin.Forms 應用程式如何使用 SQLite.Net 在本機 SQLite 資料庫中讀取和寫入資料。
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 12/05/2019
ms.openlocfilehash: 2369afc693940d83971a43877da363e2c66b31b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992406"
---
# <a name="xamarinforms-local-databases"></a>Xamarin.Forms 本機資料庫

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)

SQLite 資料庫引擎允許 Xamarin.Forms 應用程式在共用代碼中載入和保存資料物件。 範例應用程式使用 SQLite 資料庫表來儲存 todo 項。 本文介紹如何在共享代碼中使用SQLite.Net在本地資料庫中存儲和檢索資訊。

[![iOS 和 Android 上的 Todolist 應用程式的螢幕截圖](databases-images/todo-list-sml.png)](databases-images/todo-list.png#lightbox "在 iOS 和安卓上的待辦事項應用")

使用以下步驟將SQLite.NET整合到行動應用中:

1. [安裝 NuGet 套件](#install-the-sqlite-nuget-package)。
1. [設定常數](#configure-app-constants)。
1. [建立資料庫存取類別](#create-a-database-access-class)。
1. [存取 Xamarin.forms 中的資料](#access-data-in-xamarinforms)。
1. [進階設定](#advanced-configuration)。

## <a name="install-the-sqlite-nuget-package"></a>安裝 SQLite NuGet 套件

使用 NuGet 套件管理器搜尋**sqlite-net-pcl**並將最新版本添加到共享代碼專案。

有許多名稱類似的 NuGet 套件。 正確的套件有下列屬性：

- **建立者：** Frank A. Krueger
- **ID:** sqlite-net-pcl
- **NuGet 連結：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> 不論套件名稱為何，請使用 **sqlite-net-pcl** NuGet 套件，即使在專案中也一樣。

## <a name="configure-app-constants"></a>設定應用常量

範例專案包括一個**提供**常見設定資料的Constants.cs檔:

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

常量檔指定用於初始`SQLiteOpenFlag`化資料庫連接的預設枚舉值。 `SQLiteOpenFlag`列舉支援以下值:

- `Create`:如果資料庫檔不存在,連接將自動創建它。
- `FullMutex`:連接在序列化線程模式下打開。
- `NoMutex`:連接在多線程模式下打開。
- `PrivateCache`:連接不會參與共用緩存,即使它已啟用。
- `ReadWrite`:連接可以讀取和寫入數據。
- `SharedCache`:如果連接已啟用,則參與共用緩存。
- `ProtectionComplete`:檔已加密,在設備鎖定時無法訪問。
- `ProtectionCompleteUnlessOpen`:檔被加密,直到它被打開,但隨後可以訪問,即使使用者鎖定設備。
- `ProtectionCompleteUntilFirstUserAuthentication`:檔案加密,直到用戶啟動並解鎖設備。
- `ProtectionNone`:資料庫檔未加密。

您可能需要根據資料庫的使用方式指定不同的標誌。 有關的詳細資訊`SQLiteOpenFlags`,請參閱在sqlite.org[開啟新的資料庫連接](https://www.sqlite.org/c3ref/open.html)。

## <a name="create-a-database-access-class"></a>建立資料庫存取類別

資料庫包裝類從應用的其餘部分抽象數據訪問層。 此類集中查詢邏輯並簡化資料庫初始化管理,從而更輕鬆地隨著應用的增長重構或擴展數據操作。 "Todo"應用為此定義了`TodoItemDatabase`類。

### <a name="lazy-initialization"></a>延遲初始設定

使用`TodoItemDatabase`.NET`Lazy`類延遲資料庫的初始化,直到第一次訪問資料庫。 使用延遲初始化可防止資料庫載入過程延遲應用啟動。 有關詳細資訊,請參閱[&lt;延遲&gt;T 類](xref:System.Lazy`1)。

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

資料庫連接是一個靜態欄位,可確保單個資料庫連接用於應用的生命週期。 與在單個應用會話期間多次打開和關閉連接相比,使用持久靜態連接可提供更好的性能。

該方法`InitializeAsync`負責檢查是否已存在用於`TodoItem`存儲 物件的表。 如果表不存在,則此方法會自動創建它。

### <a name="the-safefireandforget-extension-method"></a>安全消防和忘記擴充方法

實例化`TodoItemDatabase`類時,它必須初始化資料庫連接,這是一個非同步過程。 但是：

- 類構造函數不能異步。
- 不等待的異步方法不會引發異常。
- 使用`Wait`方法阻止線程_並_吞下異常。

為了啟動非同步初始化,避免阻止執行,並有機會捕獲異常,範例應用程式使用名為`SafeFireAndForget`的擴展方法。 擴`SafeFireAndForget`充方法`Task`為 類別提供其他功能:

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

該方法`SafeFireAndForget`等待`Task`提供 物件的非同步執行,並允許`Action`您附加引發異常時呼叫的 。

有關詳細資訊,請參閱[基於任務的非同步模式 (TAP)。](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)

### <a name="data-manipulation-methods"></a>資料操作方法

該`TodoItemDatabase`類包括四種類型的數據操作的方法:創建、讀取、編輯和刪除。 SQLite.NET庫提供了一個簡單的物件關係圖 (ORM),允許您存儲和檢索物件而無需編寫 SQL 語句。

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

## <a name="access-data-in-xamarinforms"></a>存取 Xamarin.表單中

Xamarin.Forms`App`類公開類`TodoItemDatabase`的 實體:

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

此屬性允許 Xamarin.Forms 元件呼`Database`叫實例上的資料檢索和操作方法以回應使用者互動。 例如：

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

SQLite 提供了一個強大的 API,其功能比本文和示例應用中介紹的功能更多。 以下各節介紹對可伸縮性很重要的功能。

有關詳細資訊,請參閱有關sqlite.org[的 SQLite 文件](https://www.sqlite.org/docs.html)。

### <a name="write-ahead-logging"></a>提前寫入紀錄記錄

默認情況下,SQLite 使用傳統的回滾日誌。 未更改的資料庫內容的複本將寫入單獨的回滾檔,然後更改直接寫入資料庫檔。 刪除回滾日誌時,將發生 COMMIT。

先寫入前記錄 (WAL) 先將更改寫入單獨的 WAL 檔。 在 WAL 模式下,COMMIT 是一種特殊記錄,追加到 WAL 檔,允許在單個 WAL 檔中發生多個事務。 WAL 檔案在稱為_檢查點_的特殊操作中合併回資料庫檔。

對於本地資料庫,WAL 可以更快,因為讀取器和編寫器不會相互阻止,從而允許讀取和寫入操作是併發的。 但是,WAL 模式不允許更改_頁面大小_,向資料庫添加其他文件關聯,並添加額外的_檢查點_操作。

在SQLite.NET中啟用 WAL,`EnableWriteAheadLoggingAsync`請`SQLiteAsyncConnection`呼叫 實體上的方法:

```csharp
await Database.EnableWriteAheadLoggingAsync();
```

有關詳細資訊,請參閱 sqlite.org 上的[SQLite 提前寫入紀錄記錄](https://www.sqlite.org/wal.html)。

### <a name="copying-a-database"></a>複製資料庫

在以下幾種情況下,可能需要複製 SQLite 資料庫:

- 資料庫已隨應用程式一起提供,但必須複製或移動到行動裝置上的可寫入儲存。
- 您需要進行資料庫的備份或複製。
- 您需要對資料庫檔進行版本控制、移動或重新命名。

通常,移動、重新命名或複製資料庫檔與任何其他檔案類型的過程相同,並有幾個其他注意事項:

- 在嘗試行動資料庫檔之前,應關閉所有資料庫連接。
- 如果使用[提前寫入日誌記錄](#write-ahead-logging),SQLite 將建立共用記憶體存取 (.shm) 檔和(提前寫入日誌) (.wal) 檔案。 請確保對這些文件應用任何更改。

有關詳細資訊,請參閱[Xamarin.Forms 中的檔案處理](~/xamarin-forms/data-cloud/data/files.md)。

## <a name="related-links"></a>相關連結

- [Todo 範例應用程式](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)
- [SQLite.NET NuGet 套件](https://www.nuget.org/packages/sqlite-net-pcl/)
- [SQLite 文件](https://www.sqlite.org/docs.html)
- [將 SQLite 與安卓一起使用](~/android/data-cloud/data-access/using-sqlite-orm.md)
- [將 SQLite 與 iOS 一起使用](~/ios/data-cloud/data/using-sqlite-orm.md)
- [建基於工作的非同步模式 (TAP)](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)
- [惰&lt;&gt;性 T 類](xref:System.Lazy`1)
