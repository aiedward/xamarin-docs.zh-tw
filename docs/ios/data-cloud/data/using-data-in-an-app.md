---
title: 應用程式中使用資料
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: 3366b78479712dffee74448dcc51e193cd07564d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="using-data-in-an-app"></a>應用程式中使用資料

**DataAccess_Adv**範例會顯示可用的應用程式，可讓使用者輸入和*CRUD* （建立、 讀取、 更新和刪除） 資料庫功能。 此應用程式包含兩個畫面： 清單，以及資料輸入表單。 所有資料存取程式碼都是可在 iOS 和 Android 必須修改才能重複使用。

加入一些資料之後的應用程式畫面看起來像這樣在 iOS 上：

 ![](using-data-in-an-app-images/image9.png "iOS 範例清單")

 ![](using-data-in-an-app-images/image10.png "iOS 範例詳細資料")

IOS 專案如下所示 – 這一節顯示的程式碼包含在**Orm**目錄：

 ![](using-data-in-an-app-images/image13.png "iOS 專案樹狀結構")

在 iOS 中 ViewControllers 原生的 UI 程式碼超出此文件的範圍。
請參閱[iOS 使用資料表和資料格](~/ios/user-interface/controls/tables/index.md)如需詳細資訊，在 UI 控制項上的指南。

## <a name="read"></a>讀取

有幾個範例中的讀取作業：

-  讀取清單
-  讀取個別記錄


中的兩個方法`StockDatabase`類別：

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

iOS 會將資料呈現以不同的方式為`UITableView`。

## <a name="create-and-update"></a>建立和更新

若要簡化應用程式程式碼，單一 save 方法是提供執行 Insert 或 Update 取決於是否已設定主索引鍵。 因為`Id`屬性標記為與`[PrimaryKey]`您不應將它在程式碼中的屬性。
這個方法將會偵測是否已該值先前儲存 （藉由檢查主索引鍵屬性），然後插入或據以更新物件：

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



真實世界應用程式通常會需要某些驗證 （例如必要的欄位，最小長度或其他商務規則）。
良好的跨平台應用程式實作的驗證邏輯盡可能共用程式碼，將備份至平台的能力依據顯示的使用者介面的驗證錯誤。

## <a name="delete"></a>刪除

不同於`Insert`和`Update`方法`Delete<T>`方法可以接受只主索引鍵值而非完整`Stock`物件。
在此範例中`Stock`物件傳遞至方法，但只有 [識別碼] 屬性會傳遞給`Delete<T>`方法。

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>使用預先填入的 SQLite 資料庫檔案

某些應用程式隨附已經填入資料的資料庫。
您可以輕易地達成行動應用程式中傳送您的應用程式的現有 SQLite 資料庫檔案，並將它複製到可寫入的目錄中，才能存取它。 SQLite 是適用於多種平台的標準檔案格式，因為有數種工具可用來建立具有 SQLite 資料庫檔案：

-  **SQLite 管理員 Firefox 擴充**– 適用於 Mac 和 Windows 並且產生與 iOS 和 Android 相容的檔案。
-  **命令列**– 請參閱[www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) 。


建立發佈的資料庫檔案與應用程式，格外謹慎以確保它們符合您的程式碼所預期，特別是如果您使用 SQLite.NET 它預期能以符合您的 C# 類別和屬性名稱的資料表和資料行命名 （或相關聯的自訂屬性）。

Ios 平台，sqlite 檔案納入您的應用程式，並確保它以標示**建置動作： 內容**。 將程式碼放入`FinishedLaunching`將檔案複製到可寫入的目錄*之前*呼叫任何資料的方法。 下列程式碼將會複製現有的資料庫稱為**data.sqlite**，只有當其不存在。

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

任何資料存取程式碼 (是否 ADO.NET 或使用 SQLite.NET) 執行之後，這有已完成的將會預先填入資料的存取權。


## <a name="related-links"></a>相關連結

- [DataAccess Basic （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [資料存取進階 （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 資料配方](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Xamarin.Forms 資料存取](~/xamarin-forms/app-fundamentals/databases.md)
