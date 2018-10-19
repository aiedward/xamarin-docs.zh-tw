---
title: Xamarin.Forms 本機資料庫
description: Xamarin.Forms 可支援使用 SQLite 資料庫引擎，讓您能夠載入，並將物件儲存在共用程式碼的資料庫導向應用程式。 本文說明如何讀取和寫入資料到本機 SQLite 資料庫使用 SQLite.Net Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 05c77c4c47841a897d0d1de5c3ba004db524ea2d
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2018
ms.locfileid: "36310136"
---
# <a name="xamarinforms-local-databases"></a>Xamarin.Forms 本機資料庫

_Xamarin.Forms 可支援使用 SQLite 資料庫引擎，讓您能夠載入，並將物件儲存在共用程式碼的資料庫導向應用程式。本文說明如何讀取和寫入資料到本機 SQLite 資料庫使用 SQLite.Net Xamarin.Forms 應用程式。_

## <a name="overview"></a>總覽

Xamarin.Forms 應用程式可以使用[SQLite.NET PCL NuGet](https://www.nuget.org/packages/sqlite-net-pcl/)將資料庫作業的封裝共用程式碼，藉由參考`SQLite`隨附在 NuGet 中的類別。 資料庫作業可以定義.NET Standard 程式庫專案中的 Xamarin.Forms 方案。

隨附[範例應用程式](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo)是簡單的待辦事項清單應用程式。 下列螢幕擷取畫面顯示此範例顯示每個平台的方式：

[![Xamarin.Forms 資料庫範例螢幕擷取畫面](databases-images/todo-list-sml.png "TodoList 第一個頁面螢幕擷取畫面")](databases-images/todo-list.png#lightbox "TodoList 第一個頁面螢幕擷取畫面") [ ![Xamarin.Forms 資料庫範例螢幕擷取畫面](databases-images/todo-list-sml.png "TodoList 第一個頁面螢幕擷取畫面")](databases-images/todo-list.png#lightbox "TodoList 第一個頁面螢幕擷取畫面")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>使用 SQLite

SQLite 支援新增至 Xamarin.Forms.NET Standard 程式庫，請使用 NuGet 的 [搜尋] 功能來尋找**sqlite net pcl**並安裝最新的套件：

![新增 NuGet SQLite.NET PCL 封裝](databases-images/vs2017-sqlite-pcl-nuget.png "新增 NuGet SQLite.NET PCL 封裝")

有許多類似名稱的 NuGet 套件，正確的套件都有下列屬性：

- **所建立：** Frank A.Krueger
- **識別碼：** sqlite net pcl
- **NuGet 連結：** [sqlite net pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> 儘管套件名稱、 使用**sqlite net pcl**即使在.NET Standard 專案的 NuGet 套件。

一旦新增參考之後，將屬性新增至`App`傳回本機檔案路徑來儲存資料庫的類別：

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

`TodoItemDatabase`的建構函式，做為引數的資料庫檔案的路徑，如下所示：

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

優點將資料庫公開為單一子句可讓您不會建立單一資料庫連接保持開啟應用程式時執行，因此可避免開啟和關閉資料庫檔案每次的費用資料庫作業就會執行。

其餘部分`TodoItemDatabase`類別包含執行跨平台的 SQLite 查詢。 範例查詢程式碼如下所示 (在語法上的更多詳細資料可在[利用 Xamarin.iOS 的使用 SQLite.NET](~/ios/data-cloud/data/using-sqlite-orm.md)。

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
> 使用非同步 SQLite.Net API 的優點是該作業會移至背景執行緒的資料庫。 此外，還有不需要撰寫額外的並行處理程式碼，因為 API 會負責處理它。

## <a name="summary"></a>總結

Xamarin.Forms 可支援使用 SQLite 資料庫引擎，讓您能夠載入，並將物件儲存在共用程式碼的資料庫導向應用程式。

這篇文章著重**存取**使用 Xamarin.Forms 為 SQLite 資料庫。 如需有關使用 SQLite.Net 本身的詳細資訊，請參閱[在 Android 上的 SQLite.NET](~/android/data-cloud/data-access/using-sqlite-orm.md)或是[SQLite.NET 在 iOS 上的](~/ios/data-cloud/data/using-sqlite-orm.md)文件。

## <a name="related-links"></a>相關連結

- [待辦事項範例](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)

