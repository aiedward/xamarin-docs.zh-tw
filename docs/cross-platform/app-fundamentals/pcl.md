---
title: 可攜式類別庫簡介
description: 本文介紹可攜式類別程式庫 (PCL) 專案，並逐步解說建立和使用 Visual Studio 中的 PCL 專案，適用於 Mac 和 Visual Studio。
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 799ee198f776b963bb8779d71bacc322001e2882
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="introduction-to-portable-class-libraries"></a>可攜式類別庫簡介

_本文介紹可攜式類別程式庫 (PCL) 專案，並逐步解說建立和使用 Visual Studio 中的 PCL 專案，適用於 Mac 和 Visual Studio。_


建置跨平台應用程式的重要元件能夠在各種平台專屬專案之間共用程式碼。 不過，這被複雜的事實不同平台通常會使用不同的子集合的.NET 基底類別程式庫 (BCL)，以及因此而實際上建立不同的.NET Core 文件庫設定檔。 這表示每個平台，只能使用類別庫，所以它們會顯示為每個平台需要個別的類別庫專案，以相同的設定檔為目標。

有三個主要方式來共用程式碼，解決此問題：**標準.NET 專案**，**可攜式類別程式庫 (PCL) 專案**，和**共用資產專案**.

- **標準.NET 專案** [.NET 標準](~/cross-platform/app-fundamentals/net-standard.md)。
-  **PCL**專案以支援一組已知的 BCL 類別/功能的特定設定檔為目標。 不過，要 PCL 缺點是它們通常需要額外架構設定檔的特定程式碼分隔成自己的程式庫的投入時間。 如需這兩種方法更詳細討論，請參閱[共用程式碼選項引導](~/cross-platform/app-fundamentals/code-sharing.md)。
-  **共用資產專案**使用單一組合的檔案與提供快速且簡單的方式，用來共用程式碼在方案內，通常會使用條件式編譯指示詞來指定用於將使用它 （更多的各種平台程式碼路徑資訊，請參閱[共用專案的發行項](~/cross-platform/app-fundamentals/shared-projects.md)和[設定 Xamarin 的跨平台解決方案指南](~/cross-platform/app-fundamentals/code-sharing.md))。


此頁面說明如何建立**PCL**專案的目標特定的設定檔，然後可以由多個平台專屬專案參考。


## <a name="what-is-a-portable-class-library"></a>什麼是 「 可攜式類別庫？

當您建立應用程式專案或程式庫專案時，產生的 DLL 會限制為建立在特定平台上運作。 這會讓您從寫入 Windows 應用程式中，組件，然後重新使用 Xamarin.iOS 和 Xamarin.Android 上。

當您建立可攜式類別庫時，不過，您可以選擇您想要在上執行程式碼的平台的組合。 相容性的選擇建立可攜式類別庫時將會轉譯成"設定檔 」 的識別碼，其中描述程式庫支援哪些平台。

下表顯示的某些功能會因.NET 平台。 若要撰寫一定會在特定的裝置/平台上執行的 PCL 組件您只要選擇您建立專案時，就需要支援。

|功能|.NET Framework|UWP 應用程式|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|核心|Y|Y|Y|Y|Y|
|LINQ|Y|Y|Y|Y|Y|
|IQueryable|Y|Y|Y|7.5 +|Y|
|序列化|Y|Y|Y|Y|Y|
|資料註釋|4.0.3 +|Y|Y||Y|

Xamarin 資料行反映了事實 Xamarin.iOS 和 Xamarin.Android 支援隨附於 Visual Studio 中的所有設定檔，並在您建立的任何程式庫功能的可用性會只受限於您選擇支援的平台。

這包括設定檔的組合：

-  .NET 4 或.NET 4.5
-  Silverlight 5
-  Windows Phone 8
-  UWP 應用程式

閱讀更多有關在不同的設定檔功能[Microsoft 的網站](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)並查看其他社群成員的[PCL 設定檔摘要](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY)包括支援的架構資訊以及其他注意事項。



建立共用的程式碼的 PCL 有一些與檔案連結的替代方式優缺點：


**權益**

1. 集中式的程式碼共用 – 中撰寫及測試程式碼以供其他程式庫或應用程式的單一專案。
1. 重構作業將會影響所有的程式碼方案 （可攜式類別庫及平台特定專案） 中載入。
1. 其他專案在方案中，可以輕鬆地參考 PCL 專案，或可供其他人在其方案中參考共用輸出組件。


