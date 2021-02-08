---
title: 將資料儲存在本機 SQLite.NET 資料庫中
description: 本文說明如何從 Shell 應用程式將資料儲存在本機 SQLite.NET 資料庫中 Xamarin.Forms 。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: F669CE4E-1E1B-409F-BC1C-8364633EB7EF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.custom: contperf-fy21q3
ms.date: 01/28/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cfc479e6211692f3de7d78e2fd52cd80a2630660
ms.sourcegitcommit: 1f391667869a4541dd9b42d78862dc01d69ed160
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2021
ms.locfileid: "99818352"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>將資料儲存在本機 SQLite.NET 資料庫中

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)

在本快速入門中，您將了解如何：

- 將資料儲存在本機 SQLite.NET 資料庫。

本快速入門會逐步解說如何從 Shell 應用程式將資料儲存在本機 SQLite.NET 資料庫中 Xamarin.Forms 。 最終的應用程式如下所示：

[ ![ 附注頁面](database-images/screenshots1-sml.png)](database-images/screenshots1.png#lightbox) 
 [ ![ 筆記輸入頁面](database-images/screenshots2-sml.png)](database-images/screenshots2.png#lightbox)

## <a name="prerequisites"></a>必要條件

您應該先成功完成[先前的快速入門](navigation.md)，再嘗試本快速入門。 或者，下載[先前的快速入門範例](/samples/xamarin/xamarin-forms-samples/getstarted-notes-navigation/)，並使用此範例作為本快速入門的起點。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新應用程式

1. 啟動 Visual Studio 並開啟 Notes 方案。

2. 在 **方案總管** 中，以滑鼠右鍵按一下 **Notes** 方案，然後選取 [ **管理解決方案的 NuGet 套件**...]

    ![Manage NuGet Packages](database-images/vs/manage-nuget-packages.png)    

3. 在 **NuGet 封裝管理員** 中，選取 [ **流覽** ] 索引標籤，然後搜尋 **sqlite-net-pcl** NuGet 套件。

    > [!WARNING]
    > 有許多 NuGet 套件具有類似的名稱。 正確的套件有下列屬性：
    > - **作者：** SQLite-net
    > - **NuGet 連結：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > 不論套件名稱為何，此 NuGet 套件可用於 .NET Standard 專案。

    在 **NuGet 封裝管理員** 中，選取正確的 **sqlite-net-pcl** 套件，核取 [ **專案** ] 核取方塊，然後按一下 [ **安裝** ] 按鈕，將它新增至方案：

    ![選取 sqlite-net-pcl](database-images/vs/select-package.png)

    此封裝將用來將資料庫作業併入應用程式中，並加入至方案中的每個專案。

    關閉 **NuGet 封裝管理員**。

4. 在 [方案總管] 的 **Notes** 專案中，開啟 **Models** 資料夾中的 **Note.cs**，並以下列程式碼取代現有程式碼：

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

    此類別會定義 `Note` 模型，以儲存應用程式中每個備註的相關資料。 `ID` 屬性會以 `PrimaryKey` 和 `AutoIncrement` 屬性標記，以確保 SQLite.NET 資料庫中每個 `Note` 執行個體都具有 SQLite.NET 提供的唯一識別碼。

    按 **CTRL + S**，將變更儲存至 **Note.cs** 。

    > [!WARNING]
    > 應用程式目前不會因為將在後續步驟中修正的錯誤而建立。

5. 在 [方案總管] 中，將名為 **Data** 的新資料夾新增至 **Notes** 專案。

6. 在 [方案總管] 的 **Notes** 專案中，將名為 **NoteDatabase** 的新類別新增到 **Data** 資料夾。

7. 在 **NoteDatabase.cs** 中，以下列程式碼取代現有程式碼：

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection database;

            public NoteDatabase(string dbPath)
            {
                database = new SQLiteAsyncConnection(dbPath);
                database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                //Get all notes.
                return database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                // Get a specific note.
                return database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    // Update an existing note.
                    return database.UpdateAsync(note);
                }
                else
                {
                    // Save a new note.
                    return database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                // Delete a note.
                return database.DeleteAsync(note);
            }
        }
    }
    ```

    此類別包含的程式碼可建立資料庫、在資料庫中讀取和寫入資料，以及刪除資料庫的資料。 此程式碼會使用非同步 SQLite.Net API，以將資料庫作業移至背景執行緒。 此外，`NoteDatabase` 建構函式會採用資料庫檔案的路徑作為引數。 此路徑會由下一個步驟中的 `App` 類別提供。

    按 **CTRL + S**，將變更儲存至 **NoteDatabase.cs** 。  

    > [!WARNING]
    > 應用程式目前不會因為將在後續步驟中修正的錯誤而建立。

8. 在 **方案總管** 的 **Notes** 專案中，展開 [ **app.xaml** ]，然後按兩下 [ **App.xaml.cs** ] 將它開啟。 將現有程式碼取代成下列程式碼：

    ```csharp
    using System;
    using System.IO;
    using Notes.Data;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            // Create the database connection as a singleton.
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
                MainPage = new AppShell();
            }

            protected override void OnStart()
            {
            }

            protected override void OnSleep()
            {
            }

            protected override void OnResume()
            {
            }
        }
    }
    ```

    這段程式碼會定義 `Database` 屬性，其可建立新的 `NoteDatabase` 執行個體作為 singleton，將資料庫的檔案名稱當作引數傳入 `NoteDatabase` 建構函式。 將資料庫公開為唯一資料庫的優點為，所建立的單一資料庫連線會在應用程式執行時保持開啟，因此可避免每次執行資料庫作業時開啟和關閉資料庫檔案的費用。

    按 **CTRL + S**，將變更儲存至 **App.xaml.cs** 。  

    > [!WARNING]
    > 應用程式目前不會因為將在後續步驟中修正的錯誤而建立。

9. 在 **方案總管** 的 **Notes** 專案中，展開 **Views** 資料夾中的 **>notespage.xaml.cs** ，然後開啟 **NotesPage.xaml.cs**。 然後以下列程式碼取代 `OnAppearing` 和 `OnSelectionChanged` 方法：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        // Retrieve all the notes from the database, and set them as the
        // data source for the CollectionView.
        collectionView.ItemsSource = await App.Database.GetNotesAsync();
    }

    async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        if (e.CurrentSelection != null)
        {
            // Navigate to the NoteEntryPage, passing the ID as a query parameter.
            Note note = (Note)e.CurrentSelection.FirstOrDefault();
            await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.ID.ToString()}");
        }
    }    
    ```    

    方法會將 `OnAppearing` [`CollectionView`](xref:Xamarin.Forms.CollectionView) 儲存在資料庫中的任何附注填入。 `OnSelectionChanged`方法會導覽至 `NoteEntryPage` ，並傳遞 `ID` 所選物件的屬性 `Note` 做為查詢參數。

    按 **CTRL + S**，將變更儲存至 **NotesPage.xaml.cs** 。  

    > [!WARNING]
    > 應用程式目前不會因為將在後續步驟中修正的錯誤而建立。

