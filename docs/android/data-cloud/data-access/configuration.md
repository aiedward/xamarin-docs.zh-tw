---
title: 組態
ms.prod: xamarin
ms.assetid: 44526226-4E4E-4FFF-9A16-CA7B1E01BB8F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/11/2016
ms.openlocfilehash: b3a7858361d25f26807ea328e8bfdd30ca8d483b
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241873"
---
# <a name="configuration"></a>組態

若要在您的 Xamarin.Android 應用程式中使用 SQLite，您必須判斷您的資料庫檔案的正確檔案位置。

## <a name="database-file-path"></a>資料庫檔案路徑。

不論使用何種資料存取方法，您必須建立資料庫檔案之前的資料可以儲存使用 SQLite。 根據您的目標何種平台的檔案位置將會不同。 適用於 Android 您可以建構有效的路徑，使用環境類別，如下列程式碼片段所示：

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

有其他項目納入考量，當決定儲存資料庫檔案的位置。 例如，在 Android 上您可以選擇是否要使用內部或外部儲存體。

如果您想要在跨平台應用程式中的每個平台上使用不同的位置。 您可以使用的編譯器指示詞所示來產生不同的路徑，每個平台：

```csharp
var sqliteFilename = "MyDatabase.db3";
#if __ANDROID__
// Just use whatever directory SpecialFolder.Personal returns
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
// we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
// (they don't want non-user-generated data in Documents)
string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder instead
#endif
var path = Path.Combine (libraryPath, sqliteFilename);
```

在 Android 中使用檔案系統上的提示，請參閱[瀏覽檔案](https://github.com/xamarin/recipes/tree/master/Recipes/android/data/files/browse_files)配方。 請參閱[建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)如需有關使用編譯器指示詞寫入每個平台特定程式碼的文件。

## <a name="threading"></a>執行緒

您不應該使用相同的 SQLite 資料庫連接，跨多個執行緒。 請小心開啟、 使用，再關閉您在相同的執行緒建立任何連線。

若要確保您的程式碼，不嘗試從多個執行緒存取 SQLite 資料庫，在此同時，手動將採用鎖定時要存取資料庫時，就像這樣：

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

所有資料庫存取權 （讀取、 寫入、 更新等） 應該都包裝與相同的鎖定。 必須小心確保鎖定子句內的工作會保持簡單，而不會呼叫可能也需要鎖定的其他方法來避免死結狀況 ！


## <a name="related-links"></a>相關連結

- [DataAccess Basic （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 進階 （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Xamarin.Forms 資料存取](~/xamarin-forms/app-fundamentals/databases.md)
