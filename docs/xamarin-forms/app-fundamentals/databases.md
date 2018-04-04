---
title: 本機資料庫
description: Xamarin.Forms 支援資料庫驅動的應用程式使用 SQLite 資料庫引擎，讓您能夠載入和儲存在共用程式碼中的物件。 本文說明如何讀取和寫入資料至本機 SQLite 資料庫使用 SQLite.Net Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2017
ms.openlocfilehash: 95c5f482e1bf3e55fa4c6fef18b1dbe6274f33e8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="local-databases"></a>本機資料庫

_Xamarin.Forms 支援資料庫驅動的應用程式使用 SQLite 資料庫引擎，讓您能夠載入和儲存在共用程式碼中的物件。本文說明如何讀取和寫入資料至本機 SQLite 資料庫使用 SQLite.Net Xamarin.Forms 應用程式。_

## <a name="overview"></a>總覽

Xamarin.Forms 應用程式可以使用[SQLite.NET PCL NuGet](https://www.nuget.org/packages/sqlite-net-pcl/)封裝加入到資料庫作業藉由參考共用程式碼`SQLite`出貨 NuGet 中的類別。 資料庫作業可以定義在可攜式類別程式庫 (PCL) 專案中的 Xamarin.Forms 方案時，以傳回路徑來儲存資料庫平台專屬專案中。

隨附[範例應用程式](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo)是簡單的待辦事項清單應用程式。 下列螢幕擷取畫面顯示此範例顯示每個平台上的方式：

[![Xamarin.Forms 資料庫範例螢幕擷取畫面](databases-images/todo-list-sml.png "TodoList 第一個頁面的螢幕擷取畫面")](databases-images/todo-list.png#lightbox "TodoList 第一個頁面的螢幕擷取畫面") [ ![Xamarin.Forms 資料庫範例螢幕擷取畫面](databases-images/todo-list-sml.png "TodoList 第一個頁面的螢幕擷取畫面")](databases-images/todo-list.png#lightbox "TodoList 第一個頁面的螢幕擷取畫面")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>使用 SQLite

本節說明如何將 SQLite.Net NuGet 封裝，Xamarin.Forms 方案，撰寫方法來執行資料庫作業，並使用[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)來判斷要將資料庫儲存在每個平台的位置。

<a name="XamarinForms_PCL_Project" />

### <a name="xamarinsforms-pcl-project"></a>Xamarins.Forms PCL 專案，

若要新增 SQLite 支援 Xamarin.Forms PCL 專案，請使用 NuGet 的搜尋功能來尋找**sqlite net pcl**並安裝套件：

![](databases-images/vs2017-sqlite-pcl-nuget.png "新增 NuGet SQLite.NET PCL 封裝")

有具有類似名稱的 NuGet 封裝，正確的封裝具有下列屬性：

- **所建立：** Frank A.Krueger
- **Id:** sqlite net pcl
- **NuGet 連結：** [sqlite net pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

加入參考之後撰寫的介面，以特定平台功能，也就是判斷資料庫檔案的位置。 範例中使用的介面會定義單一方法：

```csharp
public interface IFileHelper
{
  string GetLocalFilePath(string filename);
}
```

一旦已定義介面，使用[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)以取得實作，並取得本機檔案路徑 （請注意，這個介面具有尚未實作）。 下列程式碼取得實作`App.Database`屬性：

```csharp
static TodoItemDatabase database;

public static TodoItemDatabase Database
{
  get
  {
    if (database == null)
    {
      database = new TodoItemDatabase(DependencyService.Get<IFileHelper>().GetLocalFilePath("TodoSQLite.db3"));
    }
    return database;
  }
}
```

`TodoItemDatabase`建構函式如下所示：

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

這個方法會建立單一資料庫連接，保持開啟，而應用程式執行，因此可避免開啟和關閉資料庫檔案每次執行資料庫作業的費用。

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

所有跨所有平台共用的 PCL 專案中撰寫資料存取程式碼。 只取得資料庫中的本機檔案路徑需要平台專屬的程式碼，下列各節中所述。

<a name="PCL_iOS" />

### <a name="ios-project"></a>iOS 的專案

若要設定的 iOS 應用程式，將相同的 NuGet 封裝加入至 iOS 的專案使用*NuGet*視窗：

![](databases-images/vsmac-sqlite-nuget.png "新增 NuGet SQLite.NET PCL 封裝")

只有所需的程式碼是`IFileHelper`決定的資料檔案路徑的實作。 下列程式碼 SQLite 資料庫將檔案放在**程式庫/資料庫**內沙箱應用程式的資料夾。 請參閱[iOS 檔案系統的處理](~/ios/app-fundamentals/file-system.md)文件上的不同目錄中可供儲存體的詳細資訊。

```csharp
[assembly: Dependency(typeof(FileHelper))]
namespace Todo.iOS
{
    public class FileHelper : IFileHelper
    {
        public string GetLocalFilePath(string filename)
        {
            string docFolder = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
            string libFolder = Path.Combine(docFolder, "..", "Library", "Databases");

            if (!Directory.Exists(libFolder))
            {
                Directory.CreateDirectory(libFolder);
            }

            return Path.Combine(libFolder, filename);
        }
    }
}
```

請注意，程式碼包含`assembly:Dependency`屬性，讓這項實作會找到`DependencyService`。

<a name="PCL_Android" />

### <a name="android-project"></a>Android 專案

若要設定 Android 應用程式，將相同的 NuGet 封裝加入至 Android 專案使用*NuGet*視窗：

![](databases-images/vsmac-sqlite-nuget.png "新增 NuGet SQLite.NET PCL 封裝")

只有所需的程式碼加入此參考之後, 是`IFileHelper`決定的資料檔案路徑的實作。

```csharp
[assembly: Dependency(typeof(FileHelper))]
namespace Todo.Droid
{
    public class FileHelper : IFileHelper
    {
        public string GetLocalFilePath(string filename)
        {
            string path = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
            return Path.Combine(path, filename);
        }
    }
}
```

<a name="PCL_UWP" />

### <a name="windows-10-universal-windows-platform-uwp"></a>Windows 10 通用 Windows 平台 (UWP)

若要設定 UWP 應用程式，將相同的 NuGet 封裝加入至 UWP 專案使用*NuGet*視窗：

![](databases-images/vs2017-sqlite-uwp-nuget.png "新增 NuGet SQLite.NET PCL 封裝")

加入參考後，實作`IFileHelper`介面使用平台專屬`Windows.Storage`API 來判斷資料檔案路徑。

```csharp
using Windows.Storage;
...

[assembly: Dependency(typeof(FileHelper))]
namespace Todo.UWP
{
    public class FileHelper : IFileHelper
    {
        public string GetLocalFilePath(string filename)
        {
            return Path.Combine(ApplicationData.Current.LocalFolder.Path, filename);
        }
    }
}

```

## <a name="summary"></a>總結

Xamarin.Forms 支援資料庫驅動的應用程式使用 SQLite 資料庫引擎，讓您能夠載入和儲存在共用程式碼中的物件。

本文著重於**存取**使用 Xamarin.Forms SQLite 資料庫。 如需使用 SQLite.Net 本身的詳細資訊，請參閱[資料存取： 使用 SQLite.NET](~/cross-platform/app-fundamentals/index.md)文件。 大多數 SQLite.Net 程式碼是可共用跨所有平台。只設定 SQLite 資料庫檔案的位置需要平台專屬的功能。


## <a name="related-links"></a>相關連結

- [Todo 範例](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [資料庫活頁簿](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/database/database.workbook)
