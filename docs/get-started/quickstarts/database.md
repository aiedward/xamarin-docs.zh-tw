---
title: 將資料儲存在本機 SQLite.NET 資料庫中
description: 本文說明如何將資料儲存在本機 SQLite.NET 資料庫中。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 5BF901BD-FDE8-4B74-B4AB-418E81745A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 2cd4726566e73aece5d0deef90ad1feedefaa2d8
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "71249684"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>將資料儲存在本機 SQLite.NET 資料庫中

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)

在本快速入門中，您將瞭解如何：

- 使用 NuGet 套件管理員，將 NuGet 套件新增至專案。
- 將資料儲存在本機 SQLite.NET 資料庫中。

本快速入門會逐步解說如何將資料儲存在本機 SQLite.NET 資料庫中。 最終的應用程式如下所示：

[![](database-images/screenshots1-sml.png "Notes Page")](database-images/screenshots1.png#lightbox "Notes Page")
[![](database-images/screenshots2-sml.png "Note Entry Page")](database-images/screenshots2.png#lightbox "Note Entry Page")

## <a name="prerequisites"></a>Prerequisites

您應該先成功完成[先前的快速入門](multi-page.md)，再嘗試進行本快速入門。 或者，下載[先前的快速入門範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)，並使用它做為本快速入門的起點。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新應用程式

1. 啟動 Visual Studio 並開啟 [附注] 解決方案。

2. 在**方案總管**中，選取 [ **Notes** ] 專案，按一下滑鼠右鍵，然後選取 [**管理 NuGet 套件 ...** ]：

    ![](database-images/vs/add-nuget-packages.png "Add NuGet Packages")    

3. 在 [NuGet 套件管理員] 中選取 [瀏覽] 索引標籤，搜尋 **sqlite-net-pcl** NuGet 套件並加以選取，然後按一下 [安裝] 按鈕，將其新增至專案：

    ![](database-images/vs/add-package.png "Add Package")

    > [!NOTE]
    > 有許多名稱類似的 NuGet 套件。 正確的套件有下列屬性：
    > - **作者：** Frank Krueger
    > - **識別碼：** sqlite-net-pcl
    > - **NuGet 連結：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > 不論套件名稱為何，此 NuGet 套件可用於 .NET Standard 專案。

    此套件會用來將資料庫作業併入應用程式。

4. 在**方案總管**的**Notes**專案中，開啟 [**模型**] 資料夾中的**Note.cs** ，並以下列程式碼取代現有的程式碼：

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    這個類別會定義一個 `Note` 模型，以儲存應用程式中每個便箋的相關資料。 @No__t_0 屬性會以 `PrimaryKey` 和 `AutoIncrement` 屬性標示，以確保 SQLite.NET 資料庫中的每個 `Note` 實例都具有 SQLite.NET 所提供的唯一識別碼。

    按下**CTRL + S**，將變更儲存到**Note.cs** ，然後關閉檔案。

    > [!WARNING]
    > 此時嘗試建立應用程式將會導致在後續步驟中修正的錯誤。

5. 在**方案總管**中，將名為**Data**的新資料夾新增至**Notes**專案。

6. 在**方案總管**的**Notes**專案中，將名為**NoteDatabase**的新類別新增至**Data**資料夾。

7. 在**NoteDatabase.cs**中，將現有的程式碼取代為下列程式碼：

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    此類別包含用來建立資料庫、讀取資料、將資料寫入其中，以及刪除資料的程式碼。 此程式碼會使用非同步 SQLite.Net API，以將資料庫作業移至背景執行緒。 此外，`NoteDatabase` 建構函式會採用資料庫檔案的路徑作為引數。 下一個步驟中的 `App` 類別將會提供此路徑。

    按下**CTRL + S**，將變更儲存到**NoteDatabase.cs** ，然後關閉檔案。

    > [!WARNING]
    > 此時嘗試建立應用程式將會導致在後續步驟中修正的錯誤。

8. 在**方案總管**的**Notes**專案中，按兩下 [ **App.xaml.cs** ] 將其開啟。 然後將現有的程式碼取代為下列程式碼：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    這段程式碼會定義 `Database` 屬性，以建立新的 `NoteDatabase` 實例做為 singleton，將資料庫的檔案名當做引數傳入 `NoteDatabase` 的函式。 將資料庫公開為唯一資料庫的優點為，所建立的單一資料庫連線會在應用程式執行時保持開啟，因此可避免每次執行資料庫作業時開啟和關閉資料庫檔案的費用。

    按下 **CTRL+S** 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

    > [!WARNING]
    > 此時嘗試建立應用程式將會導致在後續步驟中修正的錯誤。

9. 在**方案總管**的**Notes**專案中，按兩下 [ **NotesPage.xaml.cs** ] 將其開啟。 然後使用下列程式碼取代 `OnAppearing` 方法：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    此程式碼會在[`ListView`](xref:Xamarin.Forms.ListView)中填入儲存在資料庫中的任何附注。

    按下**CTRL + S**，將變更儲存到**NotesPage.xaml.cs** ，然後關閉檔案。

    > [!WARNING]
    > 此時嘗試建立應用程式將會導致在後續步驟中修正的錯誤。

10. 在**方案總管**中，按兩下**NoteEntryPage.xaml.cs**以將其開啟。 然後使用下列程式碼取代 `OnSaveButtonClicked` 和 `OnDeleteButtonClicked` 方法：

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      @No__t_0 會在頁面的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)中儲存 `Note` 實例（代表單一便箋）。 執行 `OnSaveButtonClicked` 事件處理常式時，`Note` 實例會儲存至資料庫，而應用程式會流覽回到上一頁。 當執行 `OnDeleteButtonClicked` 事件處理常式時，`Note` 實例會從資料庫中刪除，而應用程式會流覽回到前一頁。

      按下**CTRL + S**，將變更儲存到**NoteEntryPage.xaml.cs** ，然後關閉檔案。

11. 在每個平臺上建立並執行專案。 如需詳細資訊，請參閱[建立快速入門](single-page.md#building-the-quickstart)。

    在 [ **NotesPage** ] 上，按 [ **+** ] 按鈕以流覽至**NoteEntryPage**並輸入附注。 儲存便箋之後，應用程式會流覽回**NotesPage**。

    輸入一些不同長度的附注，以觀察應用程式行為。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新應用程式

1. 啟動 Visual Studio for Mac 並開啟 [Notes] 專案。

2. 在 [ **Solution Pad**中，選取 [ **Notes** ] 專案，按一下滑鼠右鍵，然後選取 [**新增] > 新增 NuGet 套件 ...** ]：

    ![](database-images/vsmac/add-nuget-packages.png "Add NuGet Packages")    

3. 在 [新增套件] 視窗中，搜尋 **sqlite-net-pcl** NuGet 套件並加以選取，然後按一下 [新增套件] 按鈕，將其新增至專案：

    ![](database-images/vsmac/add-package.png "Add Package")

    > [!NOTE]
    > 有許多名稱類似的 NuGet 套件。 正確的套件有下列屬性：
    > - **作者：** Frank Krueger
    > - **識別碼：** sqlite-net-pcl
    > - **NuGet 連結：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > 不論套件名稱為何，此 NuGet 套件可用於 .NET Standard 專案。

    此套件會用來將資料庫作業併入應用程式。

4. 在**Solution Pad**的**Notes**專案中，開啟 [**模型**] 資料夾中的**Note.cs** ，並以下列程式碼取代現有的程式碼：

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    這個類別會定義一個 `Note` 模型，以儲存應用程式中每個便箋的相關資料。 @No__t_0 屬性會以 `PrimaryKey` 和 `AutoIncrement` 屬性標示，以確保 SQLite.NET 資料庫中的每個 `Note` 實例都具有 SQLite.NET 所提供的唯一識別碼。

    選擇 [檔案] **> [儲存**] （或按 **&#8984; + S**），將變更儲存到**Note.cs** ，然後關閉檔案。

    > [!WARNING]
    > 此時嘗試建立應用程式將會導致在後續步驟中修正的錯誤。

5. 在  **Solution Pad**中，將名為**Data**的新資料夾新增至**Notes**專案。

6. 在**Solution Pad**的**Notes**專案中，將名為**NoteDatabase**的新類別新增至**Data**資料夾。

7. 在**NoteDatabase.cs**中，將現有的程式碼取代為下列程式碼：

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    此類別包含用來建立資料庫、讀取資料、將資料寫入其中，以及刪除資料的程式碼。 此程式碼會使用非同步 SQLite.Net API，以將資料庫作業移至背景執行緒。 此外，`NoteDatabase` 建構函式會採用資料庫檔案的路徑作為引數。 下一個步驟中的 `App` 類別將會提供此路徑。

    選擇 [檔案] **> [儲存**] （或按 **&#8984; + S**），將變更儲存到**NoteDatabase.cs** ，然後關閉檔案。

    > [!WARNING]
    > 此時嘗試建立應用程式將會導致在後續步驟中修正的錯誤。

8. 在  **Solution Pad**的**Notes**專案中，按兩下  **App.xaml.cs**  將其開啟。 然後將現有的程式碼取代為下列程式碼：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    這段程式碼會定義 `Database` 屬性，以建立新的 `NoteDatabase` 實例做為 singleton，將資料庫的檔案名當做引數傳入 `NoteDatabase` 的函式。 將資料庫公開為唯一資料庫的優點為，所建立的單一資料庫連線會在應用程式執行時保持開啟，因此可避免每次執行資料庫作業時開啟和關閉資料庫檔案的費用。

    選擇 [檔案] > [儲存] (或按下 **&#8984; + S**) 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

    > [!WARNING]
    > 此時嘗試建立應用程式將會導致在後續步驟中修正的錯誤。

9. 在  **Solution Pad**的**Notes**專案中，按兩下  **NotesPage.xaml.cs**  將其開啟。 然後使用下列程式碼取代 `OnAppearing` 方法：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    此程式碼會在[`ListView`](xref:Xamarin.Forms.ListView)中填入儲存在資料庫中的任何附注。

    選擇 [檔案] **> [儲存**] （或按 **&#8984; + S**），將變更儲存到**NotesPage.xaml.cs** ，然後關閉檔案。

    > [!WARNING]
    > 此時嘗試建立應用程式將會導致在後續步驟中修正的錯誤。

10. 在  **Solution Pad**中，按兩下**NoteEntryPage.xaml.cs**以將其開啟。 然後使用下列程式碼取代 `OnSaveButtonClicked` 和 `OnDeleteButtonClicked` 方法：

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      @No__t_0 會在頁面的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)中儲存 `Note` 實例（代表單一便箋）。 執行 `OnSaveButtonClicked` 事件處理常式時，`Note` 實例會儲存至資料庫，而應用程式會流覽回到上一頁。 當執行 `OnDeleteButtonClicked` 事件處理常式時，`Note` 實例會從資料庫中刪除，而應用程式會流覽回到前一頁。

      選擇 [檔案] **> [儲存**] （或按 **&#8984; + S**），將變更儲存到**NoteEntryPage.xaml.cs** ，然後關閉檔案。

11. 在每個平臺上建立並執行專案。 如需詳細資訊，請參閱[建立快速入門](single-page.md#building-the-quickstart)。

    在 [ **NotesPage** ] 上，按 [ **+** ] 按鈕以流覽至**NoteEntryPage**並輸入附注。 儲存便箋之後，應用程式會流覽回**NotesPage**。

    輸入一些不同長度的附注，以觀察應用程式行為。

::: zone-end

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已瞭解如何：

- 使用 NuGet 套件管理員，將 NuGet 套件新增至專案。
- 將資料儲存在本機 SQLite.NET 資料庫中。

若要使用 XAML 樣式將應用程式設為樣式，請繼續進行下一個快速入門。

> [!div class="nextstepaction"]
> [下一步](styling.md)

## <a name="related-links"></a>相關連結

- [Notes (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)
- [Xamarin. 表單快速入門深入探討](deepdive.md)
