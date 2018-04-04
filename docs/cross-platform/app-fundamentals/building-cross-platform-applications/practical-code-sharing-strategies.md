---
title: 第 5-實用的程式碼共用策略
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: d5f639cffc8ff2d134731374bd72663fec81c6a0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="part-5---practical-code-sharing-strategies"></a>第 5-實用的程式碼共用策略

本節提供有關如何共用常見的應用程式案例的程式碼的範例。



## <a name="data-layer"></a>資料層

資料層是由儲存引擎和方法來讀取和寫入資訊所組成。 效能、 彈性和跨平台相容性 SQLite 資料庫引擎是建議使用 Xamarin 的跨平台應用程式。
在各種不同的平台包括 Windows、 Android、 iOS 和 mac 上執行


### <a name="sqlite"></a>SQLite

SQLite 是開放原始碼資料庫實作。 來源和文件，請參閱[SQLite.org](http://www.sqlite.org/)。SQLite 支援位於每個行動平台：

-  **iOS** – 內建於作業系統。
- **Android** – 內建於作業系統自 Android 2.2 (API 層級 10)。
- **Windows** – 請參閱[SQLite 對通用 Windows 平台擴充](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936)。


即使所有平台上的 database engine、 原生方法來存取資料庫所不同。 這兩種 iOS 和 Android 提供內建 Api Xamarin.iOS 或 Xamarin.Android 存取可用的 SQLite，但不使用原生 SDK 方法可提供不能共用程式碼 （可能是 SQL 查詢以外，假設它們儲存為字串）. 如需詳細資訊，關於原生資料庫功能搜尋`CoreData`在 iOS 或 Android 的`SQLiteOpenHelper`類別; 這些選項不會跨平台，因為它們超出本文的範圍。



### <a name="adonet"></a>ADO.NET

Xamarin.iOS 和 Xamarin.Android 支援`System.Data`和`Mono.Data.Sqlite`(請參閱 Xamarin.iOS[文件](~/ios/data-cloud/system.data.md)如需詳細資訊)。
使用這些命名空間，可讓您撰寫 ADO.NET 程式碼的兩個平台上運作。 編輯專案的參考，以包含`System.Data.dll`和`Mono.Data.Sqlite.dll`並加入下列 using 陳述式，您的程式碼：

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

則會使用下列範例程式碼：

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "items.db3");
bool exists = File.Exists (dbPath);
if (!exists)
    SqliteConnection.CreateFile (dbPath);
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open ();
if (!exists) {
    // This is the first time the app has run and/or that we need the DB.
    // Copy a "template" DB from your assets, or programmatically create one like this:
    var commands = new[]{
        "CREATE TABLE [Items] (Key ntext, Value ntext);",
        "INSERT INTO [Items] ([Key], [Value]) VALUES ('sample', 'text')"
    };
    foreach (var command in commands) {
        using (var c = connection.CreateCommand ()) {
            c.CommandText = command;
            c.ExecuteNonQuery ();
        }
    }
}
// use `connection`... here, we'll just append the contents to a TextView
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT [Key], [Value] from [Items]";
    var r = contents.ExecuteReader ();
    while (r.Read ())
        Console.Write("\n\tKey={0}; Value={1}",
                r ["Key"].ToString (),
                r ["Value"].ToString ());
}
connection.Close ();
```

ADO.NET 的實際實作會很明顯地會分成不同的方法和類別 （此範例中是僅針對示範目的）。



### <a name="sqlite-net--cross-platform-orm"></a>SQLite-網路-跨平台 ORM

ORM （或物件關聯對應程式） 會嘗試簡化資料模型化的類別中的儲存體。 而不是手動撰寫 SQL 查詢，該建立資料表或選取，INSERT 和 DELETE 手動從擷取的資料類別的欄位和屬性，ORM 加入圖層會為您的程式碼。 您可以使用反映來檢查您的類別結構，ORM 可以自動建立資料表和資料行符合類別，並產生查詢，來讀取和寫入資料。 這可讓應用程式程式碼就只傳送和擷取 ORM，負責底下的所有 SQL 作業的物件執行個體。

SQLite NET 做為簡單的 ORM 可讓您儲存和擷取 SQLite 類別。 它會隱藏複雜度跨平台 SQLite 存取的編譯器指示詞和其他技巧。

SQLite 網路的功能：

-  資料表會透過將屬性加入至模型類別定義。
-  資料庫執行個體由子類別的`SQLiteConnection`，SQLite Net 程式庫中的主要類別。
-  資料可以插入、 查詢和刪除使用的物件。 （雖然您可以撰寫 SQL 陳述式，如果需要） 需要任何 SQL 陳述式不。
-  基本 Linq 查詢可對 SQLite NET 所傳回的集合。


原始程式碼和文件 SQLite NET 位於[github 上的 SQLite Net](https://github.com/praeclarum/sqlite-net)和這兩個案例研究中已實作。 SQLite NET 程式碼的簡單範例 (從*Tasky Pro*案例研究) 如下所示。

首先，`TodoItem`類別會使用屬性來定義欄位，為資料庫主要金鑰：

```csharp
public class TodoItem : IBusinessEntity
{
    public TodoItem () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

這可讓`TodoItem`資料表上建立下列這行程式碼 （和任何 SQL 陳述式） 與`SQLiteConnection`執行個體：

```csharp
CreateTable<TodoItem> ();
```

資料表中的資料可以也使用其他方法在上進行操作`SQLiteConnection`（同樣地，不需要的 SQL 陳述式）：

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

請參閱案例研究原始碼，如需完整範例。



## <a name="file-access"></a>檔案存取

檔案存取是特定索引鍵的任何應用程式的一部分。 常見的可能屬於應用程式包含的檔案的範例：

-  SQLite 資料庫檔案。
-  使用者產生的資料 （文字、 影像、 音效、 視訊）。
-  下載快取 （影像、 html 或 PDF 檔案） 的詳細資料。




### <a name="systemio-direct-access"></a>System.IO 直接存取

Xamarin.iOS 和 Xamarin.Android 允許使用類別中的檔案系統存取`System.IO`命名空間。

每個平台有不同的存取限制，必須納入考量：

-  以非常有限的檔案系統存取在沙箱中，執行 iOS 應用程式。 Apple 進一步決定應該如何使用檔案系統藉由指定特定位置的備份 （以及其他不）。 請參閱[使用檔案系統中 Xamarin.iOS](~/ios/app-fundamentals/file-system.md)指南以取得詳細資料。
-  Android 也會限制存取特定目錄相關的應用程式，但它也支援外部媒體 （例如。 SD 卡） 和存取共用的資料。
-  Windows Phone 8 (Silverlight) 不允許直接檔案存取 – 檔案可以只操作使用`IsolatedStorage`。
-  Windows 8.1 WinRT 及 Windows 10 UWP 專案只提供了透過非同步檔案作業`Windows.Storage`Api，其不同於其他平台。

#### <a name="example-for-ios-and-android"></a>適用於 iOS 和 Android 的範例

寫入和讀取文字檔案的簡單範例如下所示。
使用`Environment.GetFolderPath`允許執行 iOS 和 Android，讓每個傳回有效的目錄，根據其 filesystem 慣例上相同的程式碼。

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.ReadAllText (filePath));
```

指 Xamarin.iOS[檔案系統的處理](~/ios/app-fundamentals/file-system.md)文件適用於 iOS 專屬 filesystem 功能的詳細資訊。 在撰寫跨平台的檔案存取程式碼時，請記住，有些檔案系統區分大小寫有不同的目錄分隔符號。 它是不錯的作法，一律使用相同的大小寫的檔名和`Path.Combine()`方法建構的檔案或目錄的路徑時。



### <a name="windowsstorage-for-windows-8-and-windows-10"></a>適用於 Windows 8 和 Windows 10 Windows.Storage

*建立行動應用程式使用 Xamarin.Forms* [書籍](https://developer.xamarin.com/r/xamarin-forms/book/)
[章 20。Async 和檔案 I/O](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)包含[適用於 Windows 8.1 和 Windows 10 範例](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20)。

使用[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)可讀取及檔案使用支援的 Api 這些平台上的檔案：

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

請參閱[書籍章節](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)如需詳細資訊。


<a name="Isolated_Storage" />

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>在 Windows Phone 7 和 8 (Silverlight) 上的隔離儲存區

隔離儲存區是一般的 API 以儲存或載入檔案，在 iOS、 Android 和較舊的 Windows Phone 平台。

它是預設的機制適用於在 Windows Phone (Silverlight) 已實作中 Xamarin.iOS 和 Xamarin.Android 允許一般的檔案存取程式碼，要寫入的檔案存取。 `System.IO.IsolatedStorage`類別可以參考中的所有三個平台[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)。

請參閱[隔離儲存區概觀適用於 Windows Phone](http://msdn.microsoft.com/en-us/library/windowsphone/develop/ff402541(v=vs.105).aspx)如需詳細資訊。

隔離儲存區 Api 也不適用於[可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)。 是一種替代方法，如 PCL [PCLStorage NuGet](https://pclstorage.codeplex.com/)



### <a name="cross-platform-file-access-in-pcls"></a>PCLs 中跨平台檔案存取

另外還有 PCL 相容 Nuget – [PCLStorage](https://www.nuget.org/packages/PCLStorage/) – 該設備跨平台的檔案存取 Xamarin 支援的平台和最新的 Windows 應用程式開發介面。


## <a name="network-operations"></a>網路作業

大部分的行動應用程式必須使用網路元件，例如：

-  下載影像、 視訊與音訊 （例如。 縮圖、 相片、 音樂）。
-  下載文件 （例如。 HTML、 PDF)。
-  正在上傳 （例如相片或文字） 的使用者資料。
-  存取 web 服務或第 3 個合作對象應用程式開發介面 （包括 SOAP、 XML 或 JSON）。


.NET Framework 提供了幾個不同的類別來存取網路資源： `HttpClient`， `WebClient`，和`HttpWebRequest`。

### <a name="httpclient"></a>HttpClient

`HttpClient`類別`System.Net.Http`命名空間位於 Xamarin.iOS、 Xamarin.Android 和大部分 Windows 平台。 沒有[Microsoft HTTP 用戶端程式庫 Nuget](https://www.nuget.org/packages/Microsoft.Net.Http/) ，可用來將此應用程式開發介面帶到可攜式類別庫 （和 Windows Phone 8 Silverlight）。

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

`WebClient`類別提供一個簡單的 API 來擷取遠端資料從遠端伺服器。

通用 Windows Platofrm 作業*必須*是非同步，即使 Xamarin.iOS 和 Xamarin.Android 支援同步作業 （這也可以在背景執行緒上完成）。

簡單的非同步程式碼`WebClient`作業：

```csharp
var webClient = new WebClient ();
webClient.DownloadStringCompleted += (sender, e) =>
{
    var resultString = e.Result;
    // do something with downloaded string, do UI interaction on main thread
};
webClient.Encoding = System.Text.Encoding.UTF8;
webClient.DownloadStringAsync (new Uri ("http://some-server.com/file.xml"));
```

 `WebClient` 也有`DownloadFileCompleted`和`DownloadFileAsync`來擷取二進位資料。

<a name="HttpWebRequest" />

### <a name="httpwebrequest"></a>HttpWebRequest

`HttpWebRequest` 提供多個自訂比`WebClient`，因此需要更多的程式碼使用。

同步簡單的程式碼`HttpWebRequest`作業：

```csharp
var request = HttpWebRequest.Create(@"http://some-server.com/file.xml ");
request.ContentType = "text/xml";
request.Method = "GET";
using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
    if (response.StatusCode != HttpStatusCode.OK)
        Console.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
    using (StreamReader reader = new StreamReader(response.GetResponseStream()))
    {
        var content = reader.ReadToEnd();
        // do something with downloaded string, do UI interaction on main thread
    }
}
```

範例中我們[Web Services 文件集](~/cross-platform/data-cloud/web-services/index.md)。

 <a name="Reachability" />


### <a name="reachability"></a>連線

以各種不同的網路狀況從快速 Wi-fi 或 4g 連線接收狀況不佳領域和邊緣資料連結變慢，行動裝置運作。 基於此原因，是很好的作法偵測網路是否可用，因此，何種網路是否可用，然後再嘗試連線到遠端伺服器。

行動裝置應用程式可能需要在這些情況下的動作包括：

-  如果網路無法使用時，通知使用者。 如果它們是以手動方式將它關閉 （例如 飛航模式或關閉 Wi-fi） 然後他們可以解決問題。
-  如果連線是 3g，應用程式可能會有不同的行為 （例如，Apple 不允許應用程式大於 20 Mb 下載超過 3g）。 應用程式可以使用這項資訊來警告使用者過多的下載逾時擷取大型檔案。
-  即使網路功能，最好要先確認與目標伺服器的連線再初始化其他要求。 這會防止應用程式的網路作業逾時重複，而且也允許更具資訊性的錯誤訊息，若要顯示給使用者。


沒有[Xamarin.iOS 範例](https://github.com/xamarin/monotouch-samples/tree/master/ReachabilitySample)可用 (根據 Apple[性範例程式碼](http://developer.apple.com/library/ios/#samplecode/Reachability/Introduction/Intro.html)) 來協助偵測網路可用性。


## <a name="webservices"></a>WebServices

請參閱我們的文件[搭配 Web 服務使用](~/cross-platform/data-cloud/web-services/index.md)，其中涵蓋了存取 REST、 SOAP 和 WCF 端點使用 Xamarin.iOS。 它可手動特殊功能的 web 服務要求，而剖析的回應，但是沒有可用來進行這更簡單，包括 Azure、 RestSharp 和 ServiceStack 的媒體櫃。 即使基本的 WCF 作業可以存取 Xamarin 應用程式中。

### <a name="azure"></a>Azure

Microsoft Azure 是雲端平台提供各種不同的行動裝置應用程式，包括資料儲存和同步處理，以及推播通知服務。

請瀏覽[azure.microsoft.com](https://azure.microsoft.com/)若要免費試用。

### <a name="restsharp"></a>RestSharp

RestSharp 是.NET 程式庫，可以包含在行動裝置應用程式提供簡化的 web 服務存取 REST 用戶端。 藉由提供一個簡單 API 來要求資料，並剖析 REST 回應，它可幫助。 RestSharp 可能很有用

[RestSharp 網站](http://restsharp.org/)包含[文件](https://github.com/restsharp/RestSharp/wiki)如何實作使用 RestSharp REST 用戶端。
提供 Xamarin.iOS 和 Xamarin.Android 範例 RestSharp [github](https://github.com/restsharp/RestSharp/)。

另外還有 Xamarin.iOS 程式碼片段中的我們[Web Services 文件集](~/cross-platform/data-cloud/web-services/index.md)。

 <a name="ServiceStack" />


### <a name="servicestack"></a>ServiceStack

不同於 RestSharp，ServiceStack 是裝載 web 服務，以及用戶端程式庫可以存取這些服務的行動應用程式中實作這兩個伺服器端解決方案。

[ServiceStack 網站](http://servicestack.net/)文件和程式碼範例說明專案和連結的用途。 範例包括完整的伺服器端實作的 web 服務，以及各種用戶端應用程式可以存取它。

沒有[Xamarin.iOS 範例](http://www.servicestack.net/monotouch/remote-info/)ServiceStack 網站，然後在程式碼片段上我們[Web Services 文件集](~/cross-platform/data-cloud/web-services/index.md)。


### <a name="wcf"></a>WCF

Xamarin 工具可協助您使用一些 Windows Communication Foundation (WCF) 服務。 一般情況下，Xamarin 支援相同隨附 Silverlight 執行階段的 WCF 用戶端子集。 這包括 WCF 的最常見編碼和通訊協定實作： 文字編碼 SOAP 訊息透過 HTTP 傳輸通訊協定使用`BasicHttpBinding`。

由於大小和 WCF 架構的複雜度，可能是由 Xamarin 的用戶端子集定義域都會歸類支援的範圍之外的目前和未來的服務實作。 此外，WCF 支援需要使用工具僅適用於 Windows 的環境，以產生 proxy。

 <a name="Threading" />


## <a name="threading"></a>執行緒

應用程式的回應是行動應用程式的重要，因為使用者預期載入並快速執行的應用程式。 '凍結' 螢幕會停止接受使用者輸入將出現，表示應用程式當機，因此務必不困 UI 執行緒與長時間執行的封鎖呼叫，例如網路要求或慢速本機作業 （例如解壓縮檔案）。 特別是在啟動程序不應包含長時間執行工作 – 所有行動平台將會終止的時間太長，無法載入應用程式。

這表示您的使用者介面應實作 '進度列指示器' 或快速顯示，否則 '使用的' UI 和執行背景作業的非同步工作。 執行背景工作需要的使用的執行緒，這表示背景工作需要可通訊回到主執行緒指出進度的方式，或當他們完成。

 <a name="Parallel_Task_Library" />


### <a name="parallel-task-library"></a>工作平行程式庫

使用平行工作的程式庫建立的工作可以以非同步方式執行，並傳回其呼叫的執行緒，讓它們非常有用，用於觸發長時間執行的作業，而不會封鎖的使用者介面上。

簡單的平行工作作業可能會看起來像這樣：

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

索引鍵是`TaskScheduler.FromCurrentSynchronizationContext()`這將會重複使用的執行緒呼叫方法 SynchronizationContext.Current (這裡主執行緒執行`MainThreadMethod`) 做為封送處理後的呼叫該執行緒的方式。 這表示如果 UI 執行緒上呼叫方法時，它會執行`ContinueWith`回 UI 執行緒上的作業。

如果程式碼來自其他執行緒啟動工作，請使用下列模式來建立參考至 UI 執行緒和工作仍然可以呼叫回它：

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />


### <a name="invoking-on-the-ui-thread"></a>UI 執行緒上叫用

不使用平行工作的程式庫的程式碼，每個平台都有自己的語法，封送處理回 UI 執行緒的作業：

-  **iOS** – `owner.BeginInvokeOnMainThread(new NSAction(action))`
-  **Android** – `owner.RunOnUiThread(action)`
-  **Xamarin.Forms** – `Device.BeginInvokeOnMainThread(action)`
-  **Windows** – `Deployment.Current.Dispatcher.BeginInvoke(action)`



IOS 和 Android 的語法必須有 'context' 類別可表示該程式碼必須將這個物件傳遞至任何需要 UI 執行緒上的回呼的方法。

若要讓 UI 執行緒呼叫中的共用程式碼，請遵循[IDispatchOnUIThread 範例](http://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net)(通過[ @follesoe ](http://jonas.follesoe.no/))。 宣告和程式以`IDispatchOnUIThread`介面中的共用程式碼，然後實作的平台專屬的類別，如下所示：

```csharp
// program to the interface in shared code
public interface IDispatchOnUIThread {
    void Invoke (Action action);
}
// iOS
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly NSObject owner;
    public DispatchAdapter (NSObject owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.BeginInvokeOnMainThread(new NSAction(action));
    }
}
// Android
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly Activity owner;
    public DispatchAdapter (Activity owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.RunOnUiThread(action);
    }
}
// WP7
public class DispatchAdapter : IDispatchOnUIThread {
    public void Invoke (Action action) {
        Deployment.Current.Dispatcher.BeginInvoke(action);
    }
}
```

Xamarin.Forms 開發人員應該使用[ `Device.BeginInvokeOnMainThread` ](~/xamarin-forms/platform/device.md#Device_BeginInvokeOnMainThread)中常見的程式碼 （共用專案或 PCL）。

 <a name="Platform_and_Device_Capabilities_and_Degradation" />


## <a name="platform-and-device-capabilities-and-degradation"></a>平台和裝置功能與效能衰退

進一步將平台功能的文件中提供不同的功能與處理的特定範例。 它說明如何偵測不同的功能，以及如何依正常程序會降低應用程式提供良好的使用者經驗，即使應用程式無法運作，以其完整的可能性。
