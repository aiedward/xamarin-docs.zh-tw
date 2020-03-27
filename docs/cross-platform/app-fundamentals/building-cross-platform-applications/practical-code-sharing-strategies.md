---
title: 第 5 部分 - 實用的程式碼共用策略
description: 這份檔討論資料庫、檔案存取、網路作業和非同步程式碼等案例的實用程式碼共用策略。
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 0e37e138607fb0e00fbdc463ac7c53facf81395d
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292480"
---
# <a name="part-5---practical-code-sharing-strategies"></a>第 5 部分 - 實用的程式碼共用策略

本節提供一些範例，說明如何共用常見應用程式案例的程式碼。

## <a name="data-layer"></a>資料層

資料層是由儲存引擎和用來讀取和寫入資訊的方法所組成。 針對效能、彈性和跨平臺的相容性，建議將 SQLite 資料庫引擎用於 Xamarin 跨平臺應用程式。
它會在各種不同的平臺上執行，包括 Windows、Android、iOS 和 Mac。

### <a name="sqlite"></a>SQLite

SQLite 是一個開放原始碼的資料庫執行。 您可以在[SQLite.org](https://www.sqlite.org/)找到來源和檔。每個行動平臺都有 SQLite 支援：

- **iOS** –內建于作業系統中。
- **Android** –自 android 2.2 起，內建于作業系統中（API 層級10）。
- **Windows** –請參閱[SQLite for 通用 Windows 平臺延伸](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936)模組。

即使在所有平臺上都有可用的資料庫引擎，存取資料庫的原生方法也會不同。 IOS 和 Android 都提供內建 Api 來存取可以從 Xamarin 或 Xamarin 使用的 SQLite，不過，使用原生 SDK 方法並不提供共用程式碼的能力（可能是 SQL 查詢本身，假設它們是儲存為字串）. 如需原生資料庫功能的詳細資訊，請在 iOS 或 Android 的 `SQLiteOpenHelper` 類別中搜尋 `CoreData`;因為這些選項不是跨平臺的，所以不屬於本檔的討論範圍。

### <a name="adonet"></a>ADO.NET

Xamarin. iOS 和 Xamarin 都支援 `System.Data` 和 `Mono.Data.Sqlite` （如需詳細資訊，請參閱《 Xamarin 》[檔](~/ios/data-cloud/system.data.md)）。
使用這些命名空間可讓您撰寫可在兩個平臺上運作的 ADO.NET 程式碼。 編輯專案的參考以包含 `System.Data.dll` 和 `Mono.Data.Sqlite.dll`，並將這些 using 語句新增至您的程式碼：

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

然後，下列範例程式碼將可正常執行：

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

真實世界的 ADO.NET 實現顯然會分割成不同的方法和類別（此範例僅供示範之用）。

### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET –跨平臺 ORM

ORM （或物件關聯式對應程式）會嘗試簡化在類別中模型化之資料的儲存。 ORM 並不是手動撰寫 SQL 查詢來建立資料表，或選取、插入和刪除手動從類別欄位和屬性中解壓縮的資料，而是會加入一層程式碼來為您執行這項工作。 使用反映來檢查類別的結構，ORM 可以自動建立符合類別的資料表和資料行，並產生查詢以讀取和寫入資料。 這可讓應用程式程式碼直接傳送物件實例，並將其抓取到 ORM，這會處理幕後的所有 SQL 作業。

SQLite-NET 作為簡單的 ORM，可讓您在 SQLite 中儲存和取出您的類別。 它使用編譯器指示詞和其他訣竅的組合，來隱藏跨平臺 SQLite 存取的複雜性。

SQLite-NET 的功能：

- 資料表是藉由將屬性新增至模型類別來定義。
- 資料庫實例是以 `SQLiteConnection` 的子類別（SQLite 程式庫中的主要類別）來表示。
- 您可以使用物件來插入、查詢和刪除資料。 不需要任何 SQL 語句（雖然您可以視需要撰寫 SQL 語句）。
- 基本的 Linq 查詢可以在 SQLite-NET 傳回的集合上執行。

SQLite 的原始程式碼和檔可從[github 上的 sqlite](https://github.com/praeclarum/sqlite-net)取得，並已在兩個案例研究中實行。 以下顯示一個簡單的 SQLite-NET 程式碼範例（來自*Tasky Pro*案例研究）。

首先，`TodoItem` 類別會使用屬性，將欄位定義為資料庫主要金鑰：

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

這可讓您在 `SQLiteConnection` 實例上，使用下列程式程式碼（而不是 SQL 語句）來建立 `TodoItem` 資料表：

```csharp
CreateTable<TodoItem> ();
```

資料表中的資料也可以在 `SQLiteConnection` 上使用其他方法來操作（同樣地，不需要 SQL 語句）：

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

如需完整範例，請參閱案例研究原始程式碼。

## <a name="file-access"></a>檔案存取

檔案存取權一定是任何應用程式的重要部分。 可能是應用程式一部分的常見檔案範例包括：

- SQLite 資料庫檔案。
- 使用者產生的資料（文字、影像、音效、影片）。
- 已下載用於快取的資料（影像、html 或 PDF 檔案）。

### <a name="systemio-direct-access"></a>System.IO 直接存取

Xamarin. iOS 和 Xamarin 都允許使用 `System.IO` 命名空間中的類別來存取檔案系統。

每個平臺都有不同的存取限制，必須納入考慮：

- iOS 應用程式會在具有非常限制的檔案系統存取權的沙箱中執行。 Apple 會藉由指定已備份的特定位置（以及其他不是），進一步決定您應該如何使用檔案系統。 如需詳細資訊，請參閱[使用 Xamarin 中的檔案系統](~/ios/app-fundamentals/file-system.md)指南。
- Android 也會限制存取與應用程式相關的特定目錄，但它也支援外部媒體（例如 SD 記憶卡）和存取共用資料。
- Windows Phone 8 （Silverlight）不允許直接存取檔案–檔案只能使用 `IsolatedStorage`來操作。
- Windows 8.1 WinRT 和 Windows 10 UWP 專案僅透過 `Windows.Storage` Api 提供非同步檔案作業，這與其他平臺不同。

#### <a name="example-for-ios-and-android"></a>適用于 iOS 和 Android 的範例

寫入和讀取文字檔的簡單範例如下所示。
使用 `Environment.GetFolderPath` 可讓相同的程式碼在 iOS 和 Android 上執行，每個都根據其檔案系統慣例傳回有效的目錄。

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.File.ReadAllText (filePath));
```

如需有關 iOS 特定 filesystem 功能的詳細資訊，請參閱使用[檔案系統檔](~/ios/app-fundamentals/file-system.md)的 Xamarin。 撰寫跨平臺檔案存取碼時，請記住，有些檔案系統會區分大小寫，而且有不同的目錄分隔符號。 在建立檔案或目錄路徑時，一律使用相同的檔案名和 `Path.Combine()` 方法的大小寫是很好的作法。

### <a name="windowsstorage-for-windows-8-and-windows-10"></a>Windows 8 和 Windows 10 的儲存空間

*使用 Xamarin Forms*
 建立 Mobile Apps[ 書[第20章。Async 和 File i/o](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) 包括 [Windows 8.1 和 Windows 10 的範例](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20)。

使用[`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md)可以使用支援的 api，在這些平臺上讀取和檔案檔案：

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

