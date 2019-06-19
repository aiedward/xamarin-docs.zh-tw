---
title: 將資料儲存在本機 SQLite.NET 資料庫中
description: 這篇文章說明如何將資料儲存在本機的 SQLite.NET 資料庫。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 5BF901BD-FDE8-4B74-B4AB-418E81745A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: ebf0f21ed57b7d436721018abb2dca329b56baa4
ms.sourcegitcommit: 215b507b2e5a44bb023abc2c804c824b1a6190d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67194965"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>將資料儲存在本機的 SQLite.NET 資料庫

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)

在本快速入門中，您將了解如何：

- 將 NuGet 套件新增至專案中使用 NuGet 套件管理員。
- SQLite.NET 資料庫中的本機儲存資料。

快速入門逐步解說如何將資料儲存在本機的 SQLite.NET 資料庫。 最終的應用程式如下所示：

[![](database-images/screenshots1-sml.png "備忘稿")](database-images/screenshots1.png#lightbox "備忘稿")
[![](database-images/screenshots2-sml.png "附註項目頁面")](database-images/screenshots2.png#lightbox "附註項目頁面")

### <a name="prerequisites"></a>必要條件

您應該已成功完成[先前的快速入門](multi-page.md)之前嘗試本快速入門。 或者，下載[先前的快速入門範例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/)並使用它做為起點，在本快速入門。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新應用程式

1. 啟動 Visual Studio，並開啟備忘稿方案。

2. 在 [**方案總管] 中**，選取**備忘稿**專案，以滑鼠右鍵按一下並選取**管理 NuGet 套件...** :

    ![](database-images/vs/add-nuget-packages.png "新增 NuGet 套件")    

3. 在 [NuGet 套件管理員]  中選取 [瀏覽]  索引標籤，搜尋 **sqlite-net-pcl** NuGet 套件並加以選取，然後按一下 [安裝]  按鈕，將其新增至專案：

    ![](database-images/vs/add-package.png "新增套件")

    > [!NOTE]
    > 有許多名稱類似的 NuGet 套件。 正確的套件有下列屬性：
    > - **作者：** Frank A. Krueger
    > - **識別碼：** sqlite-net-pcl
    > - **NuGet 連結：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > 不論套件名稱為何，此 NuGet 套件可用於 .NET Standard 專案。

    此套件會用來將資料庫作業併入應用程式。

4. 在 [**方案總管] 中**，請在**備忘稿**專案中，開啟**Note.cs**中**模型**資料夾，並將現有程式碼下列程式碼：

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

    這個類別會定義`Note`會儲存應用程式中的每一個筆記相關資料的模型。 `ID`屬性標記著`PrimaryKey`並`AutoIncrement`屬性，以確保每個`Note`SQLite.NET 資料庫中的執行個體都會有 SQLite.NET 所提供的唯一識別碼。

    變更儲存到**Note.cs**藉由按下**CTRL + S**，並關閉檔案。

    > [!WARNING]
    > 嘗試建置應用程式現在將會產生將在後續步驟中修正的錯誤。

5. 在 [**方案總管] 中**，加入新的資料夾，名為**資料**來**備忘稿**專案。

6. 中**方案總管**，請在**備忘稿**專案，加入新的類別，名為**NoteDatabase**至**資料**資料夾。

7. 在  **NoteDatabase.cs**，以下列程式碼取代現有的程式碼：

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

    這個類別包含程式碼，以建立資料庫、 資料讀取、 寫入資料，和刪除資料。 此程式碼會使用非同步 SQLite.Net API，以將資料庫作業移至背景執行緒。 此外，`NoteDatabase` 建構函式會採用資料庫檔案的路徑作為引數。 此路徑會由`App`下一個步驟中的類別。

    變更儲存到**NoteDatabase.cs**藉由按下**CTRL + S**，並關閉檔案。

    > [!WARNING]
    > 嘗試建置應用程式現在將會產生將在後續步驟中修正的錯誤。

8. 在 [**方案總管] 中**，請在**備忘稿**專案中，按兩下**App.xaml.cs**加以開啟。 然後以下列程式碼取代現有的程式碼：

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
            //...
        }
    }
    ```

    此程式碼定義`Database`建立新的屬性`NoteDatabase`為單一性，做為引數傳入的資料庫檔名的執行個體`NoteDatabase`建構函式。 將資料庫公開為唯一資料庫的優點為，所建立的單一資料庫連線會在應用程式執行時保持開啟，因此可避免每次執行資料庫作業時開啟和關閉資料庫檔案的費用。

    按下 **CTRL+S** 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

    > [!WARNING]
    > 嘗試建置應用程式現在將會產生將在後續步驟中修正的錯誤。

9. 在 [**方案總管] 中**，請在**備忘稿**專案中，按兩下**NotesPage.xaml.cs**加以開啟。 然後取代`OnAppearing`為下列程式碼的方法：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    此程式碼會填入[ `ListView` ](xref:Xamarin.Forms.ListView)與儲存在資料庫中的任何附註。

    變更儲存到**NotesPage.xaml.cs**藉由按下**CTRL + S**，並關閉檔案。

    > [!WARNING]
    > 嘗試建置應用程式現在將會產生將在後續步驟中修正的錯誤。

10. 在 [**方案總管] 中**，按兩下**NoteEntryPage.xaml.cs**加以開啟。 然後取代`OnSaveButtonClicked`和`OnDeleteButtonClicked`為下列程式碼的方法：

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

      `NoteEntryPage`儲存`Note`執行個體，代表單一附註，這[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的頁面。 當`OnSaveButtonClicked`事件處理常式會執行，`Note`執行個體儲存至資料庫和應用程式向後巡覽至上一頁。 當`OnDeleteButtonClicked`事件處理常式會執行，`Note`從資料庫刪除執行個體和應用程式向後巡覽至上一頁。

      變更儲存到**NoteEntryPage.xaml.cs**藉由按下**CTRL + S**，並關閉檔案。

11. 建置並執行專案，每個平台。 如需詳細資訊，請參閱 <<c0> [ 建置快速入門](single-page.md#building-the-quickstart)。

    在上**NotesPage**按下 **+** 按鈕，巡覽至**NoteEntryPage**和輸入的附註。 儲存記事應用程式將會瀏覽回到之後**NotesPage**。

    輸入資訊，以觀察應用程式行為的不同長度的數的字。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新應用程式

1. 啟動 Visual Studio for Mac，並開啟備忘稿專案。

2. 在  **Solution Pad**，選取**備忘稿**專案，以滑鼠右鍵按一下並選取**新增 > 新增 NuGet 套件...** :

    ![](database-images/vsmac/add-nuget-packages.png "新增 NuGet 套件")    

3. 在 [新增套件]  視窗中，搜尋 **sqlite-net-pcl** NuGet 套件並加以選取，然後按一下 [新增套件]  按鈕，將其新增至專案：

    ![](database-images/vsmac/add-package.png "新增套件")

    > [!NOTE]
    > 有許多名稱類似的 NuGet 套件。 正確的套件有下列屬性：
    > - **作者：** Frank A. Krueger
    > - **識別碼：** sqlite-net-pcl
    > - **NuGet 連結：** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > 不論套件名稱為何，此 NuGet 套件可用於 .NET Standard 專案。

    此套件會用來將資料庫作業併入應用程式。

4. 在  **Solution Pad**，請在**備忘稿**專案中，開啟**Note.cs**中**模型**資料夾，並將現有的程式碼，以下列程式碼：

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

    這個類別會定義`Note`會儲存應用程式中的每一個筆記相關資料的模型。 `ID`屬性標記著`PrimaryKey`並`AutoIncrement`屬性，以確保每個`Note`SQLite.NET 資料庫中的執行個體都會有 SQLite.NET 所提供的唯一識別碼。

    變更儲存到**Note.cs**選擇**檔案 > 儲存**(或按下 **&#8984; + S**)，然後關閉檔案。

    > [!WARNING]
    > 嘗試建置應用程式現在將會產生將在後續步驟中修正的錯誤。

5. 在  **Solution Pad**，加入新的資料夾，名為**資料**來**備忘稿**專案。

6. 在  **Solution Pad**，請在**備忘稿**專案，加入新的類別，名為**NoteDatabase**來**資料**資料夾。

7. 在  **NoteDatabase.cs**，以下列程式碼取代現有的程式碼：

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

    這個類別包含程式碼，以建立資料庫、 資料讀取、 寫入資料，和刪除資料。 此程式碼會使用非同步 SQLite.Net API，以將資料庫作業移至背景執行緒。 此外，`NoteDatabase` 建構函式會採用資料庫檔案的路徑作為引數。 此路徑會由`App`下一個步驟中的類別。

    變更儲存到**NoteDatabase.cs**選擇**檔案 > 儲存**(或按下 **&#8984; + S**)，然後關閉檔案。

    > [!WARNING]
    > 嘗試建置應用程式現在將會產生將在後續步驟中修正的錯誤。

8. 在  **Solution Pad**，請在**備忘稿**專案中，按兩下**App.xaml.cs**加以開啟。 然後以下列程式碼取代現有的程式碼：

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
            ...
        }
    }
    ```

    此程式碼定義`Database`建立新的屬性`NoteDatabase`為單一性，做為引數傳入的資料庫檔名的執行個體`NoteDatabase`建構函式。 將資料庫公開為唯一資料庫的優點為，所建立的單一資料庫連線會在應用程式執行時保持開啟，因此可避免每次執行資料庫作業時開啟和關閉資料庫檔案的費用。

    選擇 [檔案] > [儲存]  (或按下 **&#8984; + S**) 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

    > [!WARNING]
    > 嘗試建置應用程式現在將會產生將在後續步驟中修正的錯誤。

9. 在  **Solution Pad**，請在**備忘稿**專案中，按兩下**NotesPage.xaml.cs**加以開啟。 然後取代`OnAppearing`為下列程式碼的方法：

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    此程式碼會填入[ `ListView` ](xref:Xamarin.Forms.ListView)與儲存在資料庫中的任何附註。

    變更儲存到**NotesPage.xaml.cs**選擇**檔案 > 儲存**(或按下 **&#8984; + S**)，然後關閉檔案。

    > [!WARNING]
    > 嘗試建置應用程式現在將會產生將在後續步驟中修正的錯誤。

10. 在  **Solution Pad**，按兩下**NoteEntryPage.xaml.cs**加以開啟。 然後取代`OnSaveButtonClicked`和`OnDeleteButtonClicked`為下列程式碼的方法：

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

      `NoteEntryPage`儲存`Note`執行個體，代表單一附註，這[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的頁面。 當`OnSaveButtonClicked`事件處理常式會執行，`Note`執行個體儲存至資料庫和應用程式向後巡覽至上一頁。 當`OnDeleteButtonClicked`事件處理常式會執行，`Note`從資料庫刪除執行個體和應用程式向後巡覽至上一頁。

      變更儲存到**NoteEntryPage.xaml.cs**選擇**檔案 > 儲存**(或按下 **&#8984; + S**)，然後關閉檔案。

11. 建置並執行專案，每個平台。 如需詳細資訊，請參閱 <<c0> [ 建置快速入門](single-page.md#building-the-quickstart)。

    在上**NotesPage**按下 **+** 按鈕，巡覽至**NoteEntryPage**和輸入的附註。 儲存記事應用程式將會瀏覽回到之後**NotesPage**。

    輸入資訊，以觀察應用程式行為的不同長度的數的字。

::: zone-end

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

- 將 NuGet 套件新增至專案中使用 NuGet 套件管理員。
- SQLite.NET 資料庫中的本機儲存資料。

若要設定應用程式使用 XAML 樣式的樣式，繼續下一個快速入門。

> [!div class="nextstepaction"]
> [下一步](styling.md)

## <a name="related-links"></a>相關連結

- [Notes (範例)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)
- [Xamarin.Forms 快速入門深入探討](deepdive.md)
