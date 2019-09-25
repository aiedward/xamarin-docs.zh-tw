---
title: 在 Android 應用程式中使用資料
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 922b1fa411a176df580050384e7555120fd68137
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "70754450"
---
# <a name="using-data-in-an-app"></a>在應用程式中使用資料

**DataAccess_Adv**範例會顯示可運作的應用程式，允許使用者輸入和 CRUD （建立、讀取、更新和刪除）資料庫功能。 應用程式包含兩個畫面：清單和資料輸入表單。 所有的資料存取程式碼都可在 iOS 和 Android 中重複使用，而不需要修改。

新增一些資料之後，應用程式畫面在 Android 上看起來像這樣：

![Android 範例清單](using-data-in-an-app-images/image11.png "Android 範例清單")

![Android 範例詳細資料](using-data-in-an-app-images/image12.png "Android 範例詳細資料")

Android 專案顯示在此區段&ndash;所顯示的程式碼下方，包含在**Orm**目錄中：

![Android 專案樹狀結構](using-data-in-an-app-images/image14.png "Android 專案樹狀結構")

Android 中活動的原生 UI 程式碼超出本檔的範圍。 如需 UI 控制項的詳細資訊，請參閱[Android listview 和介面卡](~/android/user-interface/layouts/list-view/index.md)指南。

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

Android 會將資料呈現為`ListView`。

## <a name="create-and-update"></a>建立和更新

為了簡化應用程式的程式碼，會根據是否已設定 PrimaryKey 來進行插入或更新，提供單一 save 方法。 因為屬性是`[PrimaryKey]`以屬性標記，所以您不應該在程式碼中設定它。 `Id` 這個方法會偵測先前是否已儲存值（藉由檢查 primary key 屬性），並據以插入或更新物件：

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

真實世界的應用程式通常需要進行一些驗證（例如必要欄位、最小長度或其他商務規則）。 良好的跨平臺應用程式可在共用程式碼中盡可能地實作為驗證邏輯，將驗證錯誤傳回給 UI，以根據平臺的功能來顯示。

## <a name="delete"></a>刪除

`Delete<T>` `Stock`與和方法`Update`不同的是，方法只能接受主鍵值，而不是完整的物件。 `Insert` 在此範例`Stock`中，會將物件傳遞至方法，但只會將 Id 屬性傳遞`Delete<T>`給方法。

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>使用預先填入的 SQLite 資料庫檔案

有些應用程式隨附于已填入資料的資料庫。 您可以在行動應用程式中輕鬆完成此動作，方法是在應用程式中傳送現有的 SQLite 資料庫檔案，並將它複製到可寫入的目錄，然後再存取該檔案。 因為 SQLite 是在許多平臺上使用的標準檔案格式，所以有一些工具可用來建立 SQLite 資料庫檔案：

- **SQLite Manager Firefox 延伸**模組&ndash;適用于 Mac 和 Windows，並產生與 iOS 和 Android 相容的檔案。

- **命令列**請參閱[www.sqlite.org/sqlite.html。](http://www.sqlite.org/sqlite.html) &ndash;

建立與您的應用程式一起散發的資料庫檔案時，請留意資料表和資料行的命名，以確保它們符合您的程式碼所預期的內容，特別是當您使用的是會C#預期名稱符合您的類別和屬性的 SQLite.NET 時（或相關聯的自訂屬性）。

若要確保某些程式碼會在 Android 應用程式中的任何其他專案之前執行，您可以將它放在要載入的第`Application`一個活動中，或者您可以建立在任何活動之前載入的子類別。 下列程式`Application`代碼顯示的子類別會複製現有的資料庫檔案**資料。 sqlite**超出 **/Resources/Raw/** 目錄。

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

- [DataAccess 基本（範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced （範例）](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Android 資料配方](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Xamarin. 表單資料存取](~/xamarin-forms/data-cloud/data/databases.md)
