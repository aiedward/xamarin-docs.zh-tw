---
title: "應用程式中使用資料"
ms.topic: article
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 5350317c82d1073d7679843272e3215634f91217
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="using-data-in-an-app"></a>應用程式中使用資料

**DataAccess_Adv**範例會顯示可用的應用程式，可讓使用者輸入和 CRUD （建立、 讀取、 更新和刪除） 資料庫功能。 此應用程式包含兩個畫面： 清單，以及資料輸入表單。 所有資料存取程式碼都是可在 iOS 和 Android 必須修改才能重複使用。

加入一些資料之後的應用程式畫面看起來像這樣在 Android 上：

![Android 範例清單](using-data-in-an-app-images/image11.png "Android 範例清單")

![Android 範例詳細資料](using-data-in-an-app-images/image12.png "Android 範例詳細資料")

如下所示的 Android 專案&ndash;本節中所顯示的程式碼包含在**Orm**目錄：

![Android 專案樹狀結構](using-data-in-an-app-images/image14.png "Android 專案樹狀結構")

原生的 UI 程式碼中 Android 活動已超出本文件。 請參閱[Android Listview 和配接器](~/android/user-interface/layouts/list-view/index.md)如需詳細資訊，在 UI 控制項上的指南。

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

Android 呈現資料為`ListView`。

## <a name="create-and-update"></a>建立和更新

若要簡化應用程式程式碼，單一 save 方法是提供執行 Insert 或 Update 取決於是否已設定主索引鍵。 因為`Id`屬性標記為與`[PrimaryKey]`您不應將它在程式碼中的屬性。 這個方法將會偵測是否已該值先前儲存 （藉由檢查主索引鍵屬性），然後插入或據以更新物件：

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

真實世界應用程式通常會需要某些驗證 （例如必要的欄位，最小長度或其他商務規則）。 良好的跨平台應用程式實作的驗證邏輯盡可能共用程式碼，將備份至平台的能力依據顯示的使用者介面的驗證錯誤。

## <a name="delete"></a>刪除

不同於`Insert`和`Update`方法`Delete<T>`方法可以接受只主索引鍵值而非完整`Stock`物件。 在此範例中`Stock`物件傳遞至方法，但只有 [識別碼] 屬性會傳遞給`Delete<T>`方法。

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>使用預先填入的 SQLite 資料庫檔案

某些應用程式隨附已經填入資料的資料庫。 您可以輕易地達成行動應用程式中傳送您的應用程式的現有 SQLite 資料庫檔案，並將它複製到可寫入的目錄中，才能存取它。 SQLite 是適用於多種平台的標準檔案格式，因為有數種工具可用來建立具有 SQLite 資料庫檔案：

-   **SQLite 管理員 Firefox 擴充**&ndash;適用於 Mac 和 Windows 並且產生與 iOS 和 Android 相容的檔案。

-   **命令列**&ndash;看到[www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) 。

建立發佈的資料庫檔案與應用程式，格外謹慎以確保它們符合您的程式碼所預期，特別是如果您使用 SQLite.NET 它預期能以符合您的 C# 類別和屬性名稱的資料表和資料行命名 （或相關聯的自訂屬性）。

若要確保一些程式碼執行 Android 應用程式中的其他任何項目之前，您可以在載入第一個活動，或者您可以建立`Application`載入之前的任何活動的子類別。 程式碼所示`Application`複製現有的資料庫檔案的子類別**data.sqlite**超出**/Resources/Raw/**目錄。

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
- [資料存取進階 （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 資料配方](https://developer.xamarin.com/recipes/android/data/)
- [Xamarin.Forms 資料存取](~/xamarin-forms/app-fundamentals/databases.md)
