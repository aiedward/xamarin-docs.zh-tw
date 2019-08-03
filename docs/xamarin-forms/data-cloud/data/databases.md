---
title: Xamarin.Forms 本機資料庫
description: Xamarin.Forms 使用 SQLite 資料庫引擎來支援資料庫驅動型應用程式，讓您可以將物件載入和儲存至共用程式碼。 本文描述 Xamarin.Forms 應用程式如何使用 SQLite.Net 在本機 SQLite 資料庫中讀取和寫入資料。
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 9ea105b27aacef9ca9d63af0c57de880d039ff53
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739170"
---
# <a name="xamarinforms-local-databases"></a>Xamarin.Forms 本機資料庫

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)

_Xamarin.Forms 使用 SQLite 資料庫引擎來支援資料庫驅動型應用程式，讓您可以將物件載入和儲存至共用程式碼。本文描述 Xamarin.Forms 應用程式如何使用 SQLite.Net 在本機 SQLite 資料庫中讀取和寫入資料。_

## <a name="overview"></a>總覽

Xamarin.Forms 應用程式可以使用 [SQLite.NET PCL NuGet](https://www.nuget.org/packages/sqlite-net-pcl/) 套件，透過參考 NuGet 中隨附的 `SQLite` 類別，將資料庫作業併入共用程式碼中。 這些資料庫作業可在 Xamarin.Forms 解決方案的 .NET Standard 程式庫專案中定義。

隨附的[範例應用程式](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)是簡單的待辦事項清單應用程式。 下列螢幕擷取畫面顯示該範例在每個平台上的外觀：

[![Xamarin.Forms 資料庫範例螢幕擷取畫面](databases-images/todo-list-sml.png "TodoList 第一頁螢幕擷取畫面")](databases-images/todo-list.png#lightbox "TodoList 第一頁螢幕擷取畫面") [ ![Xamarin.Forms 資料庫範例螢幕擷取畫面](databases-images/todo-list-sml.png "TodoList 第一頁螢幕擷取畫面")](databases-images/todo-list.png#lightbox "TodoList 第一頁螢幕擷取畫面")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>使用 SQLite

若要將 SQLite 支援新增至 Xamarin.Forms .NET Standard 程式庫，請使用 NuGet 的搜尋函式來尋找 **sqlite-net-pcl** 並安裝最新的套件：

![新增 NuGet SQLite.NET PCL 套件](databases-images/vs2017-sqlite-pcl-nuget.png "新增 NuGet SQLite.NET PCL 套件")

有些 NuGet 套件的名稱很類似，正確套件具有下列屬性：

- **建立者：** Frank A. Krueger
- **識別碼：** sqlite-net-pcl
- **NuGet 連結：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> 不論套件名稱為何，請使用 **sqlite-net-pcl** NuGet 套件，即使在專案中也一樣。

新增參考之後，將傳回儲存資料庫之本機檔案路徑的屬性新增至 `App` 類別：

```csharp
static TodoItemDatabase database;

public static TodoItemDatabase Database
{
  get
  {
    if (database == null)
    {
      database = new TodoItemDatabase(
        Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "TodoSQLite.db3"));
    }
    return database;
  }
}
```

`TodoItemDatabase` 建構函式採用資料庫檔案的路徑作為引數，如下所示：

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

將資料庫公開為唯一資料庫的優點為，所建立的單一資料庫連線會在應用程式執行時保持開啟，因此可避免每次執行資料庫作業時開啟和關閉資料庫檔案的費用。

`TodoItemDatabase` 類別的其餘部分包含跨平台執行的 SQLite 查詢。 範例查詢程式碼如下所示 (如需該語法的詳細資料，請參閱[搭配 Xamarin.iOS 使用 SQLite.NET](~/ios/data-cloud/data/using-sqlite-orm.md)。

```csharp
public Task<List<TodoItem>> GetItemsAsync()
{
  return database.Table<TodoItem>().ToListAsync();
}

public Task<List<TodoItem>> GetItemsNotDoneAsync()
{
  return database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
}

public Task<TodoItem> GetItemAsync(int id)
{
  return database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
}

public Task<int> SaveItemAsync(TodoItem item)
{
  if (item.ID != 0)
  {
    return database.UpdateAsync(item);
  }
  else {
    return database.InsertAsync(item);
  }
}

public Task<int> DeleteItemAsync(TodoItem item)
{
  return database.DeleteAsync(item);
}
```

> [!NOTE]
> 使用非同步 SQLite.Net API 的優點為，資料庫作業會移至背景執行緒。 此外，不需要撰寫額外的並行處理程式碼，因為 API 會負責處理。

## <a name="summary"></a>總結

Xamarin.Forms 使用 SQLite 資料庫引擎來支援資料庫驅動型應用程式，讓您可以將物件載入和儲存至共用程式碼。

本文著重於使用 Xamarin.Forms 來**存取** SQLite 資料庫。 如需使用 SQLite.Net 本身的詳細資訊，請參閱 [Android 上的 SQLite.NET](~/android/data-cloud/data-access/using-sqlite-orm.md) 或 [iOS 上的 SQLite.NET](~/ios/data-cloud/data/using-sqlite-orm.md) 文件。

## <a name="related-links"></a>相關連結

- [範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)
- [Xamarin.Forms 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
