---
title: 可移植類別庫簡介（PCL）
description: 本文介紹可移植的類別庫（PCL）專案，並逐步解說如何在 Visual Studio for Mac 和 Visual Studio 中建立和使用 PCL 專案。
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: 1684bddaf5b418f63abc7ee528f646f7795396d8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016736"
---
# <a name="portable-class-libraries-pcl"></a>可攜式類別庫 (PCL)

> [!TIP]
> 可移植的類別庫（Pcl）在最新版本的 Visual Studio 中視為已被取代。
> 雖然您仍然可以開啟、編輯和編譯 Pcl，但建議您針對新的專案使用[.NET Standard 程式庫](~/cross-platform/app-fundamentals/net-standard.md)來存取較大的 API 介面區。

建立跨平臺應用程式的主要元件，是能夠在各種平臺特定專案之間共用程式碼。 不過，這很複雜，因為不同的平臺通常會使用不同的 .NET 基類庫（BCL）子集合，因此實際上是建立在不同的 .NET Core 程式庫設定檔中。 這表示每個平臺只能使用以相同設定檔為目標的類別庫，因此它們會顯示為每個平臺需要個別的類別庫專案。

有三個主要的程式碼共用方法可解決此問題： **.NET Standard 專案**、**共用的資產專案**，以及**可移植的類別庫（PCL）專案**。

- **.NET Standard 專案**是共用 .net 程式碼的慣用方法，請參閱[.NET Standard 專案和 Xamarin](~/cross-platform/app-fundamentals/net-standard.md)的詳細資訊。
- **共用的資產專案**會使用一組檔案，並提供快速且簡單的方法，讓您在方案中共用程式碼，並且通常會採用條件式編譯指示詞，為使用它的各種平臺指定程式碼路徑（適用于更多資訊請參閱[共用專案一文](~/cross-platform/app-fundamentals/shared-projects.md)）。
- **PCL**專案以支援一組已知 BCL 類別/功能的特定設定檔為目標。 不過，向下到 PCL 的是，它們通常需要額外的架構工作，才能將設定檔特定的程式碼分隔到自己的程式庫中。

此頁面說明如何建立以特定設定檔為目標的**PCL**專案，然後可供多個平臺特定專案參考。

## <a name="what-is-a-portable-class-library"></a>什麼是可移植的類別庫？

當您建立應用程式專案或程式庫專案時，所產生的 DLL 會受到限制，以在其所建立的特定平臺上運作。 這可防止您撰寫 Windows 應用程式的元件，然後在 Xamarin 和 Xamarin 上重複使用它。

不過，當您建立可移植的類別庫時，您可以選擇您想要讓程式碼在其中執行的平臺組合。 您在建立可移植類別庫時所做的相容性選擇會轉譯成「設定檔」識別碼，其中描述程式庫支援的平臺。

下表顯示因 .NET 平臺而異的部分功能。 若要撰寫保證在特定裝置/平臺上執行的 PCL 元件，您只要選擇建立專案時所需的支援。

|特殊功能|.NET Framework|UWP 應用程式|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|核心|Y|Y|Y|Y|Y|
|LINQ|Y|Y|Y|Y|Y|
|IQueryable|Y|Y|Y|7.5 +|Y|
|序列化|Y|Y|Y|Y|Y|
|資料註釋|4.0.3 +|Y|Y||Y|

Xamarin 資料行反映了 Xamarin 和 Xamarin 支援 Visual Studio 隨附的所有設定檔，而您所建立之任何程式庫中的功能可用性只會受到您選擇支援的其他平臺所限制。

這包括下列組合的設定檔：

- .NET 4 或 .NET 4。5
- Silverlight 5
- Windows Phone 8
- UWP 應用程式

