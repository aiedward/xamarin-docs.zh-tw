---
title: ''
description: ''
zone_pivot_groups: ''
ms.topic: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2864c7edabde18faec06c2c42042168d3e6c9671
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139798"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>將資料儲存在本機 SQLite.NET 資料庫中

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)

在本快速入門中，您將了解如何：

- 使用 NuGet 套件管理員將 NuGet 套件新增至專案。
- 將資料儲存在本機 SQLite.NET 資料庫。

快速入門會逐步說明如何將資料儲存在本機 SQLite.NET 資料庫中。 最終的應用程式如下所示：

[![](database-images/screenshots1-sml.png "Notes Page")](database-images/screenshots1.png#lightbox "Notes Page")
[![](database-images/screenshots2-sml.png "Note Entry Page")](database-images/screenshots2.png#lightbox "Note Entry Page")

## <a name="prerequisites"></a>Prerequisites

您應該先成功完成[先前的快速入門](multi-page.md)，再嘗試本快速入門。 或者，下載[先前的快速入門範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)，並使用此範例作為本快速入門的起點。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新應用程式

1. 啟動 Visual Studio 並開啟 Notes 方案。

2. 在 [方案總管]**** 中選取 **Notes** 專案，按一下滑鼠右鍵，然後選取 [管理 NuGet 套件]****：

    ![](database-images/vs/add-nuget-packages.png "Add NuGet Packages")    

3. 在 [NuGet 套件管理員]**** 中選取 [瀏覽]**** 索引標籤，搜尋 **sqlite-net-pcl** NuGet 套件並加以選取，然後按一下 [安裝]**** 按鈕，將其新增至專案：

    ![](database-images/vs/add-package.png "Add Package")

    > [!NOTE]
    > 有許多名稱類似的 NuGet 套件。 正確的套件有下列屬性：
    > - **作者：** Frank Krueger
    > - **識別碼：** sqlite-net-pcl
    > - **NuGet 連結：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > 不論套件名稱為何，此 NuGet 套件可用於 .NET Standard 專案。

    此套件會用來將資料庫作業併入應用程式。

4. 在 [方案總管]**** 的 **Notes** 專案中，開啟 **Models** 資料夾中的 **Note.cs**，並以下列程式碼取代現有程式碼：

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

    按下 **CTRL+S**，將變更儲存到 **Note.cs**，然後關閉檔案。

    > [!WARNING]
    > 此時若嘗試建置應用程式，將導致會在後續步驟中修正的錯誤。

5. 在 [方案總管]**** 中，將名為 **Data** 的新資料夾新增至 **Notes** 專案。

6. 在 [方案總管]**** 的 **Notes** 專案中，將名為 **NoteDatabase** 的新類別新增到 **Data** 資料夾。

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

    此類別包含的程式碼可建立資料庫、在資料庫中讀取和寫入資料，以及刪除資料庫的資料。 此程式碼會使用非同步 SQLite.Net API，以將資料庫作業移至背景執行緒。 此外，`NoteDatabase` 建構函式會採用資料庫檔案的路徑作為引數。 此路徑會由下一個步驟中的 `App` 類別提供。

    按下 **CTRL+S** 以將變更儲存到 **NoteDatabase.cs**，然後關閉檔案。

    > [!WARNING]
    > 此時若嘗試建置應用程式，將導致會在後續步驟中修正的錯誤。

8. 在 [方案總管]**** 的 **Notes** 專案中，按兩下 **App.xaml.cs** 以開啟此檔案。 將現有程式碼取代成下列程式碼：

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

    這段程式碼會定義 `Database` 屬性，其可建立新的 `NoteDatabase` 執行個體作為 singleton，將資料庫的檔案名稱當作引數傳入 `NoteDatabase` 建構函式。 將資料庫公開為唯一資料庫的優點為，所建立的單一資料庫連線會在應用程式執行時保持開啟，因此可避免每次執行資料庫作業時開啟和關閉資料庫檔案的費用。

    按下 **CTRL+S** 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

    > [!WARNING]
    > 此時若嘗試建置應用程式，將導致會在後續步驟中修正的錯誤。

9. 在 [方案總管]**** 的 **Notes** 專案中，按兩下 **NotesPage.xaml.cs** 以開啟此檔案。 然後以下列程式碼取代 `OnAppearing` 方法：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    此程式碼會將 [`ListView`](xref:Xamarin.Forms.ListView) 儲存在資料庫中的任何附注填入其中。

    按下 **CTRL+S** 將變更儲存到 **NotesPage.xaml.cs**，然後關閉檔案。

    > [!WARNING]
    > 此時若嘗試建置應用程式，將導致會在後續步驟中修正的錯誤。

10. 在 [方案總管]**** 中，按兩下 **NoteEntryPage.xaml.cs** 以開啟此檔案。 然後以下列程式碼取代 `OnSaveButtonClicked` 和 `OnDeleteButtonClicked` 方法：

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

      會 `NoteEntryPage` `Note` 在頁面的中，儲存代表單一便箋的實例 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 執行 `OnSaveButtonClicked` 事件處理常式，即會將 `Note` 執行個體儲存至資料庫，且應用程式會巡覽回上一頁。 執行 `OnDeleteButtonClicked` 事件處理常式，即會從資料庫刪除 `Note` 執行個體，且應用程式會巡覽回上一頁。

      按下 **CTRL+S** 將變更儲存到 **NoteEntryPage.xaml.cs**，然後關閉檔案。

11. 在每個平台上建置並執行專案。 如需詳細資訊，請參閱[建置快速入門](single-page.md#building-the-quickstart)。

    在 **NotesPage** 上按 **+** 按鈕，以巡覽至 **NoteEntryPage** 並輸入備註。 儲存備註之後，應用程式會巡覽回 **NotesPage**。

    輸入多個長短不一的備註，以觀察應用程式的行為。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新應用程式

1. 啟動 Visual Studio for Mac 並開啟 Notes 專案。

2. 在 **Solution Pad** 中，選取 **Notes** 專案，按一下滑鼠右鍵，然後選取 [新增] > [新增 NuGet 套件]****：

    ![](database-images/vsmac/add-nuget-packages.png "Add NuGet Packages")    

3. 在 [新增套件]**** 視窗中，搜尋 **sqlite-net-pcl** NuGet 套件並加以選取，然後按一下 [新增套件]**** 按鈕，將其新增至專案：

    ![](database-images/vsmac/add-package.png "Add Package")

    > [!NOTE]
    > 有許多名稱類似的 NuGet 套件。 正確的套件有下列屬性：
    > - **作者：** Frank Krueger
    > - **識別碼：** sqlite-net-pcl
    > - **NuGet 連結：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > 不論套件名稱為何，此 NuGet 套件可用於 .NET Standard 專案。

    此套件會用來將資料庫作業併入應用程式。

4. 在 **Solution Pad** 的 **Notes** 專案中，開啟 **Models** 資料夾中的 **Note.cs**，並以下列程式碼取代現有程式碼：

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

    選擇 [檔案] > [儲存]**** (或按下 **&#8984; + S**)，將變更儲存到 **Note.cs**，然後關閉檔案。

    > [!WARNING]
    > 此時若嘗試建置應用程式，將導致會在後續步驟中修正的錯誤。

5. 在 **Solution Pad** 中，將名為 **Data** 的新資料夾新增至 **Notes** 專案。

6. 在 **Solution Pad** 的 **Notes** 專案中，將名為 **NoteDatabase** 的新類別新增到 **Data** 資料夾。

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

    此類別包含的程式碼可建立資料庫、在資料庫中讀取和寫入資料，以及刪除資料庫的資料。 此程式碼會使用非同步 SQLite.Net API，以將資料庫作業移至背景執行緒。 此外，`NoteDatabase` 建構函式會採用資料庫檔案的路徑作為引數。 此路徑會由下一個步驟中的 `App` 類別提供。

    選擇 [檔案] > [儲存]**** (或按下 **&#8984; + S**) 將變更儲存到 **NoteDatabase.cs**，然後關閉檔案。

    > [!WARNING]
    > 此時若嘗試建置應用程式，將導致會在後續步驟中修正的錯誤。

8. 在 **Solution Pad** 的 **Notes** 專案中，按兩下 **App.xaml.cs** 以開啟此檔案。 將現有程式碼取代成下列程式碼：

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

    這段程式碼會定義 `Database` 屬性，其可建立新的 `NoteDatabase` 執行個體作為 singleton，將資料庫的檔案名稱當作引數傳入 `NoteDatabase` 建構函式。 將資料庫公開為唯一資料庫的優點為，所建立的單一資料庫連線會在應用程式執行時保持開啟，因此可避免每次執行資料庫作業時開啟和關閉資料庫檔案的費用。

    選擇 [檔案] > [儲存]**** (或按下 **&#8984; + S**) 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

    > [!WARNING]
    > 此時若嘗試建置應用程式，將導致會在後續步驟中修正的錯誤。

9. 在 **Solution Pad** 的 **Notes** 專案中，按兩下 **NotesPage.xaml.cs** 以開啟此檔案。 然後以下列程式碼取代 `OnAppearing` 方法：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    此程式碼會將 [`ListView`](xref:Xamarin.Forms.ListView) 儲存在資料庫中的任何附注填入其中。

    選擇 [檔案] > [儲存]**** (或按下 **&#8984; + S**) 將變更儲存到 **NotesPage.xaml.cs**，然後關閉檔案。

    > [!WARNING]
    > 此時若嘗試建置應用程式，將導致會在後續步驟中修正的錯誤。

10. 在 **Solution Pad** 中，按兩下 **NoteEntryPage.xaml.cs** 以開啟此檔案。 然後以下列程式碼取代 `OnSaveButtonClicked` 和 `OnDeleteButtonClicked` 方法：

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

      會 `NoteEntryPage` `Note` 在頁面的中，儲存代表單一便箋的實例 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 執行 `OnSaveButtonClicked` 事件處理常式，即會將 `Note` 執行個體儲存至資料庫，且應用程式會巡覽回上一頁。 執行 `OnDeleteButtonClicked` 事件處理常式，即會從資料庫刪除 `Note` 執行個體，且應用程式會巡覽回上一頁。

      選擇 [檔案] > [儲存]**** (或按下 **&#8984; + S**) 將變更儲存到 **NoteEntryPage.xaml.cs**，然後關閉檔案。

11. 在每個平台上建置並執行專案。 如需詳細資訊，請參閱[建置快速入門](single-page.md#building-the-quickstart)。

    在 **NotesPage** 上按 **+** 按鈕，以巡覽至 **NoteEntryPage** 並輸入備註。 儲存備註之後，應用程式會巡覽回 **NotesPage**。

    輸入多個長短不一的備註，以觀察應用程式的行為。

::: zone-end

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

- 使用 NuGet 套件管理員將 NuGet 套件新增至專案。
- 將資料儲存在本機 SQLite.NET 資料庫。

若要使用 XAML 樣式設定應用程式樣式，請繼續下一個快速入門。

> [!div class="nextstepaction"]
> [下一步](styling.md)

## <a name="related-links"></a>相關連結

- [Notes (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)
- [Xamarin.Forms快速入門深入探討](deepdive.md)
