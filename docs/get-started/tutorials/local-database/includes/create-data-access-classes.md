---
ms.openlocfilehash: b11a5972c2aabace8a6991a82f5719f34450297d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "67841535"
---
在此練習中，您會將資料存取類別新增至 **LocalDatabaseTutorial** 專案，該專案用於將人員相關資料保存到資料庫。

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 [方案總管]  的 **LocalDatabaseTutorial** 專案中，將名為 `Person` 的新類別新增到此專案。 然後在 **Person.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Person
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }
        }
    }
    ```

    此程式碼會定義 `Person` 類別，以儲存應用程式中每個人員的相關資料。 `ID` 屬性會以 `PrimaryKey` 和 `AutoIncrement` 屬性標示，以確保資料庫中的每個 `Person` 執行個體都具有 SQLite.NET 所提供的唯一識別碼。

1. 在 [方案總管]  的 **LocalDatabaseTutorial** 專案中，將名為 `Database` 的新類別新增到此專案。 然後在 **Database.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Database
        {
            readonly SQLiteAsyncConnection _database;

            public Database(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Person>().Wait();
            }

            public Task<List<Person>> GetPeopleAsync()
            {
                return _database.Table<Person>().ToListAsync();
            }

            public Task<int> SavePersonAsync(Person person)
            {
                return _database.InsertAsync(person);
            }
        }
    }
    ```

    此類別包含的程式碼可建立資料庫、從中讀取資料，以及將資料寫入其中。 此程式碼會使用非同步 SQLite.Net API，以將資料庫作業移至背景執行緒。 此外，`Database` 建構函式會採用資料庫檔案的路徑作為引數。 此路徑會由下一個練習中的 `App` 類別所提供。

1. 在 [方案總管]  中的 **LocalDatabaseTutorial** 專案中，展開 **App.xaml**，然後按兩下 **App.xaml.cs** 將其開啟。 接著，在 **App.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace LocalDatabaseTutorial
    {
        public partial class App : Application
        {
            static Database database;

            public static Database Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new Database(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "people.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
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

    此程式碼會定義 `Database` 屬性，該屬性會建立新的 `Database` 執行個體作為單例。 系統會將本機檔案路徑和檔名 (代表儲存資料庫的位置) 當作引數傳遞至 `Database` 類別建構函式。

    > [!IMPORTANT]
    > 將資料庫公開為唯一資料庫的優點為，所建立的單一資料庫連線會在應用程式執行時保持開啟，因此可避免每次執行資料庫作業時開啟和關閉資料庫檔案的費用。

1. 建置解決方案以確定沒有任何錯誤。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 [Solution Pad]  的 **LocalDatabaseTutorial** 專案中，將名為 `Person` 的新類別新增到此專案。 然後在 **Person.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Person
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }
        }
    }
    ```

    此程式碼會定義 `Person` 類別，以儲存應用程式中每個人員的相關資料。 `ID` 屬性會以 `PrimaryKey` 和 `AutoIncrement` 屬性標示，以確保資料庫中的每個 `Person` 執行個體都具有 SQLite.NET 所提供的唯一識別碼。

1. 在 [Solution Pad]  的 **LocalDatabaseTutorial** 專案中，將名為 `Database` 的新類別新增到此專案。 然後在 **Database.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Database
        {
            readonly SQLiteAsyncConnection _database;

            public Database(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Person>().Wait();
            }

            public Task<List<Person>> GetPeopleAsync()
            {
                return _database.Table<Person>().ToListAsync();
            }

            public Task<int> SavePersonAsync(Person person)
            {
                return _database.InsertAsync(person);
            }
        }
    }
    ```

    此類別包含的程式碼可建立資料庫、從中讀取資料，以及將資料寫入其中。 此程式碼會使用非同步 SQLite.Net API，以將資料庫作業移至背景執行緒。 此外，`Database` 建構函式會採用資料庫檔案的路徑作為引數。 此路徑會由下一個練習中的 `App` 類別所提供。

1. 在 [Solution Pad]  中的 **LocalDatabaseTutorial** 專案中，展開 **App.xaml**，然後按兩下 **App.xaml.cs** 將其開啟。 接著，在 **App.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace LocalDatabaseTutorial
    {
        public partial class App : Application
        {
            static Database database;

            public static Database Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new Database(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "people.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
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

    此程式碼會定義 `Database` 屬性，該屬性會建立新的 `Database` 執行個體作為單例。 系統會將本機檔案路徑和檔名 (代表儲存資料庫的位置) 當作引數傳遞至 `Database` 類別建構函式。

    > [!IMPORTANT]
    > 將資料庫公開為唯一資料庫的優點為，所建立的單一資料庫連線會在應用程式執行時保持開啟，因此可避免每次執行資料庫作業時開啟和關閉資料庫檔案的費用。

1. 建置解決方案以確定沒有任何錯誤。