10. 在 **方案總管** 中，展開 **Views** 資料夾中的 **>noteentrypage.xaml.cs** ，然後開啟 **NoteEntryPage.xaml.cs**。 然後 `LoadNote` `OnSaveButtonClicked` 以下列程式碼取代、和 `OnDeleteButtonClicked` 方法：

      ```csharp
      async void LoadNote(string itemId)
      {
          try
          {
              int id = Convert.ToInt32(itemId);
              // Retrieve the note and set it as the BindingContext of the page.
              Note note = await App.Database.GetNoteAsync(id);
              BindingContext = note;
          }
          catch (Exception)
          {
              Console.WriteLine("Failed to load note.");
          }
      }

      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          if (!string.IsNullOrWhiteSpace(note.Text))
          {
              await App.Database.SaveNoteAsync(note);
          }

          // Navigate backwards
          await Shell.Current.GoToAsync("..");
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);

          // Navigate backwards
          await Shell.Current.GoToAsync("..");
      }
      ```    

      會 `NoteEntryPage` 使用 `LoadNote` 方法從資料庫取出附注，其識別碼會以查詢參數的形式傳遞至頁面，並將它儲存為 `Note` 頁面中的物件 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 執行 `OnSaveButtonClicked` 事件處理常式，即會將 `Note` 執行個體儲存至資料庫，且應用程式會巡覽回上一頁。 執行 `OnDeleteButtonClicked` 事件處理常式，即會從資料庫刪除 `Note` 執行個體，且應用程式會巡覽回上一頁。

      按 **CTRL + S**，將變更儲存至 **NoteEntryPage.xaml.cs** 。  

