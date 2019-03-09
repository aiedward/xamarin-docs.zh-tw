---
title: 可攜式類別庫 (PCL) 簡介
description: 本文介紹可攜式類別庫 (PCL) 專案，並逐步解說建立和使用適用於 Mac 和 Visual Studio 的 Visual Studio 中的 PCL 專案。
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: a1e7152ca0f24d1ac660742e97907ea9300df9a7
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666875"
---
# <a name="portable-class-libraries-pcl"></a>可攜式類別庫 (PCL)

> [!WARNING]
> 可攜式類別庫 (Pcl) 會被視為最新版本的 Visual Studio 中已被取代。
> 雖然您仍然可以開啟、 編輯和編譯的 Pcl，針對新專案建議使用[.NET Standard 程式庫](~/cross-platform/app-fundamentals/net-standard.md)。

建置跨平台應用程式的重要元件能夠在各種平台專屬專案之間共用程式碼。 不過，這被複雜的事實，不同的平台通常會使用不同的子組的.NET 基底類別庫 (BCL)，並因此實際建置至不同的.NET Core 程式庫設定檔。 這表示每個平台，只能使用類別庫，使它們會顯示為每個平台需要個別的類別庫專案，以相同的設定檔為目標。

有三種主要方法來共用程式碼，以解決此問題： **.NET Standard 專案**，**共用資產專案**，和**可攜式類別庫 (PCL) 專案**.

- **.NET standard 專案**是慣用的方法，來共用.NET 程式碼，深入了解[.NET Standard 專案和 Xamarin](~/cross-platform/app-fundamentals/net-standard.md)。
- **共用資產專案**使用一組檔案和供應項目快速且簡單的方式，用來共用程式碼解決方案中，通常會採用條件式編譯指示詞來指定將使用它 （如需詳細的各種平台的程式碼路徑資訊，請參閱[共用的專案文件](~/cross-platform/app-fundamentals/shared-projects.md))。
- **PCL**專案以支援一組已知的 BCL 類別/功能的特定設定檔為目標。 不過，到 PCL 的缺點是它們通常需要額外架構的工作，才能設定檔特定程式碼分隔成自己的程式庫。

此頁面說明如何建立**PCL**目標特定的設定檔，然後由多個平台特定專案所參考的專案。

## <a name="what-is-a-portable-class-library"></a>什麼是可攜式類別庫？

當您建立應用程式專案或程式庫專案時，產生的 DLL 會僅限於它建立在特定平台上運作。 這會讓您撰寫 Windows 應用程式中，組件，並再重新使用 Xamarin.iOS 和 Xamarin.Android。

當您建立可攜式類別庫時，不過，您可以選擇您想要您的程式碼上執行的平台的組合。 建立可攜式類別庫時所做的相容性選項會轉譯成 「 設定檔 」 的識別碼，當中會描述程式庫支援哪些平台。

下表顯示一些.NET 平台而異的功能。 若要撰寫保證會在特定裝置/平台上執行的 PCL 組件您只要選擇您建立專案時，就需要支援。

|功能|.NET Framework|UWP 應用程式|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|核心|Y|Y|Y|Y|Y|
|LINQ|Y|Y|Y|Y|Y|
|IQueryable|Y|Y|Y|7.5 +|Y|
|序列化|Y|Y|Y|Y|Y|
|資料註釋|4.0.3 +|Y|Y||Y|

[Xamarin] 欄反映 Xamarin.iOS 和 Xamarin.Android 支援隨附於 Visual Studio 中的所有設定檔，並在您建立的任何程式庫中的功能的可用性會只受限於您選擇支援的平台。

這包括組合的設定檔：

- .NET 4 或.NET 4.5
- Silverlight 5
- Windows Phone 8
- UWP 應用程式

閱讀更多不同的設定檔功能的相關[Microsoft 的網站](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)並查看其他社群成員的[PCL 設定檔摘要](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY)framework 資訊和其他注意事項，其中包括支援。

**權益**

1. 集中式的程式碼共用 – 撰寫和測試程式碼可供其他程式庫或應用程式的單一專案中。
2. 重構作業將會影響所有的程式碼解決方案 （可攜式類別庫及平台專屬專案） 中載入。
3. PCL 專案可以輕鬆地參考其他專案，在解決方案中，或可供其他人在其解決方案中參考共用輸出組件。

**缺點**

