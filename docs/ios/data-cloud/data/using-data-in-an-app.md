---
title: 在 iOS 應用程式中使用資料
description: 本檔說明 DataAccess_Adv 範例，其中示範如何收集使用者輸入，並在 Xamarin iOS 應用程式中執行建立、讀取、更新和刪除（CRUD）資料庫作業。
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/11/2016
ms.openlocfilehash: c888c132748c4212b1e52413647614ca83897d75
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938511"
---
# <a name="using-data-in-an-ios-app"></a>在 iOS 應用程式中使用資料

**DataAccess_Adv**範例顯示可讓使用者輸入和*CRUD* （建立、讀取、更新和刪除）資料庫功能的工作應用程式。 應用程式包含兩個畫面：清單和資料輸入表單。 所有的資料存取程式碼都可在 iOS 和 Android 中重複使用，而不需要修改。

新增一些資料之後，應用程式畫面在 iOS 上看起來像這樣：

 ![iOS 範例清單](using-data-in-an-app-images/image9.png)

 ![iOS 範例詳細資料](using-data-in-an-app-images/image10.png)

IOS 專案如下所示–這一節中所顯示的程式碼包含在**Orm**目錄中：

 ![iOS 專案樹狀結構](using-data-in-an-app-images/image13.png)

IOS 中 ViewControllers 的原生 UI 程式碼超出本檔的範圍。
如需 UI 控制項的詳細資訊，請參閱[IOS 使用資料表和資料格](~/ios/user-interface/controls/tables/index.md)指南。

## <a name="read"></a>讀取

範例中有幾個讀取作業：

- 讀取清單
- 讀取個別記錄

類別中的兩個方法 `StockDatabase` 是：

```csharp
public IEnumerable<Stock> GetStocks ()
{
    lock (locker) {
        return (from i in Table<Stock> () select i).ToList ();
    }
}
public Stock GetStock (int id)
{
    lock (locker) {
        return Table<Stock>().FirstOrDefault(x => x.Id == id);
    }
}
```

iOS 會以不同的方式呈現資料 `UITableView` 。

## <a name="create-and-update"></a>建立和更新

為了簡化應用程式的程式碼，會根據是否已設定 PrimaryKey 來進行插入或更新，提供單一 save 方法。 因為 `Id` 屬性是以 `[PrimaryKey]` 屬性標記，所以您不應該在程式碼中設定它。
這個方法會偵測先前是否已儲存值（藉由檢查 primary key 屬性），並據以插入或更新物件：

```csharp
public int SaveStock (Stock item)
{
    lock (locker) {
        if (item.Id != 0) {
            Update (item);
            return item.Id;
    } else {
            return Insert (item);
        }
    }
}
```

真實世界的應用程式通常需要進行一些驗證（例如必要欄位、最小長度或其他商務規則）。
良好的跨平臺應用程式可在共用程式碼中盡可能地實作為驗證邏輯，將驗證錯誤傳回給 UI，以根據平臺的功能來顯示。

## <a name="delete"></a>刪除

與和方法不同的是 `Insert` `Update` ， `Delete<T>` 方法只能接受主鍵值，而不是完整的 `Stock` 物件。
在此範例中， `Stock` 會將物件傳遞至方法，但只會將 Id 屬性傳遞給 `Delete<T>` 方法。

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>使用預先填入的 SQLite 資料庫檔案

有些應用程式隨附于已填入資料的資料庫。
您可以在行動應用程式中輕鬆完成此動作，方法是在應用程式中傳送現有的 SQLite 資料庫檔案，並將它複製到可寫入的目錄，然後再存取該檔案。 因為 SQLite 是在許多平臺上使用的標準檔案格式，所以有一些工具可用來建立 SQLite 資料庫檔案：

- **SQLite Manager Firefox 延伸**模組–適用于 Mac 和 Windows，並產生與 IOS 和 Android 相容的檔案。
- **命令列**–請參閱[www.sqlite.org/sqlite.html](https://www.sqlite.org/sqlite.html) 。

建立與您的應用程式一起散發的資料庫檔案時，請留意資料表和資料行的命名，以確保它們符合程式碼所預期的內容，特別是當您使用的是會預期名稱符合 c # 類別和屬性（或相關聯的自訂屬性）的 SQLite.NET 時。

若是 iOS，請在您的應用程式中包含 sqlite 檔案，並確定它已標記為 [**組建動作：內容**]。 將程式碼放入，將檔案 `FinishedLaunching` 複製到可寫入的目錄，*然後再*呼叫任何資料方法。 下列程式碼會複製名為**data. sqlite**的現有資料庫（只有在它尚未存在的情況下）。

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

在此完成後執行的任何資料存取程式碼（不論是 ADO.NET 或使用 SQLite.NET）都可以存取預先填入的資料。

## <a name="related-links"></a>相關連結

- [DataAccess 基本（範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin. 表單資料存取](~/xamarin-forms/data-cloud/data/databases.md)