11. 在每個平台上建置並執行專案。 如需詳細資訊，請參閱[建置快速入門](app.md#building-the-quickstart)。

    在 **>notespage.xaml.cs** 上，按下 [ **新增** ] 按鈕以流覽至 **>noteentrypage.xaml.cs** 並輸入備註。 儲存備註之後，應用程式會巡覽回 **NotesPage**。

    輸入數個不同長度的附注，以觀察應用程式行為。 關閉應用程式並重新啟動，以確定您輸入的附注已儲存至資料庫。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新應用程式

1. 啟動 Visual Studio for Mac 並開啟 [附注] 方案。

2. 在 **Solution Pad** 中，以滑鼠右鍵按一下 [ **Notes** ] 方案，然後選取 [ **管理 NuGet 套件 ...**：

    ![Manage NuGet Packages](database-images/vsmac/manage-nuget-packages.png)    

3. 在 [ **管理 NuGet 封裝** ] 對話方塊中，選取 [ **流覽** ] 索引標籤，然後搜尋 **sqlite-net-pcl** NuGet 套件。

    > [!WARNING]
    > 有許多 NuGet 套件具有類似的名稱。 正確的套件有下列屬性：
    > - **作者：** SQLite-net
    > - **NuGet 連結：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > 不論套件名稱為何，此 NuGet 套件可用於 .NET Standard 專案。

    在 [ **管理 NuGet 封裝** ] 對話方塊中，選取 [ **sqlite-net-pcl** ] 套件，然後按一下 [ **新增套件** ] 按鈕將它新增至方案：

      ![選取 sqlite-net-pcl](database-images/vsmac/select-package.png)

    此套件會用來將資料庫作業併入應用程式。

4. 在 [ **選取專案** ] 對話方塊中，確定已核取每個核取方塊，然後按 [ **確定]** 按鈕：

    ![將封裝新增至所有專案](database-images/vsmac/add-package.png)

    這會將 NuGet 套件新增至方案中的每個專案。

5. 在 **Solution Pad** 的 **Notes** 專案中，開啟 **Models** 資料夾中的 **Note.cs**，並以下列程式碼取代現有程式碼：

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

    此類別會定義 `Note` 模型，以儲存應用程式中每個備註的相關資料。 `ID` 屬性會以 `PrimaryKey` 和 `AutoIncrement` 屬性標記，以確保 SQLite.NET 資料庫中每個 `Note` 執行個體都具有 SQLite.NET 提供的唯一識別碼。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 **Note.cs** 的變更。

    > [!WARNING]
    > 應用程式目前不會因為將在後續步驟中修正的錯誤而建立。

6. 在 **Solution Pad** 中，將名為 **Data** 的新資料夾新增至 **Notes** 專案。

7. 在 **Solution Pad** 的 **Notes** 專案中，將名為 **NoteDatabase** 的新類別新增到 **Data** 資料夾。

8. 在 **NoteDatabase.cs** 中，以下列程式碼取代現有程式碼：

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection database;

            public NoteDatabase(string dbPath)
            {
                database = new SQLiteAsyncConnection(dbPath);
                database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                //Get all notes.
                return database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                // Get a specific note.
                return database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    // Update an existing note.
                    return database.UpdateAsync(note);
                }
                else
                {
                    // Save a new note.
                    return database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                // Delete a note.
                return database.DeleteAsync(note);
            }
        }
    }
    ```

    此類別包含的程式碼可建立資料庫、在資料庫中讀取和寫入資料，以及刪除資料庫的資料。 此程式碼會使用非同步 SQLite.Net API，以將資料庫作業移至背景執行緒。 此外，`NoteDatabase` 建構函式會採用資料庫檔案的路徑作為引數。 此路徑會由下一個步驟中的 `App` 類別提供。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 **NoteDatabase.cs** 的變更。

    > [!WARNING]
    > 應用程式目前不會因為將在後續步驟中修正的錯誤而建立。

9. 在 [**附注**] 專案的 [ **Solution Pad**] 中，展開 [ **app.xaml** ]，然後按兩下 [ **App.xaml.cs** ] 將它開啟。 將現有程式碼取代成下列程式碼：

    ```csharp
    using System;
    using System.IO;
    using Notes.Data;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            // Create the database connection as a singleton.
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
                MainPage = new AppShell();
            }

            protected override void OnStart()
            {
            }

            protected override void OnSleep()
            {
            }

            protected override void OnResume()
            {
            }
        }
    }
    ```

    這段程式碼會定義 `Database` 屬性，其可建立新的 `NoteDatabase` 執行個體作為 singleton，將資料庫的檔案名稱當作引數傳入 `NoteDatabase` 建構函式。 將資料庫公開為唯一資料庫的優點為，所建立的單一資料庫連線會在應用程式執行時保持開啟，因此可避免每次執行資料庫作業時開啟和關閉資料庫檔案的費用。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 **App.xaml.cs** 的變更。

    > [!WARNING]
    > 應用程式目前不會因為將在後續步驟中修正的錯誤而建立。

10. 在 **Solution Pad** 的 **Notes** 專案中，展開 **Views** 資料夾中的 **>notespage.xaml.cs** ，然後開啟 **NotesPage.xaml.cs**。 然後以下列程式碼取代 `OnAppearing` 和 `OnSelectionChanged` 方法：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        // Retrieve all the notes from the database, and set them as the
        // data source for the CollectionView.
        collectionView.ItemsSource = await App.Database.GetNotesAsync();
    }

    async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        if (e.CurrentSelection != null)
        {
            // Navigate to the NoteEntryPage, passing the ID as a query parameter.
            Note note = (Note)e.CurrentSelection.FirstOrDefault();
            await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.ID.ToString()}");
        }
    }    
    ```    

    方法會將 `OnAppearing` [`CollectionView`](xref:Xamarin.Forms.CollectionView) 儲存在資料庫中的任何附注填入。 `OnSelectionChanged`方法會導覽至 `NoteEntryPage` ，並傳遞 `ID` 所選物件的屬性 `Note` 做為查詢參數。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 **NotesPage.xaml.cs** 的變更。

    > [!WARNING]
    > 應用程式目前不會因為將在後續步驟中修正的錯誤而建立。