您可以閱讀更多有關[Microsoft 網站](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)上不同設定檔功能的資訊，並參閱另一個社區成員的[PCL 設定檔摘要](https://portablelibraryprofiles.stephencleary.com/)，其中包含支援的架構資訊和其他注意事項。

**權益**

1. 集中式程式碼共用–在可供其他程式庫或應用程式使用的單一專案中撰寫及測試程式碼。
2. 重構作業會影響方案中載入的所有程式碼（便攜類別庫和平臺特定專案）。
3. 方案中的其他專案可以輕鬆地參考 PCL 專案，或者可以共用輸出元件供其他人在其解決方案中參考。

**缺點**

1. 因為多個應用程式之間共用相同的可移植類別庫，所以無法參考平臺特定的程式庫（例如 CsharpSqlite. WP7）。
2. 可移植的類別庫子集可能不會包含在 MonoTouch 和 Mono for Android （例如 DllImport 或 system.string）中都可以使用的類別。

> [!NOTE]
> 可移植的類別庫已在最新版的 Visual Studio 中被取代，而改為使用[.NET Standard 程式庫](net-standard.md)。

在某種程度上，您可以使用提供者模式或相依性插入，針對在可移植類別庫中定義的介面或基類撰寫平臺專案中的實際實作為程式碼，以規避缺點。

此圖表顯示使用可移植類別庫來共用程式碼的跨平臺應用程式架構，但也使用相依性插入來傳遞與平臺相關的功能：

[![](pcl-images/image1.png "This diagram shows the architecture of a cross-platform application using a Portable Class Library to share code, but also using Dependency Injection to pass in platform-dependent features")](pcl-images/image1.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 逐步解說

本節逐步解說如何使用 Visual Studio for Mac 建立和使用可移植的類別庫。 如需完整的執行方式，請參閱 PCL 範例一節。

### <a name="creating-a-pcl"></a>建立 PCL

將可移植的類別庫新增至您的方案，非常類似于加入一般程式庫專案。

1. 在 [**新增專案**] 對話方塊中，選取 [多**平臺 > 程式庫 > 便攜程式庫**] 選項：

    ![建立新的 PCL 專案](pcl-images/image2.png)

1. 在中建立 PCL 時 Visual Studio for Mac 會使用適用于 Xamarin. iOS 和 Xamarin 的設定檔自動設定。 此時會出現 PCL 專案，如下列螢幕擷取畫面所示：

    ![Solution pad 中的 PCL 專案](pcl-images/image3.png)

PCL 現在已準備就緒，可供新增程式碼。 其他專案也可以參考它（應用程式專案、程式庫專案，甚至是其他 PCL 專案）。

### <a name="editing-pcl-settings"></a>編輯 PCL 設定

若要查看和變更此專案的 PCL 設定，請以滑鼠右鍵按一下專案，然後選擇 **選項 > 組建 > 一般**，以查看如下所示的畫面：

[![PCL 專案選項來設定設定檔](pcl-images/image4-sml.png)](pcl-images/image4.png#lightbox)

按一下 [**變更 ...** ]，以變更此可移植類別庫的目標設定檔。

如果在程式碼已新增至 PCL 之後變更了設定檔，當程式碼參考不屬於新選取之設定檔的功能時，程式庫就可能無法再編譯。

## <a name="working-with-a-pcl"></a>使用 PCL

當程式碼是以 PCL 程式庫撰寫時，[Visual Studio for Mac 編輯器] 將會辨識所選設定檔的限制，並據以調整自動完成選項。 例如，此螢幕擷取畫面顯示使用 Visual Studio for Mac 中使用的預設設定檔（Profile136）進行 System.IO 的自動完成選項-請注意，會顯示大約一半可用類別的捲軸（事實上，只有14個可用的類別）。

[在 PCL 的 System.IO 類別中![Intellisense 清單14個類別](pcl-images/image6.png)](pcl-images/image6.png#lightbox)

將其與 Xamarin 或 Xamarin 專案中的 System.IO 自動完成進行比較-有40類別可用，包括常用的類別，例如 `File` 和 `Directory`，但不在任何 PCL 設定檔中。

[在 .NET Framework System.IO 命名空間中![40 類別的 Intellisense 清單](pcl-images/image7.png)](pcl-images/image7.png#lightbox)

這反映了使用 PCL 的基礎取捨–能夠在許多平臺上順暢地共用程式碼，表示某些 Api 無法供您使用，因為它們在所有可能的平臺上都沒有可比較的執行。

### <a name="using-pcl"></a>使用 PCL

一旦建立了 PCL 專案，您就可以從任何相容的應用程式或程式庫專案加入其參考，就像平常加入參考一樣。 在 Visual Studio for Mac 中，以滑鼠右鍵按一下 [參考] 節點，然後選擇 [**編輯參考 ...** ]，然後切換至 [**專案**] 索引標籤，如下所示：

[![透過 [編輯參考] 選項新增 PCL 的參考](pcl-images/image8.png)](pcl-images/image8.png#lightbox)

下列螢幕擷取畫面顯示 TaskyPortable 範例應用程式的 Solution pad，其中顯示底部的 PCL 程式庫，以及在 Xamarin 專案中對該 PCL 程式庫的參考。

[顯示 PCL 專案的![TaskyPortable 範例解決方案](pcl-images/image9.png)](pcl-images/image9.png#lightbox)

PCL （也就是產生的元件 DLL）的輸出也可以加入做為大部分專案的參考。 這讓 PCL 成為傳送跨平臺元件和程式庫的理想方式。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Visual Studio 逐步解說

本節逐步解說如何使用 Visual Studio 建立和使用可移植的類別庫。 如需完整的執行方式，請參閱 PCL 範例一節。

### <a name="creating-a-pcl"></a>建立 PCL

在 Visual Studio 中將 PCL 新增至您的方案，與新增一般專案稍有不同：

1. 在 [**加入新專案**] 畫面中，選取 [**類別庫（舊版可移植）** ] 選項。 請注意，右側的描述會建議此專案類型已被取代。

    [![建立可移植類別庫的 [新增專案] 視窗](pcl-images/image10-sml.png "可攜式類別庫")](pcl-images/image10.png#lightbox)

2. Visual Studio 會立即以下列對話方塊提示，讓您可以設定設定檔。
 勾選您需要支援的平臺，然後按 [確定]。

    [![選取程式庫的目標平臺](pcl-images/image11-sml.png "勾選您需要支援的平臺，然後按 [確定]")](pcl-images/image11.png#lightbox)

3. PCL 專案會如方案總管所示出現，&ndash; 文字 **（便攜）** 出現在專案名稱旁邊，表示它是 PCL：

    ![PCL 設定檔所定義的 NET Framework](pcl-images/image12.png "PCL 設定檔所定義的 NET Framework")

PCL 現在已準備就緒，可供新增程式碼。 其他專案也可以參考它（應用程式專案、程式庫專案，甚至其他 PCL 專案）。

### <a name="editing-pcl-settings"></a>編輯 PCL 設定

以滑鼠右鍵按一下專案，然後選擇 [屬性] [ **> 程式庫**]，即可查看和變更 PCL 設定，如下列螢幕擷取畫面所示：

[![編輯平臺目標](pcl-images/image13-sml.png)](pcl-images/image13.png#lightbox)

如果在程式碼已新增至 PCL 之後變更了設定檔，當程式碼參考不屬於新選取之設定檔的功能時，程式庫就可能無法再編譯。

> [!TIP]
> 另外還有一則訊息，建議您 **。NETStandard 是共用程式碼的建議方法**。 這表示仍支援 Pcl 時，建議您升級至 .NET Standard。

### <a name="working-with-a-pcl"></a>使用 PCL

當程式碼是以 PCL 程式庫撰寫時，Visual Studio 將會辨識所選設定檔的限制，並據以調整 Intellisense 選項。 例如，此螢幕擷取畫面顯示使用預設設定檔（Profile136）的 System.IO 自動完成選項-請注意，會顯示大約一半可用類別的捲軸（事實上，只有14個類別可供使用）。

[![減少 PCL 中可用的 IO 類別數目](pcl-images/image14.png)](pcl-images/image14.png#lightbox)

將它與一般專案中的 System.IO 自動完成進行比較-有40類別可用，包括常用的類別，例如 `File` 和 `Directory`，而不在任何 PCL 設定檔中。

[![.NET Framework 中提供的多個 IO 類別](pcl-images/image15.png)](pcl-images/image15.png#lightbox)

這反映了使用 PCL 的基礎取捨–能夠在許多平臺上順暢地共用程式碼，表示某些 Api 無法供您使用，因為它們在所有可能的平臺上都沒有可比較的執行。

> [!TIP]
> .NET Standard 2.0 代表比 Pcl 更大的 API 介面區，包括 System.IO 命名空間。 針對新的專案，建議您透過 PCL 來 .NET Standard。

### <a name="using-pcl"></a>使用 PCL

一旦建立了 PCL 專案，您就可以從任何相容的應用程式或程式庫專案加入其參考，就像平常加入參考一樣。 在 Visual Studio 中，以滑鼠右鍵按一下 參考 節點，然後選擇 `Add Reference...` 然後切換至 **方案 > 專案** 索引標籤，如下所示：

[![透過 [加入參考專案] 索引標籤新增 PCL 的參考](pcl-images/image16.png)](pcl-images/image16.png#lightbox)

下列螢幕擷取畫面顯示 TaskyPortable 範例應用程式的 [解決方案] 窗格，並在底部顯示 PCL 程式庫，以及在 Xamarin 專案中顯示該 PCL 程式庫的參考。

[顯示 PCL 程式庫的![TaskyPortable 範例解決方案](pcl-images/image17.png)](pcl-images/image17.png#lightbox)

PCL （也就是產生的元件 DLL）的輸出也可以加入做為大部分專案的參考。
這讓 PCL 成為傳送跨平臺元件和程式庫的理想方式。

-----

## <a name="pcl-example"></a>PCL 範例

[TaskyPortable](https://docs.microsoft.com/samples/xamarin/mobile-samples/taskyportable/)範例應用程式會示範如何將可移植的類別庫與 Xamarin 搭配使用。
以下是在 iOS 和 Android 上執行之應用程式的一些螢幕擷取畫面：

[![](pcl-images/image18.png "Here are some screenshots of the resulting apps running on iOS, Android and Windows Phone")](pcl-images/image18.png#lightbox)

它會共用一些純粹是可移植程式碼的資料和邏輯類別，也會示範如何使用 SQLite 資料庫執行的相依性插入，來合併平臺特定的需求。

解決方案結構如下所示（分別在 Visual Studio for Mac 和 Visual Studio 中）：

[![](pcl-images/image19.png "The solution structure is shown here in Visual Studio for Mac and Visual Studio respectively")](pcl-images/image19.png#lightbox)

由於 SQLite 程式碼具有平臺特定的部分（在每個不同的作業系統上使用 SQLite 執行），因此為了示範目的，它已重構成可編譯成可移植類別庫的抽象類別，而在 iOS 和 Android 專案中實作為子類別的實際程式碼。

### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

可移植的類別庫在可支援的 .NET 功能中受到限制。 因為它編譯為在多個平臺上執行，所以無法利用 SQLite-NET 中使用的 `[DllImport]` 功能。 相反地，SQLite-NET 會實作為抽象類別，然後透過其餘的共用程式碼來參考。 抽象 API 的摘錄如下所示：

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

共用程式碼的其餘部分會使用抽象類別，從資料庫「儲存」和「取出」物件。 在使用這個抽象類別的任何應用程式中，我們必須傳入完整的執行，以提供實際的資料庫功能。

### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid 和 TaskyiOS

IOS 和 Android 應用程式專案包含使用者介面和其他平臺特定程式碼，用來連接 PCL 中的共用程式碼。

這些專案也包含在該平臺上運作的抽象資料庫 API 的執行。 在 iOS 和 Android 上，Sqlite 資料庫引擎內建于作業系統中，因此執行可以使用所示的 `[DllImport]` 來提供資料庫連接的具體執行。 以下顯示平臺特定的實作為程式碼摘錄：

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```

在範例程式碼中可以看到完整的執行。

## <a name="summary"></a>總結

本文簡要討論了可移植類別庫的優點和陷阱，示範如何從 Visual Studio for Mac 和 Visual Studio 內建立和使用 Pcl;最後，導入了一個完整的範例應用程式– TaskyPortable –它會顯示一個 PCL 的作用。

## <a name="related-links"></a>相關連結

- [TaskyPortable （範例）](https://docs.microsoft.com/samples/xamarin/mobile-samples/taskyportable/)
- [建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [可移植 Visual Basic](~/cross-platform/platform/visual-basic/index.md)
- [共用的專案](~/cross-platform/app-fundamentals/shared-projects.md)
- [共用程式碼選項](~/cross-platform/app-fundamentals/code-sharing.md)
- [使用 .NET Framework 進行跨平臺開發（Microsoft）](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)