1. 無法參考 （例如多個應用程式之間共用相同的可攜式類別庫，因為平台專屬程式庫。 Community.CsharpSqlite.WP7).
2. 可攜式類別庫子集可能不包含會 MonoTouch 和 Mono for Android （例如 DllImport 或 System.IO.File） 中可用的類別。

> [!NOTE]
> 中的最新版本的 Visual Studio 中，已被取代的可攜式類別庫和[.NET 標準程式庫](net-standard.md)建議改用。

某種程度來說這兩個缺點可以規避使用的提供者模式 」 或 「 相依性插入程式碼平台專案中，針對可攜式類別庫中定義的介面或基底類別中的實際實作。

下圖顯示跨平台應用程式共用程式碼，使用可攜式類別庫，但也使用傳遞平台相依的功能中的 相依性插入的架構：

[![](pcl-images/image1.png "下圖顯示跨平台應用程式共用程式碼，使用可攜式類別庫，但也使用傳遞平台相依的功能中的 相依性插入的架構")](pcl-images/image1.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 逐步解說

本節逐步解說如何建立及使用 Visual Studio for mac 中使用可攜式類別庫 請參閱 PCL 範例一節以取得完整的實作。

### <a name="creating-a-pcl"></a>建立 PCL

您的方案中加入可攜式類別庫是非常類似於一般的程式庫專案加入。

1. 在 **新的專案**對話方塊中，選取**多平台 > 文件庫 > 可攜式程式庫**選項：

    ![建立新的 PCL 專案](pcl-images/image2.png)

1. PCL 中建立時 Visual Studio for Mac 會自動設定為 Xamarin.iOS 和 Xamarin.Android 的運作方式的設定檔。 PCL 專案會出現此螢幕擷取畫面所示：

    ![在 solution pad 中的 PCL 專案](pcl-images/image3.png)

PCL 已準備好要加入的程式碼。 它也可以參考其他專案 （應用程式專案、 程式庫專案和甚至是其他 PCL 專案）。

### <a name="editing-pcl-settings"></a>編輯 PCL 設定

若要檢視及變更此專案的 PCL 設定，以滑鼠右鍵按一下專案，然後選擇**選項 > 建置 > 一般**查看畫面如下所示：

[![若要設定的設定檔的 PCL 專案選項](pcl-images/image4-sml.png)](pcl-images/image4.png#lightbox)

按一下 **變更...** 改變此可攜式類別庫的目標設定檔。

如果程式碼已新增至 PCL 之後變更設定檔，，就可以如果程式碼會參考不是在剛選取的設定檔的功能，將不再會編譯程式庫。

## <a name="working-with-a-pcl"></a>使用 PCL

PCL 程式庫中撰寫程式碼時，Visual Studio for Mac 編輯器會辨識選取的設定檔的限制，並據以調整自動完成選項。 比方說，此螢幕擷取畫面會顯示 System.IO 的自動完成選項使用預設設定檔 (Profile136) 用於 Visual Studio for Mac – 請注意這表示可用的類別中大約有一半會顯示捲軸 （事實上有 14可用類別）。

[![14 類別 System.IO 類別的 PCL 中的 Intellisense 清單](pcl-images/image6.png)](pcl-images/image6.png#lightbox)

比較，以自動完成中的 Xamarin.iOS 或 Xamarin.Android 專案 – System.IO 有 40 類別可包括常使用的類別，如`File`和`Directory`並不是任何 PCL 設定檔中。

[![40 類別在.NET Framework System.IO 命名空間中的 Intellisense 清單](pcl-images/image7.png)](pcl-images/image7.png#lightbox)

這會反映使用 PCL 的基礎的取捨，能夠順暢地跨多平台共用程式碼表示某些 Api 不提供給您，因為它們沒有跨所有可能的平台比較實作。

### <a name="using-pcl"></a>使用 PCL

一旦建立 PCL 專案，您可以新增任何相容的應用程式庫專案的參考，您通常會將參考的方式相同。 在 Visual Studio for Mac，以滑鼠右鍵按一下 參考 節點，然後選擇 **編輯參考...** 然後切換到**專案**索引標籤所示：

[![將參考加入 PCL，以透過 [編輯參考] 選項](pcl-images/image8.png)](pcl-images/image8.png#lightbox)

下列螢幕擷取畫面顯示 Solution pad TaskyPortable 範例應用程式中，顯示在下方和該 PCL 程式庫的參考 PCL 程式庫的 Xamarin.iOS 專案中。

[![TaskyPortable 範例解決方案顯示 PCL 專案](pcl-images/image9.png)](pcl-images/image9.png#lightbox)

PCL 中的輸出 (亦即。 產生的組件 DLL) 也會新增為大多數專案的參考。 這可讓 PCL 交付跨平台元件和程式庫的理想方式。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Visual Studio 逐步解說

本節逐步解說如何建立和使用可攜式類別庫，使用 Visual Studio。 請參閱 PCL 範例一節以取得完整的實作。

### <a name="creating-a-pcl"></a>建立 PCL

將 PCL 新增至您在 Visual Studio 中的解決方案是新增一般專案稍有不同：

1. 在 **加入新的專案**畫面上，選取**類別庫 （舊版可攜式）** 選項。 請注意右邊的描述會建議這種專案類型已被取代。

    [![若要建立可攜式類別庫的新增專案 視窗](pcl-images/image10-sml.png "可攜式類別庫")](pcl-images/image10.png#lightbox)

2. Visual Studio 會立即提示下列對話方塊，讓您可以設定設定檔。
 刻度的平台，您需要支援，並按 [確定]。

    [![選取程式庫的目標平台](pcl-images/image11-sml.png "勾選的平台，您需要支援，並按 [確定]")](pcl-images/image11.png#lightbox)

3. PCL 專案會出現在 [方案總管] 中所示&ndash;文字 **（可攜式）** 以指出它是 PCL 專案名稱旁邊會出現：

    ![PCL 設定檔所定義的.NET Framework](pcl-images/image12.png "PCL 設定檔所定義的.NET Framework")

PCL 已準備好要加入的程式碼。 它也可以參考其他專案 （應用程式專案、 程式庫專案和甚至是其他 PCL 專案）。

### <a name="editing-pcl-settings"></a>編輯 PCL 設定

PCL 設定可以檢視和變更專案上按一下滑鼠右鍵，然後選擇**屬性 > 程式庫**，在此螢幕擷取畫面所示：

[![編輯平台目標](pcl-images/image13-sml.png)](pcl-images/image13.png#lightbox)

如果程式碼已新增至 PCL 之後變更設定檔，，就可以如果程式碼會參考不是在剛選取的設定檔的功能，將不再會編譯程式庫。

> [!TIP]
> 另外還有一個訊息，告知， **。NETStandard 是建議的方法，來共用程式碼**。 這是表示，雖然 Pcl 中仍然受到支援，建議您升級至.NET Standard。

### <a name="working-with-a-pcl"></a>使用 PCL

PCL 程式庫中撰寫程式碼時，Visual Studio 會辨識選取的設定檔的限制，並據以調整 Intellisense 選項。 比方說，此螢幕擷取畫面會顯示 System.IO 的自動完成選項使用預設設定檔 (Profile136) – 請注意這表示可用的類別中大約有一半會顯示捲軸 （事實上有只有 14 的類別）。

[![PCL 中可用的 IO 類別已減少](pcl-images/image14.png)](pcl-images/image14.png#lightbox)

比較，與一般的專案-中的自動完成 System.IO 有 40 類別可包括常使用的類別，如`File`和`Directory`並不是任何 PCL 設定檔中。

[![許多更多 IO 類別在.NET Framework](pcl-images/image15.png)](pcl-images/image15.png#lightbox)

這會反映使用 PCL 的基礎的取捨，能夠順暢地跨多平台共用程式碼表示某些 Api 不提供給您，因為它們沒有跨所有可能的平台比較實作。

> [!TIP]
> .NET standard 2.0 表示更大的 API 介面的空間比 Pcl，包括 System.IO 命名空間。 對於新專案，.NET Standard 建議 PCL。

### <a name="using-pcl"></a>使用 PCL

一旦建立 PCL 專案，您可以新增任何相容的應用程式庫專案的參考，您通常會將參考的方式相同。 在 Visual Studio 中，以滑鼠右鍵按一下 [參考] 節點，然後選擇`Add Reference...`然後切換到**解決方案 > 專案**索引標籤所示：

[![將參考加入 PCL，以透過將參考專案 索引標籤](pcl-images/image16.png)](pcl-images/image16.png#lightbox)

下列螢幕擷取畫面顯示 TaskyPortable 範例應用程式中，顯示在下方和該 PCL 程式庫的參考 PCL 程式庫的 Xamarin.iOS 專案中的 [解決方案] 窗格。

[![顯示的 PCL 程式庫的 TaskyPortable 範例解決方案](pcl-images/image17.png)](pcl-images/image17.png#lightbox)

PCL 中的輸出 (亦即。 產生的組件 DLL) 也會新增為大多數專案的參考。
這可讓 PCL 交付跨平台元件和程式庫的理想方式。

-----

## <a name="pcl-example"></a>PCL 範例

[TaskyPortable](https://developer.xamarin.com/samples/mobile/TaskyPortable/)範例應用程式示範如何搭配 Xamarin 使用可攜式類別庫。
以下是產生在 iOS 和 Android 上執行的應用程式的一些螢幕擷取畫面：

[![](pcl-images/image18.png "以下是產生在 iOS、 Android 和 Windows Phone 上執行的應用程式的一些螢幕擷取畫面")](pcl-images/image18.png#lightbox)

它共用數個資料和邏輯是純粹的可攜式程式碼的類別，它也會示範如何將使用 SQLite 資料庫實作的相依性插入的平台特定需求。

方案結構如下所示 (在 Visual Studio for Mac 和 Visual Studio 分別):

[![](pcl-images/image19.png "方案結構是這裡顯示的 Visual Studio for Mac 和 Visual Studio 分別")](pcl-images/image19.png#lightbox)

因為針對示範目的，它已重構為抽象類別，可編譯至可攜式類別庫，SQLite NET 程式碼平台專屬的片段 （只適用於每個不同的作業系統上的 SQLite 實作） 和iOS 和 Android 專案中的子類別以實作到實際的程式碼。

### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

它可以支援的.NET 功能僅限於可攜式類別庫。 它會編譯成多個平台上執行，因為它不能使用`[DllImport]`SQLite NET 中使用的功能。 SQLite NET 是實作為抽象類別，而參考的共用程式碼的其餘部分。 如下所示的抽象 API 擷取內容：

```csharp
public abstract class SQLiteConnection : IDisposable {

    public string DatabasePath { get; private set; }
    public bool TimeExecution { get; set; }
    public bool Trace { get; set; }
    public SQLiteConnection(string databasePath) {
         DatabasePath = databasePath;
    }
    public abstract int CreateTable<T>();
    public abstract SQLiteCommand CreateCommand(string cmdText, params object[] ps);
    public abstract int Execute(string query, params object[] args);
    public abstract List<T> Query<T>(string query, params object[] args) where T : new();
    public abstract TableQuery<T> Table<T>() where T : new();
    public abstract T Get<T>(object pk) where T : new();
    public bool IsInTransaction { get; protected set; }
    public abstract void BeginTransaction();
    public abstract void Rollback();
    public abstract void Commit();
    public abstract void RunInTransaction(Action action);
    public abstract int Insert(object obj);
    public abstract int Update(object obj);
    public abstract int Delete<T>(T obj);

    public void Dispose()
    {
        Close();
    }
    public abstract void Close();

}
```

共用程式碼的其餘部分會使用 [儲存]，並從資料庫 「 擷取 」 物件的抽象類別。 在任何應用程式會使用這個抽象類別，我們必須傳入提供實際的資料庫功能的完整實作。

### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid 和 TaskyiOS

IOS 和 Android 應用程式專案包含使用者介面和其他平台特定程式碼用來連線接在 PCL 中的共用程式碼。

這些專案也包含實作抽象的資料庫在該平台運作的 API。 在 iOS 和 Android 的 Sqlite 資料庫引擎是內建於作業系統中，因此可以使用實作`[DllImport]`提供資料庫連接的具體實作所示。 平台特定實作程式碼的摘錄，如下所示：

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```

中的範例程式碼，就可以看到完整的實作。

## <a name="summary"></a>總結

這篇文章簡要討論可攜式類別庫的優缺點，示範了如何建立和使用 Pcl 從在 Visual Studio for Mac 和 Visual Studio;與最後導入了 PCL 顯示作用中的完整範例應用程式 – TaskyPortable –。

## <a name="related-links"></a>相關連結

- [TaskyPortable （範例）](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [可移植的 Visual Basic](~/cross-platform/platform/visual-basic/index.md)
- [共用的專案](~/cross-platform/app-fundamentals/shared-projects.md)
- [共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)
- [使用.NET Framework (Microsoft) 的跨平台開發](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)