如需詳細資訊，請參閱[書籍章節](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)。

<a name="Isolated_Storage" />

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Windows Phone 7 & 8 （Silverlight）上的隔離儲存區

隔離儲存區是在所有 iOS、Android 和舊版 Windows Phone 平臺上儲存和載入檔案的通用 API。

這是 Windows Phone （Silverlight）中已在 Xamarin. iOS 和 Xamarin 中執行之檔案存取的預設機制，可允許寫入一般檔案存取程式碼。 在[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)中的所有三個平臺上，都可以參考 `System.IO.IsolatedStorage` 類別。

如需詳細資訊，請參閱[隔離儲存區總覽](https://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx)中的 Windows Phone。

獨立的儲存 Api 無法在[可移植的類別庫](~/cross-platform/app-fundamentals/pcl.md)中使用。 PCL 的另一個替代方法是[PCLStorage NuGet](https://pclstorage.codeplex.com/)

### <a name="cross-platform-file-access-in-pcls"></a>Pcl 中的跨平臺檔案存取

另外還有 PCL 相容的 NuGet – [PCLStorage](https://www.nuget.org/packages/PCLStorage/) –可跨平臺檔案存取 Xamarin 支援的平臺和最新的 Windows api。

## <a name="network-operations"></a>網路作業

大部分的行動應用程式都有網路元件，例如：

- 下載影像、影片和音訊（例如 縮圖、相片、音樂）。
- 下載檔案（例如 HTML、PDF）。
- 上傳使用者資料（例如相片或文字）。
- 存取 web 服務或協力廠商 Api （包括 SOAP、XML 或 JSON）。

.NET Framework 提供幾種不同的類別來存取網路資源： `HttpClient`、`WebClient`和 `HttpWebRequest`。

### <a name="httpclient"></a>HttpClient

`System.Net.Http` 命名空間中的 `HttpClient` 類別可在 Xamarin. iOS、Xamarin 和大部分的 Windows 平臺中使用。 您可以使用[MICROSOFT HTTP 用戶端程式庫 NuGet](https://www.nuget.org/packages/Microsoft.Net.Http/) ，將此 API 帶入可移植的類別庫（和 Windows Phone 8 Silverlight）。

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

`WebClient` 類別提供簡單的 API 來抓取遠端伺服器的遠端資料。

通用 Windows 平臺作業*必須*是非同步，即使是 Xamarin 和 xamarin 也支援同步作業（這可以在背景執行緒上完成）。

簡單非同步 `WebClient` 作業的程式碼為：

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

 `WebClient` 也有 `DownloadFileCompleted` 和 `DownloadFileAsync` 來抓取二進位資料。

<a name="HttpWebRequest" />

### <a name="httpwebrequest"></a>HttpWebRequest

`HttpWebRequest` 提供比 `WebClient` 更多的自訂，因此需要更多的程式碼才能使用。

簡單同步 `HttpWebRequest` 作業的程式碼為：

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

我們的[Web 服務檔](~/cross-platform/data-cloud/web-services/index.md)中有一個範例。

 <a name="Reachability" />

### <a name="reachability"></a>達

行動裝置會在各種網路狀況下運作，從快速的 Wi-fi 或4G 連線到不佳的接收區域和緩慢的邊緣資料連結。 因此，在嘗試連線到遠端伺服器之前，最好先偵測網路是否可用，以及是否有可用的網路類型。

行動應用程式在這些情況下可能會採取的動作包括：

- 如果網路無法使用，請通知使用者。 如果他們已手動停用它（例如 飛機模式或關閉 Wi-fi）之後，他們就可以解決問題。
- 如果連接是3G，應用程式可能會有不同的行為（例如，Apple 不允許超過20Mb 的應用程式透過3G 下載）。 應用程式可以使用此資訊，在抓取大型檔案時警告使用者過多的下載時間。
- 即使網路可用，在起始其他要求之前，最好先確認與目標伺服器之間的連線能力。 這會讓應用程式的網路作業無法重複計時，同時也允許向使用者顯示更具資訊性的錯誤訊息。

## <a name="webservices"></a>WebServices

請參閱我們關於使用[Web 服務](~/cross-platform/data-cloud/web-services/index.md)的檔，其中涵蓋了使用 Xamarin 來存取 REST、SOAP 和 WCF 端點。 您可以手動製作 web 服務要求並剖析回應，不過有一些程式庫可讓它更簡單，包括 Azure、RestSharp 和 Servicestack.redis 時。 即使是基本的 WCF 作業也可以在 Xamarin 應用程式中存取。

### <a name="azure"></a>Azure

Microsoft Azure 是雲端平臺，可為行動應用程式提供各種不同的服務，包括資料儲存和同步處理，以及推播通知。

請造訪[azure.microsoft.com](https://azure.microsoft.com/)免費試用。

### <a name="restsharp"></a>RestSharp

RestSharp 是一種 .NET 程式庫，可以包含在行動應用程式中，以提供可簡化 web 服務存取的 REST 用戶端。 它會提供簡單的 API 來要求資料並剖析 REST 回應，藉此協助。 RestSharp 很有用

[RestSharp 網站](http://restsharp.org/)包含如何使用 RestSharp 來執行 REST 用戶端的[檔](https://github.com/restsharp/RestSharp/wiki)。
RestSharp 在[github](https://github.com/restsharp/RestSharp/)上提供了 Xamarin 和 xamarin 的範例。

我們的[Web 服務檔](~/cross-platform/data-cloud/web-services/index.md)中也有一個 Xamarin. iOS 程式碼片段。

 <a name="ServiceStack" />

### <a name="servicestack"></a>Servicestack.redis 時

不同于 RestSharp，Servicestack.redis 時是裝載 web 服務的伺服器端解決方案，也是可在行動應用程式中執行以存取這些服務的用戶端程式庫。

[Servicestack.redis 時網站](http://servicestack.net/)會說明專案的用途，以及檔和程式碼範例的連結。 這些範例包括 web 服務的完整伺服器端執行，以及可存取它的各種用戶端應用程式。

### <a name="wcf"></a>WCF

Xamarin 工具可協助您使用一些 Windows Communication Foundation （WCF）服務。 一般來說，Xamarin 支援 Silverlight 執行時間隨附之 WCF 的相同用戶端子集。 這包括 WCF 的最常見編碼和通訊協定執行：使用 `BasicHttpBinding`透過 HTTP 傳輸通訊協定進行文字編碼的 SOAP 訊息。

由於 WCF 架構的大小和複雜度，可能會有目前和未來的服務執行，而不在 Xamarin 的用戶端子集網域支援的範圍外。 此外，WCF 支援需要使用僅在 Windows 環境中提供的工具來產生 proxy。

 <a name="Threading" />

## <a name="threading"></a>執行緒

應用程式回應性對於行動應用程式很重要–使用者希望應用程式能夠快速載入並執行。 停止接受使用者輸入的「凍結」畫面會出現，指出應用程式已損毀，因此請務必不要將 UI 執行緒與長時間執行的封鎖呼叫（例如網路要求或緩慢的本機作業（例如解壓縮檔案）結合在一起。 特別是啟動程式不應包含長時間執行的工作–所有行動平臺都會終止載入時間太長的應用程式。

這表示您的使用者介面應該會執行「進度指標」或其他可快速顯示的「可用」 UI，以及執行背景作業的非同步工作。 執行背景工作需要使用執行緒，這表示背景工作需要一種方式來與主執行緒通訊，以指出進度或完成的時間。

 <a name="Parallel_Task_Library" />

### <a name="parallel-task-library"></a>平行工作程式庫

使用平行工作程式庫建立的工作可以在其呼叫執行緒上以非同步方式執行並傳回，使其非常適合用來觸發長時間執行的作業，而不會封鎖使用者介面。

簡單的平行工作作業看起來可能像這樣：

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

索引鍵是 `TaskScheduler.FromCurrentSynchronizationContext()` 的，它會重複使用 SynchronizationCoNtext，這是呼叫方法的執行緒（在這裡 `MainThreadMethod`執行的主要執行緒），以封送處理該執行緒的呼叫。 這表示如果在 UI 執行緒上呼叫方法，它會在 UI 執行緒上執行 `ContinueWith` 作業。

如果程式碼是從其他執行緒啟動工作，請使用下列模式來建立 UI 執行緒的參考，而且工作仍然可以回呼它：

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />

### <a name="invoking-on-the-ui-thread"></a>在 UI 執行緒上叫用

針對不使用平行工作程式庫的程式碼，每個平臺都有自己的語法可將作業封送處理回 UI 執行緒：

- **iOS** – `owner.BeginInvokeOnMainThread(new NSAction(action))`
- **Android** – `owner.RunOnUiThread(action)`
- **Xamarin. Forms** – `Device.BeginInvokeOnMainThread(action)`
- **Windows** – `Deployment.Current.Dispatcher.BeginInvoke(action)`

IOS 和 Android 語法都需要有「內容」類別，這表示程式碼需要將此物件傳遞至在 UI 執行緒上需要回呼的任何方法。

若要在共用程式碼中進行 UI 執行緒呼叫，請遵循[IDispatchOnUIThread 範例](https://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net)（ [@follesoe](https://twitter.com/follesoe)）。 在共用程式碼中，將和設計為 `IDispatchOnUIThread` 介面，然後執行平臺特定的類別，如下所示：

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

Xamarin 開發人員應該使用通用程式碼中的[`Device.BeginInvokeOnMainThread`](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) （共用的專案或 PCL）。

 <a name="Platform_and_Device_Capabilities_and_Degradation" />

## <a name="platform-and-device-capabilities-and-degradation"></a>平臺和裝置功能和效能降低

如需處理不同功能的進一步特定範例，請參閱平臺功能檔。 它負責偵測不同的功能，以及如何以適當的方式降低應用程式的效能，以提供良好的使用者體驗，即使應用程式無法充分發揮潛能。
