---
title: 在 Android 應用程式中使用資料
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 563c04ef1c8eec00108844894c5f9bdc0e9950e3
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241886"
---
# <a name="using-data-in-an-app"></a>在應用程式中使用資料

**DataAccess_Adv**範例會顯示可用的應用程式，可讓使用者輸入和 CRUD （建立、 讀取、 更新和刪除） 資料庫功能。 在應用程式包含兩個畫面： 清單，以及資料輸入表單。 所有資料存取程式碼都會重複使用 iOS 和 Android 中而不需修改。

新增一些資料後的應用程式的畫面看起來像這樣在 Android 上：

![Android 範例清單](using-data-in-an-app-images/image11.png "Android 範例清單")

![Android 範例詳細](using-data-in-an-app-images/image12.png "Android 範例詳細資料")

如下所示的 Android 專案&ndash;顯示這一節的程式碼包含在**Orm**目錄：

![Android 專案樹狀結構](using-data-in-an-app-images/image14.png "Android 專案樹狀結構")

原生的 UI 程式碼，在 Android 中的活動不在本文的範圍。 請參閱[Android Listview 和 Adapter](~/android/user-interface/layouts/list-view/index.md)對 UI 控制項的詳細資訊的指南。

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

Android 會將資料轉譯為`ListView`。

## <a name="create-and-update"></a>建立和更新

若要簡化應用程式程式碼，單一 save 方法是提供執行 Insert 或 Update 取決於是否已設定主索引鍵。 因為`Id`屬性標記著`[PrimaryKey]`則不應該將其程式碼中的屬性。 這個方法會偵測是否已擷取先前儲存 （藉由檢查主索引鍵屬性），然後插入或據以更新物件：

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

真實世界應用程式通常會需要一些驗證 （例如必要的欄位、 最小長度或其他商務規則）。 良好的跨平台應用程式實作的邏輯越好共用的程式碼，傳遞驗證錯誤，備份至根據平台的功能顯示的 UI 中的驗證。

## <a name="delete"></a>刪除

不同於`Insert`並`Update`方法`Delete<T>`方法可以接受只是主索引鍵的值而不是完整`Stock`物件。 在此範例中`Stock`物件傳遞至方法，但只能使用 Id 屬性會傳遞給`Delete<T>`方法。

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>使用預先填入的 SQLite 資料庫檔案

有些應用程式隨附已填入資料的資料庫。 您可以輕鬆地達成行動應用程式中傳送您的應用程式與現有的 SQLite 資料庫檔案，並將它複製到可寫入的目錄中，才能存取它。 SQLite 是許多平台使用的標準檔案格式，因為有許多工具可用來建立具有 SQLite 的資料庫檔案：

-   **SQLite Manager Firefox 的延伸模組**&ndash;適用於 Mac 和 Windows 並且產生使用 iOS 和 Android 的相容的檔案。

-   **命令列**&ndash;請參閱[www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) 。

使用您的應用程式中建立發佈的資料庫檔案，請留意以確保它們符合您的程式碼的預期，尤其是如果您使用 SQLite.NET 會預期以符合您的 C# 類別和屬性名稱的資料表和資料行命名 （或相關聯的自訂屬性）。

若要確保某些程式碼執行 Android 應用程式中的其他任何動作之前，您可以將它放在載入的第一個活動，或者您可以建立`Application`載入任何活動之前的子類別。 以下顯示的程式碼`Application`複製現有的資料庫檔案的子類別**data.sqlite**共 **/Resources/Raw/** 目錄。

```csharp
[Application]
public class YourAndroidApp : Application {
    public override void OnCreate ()
    {
        base.OnCreate ();
        var docFolder = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
        Console.WriteLine ("Data path:" + Database.DatabaseFilePath);
        var dbFile = Path.Combine(docFolder, "data.sqlite"); // FILE NAME TO USE WHEN COPIED
        if (!System.IO.File.Exists(dbFile)) {
            var s = Resources.OpenRawResource(Resource.Raw.data);  // DATA FILE RESOURCE ID
            FileStream writeStream = new FileStream(dbFile, FileMode.OpenOrCreate, FileAccess.Write);
            ReadWriteStream(s, writeStream);
        }
    }
    // readStream is the stream you need to read
    // writeStream is the stream you want to write to
    private void ReadWriteStream(Stream readStream, Stream writeStream)
    {
        int Length = 256;
        Byte[] buffer = new Byte[Length];
        int bytesRead = readStream.Read(buffer, 0, Length);
        // write the required bytes
        while (bytesRead > 0)
        {
            writeStream.Write(buffer, 0, bytesRead);
            bytesRead = readStream.Read(buffer, 0, Length);
        }
        readStream.Close();
        writeStream.Close();
    }
}
```


## <a name="related-links"></a>相關連結

- [DataAccess Basic （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess 進階 （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Xamarin.Forms 資料存取](~/xamarin-forms/app-fundamentals/databases.md)
