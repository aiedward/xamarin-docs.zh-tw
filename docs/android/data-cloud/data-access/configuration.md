---
title: 組態
ms.prod: xamarin
ms.assetid: 44526226-4E4E-4FFF-9A16-CA7B1E01BB8F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/11/2016
ms.openlocfilehash: cf474015b28d9708d69719b38348391091040a28
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762580"
---
# <a name="configuration"></a>組態

若要使用 SQLite Xamarin.Android 應用程式中，您必須判斷您的資料庫檔案的正確檔案位置。

## <a name="database-file-path"></a>資料庫檔案路徑。

不論您使用的資料存取方法，您必須建立資料庫檔案之前可以與 SQLite 儲存資料。 根據您的目標平台的檔案位置將會不同。 適用於 Android 您可用於環境類別建構有效的路徑，如下列程式碼片段所示：

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

有其他項目納入考量，當您決定儲存資料庫檔案的位置時。 例如，在 Android 上您可以選擇是否要使用內部或外部儲存體。

如果您想要使用不同的位置，在跨平台應用程式中的每個平台上您可以使用編譯器指示詞所示來產生不同的路徑，每個平台：

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

在 Android 中使用檔案系統的提示，請參閱[瀏覽檔案](https://developer.xamarin.com/recipes/android/data/Files/Browse_Files)配方。 請參閱[建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)如需有關每個平台撰寫特定的程式碼使用編譯器指示詞的文件。

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
- [Android 資料配方](https://developer.xamarin.com/recipes/android/data/)
- [Xamarin.Forms 資料存取](~/xamarin-forms/app-fundamentals/databases.md)