11. 在 **Solution Pad** 中，展開 **Views** 資料夾中的 **>noteentrypage.xaml.cs** ，然後開啟 **NoteEntryPage.xaml.cs**。 然後 `LoadNote` `OnSaveButtonClicked` 以下列程式碼取代、和 `OnDeleteButtonClicked` 方法：

      ```csharp
      async void LoadNote(string itemId)
      {
          try
          {
              int id = Convert.ToInt32(itemId);
              // Retrieve the note and set it as the BindingContext of the page.
              Note note = await App.Database.GetNoteAsync(id);
              BindingContext = note;
          }
          catch (Exception)
          {
              Console.WriteLine("Failed to load note.");
          }
      }

      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          if (!string.IsNullOrWhiteSpace(note.Text))
          {
              await App.Database.SaveNoteAsync(note);
          }

          // Navigate backwards
          await Shell.Current.GoToAsync("..");
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);

          // Navigate backwards
          await Shell.Current.GoToAsync("..");
      }
      ```    

      會 `NoteEntryPage` 使用 `LoadNote` 方法從資料庫取出附注，其識別碼會以查詢參數的形式傳遞至頁面，並將它儲存為 `Note` 頁面中的物件 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 執行 `OnSaveButtonClicked` 事件處理常式，即會將 `Note` 執行個體儲存至資料庫，且應用程式會巡覽回上一頁。 執行 `OnDeleteButtonClicked` 事件處理常式，即會從資料庫刪除 `Note` 執行個體，且應用程式會巡覽回上一頁。

      選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 **NoteEntryPage.xaml.cs** 的變更。

12. 在每個平台上建置並執行專案。 如需詳細資訊，請參閱[建置快速入門](app.md#building-the-quickstart)。

    在 **>notespage.xaml.cs** 上，按下 [ **新增** ] 按鈕以流覽至 **>noteentrypage.xaml.cs** 並輸入備註。 儲存備註之後，應用程式會巡覽回 **NotesPage**。

    輸入數個不同長度的附注，以觀察應用程式行為。 關閉應用程式並重新啟動，以確定您輸入的附注已儲存至資料庫。

::: zone-end

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

- 將資料儲存在本機 SQLite.NET 資料庫。

繼續進行下一個快速入門，以使用 XAML 樣式來為應用程式進行樣式。

> [!div class="nextstepaction"]
> [下一步](styling.md)

## <a name="related-links"></a>相關連結

- [Notes (範例)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)
- [Xamarin.Forms Shell 快速入門深入探討](deepdive.md)
