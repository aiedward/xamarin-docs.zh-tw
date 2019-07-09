---
title: 在 iOS 應用程式中使用資料
description: 本文件說明 DataAccess_Adv 範例，示範如何收集使用者輸入並執行建立、 讀取、 更新和刪除 (CRUD) 資料庫中的 Xamarin.iOS 應用程式的作業。
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: a47ab26777c594658810b014025477486bbe5cc2
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650156"
---
# <a name="using-data-in-an-ios-app"></a>在 iOS 應用程式中使用資料

**DataAccess_Adv**範例會顯示可用的應用程式，可讓使用者輸入並*CRUD* （建立、 讀取、 更新和刪除） 的資料庫功能。 在應用程式包含兩個畫面： 清單，以及資料輸入表單。 所有資料存取程式碼都會重複使用 iOS 和 Android 中而不需修改。

新增一些資料後的應用程式的畫面看起來像這樣在 iOS 上：

 ![](using-data-in-an-app-images/image9.png "iOS 範例清單")

 ![](using-data-in-an-app-images/image10.png "iOS 範例的詳細資料")

IOS 專案會如下所示，在這一節中所顯示的程式碼包含在**Orm**目錄：

 ![](using-data-in-an-app-images/image13.png "iOS 專案樹狀結構")

在 iOS 中 ViewControllers 的原生 UI 程式碼超出本文的範圍。
請參閱[iOS 使用資料表和資料格](~/ios/user-interface/controls/tables/index.md)對 UI 控制項的詳細資訊的指南。

## <a name="read"></a>讀取

有幾個範例中的讀取作業：

-  閱讀清單
-  讀取個別的記錄


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

iOS 會將資料呈現方式為`UITableView`。

## <a name="create-and-update"></a>建立和更新

若要簡化應用程式程式碼，單一 save 方法是提供執行 Insert 或 Update 取決於是否已設定主索引鍵。 因為`Id`屬性標記著`[PrimaryKey]`則不應該將其程式碼中的屬性。
這個方法會偵測是否已擷取先前儲存 （藉由檢查主索引鍵屬性），然後插入或據以更新物件：

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



真實世界應用程式通常會需要一些驗證 （例如必要的欄位、 最小長度或其他商務規則）。
良好的跨平台應用程式實作的邏輯越好共用的程式碼，傳遞驗證錯誤，備份至根據平台的功能顯示的 UI 中的驗證。

## <a name="delete"></a>刪除

不同於`Insert`並`Update`方法`Delete<T>`方法可以接受只是主索引鍵的值而不是完整`Stock`物件。
在此範例中`Stock`物件傳遞至方法，但只能使用 Id 屬性會傳遞給`Delete<T>`方法。

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>使用預先填入的 SQLite 資料庫檔案

有些應用程式隨附已填入資料的資料庫。
您可以輕鬆地達成行動應用程式中傳送您的應用程式與現有的 SQLite 資料庫檔案，並將它複製到可寫入的目錄中，才能存取它。 SQLite 是許多平台使用的標準檔案格式，因為有許多工具可用來建立具有 SQLite 的資料庫檔案：

-  **SQLite Manager Firefox 的延伸模組**– 適用於 Mac 和 Windows 並且產生使用 iOS 和 Android 的相容的檔案。
-  **命令列**– 請參閱 < [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) 。


使用您的應用程式中建立發佈的資料庫檔案，請留意以確保它們符合您的程式碼的預期，尤其是如果您使用 SQLite.NET 會預期以符合您的 C# 類別和屬性名稱的資料表和資料行命名 （或相關聯的自訂屬性）。

適用於 iOS、 sqlite 檔案納入您的應用程式，並確保它以標示**建置動作：內容**。 中的程式碼放`FinishedLaunching`將檔案複製到可寫入的目錄*之前*您呼叫任何資料的方法。 下列程式碼會複製現有的資料庫稱為**data.sqlite**，只有當不存在。

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

任何資料存取程式碼 (是否 ADO.NET 或使用 SQLite.NET) 執行之後這已完成的將會預先填入資料的存取權。


## <a name="related-links"></a>相關連結

- [DataAccess Basic （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 進階 （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Xamarin.Forms 資料存取](~/xamarin-forms/data-cloud/data/databases.md)
