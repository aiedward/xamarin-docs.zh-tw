---
title: 組態
ms.prod: xamarin
ms.assetid: 44526226-4E4E-4FFF-9A16-CA7B1E01BB8F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: 5ebafa70239305210da631c3e9c34278f83b272b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754657"
---
# <a name="configuration"></a>組態

若要在您的 Xamarin Android 應用程式中使用 SQLite，您必須為資料庫檔案判斷正確的檔案位置。

## <a name="database-file-path"></a>資料庫檔案路徑

不論您使用哪一種資料存取方法，您都必須先建立資料庫檔案，才能使用 SQLite 來儲存資料。 根據您的目標平臺而定，檔案位置將會不同。 針對 Android，您可以使用環境類別來建立有效的路徑，如下列程式碼片段所示：

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

在決定要儲存資料庫檔案的位置時，還有其他事項需要考慮。 例如，在 Android 上，您可以選擇是否要使用內部或外部儲存體。

如果您想要在跨平臺應用程式的每個平臺上使用不同的位置，您可以使用如下所示的編譯器指示詞，為每個平臺產生不同的路徑：

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

如需在 Android 中使用檔案系統的提示，請參閱[流覽](https://github.com/xamarin/recipes/tree/master/Recipes/android/data/files/browse_files)檔案配方。 如需使用編譯器指示詞來撰寫每個平臺特定程式碼的詳細資訊，請參閱[建立跨平臺應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)檔。

## <a name="threading"></a>執行緒

您不應該在多個執行緒之間使用相同的 SQLite 資料庫連接。 請小心開啟，使用，然後關閉您在同一個執行緒上建立的任何連接。

若要確保您的程式碼不會同時嘗試從多個執行緒存取 SQLite 資料庫，請在每次存取資料庫時手動採取鎖定，如下所示：

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

所有資料庫存取（讀取、寫入、更新等等）都應該以相同的鎖定來包裝。 請務必小心避免鎖死的情況，方法是確保鎖定子句內的工作保持簡單，而且不會呼叫其他可能也會鎖定的方法！

## <a name="related-links"></a>相關連結

- [DataAccess 基本（範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Xamarin. 表單資料存取](~/xamarin-forms/data-cloud/data/databases.md)
