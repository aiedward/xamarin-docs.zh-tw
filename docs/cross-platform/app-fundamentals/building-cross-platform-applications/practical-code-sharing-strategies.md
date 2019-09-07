---
title: 第 5 部分 - 實用的程式碼共用策略
description: 本文件討論實際的程式碼共用策略的案例，例如資料庫、 檔案存取、 網路作業和非同步程式碼。
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: 3ce2530c6f9c81c287ff6c51c96fde12f3902cb8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762080"
---
# <a name="part-5---practical-code-sharing-strategies"></a>第 5 部分 - 實用的程式碼共用策略

本節提供如何共用常見的應用程式案例的程式碼的範例。

## <a name="data-layer"></a>資料層

資料層是由儲存引擎和方法來讀取和寫入資訊所組成。 效能、 彈性和跨平台的相容性的 SQLite 資料庫引擎被建議 Xamarin 跨平台應用程式。
它會在各種平台包括 Windows、 Android、 iOS 和 mac 上執行

### <a name="sqlite"></a>SQLite

SQLite 是開放原始碼資料庫實作。 來源和文件，請參閱[SQLite.org](http://www.sqlite.org/)。SQLite 支援是可在每個行動平台：

- **iOS** – 內建於作業系統。
- **Android** – 內建於作業系統自 Android 2.2 (API 層級 10)。
- **Windows** – 請參閱 <<c2> [ 適用於通用 Windows 平台延伸模組的 SQLite](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936)。

即使有了適用於所有平台的資料庫引擎，來存取資料庫的原生方法會不同。 這兩種 iOS 和 Android 提供內建的 Api，來存取可用的 SQLite 從 Xamarin.iOS 或 Xamarin.Android，不過使用原生 SDK 方法可提供無法共用程式碼 （也許是 SQL 查詢以外，假設它們儲存為字串）. 如需原生資料庫功能搜尋詳細資料`CoreData`在 iOS 或 Android 的`SQLiteOpenHelper`類別，因為這些選項不是跨平台已超過本文的範圍。

### <a name="adonet"></a>ADO.NET

Xamarin.iOS 和 Xamarin.Android 支援`System.Data`並`Mono.Data.Sqlite`(請參閱 Xamarin.iOS[文件](~/ios/data-cloud/system.data.md)如需詳細資訊)。
使用這些命名空間，可讓您撰寫 ADO.NET 程式碼可在兩個平台上運作。 編輯專案的參考，以包含`System.Data.dll`和`Mono.Data.Sqlite.dll`將這些 using 陳述式，以您的程式碼新增：

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

然後會使用下列的範例程式碼：

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

ADO.NET 的真實世界實作會很明顯地會分成不同的方法和類別 （此範例是僅針對示範目的）。

### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET-跨平台 ORM

ORM （或物件關聯式對應程式） 會嘗試簡化資料模型類別中的儲存體。 而非手動撰寫 SQL 查詢，該建立資料表或選取，再插入和刪除以手動方式從擷取的資料類別的欄位，並在屬性、 ORM 新增會為您的程式碼層。 您可以使用反映來檢查您的類別結構，ORM 可以自動建立資料表和資料行，以符合類別並產生查詢，來讀取和寫入資料。 這可讓應用程式程式碼，只需傳送和擷取的 ORM，會處理幕後的所有 SQL 作業的物件執行個體。

SQLite NET 做為簡單的 ORM，可讓您儲存和擷取您的類別，而在 Sqllite 中。 它會隱藏複雜度的跨平台的 SQLite 存取編譯器指示詞和其他技巧所組成。

SQLite NET 的功能：

- 資料表是透過將屬性加入至模型類別定義。
- 資料庫執行個體表示的子類別`SQLiteConnection`，SQLite Net 程式庫中的主要類別。
- 可插入的資料，查詢和刪除使用的物件。 （雖然您可以撰寫 SQL 陳述式，如有必要），不則需要任何 SQL 陳述式。
- 基本 Linq 查詢可以對 SQLite NET 所傳回的集合。

原始程式碼和 SQLite NET 的文件將會位於[github 上的 SQLite Net](https://github.com/praeclarum/sqlite-net) ，並已在這兩個案例研究中實作。 SQLite NET 程式碼的簡單範例 (來自*Tasky Pro*案例研究) 如下所示。

首先，`TodoItem`類別會使用屬性來定義資料庫的主索引鍵的欄位：

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

這可讓`TodoItem`資料表上使用下列程式碼 （和任何 SQL 陳述式） 來建立`SQLiteConnection`執行個體：

```csharp
CreateTable<TodoItem> ();
```

資料表中的資料也可使用其他方法操作上`SQLiteConnection`（同樣地，而不需要 SQL 陳述式）：

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

請參閱案例研究原始程式碼，如需完整範例。

## <a name="file-access"></a>檔案存取

檔案存取是一定是任何應用程式的重要部分。 常見範例可能是一部分的應用程式包含的檔案：

- SQLite 資料庫檔案。
- 使用者產生的資料 （文字、 影像、 聲音、 視訊）。
- 下載快取 （影像、 html 或 PDF 檔案） 的詳細資料。

### <a name="systemio-direct-access"></a>System.IO 直接存取

Xamarin.iOS 和 Xamarin.Android 允許使用類別中的檔案系統存取`System.IO`命名空間。

每個平台會有不同的存取限制必須列入考量：

- 具有非常有限的檔案系統存取權的沙箱中，執行 iOS 應用程式。 Apple 進一步決定您應該如何使用檔案系統藉由指定會備份的特定位置 （以及其他不是）。 請參閱[使用 Xamarin.iOS 中的檔案系統](~/ios/app-fundamentals/file-system.md)指南以取得詳細資料。
- Android 也會限制存取特定應用程式中，相關的目錄，但它也支援外部媒體 （例如。 Sd 記憶卡），並存取共用的資料。
- Windows Phone 8 (Silverlight) 不允許直接與檔案存取 – 檔案只可操作使用`IsolatedStorage`。
- Windows 8.1 WinRT 和 Windows 10 UWP 專案只會提供透過非同步檔案作業`Windows.Storage`不同於其他平台的 Api。

#### <a name="example-for-ios-and-android"></a>適用於 iOS 和 Android 的範例

寫入和讀取文字檔的簡單範例如下所示。
使用`Environment.GetFolderPath`允許執行 iOS 和 Android，讓每個傳回有效的目錄，根據其檔案系統的慣例上相同的程式碼。

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.ReadAllText (filePath));
```

參考 Xamarin.iOS[使用檔案系統](~/ios/app-fundamentals/file-system.md)如需有關 iOS 專用的 filesystem 功能的文件。 在撰寫跨平台檔案存取程式碼，請記住，有些檔案系統區分大小寫有不同的目錄分隔符號。 最好一律將相同的大小寫用於檔案名稱和`Path.Combine()`方法建構的檔案或目錄的路徑時。

### <a name="windowsstorage-for-windows-8-and-windows-10"></a>適用於 Windows 8 和 Windows 10 的 Windows.Storage

*使用 Xamarin.Forms 建立行動應用程式*[活頁簿](https://developer.xamarin.com/r/xamarin-forms/book/)
[第 20 章。Async 和檔案 I/O](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)包含[適用於 Windows 8.1 和 Windows 10 範例](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20)。

使用[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)可讀檔案在這些使用支援的 Api 的平台上的檔案：

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

請參閱[書籍章節](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)如需詳細資訊。

<a name="Isolated_Storage" />

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>在 Windows Phone 7 和 8 (Silverlight) 上的隔離儲存區

隔離儲存區是常見的 API 以儲存或載入檔案，跨所有的 iOS、 Android 和較舊的 Windows Phone 平台。

它是在 Windows Phone (Silverlight) 已實作在 Xamarin.iOS 和 Xamarin.Android 可讓一般的檔案存取程式碼，可寫入的檔案存取的預設機制。 `System.IO.IsolatedStorage`類別，可參考中的所有三個平台[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)。

請參閱[隔離儲存體概觀適用於 Windows Phone](https://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx)如需詳細資訊。

隔離的儲存體 Api 不適用於[可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)。 PCL 的一個替代選項是[PCLStorage NuGet](https://pclstorage.codeplex.com/)

### <a name="cross-platform-file-access-in-pcls"></a>Pcl 中的跨平台檔案存取

另外還有 PCL 相容 Nuget – [PCLStorage](https://www.nuget.org/packages/PCLStorage/) – 該設備的跨平台檔案存取 Xamarin 支援的平台和最新的 Windows Api。

## <a name="network-operations"></a>網路作業

大部分的行動應用程式將會有網路元件，例如：

- 下載影像、 視訊和音訊 （例如。 縮圖、 相片、 音樂）。
- 下載文件 （例如。 HTML、 PDF)。
- 正在上傳 （例如相片或文字） 的使用者資料。
- 存取 web 服務或第 3 方 （包括 SOAP、 XML 或 JSON） 的 Api。

.NET Framework 來存取網路資源提供了幾個不同的類別： `HttpClient`， `WebClient`，和`HttpWebRequest`。

### <a name="httpclient"></a>HttpClient

`HttpClient`類別中`System.Net.Http`命名空間是適用於 Xamarin.iOS、 Xamarin.Android 和大部分的 Windows 平台。 沒有[Microsoft HTTP 用戶端程式庫 Nuget](https://www.nuget.org/packages/Microsoft.Net.Http/) ，可以用來帶入可攜式類別庫 （和 Windows Phone 8 Silverlight） 中的此 API。

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

`WebClient`類別提供一個簡單的 API 來擷取遠端伺服器的遠端資料。

通用 Windows 平台 operations*必須*是非同步，即使 Xamarin.iOS 和 Xamarin.Android 支援同步作業 （這可以在背景執行緒上完成）。

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

 `WebClient` 也有`DownloadFileCompleted`和`DownloadFileAsync`來獨立擷取二進位資料。

<a name="HttpWebRequest" />

### <a name="httpwebrequest"></a>HttpWebRequest

`HttpWebRequest` 提供更多自訂比`WebClient`，因此需要更多的程式碼使用。

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

範例中我們[Web 服務文件](~/cross-platform/data-cloud/web-services/index.md)。

 <a name="Reachability" />

### <a name="reachability"></a>連線能力

行動裝置操作以各種不同的網路狀況，從快速的 Wi-fi 或 4g 連線不佳的接待區都有和緩慢的邊緣資料連結。 基於這個原因，是很好的做法，來偵測網路是否可用，因此，何種網路是否可用，然後再嘗試連接到遠端伺服器。

行動裝置應用程式可能需要在這些情況下的動作包括：

- 如果網路無法使用時，通知使用者。 如果它們已手動停用它 （例如。 飛航模式或關閉 Wi-fi） 然後他們可以解決此問題。
- 如果連線是 3g，應用程式可能會有不同的行為 （例如，Apple 不允許應用程式超過 20 Mb 下載超過 3g）。 應用程式可以使用這項資訊來警告使用者過多的下載逾時擷取大型檔案。
- 即使在網路為可用，最好要確認與目標伺服器的連線，然後再起始其他要求。 這會防止應用程式的網路作業逾時重複，並也允許更具參考性的錯誤訊息，以顯示給使用者。

沒有[Xamarin.iOS 範例](https://github.com/xamarin/monotouch-samples/tree/master/ReachabilitySample)可用 (根據 Apple[連線能力的範例程式碼](https://developer.apple.com/library/ios/#samplecode/Reachability/Introduction/Intro.html)) 來協助偵測網路可用性。

## <a name="webservices"></a>WebServices

請參閱我們的文件[搭配 Web 服務使用](~/cross-platform/data-cloud/web-services/index.md)，其中涵蓋存取 REST、 SOAP 和 WCF 使用 Xamarin.iOS 的端點。 它可手動製作 web 服務要求，並剖析的回應，但有可用來進行這簡單許多，包括 Azure、 RestSharp 和 ServiceStack 的程式庫。 即使基本的 WCF 作業可以存取 Xamarin 應用程式中。

### <a name="azure"></a>Azure

Microsoft Azure 是雲端平台，提供各種不同的行動應用程式，包括資料儲存體和同步處理，以及推播通知的服務。

請瀏覽[azure.microsoft.com](https://azure.microsoft.com/)來免費試用。

### <a name="restsharp"></a>RestSharp

RestSharp 是可以包含在提供可簡化存取 web 服務的 REST 用戶端的行動應用程式的.NET 程式庫。 它可協助藉由提供一個簡單的 API，可要求資料，以及剖析 REST 回應。 RestSharp 很有用

[RestSharp 網站](http://restsharp.org/)包含[文件](https://github.com/restsharp/RestSharp/wiki)如何實作使用 RestSharp REST 用戶端。
提供 Xamarin.iOS 和 Xamarin.Android 範例 RestSharp [github](https://github.com/restsharp/RestSharp/)。

另外還有 Xamarin.iOS 程式碼片段中的我們[Web 服務文件](~/cross-platform/data-cloud/web-services/index.md)。

 <a name="ServiceStack" />

### <a name="servicestack"></a>ServiceStack

不同於 RestSharp，ServiceStack 是裝載 web 服務，以及用戶端程式庫可以存取這些服務的行動應用程式中實作這兩個伺服器端解決方案。

[ServiceStack 網站](http://servicestack.net/)文件和程式碼範例來說明專案和連結的用途。 範例包括完整的伺服器端實作的 web 服務，以及各種用戶端應用程式可存取它。

沒有[Xamarin.iOS 範例](http://www.servicestack.net/monotouch/remote-info/)ServiceStack 網站上，程式碼片段中的我們[Web 服務文件](~/cross-platform/data-cloud/web-services/index.md)。

### <a name="wcf"></a>WCF

Xamarin 工具可協助您使用一些 Windows Communication Foundation (WCF) 服務。 一般情況下，Xamarin 支援相同的用戶端將在隨附的 Silverlight 執行階段的 WCF 中子集。 這包括最常見的編碼和通訊協定實作的 WCF： 文字編碼的 SOAP 訊息透過 HTTP 傳輸通訊協定使用`BasicHttpBinding`。

由於的大小和 WCF 架構的複雜度，可能會將範圍支援的範圍之外，Xamarin 的用戶端子集定義域的目前和未來的服務實作。 此外，WCF 支援需要使用工具，僅適用於以產生 proxy 的 Windows 環境。

 <a name="Threading" />

## <a name="threading"></a>執行緒

應用程式回應速度很重要的行動應用程式 – 使用者預期負載，並快速執行的應用程式。 '凍結' 螢幕，就會停止接受使用者輸入會出現，表示應用程式當機，因此很重要，不會佔用與長時間執行的封鎖呼叫，例如網路要求或緩慢的本機作業 （例如解壓縮檔案） 的 UI 執行緒。 特別是在啟動程序不應包含長時間執行工作 – 所有行動平台會終止耗費太多時間載入的應用程式。

這表示您的使用者介面應該實作 '進度列指示器' 或快速顯示，否則為 '使用的' UI 和非同步工作來執行背景作業。 執行背景工作所的使用的執行緒，這表示用來在通訊回到主執行緒以表明進度背景工作的需求，或何時完成。

 <a name="Parallel_Task_Library" />

### <a name="parallel-task-library"></a>工作平行程式庫

以平行的工作程式庫建立的工作可以非同步方式執行，並傳回其呼叫的執行緒，使其非常適用於觸發長時間執行的作業，而不會封鎖使用者介面上。

簡單的平行工作的作業可能會看起來像這樣：

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

索引鍵是`TaskScheduler.FromCurrentSynchronizationContext()`它會重複使用的呼叫方法的執行緒 SynchronizationContext.Current (這裡主執行緒執行`MainThreadMethod`) 做為封送處理後的呼叫該執行緒的方式。 這表示如果 UI 執行緒上呼叫方法，它會執行`ContinueWith`回 UI 執行緒上的作業。

如果程式碼從其他執行緒開始工作，使用下列模式來建立 UI 執行緒的參考，以及工作仍然可以呼叫傳回給它：

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />

### <a name="invoking-on-the-ui-thread"></a>在 UI 執行緒上叫用

不會利用平行的工作程式庫的程式碼，每個平台會有自己的語法，封送處理回 UI 執行緒的作業：

- **iOS** – `owner.BeginInvokeOnMainThread(new NSAction(action))`
- **Android** – `owner.RunOnUiThread(action)`
- **Xamarin.Forms** – `Device.BeginInvokeOnMainThread(action)`
- **Windows** – `Deployment.Current.Dispatcher.BeginInvoke(action)`

IOS 和 Android 的語法需要的 'context' 類別可表示的程式碼需要將這個物件傳遞至任何需要在 UI 執行緒上的回呼的方法。

若要讓 UI 執行緒呼叫共用程式碼中，請遵循[IDispatchOnUIThread 範例](https://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net)(由[ @follesoe ](http://jonas.follesoe.no/))。 宣告和程式以`IDispatchOnUIThread`介面中的共用程式碼，然後再實作的平台特定類別，如下所示：

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

使用 Xamarin.Forms 開發人員應該[ `Device.BeginInvokeOnMainThread` ](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads)中常見的程式碼 （共用專案或 PCL）。

 <a name="Platform_and_Device_Capabilities_and_Degradation" />

## <a name="platform-and-device-capabilities-and-degradation"></a>平台和裝置功能與效能降低

進一步的處理不同功能的特定範例提供的平台功能文件。 它會處理偵測不同的功能，以及如何依正常程序會降低應用程式提供良好的使用者經驗，即使應用程式無法操作，其完整潛能。
