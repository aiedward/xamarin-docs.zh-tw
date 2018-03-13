---
title: "組態"
ms.topic: article
ms.prod: xamarin
ms.assetid: E5582F4B-AD74-420F-9E6D-B07CFB420B3A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: 73bb48bb9744c7245e02bac9df5d384cd0e056f6
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="configuration"></a>組態

若要使用 SQLite Xamarin.iOS 應用程式中，您必須判斷您的資料庫檔案的正確檔案位置。

## <a name="database-file-path"></a>資料庫檔案路徑。

不論您使用的資料存取方法，您必須建立資料庫檔案之前可以與 SQLite 儲存資料。 根據您的目標平台的檔案位置將會不同。 適用於 iOS 您可用於環境類別建構有效的路徑，如下列程式碼片段所示：

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

有其他項目納入考量，當您決定儲存資料庫檔案的位置時。 在 iOS 上，您可能想要備份自動 （或停用） 的資料庫。

如果您想要使用不同的位置，在跨平台應用程式中的每個平台上您可以使用編譯器指示詞所示來產生不同的路徑，每個平台：

```csharp
var sqliteFilename = "MyDatabase.db3";
#if __ANDROID__
// Just use whatever directory SpecialFolder.Personal returns
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
// we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
// (they don't want non-user-generated data in Documents)
string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder instead
#endif
var path = Path.Combine (libraryPath, sqliteFilename);
```

請參閱[檔案系統的處理](~/ios/app-fundamentals/file-system.md)如需有關在 iOS 上使用哪些檔案位置的發行項。 請參閱[建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)如需有關每個平台撰寫特定的程式碼使用編譯器指示詞的文件。

## <a name="threading"></a>執行緒

您不應該使用同一個 SQLite 資料庫連線，跨多個執行緒。 請小心開啟、 使用，再關閉任何在相同執行緒所建立的連接。

若要確保您的程式碼，不嘗試從多個執行緒存取 SQLite 資料庫，在相同的時間，以手動方式將採用鎖定每當您要存取資料庫時，就像這樣：

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

所有資料庫存取權 （讀取、 寫入、 更新等） 應該都包含具有相同的鎖定。 必須小心確保鎖定子句內的工作會保留簡單，而且不會呼叫可能也會採用鎖定的其他方法來避免發生死結狀況 ！


## <a name="related-links"></a>相關連結

- [DataAccess Basic （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [資料存取進階 （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 資料配方](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Xamarin.Forms 資料存取](~/xamarin-forms/app-fundamentals/databases.md)
