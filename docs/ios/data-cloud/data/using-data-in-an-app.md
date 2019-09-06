---
title: 在 iOS 應用程式中使用資料
description: 本檔描述 DataAccess_Adv 範例，其中示範如何收集使用者輸入，並在 Xamarin iOS 應用程式中執行建立、讀取、更新和刪除（CRUD）資料庫作業。
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: 9cd93a94361c11ecaa454a804e58180a33ec08fe
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290934"
---
# <a name="using-data-in-an-ios-app"></a>在 iOS 應用程式中使用資料

**DataAccess_Adv**範例會顯示可運作的應用程式，允許使用者輸入和*CRUD* （建立、讀取、更新和刪除）資料庫功能。 應用程式包含兩個畫面：清單和資料輸入表單。 所有的資料存取程式碼都可在 iOS 和 Android 中重複使用，而不需要修改。

新增一些資料之後，應用程式畫面在 iOS 上看起來像這樣：

 ![](using-data-in-an-app-images/image9.png "iOS 範例清單")

 ![](using-data-in-an-app-images/image10.png "iOS 範例詳細資料")

IOS 專案如下所示–這一節中所顯示的程式碼包含在**Orm**目錄中：

 ![](using-data-in-an-app-images/image13.png "iOS 專案樹狀結構")

IOS 中 ViewControllers 的原生 UI 程式碼超出本檔的範圍。
如需 UI 控制項的詳細資訊，請參閱[IOS 使用資料表和資料格](~/ios/user-interface/controls/tables/index.md)指南。

## <a name="read"></a>讀取

範例中有幾個讀取作業：

- 讀取清單
- 讀取個別記錄


`StockDatabase`類別中的兩個方法是：

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

iOS 會以不同的`UITableView`方式呈現資料。

## <a name="create-and-update"></a>建立和更新

為了簡化應用程式的程式碼，會根據是否已設定 PrimaryKey 來進行插入或更新，提供單一 save 方法。 因為屬性是`[PrimaryKey]`以屬性標記，所以您不應該在程式碼中設定它。 `Id`
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

`Delete<T>` `Stock`與和方法`Update`不同的是，方法只能接受主鍵值，而不是完整的物件。 `Insert`
在此範例`Stock`中，會將物件傳遞至方法，但只會將 Id 屬性傳遞`Delete<T>`給方法。

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
- **命令列**–請參閱[www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) 。


建立與您的應用程式一起散發的資料庫檔案時，請留意資料表和資料行的命名，以確保它們符合您的程式碼所預期的內容，特別是當您使用的是會C#預期名稱符合您的類別和屬性的 SQLite.NET 時（或相關聯的自訂屬性）。

若是 iOS，請在您的應用程式中包含 sqlite 檔案，並確定**它已標記為 [組建動作]：內容**。 將程式碼`FinishedLaunching`放入，將檔案複製到可寫入的目錄，*然後再*呼叫任何資料方法。 下列程式碼會複製名為**data. sqlite**的現有資料庫（只有在它尚未存在的情況下）。

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
