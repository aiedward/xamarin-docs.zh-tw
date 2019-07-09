---
title: 在 Xamarin.iOS 中設定 SQLite
description: 本文件說明如何判斷在 Xamarin.iOS 應用程式中的 SQLite 資料庫檔案的位置。 這些概念是相關不論選取的資料存取機制。
ms.prod: xamarin
ms.assetid: E5582F4B-AD74-420F-9E6D-B07CFB420B3A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: afb582129a5587e6a386a0ce2c23368af9bcd619
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650253"
---
# <a name="configuring-sqlite-in-xamarinios"></a>在 Xamarin.iOS 中設定 SQLite

若要在您的 Xamarin.iOS 應用程式中使用 SQLite，您必須判斷您的資料庫檔案的正確檔案位置。

## <a name="database-file-path"></a>資料庫檔案路徑。

不論使用何種資料存取方法，您必須建立資料庫檔案之前的資料可以儲存使用 SQLite。 根據您的目標何種平台的檔案位置將會不同。 適用於 iOS 您可以建構有效的路徑，使用環境類別，如下列程式碼片段所示：

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

有其他項目納入考量，當決定儲存資料庫檔案的位置。 在 iOS 上，您可能想要備份自動 （或停用） 的資料庫。

如果您想要在跨平台應用程式中的每個平台上使用不同的位置。 您可以使用的編譯器指示詞所示來產生不同的路徑，每個平台：

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

請參閱[使用檔案系統](~/ios/app-fundamentals/file-system.md)如需有關要在 iOS 上使用哪些檔案位置的文件。 請參閱[建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)如需有關使用編譯器指示詞寫入每個平台特定程式碼的文件。

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
- [iOS 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin.Forms 資料存取](~/xamarin-forms/data-cloud/data/databases.md)