**缺點**

1. 無法參考 （例如多個應用程式之間共用相同的可攜式類別庫，因為平台特定程式庫 Community.CsharpSqlite.WP7).
1. 可攜式類別庫子集可能不會包含類別，原本是用於 MonoTouch 和 Mono for Android （例如 DllImport 或 System.IO.File）。



在某個程度這兩個缺點可以規避使用的提供者模式或相依性插入程式碼在可攜式類別庫中定義的介面或基底類別針對平台專案中的實際實作。



下圖顯示跨平台應用程式共用程式碼，使用可攜式類別庫，但是也將在平台相依的功能中使用相依性插入的架構：



[![](pcl-images/image1.png "下圖顯示跨平台應用程式共用程式碼，使用可攜式類別庫，但是也將在平台相依的功能中使用相依性插入的架構")](pcl-images/image1.png#lightbox)



# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)



## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 逐步解說


本節逐步解說如何建立及使用可攜式類別庫，使用 Visual Studio for mac。 參考到的完整實作的 PCL 範例 > 一節。



### <a name="creating-a-pcl"></a>建立 PCL


可攜式類別庫加入您的方案是非常類似於將一般的程式庫專案。


1. 在 [新增專案] 對話方塊中選取`Multiplatform > Library > Portable Library`選項


    ![](pcl-images/image2.png "多平台 > 文件庫 > 可攜式程式庫")


1. PCL 中建立時 Visual Studio for Mac，就會自動設定適用於 Xamarin.iOS 和 Xamarin.Android 的設定檔。 PCL 專案會出現此螢幕擷取畫面所示：

    ![](pcl-images/image3.png "PCL 專案會出現此螢幕擷取畫面所示")

PCL 現在已準備好要加入的程式碼的。 它也可以參考其他專案 （應用程式專案、 程式庫專案和甚至是其他 PCL 專案）。



### <a name="editing-pcl-settings"></a>編輯 PCL 設定


若要檢視及變更此專案的 PCL 設定，以滑鼠右鍵按一下專案，然後選擇 **選項 > 建置 > 一般**查看畫面如下所示：



[![](pcl-images/image4.png "若要檢視及變更此專案的 PCL 設定，以滑鼠右鍵按一下專案，選擇 選項建置一般查看如下所示的畫面")](pcl-images/image4.png#lightbox)



在這個畫面上的設定會控制此特定 PCL 是相容的平台。 變更任何這些選項會改變此 PCL，依次控制功能可以是正在使用的設定檔使用可攜式程式碼中。



變更任何`Target Framework`選項會自動更新`Current Profile`; 如果選取了不相容的選項，畫面也會顯示警告。



[![](pcl-images/image5.png "變更任何目標架構選項會自動更新目前的設定檔畫面也會顯示警告，如果選取了不相容的選項")](pcl-images/image5.png#lightbox)



如果在設定檔之後加入 PCL 已新增過程式碼變更，，很可能如果程式碼會參考不屬於新選取的設定檔的功能，將不再編譯程式庫。


## <a name="working-with-a-pcl"></a>使用 PCL


當 PCL 程式庫中寫入程式碼時，Visual Studio for Mac 編輯器會辨識選取的設定檔的限制，並據以調整自動完成選項。 例如，此螢幕擷取畫面會顯示 System.IO 的自動完成選項使用 Visual Studio 中使用適用於 Mac 的預設設定檔 (Profile136) – 請注意表示大約有一半可用的類別會顯示捲軸 （事實上有 14類別可用）。



[![](pcl-images/image6.png "使用預設設定檔 Profile136 for Mac 注意事項，指出確實有大約有一半可用的類別顯示的捲軸只會提供 14 類別用於 Visual Studio 中的 IO")](pcl-images/image6.png#lightbox)



比較，使用自動完成在 Xamarin.iOS 或 Xamarin.Android 專案 – System.IO 分為 40 可用包括常使用的類別，如`File`和`Directory`並不是任何 PCL 設定檔中。



[![](pcl-images/image7.png "有可用包括常使用的類別，例如檔案和目錄中任何 PCL 設定檔不是 40 類別")](pcl-images/image7.png#lightbox)



這反映出使用 PCL 的基礎取捨 – 能夠順暢地跨多平台共用程式碼表示特定應用程式開發介面不是可供您使用，因為它們不需要可比較實作所有可能的平台。



### <a name="using-pcl"></a>使用 PCL


一旦建立 PCL 專案，您可以加入任何相容的應用程式庫專案的參考，您通常會將參考的方式相同。 在 Visual Studio for Mac，以滑鼠右鍵按一下 參考 節點，然後選擇 `Edit References…`然後切換到 專案 索引標籤所示：



[![](pcl-images/image8.png "在 Visual Studio for Mac，以滑鼠右鍵按一下 [參考] 節點，然後選擇編輯參考接著切換至 [專案] 索引標籤所示")](pcl-images/image8.png#lightbox)



下列螢幕擷取畫面顯示 TaskyPortable 範例應用程式顯示在與該 PCL 程式庫的參考 PCL 程式庫 Xamarin.iOS 專案中的方案板。



[![](pcl-images/image9.png "TaskyPortable 範例應用程式方案板")](pcl-images/image9.png#lightbox)



PCL 的輸出 (ie。 產生的組件 DLL) 也可以加入為大多數專案的參考。 這可讓 PCL 出貨跨平台元件和程式庫的理想方式。




# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)




## <a name="visual-studio-walkthrough"></a>Visual Studio 逐步解說


本節逐步解說如何建立及使用可攜式類別庫，使用 Visual Studio。 參考到的完整實作的 PCL 範例 > 一節。



### <a name="creating-a-pcl"></a>建立 PCL


在 Visual Studio 方案中加入 PCL 是加入一般專案稍有不同。



1. 在 [加入新的專案] 畫面中，選取`Portable Class Library`選項


    ![](pcl-images/image10.png "可攜式類別庫")


1. Visual Studio 會立即出現提示，以下列對話方塊，讓您可以設定設定檔。
 刻度的平台，您需要支援，並按下 [確定]。


    ![](pcl-images/image11.png "刻度的平台，您需要支援，並按下 [確定]")


1. PCL 專案會出現在 [方案總管] 中所示。 [參考] 節點會指出程式庫會使用.NET Framework （PCL 設定檔所定義） 的子集。

    ![](pcl-images/image12.png "NET Framework 由 PCL 檔定義")

PCL 現在已準備好要加入的程式碼的。 它也可以參考其他專案 （應用程式專案、 程式庫專案和甚至是其他 PCL 專案）。



### <a name="editing-pcl-settings"></a>編輯 PCL 設定


可以檢視和變更專案上按一下滑鼠右鍵，然後選擇 PCL 設定**屬性 > 程式庫**這個螢幕擷取畫面所示：



[![](pcl-images/image13.png "可以檢視和變更專案上按一下滑鼠右鍵，然後選擇內容庫，此螢幕擷取畫面所示的 PCL 設定")](pcl-images/image13.png#lightbox)



如果在設定檔之後加入 PCL 已新增過程式碼變更，，很可能如果程式碼會參考不屬於新選取的設定檔的功能，將不再編譯程式庫。



### <a name="working-with-a-pcl"></a>使用 PCL


當 PCL 程式庫中寫入程式碼時，Visual Studio 會辨識選取的設定檔的限制，並據以調整 Intellisense 選項。 例如，此螢幕擷取畫面會顯示 System.IO 的自動完成選項使用預設設定檔 (Profile136) – 請注意表示大約有一半可用的類別會顯示捲軸 （事實上有只 14 類別）。



[![](pcl-images/image14.png "使用預設設定檔 Profile136 IO")](pcl-images/image14.png#lightbox)



比較與一般的專案 – 中的自動完成 System.IO 有 40 類別可包括常使用的類別，如`File`和`Directory`並不是任何 PCL 設定檔中。



[![](pcl-images/image15.png "一般專案中的自動完成")](pcl-images/image15.png#lightbox)



這反映出使用 PCL 的基礎取捨 – 能夠順暢地跨多平台共用程式碼表示特定應用程式開發介面不是可供您使用，因為它們不需要可比較實作所有可能的平台。



### <a name="using-pcl"></a>使用 PCL


一旦建立 PCL 專案，您可以加入任何相容的應用程式庫專案的參考，您通常會將參考的方式相同。 在 Visual Studio 中，以滑鼠右鍵按一下 參考 節點，然後選擇 `Add Reference...`然後切換到**方案： 專案**索引標籤上所示：



[![](pcl-images/image16.png "專案 索引標籤所示")](pcl-images/image16.png#lightbox)



下列螢幕擷取畫面顯示 TaskyPortable 範例應用程式中，顯示與該 PCL 程式庫的參考 PCL 程式庫 Xamarin.iOS 專案中的 [方案] 窗格。



[![](pcl-images/image17.png "TaskyPortable 範例應用程式的 [方案] 窗格")](pcl-images/image17.png#lightbox)



PCL 的輸出 (ie。 產生的組件 DLL) 也可以加入為大多數專案的參考。
這可讓 PCL 出貨跨平台元件和程式庫的理想方式。




-----



## <a name="pcl-example"></a>PCL 範例


[TaskyPortable](https://developer.xamarin.com/samples/mobile/TaskyPortable/)範例應用程式示範如何使用 Xamarin 使用可攜式類別庫。
以下是一些產生在 iOS、 Android 和 Windows Phone 上執行的應用程式的螢幕擷取畫面：



[![](pcl-images/image18.png "以下是一些產生在 iOS、 Android 和 Windows Phone 上執行的應用程式的螢幕擷取畫面")](pcl-images/image18.png#lightbox)



它共用數個資料和邏輯是單純地移植的程式碼的類別，它也會示範如何將使用相依性插入 SQLite 資料庫實作的特定平台需求。




方案結構如下所示 (適用於 Mac 和 Visual Studio 的 Visual Studio 中分別):



[![](pcl-images/image19.png "方案結構是這裡顯示的 Visual Studio 用於 Mac 和 Visual Studio 分別")](pcl-images/image19.png#lightbox)



因為 SQLite NET 程式碼有平台專屬的部分 （可以使用每個不同的作業系統上的 SQLite 實作），針對示範目的，它已重構為抽象類別，可編譯為可攜式類別庫，和實際程式碼實作為 iOS 和 Android 專案中的子類別。



### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

可攜式類別庫會受限於它可以支援的.NET 功能。 它編譯為多個平台上執行，因為它不能使用`[DllImport]`SQLite 網路中使用的功能。 SQLite NET 是實作為抽象類別，而參考共用程式碼的其餘。 抽象應用程式開發介面擷取內容如下所示：


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


共用的程式碼的其餘部分使用抽象類別 [儲存]，並從資料庫 「 擷取 」 物件。 在任何應用程式會使用這個抽象類別，我們必須提供實際的資料庫功能的完整實作中傳遞。



### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid 和 TaskyiOS


IOS 和 Android 的應用程式專案包含使用者介面和其他平台專屬的程式碼用於網路上共用的程式碼的 PCL 中。



這些專案也包含抽象資料庫在該平台運作的應用程式開發介面的實作。 在 iOS 和 Android Sqlite database engine 是內建於作業系統中，因此可以使用實作`[DllImport]`提供資料庫連接的實體實作所示。 平台特定實作程式碼的摘錄，如下所示：


```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```


範例程式碼中可以看到完整的實作。

### <a name="taskywinphone"></a>TaskyWinPhone


Windows Phone 應用程式有使用 XAML 建置 UI，並包含其他平台專屬程式碼使用者介面連接共用的物件。



相較於使用適用於 iOS 和 Android 的實作，Windows Phone 應用程式必須建立和使用的執行個體`Community.Sqlite.dll`其抽象部分資料庫 API。 而不是使用`DllImport`，開啟類似的方法會實作針對 Community.Sqlite 組件中所參考`TaskWinPhone`專案。 摘錄，如下所示，使用 iOS 和 Android 版以上的比較


```csharp
public static Result Open(string filename, out Sqlite3.sqlite3 db)
{
    db = new Sqlite3.sqlite3();
    return (Result)Sqlite3.sqlite3_open(filename, ref db);
}

public static Result Close(Sqlite3.sqlite3 db)
{
    return (Result)Sqlite3.sqlite3_close(db);
}
```


## <a name="summary"></a>總結


本文簡短討論可攜式類別庫的優缺點，示範如何建立和使用 PCLs 從 Visual Studio for Mac 和 Visual Studio; 內最後所導入 PCL 顯示作用中的完整範例應用程式 – TaskyPortable –。


## <a name="related-links"></a>相關連結

- [TaskyPortable （範例）](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [移植 Visual Basic](~/cross-platform/platform/visual-basic/index.md)
- [共用的專案](~/cross-platform/app-fundamentals/shared-projects.md)
- [共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)
- [使用.NET Framework (Microsoft) 進行跨平台開發](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
