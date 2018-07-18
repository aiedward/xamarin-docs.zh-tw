---
title: Xamarin.Forms 本機資料庫
description: Xamarin.Forms 支援資料庫驅動的應用程式使用 SQLite 資料庫引擎，讓您能夠載入和儲存在共用程式碼中的物件。 本文說明如何讀取和寫入資料至本機 SQLite 資料庫使用 SQLite.Net Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: feec4993a0719a083d713e084552b18aead8ee42
ms.sourcegitcommit: eac092f84b603958c761df305f015ff84e0fad44
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310136"
---
# <a name="xamarinforms-local-databases"></a>Xamarin.Forms 本機資料庫

_Xamarin.Forms 支援資料庫驅動的應用程式使用 SQLite 資料庫引擎，讓您能夠載入和儲存在共用程式碼中的物件。本文說明如何讀取和寫入資料至本機 SQLite 資料庫使用 SQLite.Net Xamarin.Forms 應用程式。_

## <a name="overview"></a>總覽

Xamarin.Forms 應用程式可以使用[SQLite.NET PCL NuGet](https://www.nuget.org/packages/sqlite-net-pcl/)封裝加入到資料庫作業藉由參考共用程式碼`SQLite`出貨 NuGet 中的類別。 .NET 標準程式庫專案中的 Xamarin.Forms 方案，就可以定義資料庫作業。

隨附[範例應用程式](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo)是簡單的待辦事項清單應用程式。 下列螢幕擷取畫面顯示此範例顯示每個平台上的方式：

[![Xamarin.Forms 資料庫範例螢幕擷取畫面](databases-images/todo-list-sml.png "TodoList 第一個頁面的螢幕擷取畫面")](databases-images/todo-list.png#lightbox "TodoList 第一個頁面的螢幕擷取畫面") [ ![Xamarin.Forms 資料庫範例螢幕擷取畫面](databases-images/todo-list-sml.png "TodoList 第一個頁面的螢幕擷取畫面")](databases-images/todo-list.png#lightbox "TodoList 第一個頁面的螢幕擷取畫面")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>使用 SQLite

若要加入 SQLite 支援 Xamarin.Forms.NET 標準程式庫，使用 NuGet 的搜尋功能來尋找**sqlite net pcl**並安裝最新的封裝：

![新增 NuGet SQLite.NET PCL 封裝](databases-images/vs2017-sqlite-pcl-nuget.png "新增 NuGet SQLite.NET PCL 封裝")

有具有類似名稱的 NuGet 封裝，正確的封裝具有下列屬性：

- **所建立：** Frank A.Krueger
- **Id:** sqlite net pcl
- **NuGet 連結：** [sqlite net pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> 儘管封裝名稱，使用**sqlite net pcl**甚至.NET 標準的專案中的 NuGet 封裝。

一旦加入參考，將屬性加入`App`傳回用於儲存資料庫的本機檔案路徑的類別：

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

`TodoItemDatabase`建構函式，會以做為引數的資料庫檔案的路徑，如下所示：

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

優點公開為單一值會建立單一資料庫連接的資料庫會保持開啟應用程式時，會執行，因此可避免支出開頭和結尾的資料庫檔案每次資料庫作業則會執行。

其餘部分`TodoItemDatabase`類別包含執行跨平台的 SQLite 查詢。 範例查詢程式碼如下所示 (位於語法的詳細[使用 SQLite.NET](~/cross-platform/app-fundamentals/index.md)文件):

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
> 使用非同步 SQLite.Net API 的優點是該資料庫作業都會移到背景執行緒。 此外，還有不需要撰寫額外的並行處理程式碼，因為它會處理應用程式開發介面。

## <a name="summary"></a>總結

Xamarin.Forms 支援資料庫驅動的應用程式使用 SQLite 資料庫引擎，讓您能夠載入和儲存在共用程式碼中的物件。

本文著重於**存取**使用 Xamarin.Forms SQLite 資料庫。 如需使用 SQLite.Net 本身的詳細資訊，請參閱[SQLite.NET 在 Android 上的](~/android/data-cloud/data-access/using-sqlite-orm.md)或[在 iOS 上的 SQLite.NET](~/ios/data-cloud/data/using-sqlite-orm.md)文件。

## <a name="related-links"></a>相關連結

- [Todo 範例](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [資料庫活頁簿](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/database/database.workbook)
