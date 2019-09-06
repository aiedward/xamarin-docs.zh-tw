---
title: Web 服務簡介
description: 本指南示範如何使用不同的 web 服務技術。 涵蓋的主題包括與 REST 服務、SOAP 服務和 Windows Communication Foundation 服務的通訊。
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: 3e5ce4496b4a2deb86bb2b7dd6d36e5e9b3f446c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287566"
---
# <a name="introduction-to-web-services"></a>Web 服務簡介

_本指南示範如何使用不同的 web 服務技術。涵蓋的主題包括與 REST 服務、SOAP 服務和 Windows Communication Foundation 服務的通訊。_

為了正確運作，許多行動應用程式都依存于雲端，因此將 web 服務整合到行動應用程式是常見的案例。 Xamarin 平臺支援使用不同的 web 服務技術，並包含使用 RESTful、.ASMX 和 Windows Communication Foundation （WCF）服務的內建和協力廠商支援。

針對使用 Xamarin 的客戶，有完整的範例會在 Xamarin 中使用這些技術[。 Forms Web Services](~/xamarin-forms/data-cloud/index.yml)檔。

> [!IMPORTANT]
> 在 iOS 9 中，應用程式傳輸安全性（ATS）會強制執行網際網路資源（例如應用程式的後端伺服器）與應用程式之間的安全連線，以防止意外洩漏機密資訊。
> 針對 iOS 9 所建置的應用程式中的預設會啟用 ATS，因為所有連線將會都受限於 ATS 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。

如果無法針對網際網路資源使用`HTTPS`通訊協定和安全通訊，您可以退出宣告 ATS。 這可以藉由更新應用程式的達成**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="rest"></a>REST

Representational State Transfer (REST) 是建置 web 服務的架構樣式。 REST 要求都是透過 HTTP 使用網頁瀏覽器使用來擷取 web 網頁，並將資料傳送至伺服器的相同 HTTP 動詞命令。 動詞命令是：

- **取得**– 這項作業用來從 web 服務擷取資料。
- **POST** – 這項作業用來建立 web 服務上的資料的新項目。
- **PUT** – 這項作業用來更新 web 服務上的資料的項目。
- **修補程式**– 這項作業用來描述一組指示相關的項目應該修改的方式來更新 web 服務上的資料的項目。 此動詞命令不會使用在範例應用程式。
- **刪除**– 這項作業用來刪除項目上的 web 服務的資料。

符合 REST Api 呼叫 RESTful Api，並使用定義的 web 服務：

- 基底 URI。
- HTTP 方法，例如 GET、 POST、 PUT、 PATCH 或 DELETE。
- 媒體類型的資料，例如 JavaScript Object Notation (JSON)。

簡單的 REST 也促使它來存取行動應用程式中的 web 服務的主要方法。

## <a name="consuming-rest-services"></a>使用 REST 服務

您可以使用一些程式庫和類別來取用 REST 服務，而下列小節會加以討論。 如需使用 REST 服務的詳細資訊，請參閱[使用 RESTful Web 服務](~/xamarin-forms/data-cloud/web-services/rest.md)。

### <a name="httpclient"></a>HttpClient

[Microsoft HTTP 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Net.Http)會`HttpClient`提供類別，用來透過 HTTP 傳送和接收要求。 它提供的功能可用於傳送 HTTP 要求，以及從 URI 識別的資源接收 HTTP 回應。 每個要求會以非同步作業傳送。 如需有關非同步作業的詳細資訊，請參閱 <<c0> [ 非同步支援概觀](~/cross-platform/platform/async.md)。

`HttpResponseMessage`類別代表進行 HTTP 要求後，從 web 服務收到的 HTTP 回應訊息。 其中包含回應的相關資訊，包括狀態碼、標頭和主體。 `HttpContent`類別可表示的 HTTP 內容和內容標頭，例如`Content-Type`和`Content-Encoding`。 可以讀取內容，使用任一`ReadAs`方法，例如`ReadAsStringAsync`和`ReadAsByteArrayAsync`，視資料的格式。

如需類別的`HttpClient`詳細資訊，請參閱[建立 HTTPClient 物件](~/xamarin-forms/data-cloud/web-services/rest.md)。

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

使用`HTTPWebRequest`呼叫 web 服務包含：

- 建立特定 URI 的要求實例。
- 在要求實例上設定各種 HTTP 屬性。
- `HttpWebResponse`正在從要求中抓取。
- 讀取回應中的資料。

例如，下列程式碼會從美國取得資料國家/地區的醫學 web 服務程式庫：

```csharp
var rxcui = "198440";
var request = HttpWebRequest.Create(string.Format(@"http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui));
request.ContentType = "application/json";
request.Method = "GET";

using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
  if (response.StatusCode != HttpStatusCode.OK)
     Console.Out.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
  using (StreamReader reader = new StreamReader(response.GetResponseStream()))
  {
               var content = reader.ReadToEnd();
               if(string.IsNullOrWhiteSpace(content)) {
                       Console.Out.WriteLine("Response contained empty body...");
               }
               else {
                       Console.Out.WriteLine("Response Body: \r\n {0}", content);
               }

               Assert.NotNull(content);
  }
}
```

上述範例會建立， `HttpWebRequest`其會傳回格式化為 JSON 的資料。 資料會在中`HttpWebResponse`傳回， `StreamReader`從中取得可讀取資料的。

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

取用 REST 服務的另一種方法是使用[RestSharp](http://restsharp.org/)程式庫。 RestSharp 會封裝 HTTP 要求，包括以原始字串內容或還原序列化C#物件的形式來抓取結果的支援。 例如，下列程式碼會對美國全國性的醫學 web 服務程式庫，並以 JSON 格式的字串抓取結果：

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm`是一種方法，會從`RestSharp.RestResponse.Content`屬性採用原始 JSON 字串，並將其轉換成C#物件。 本文稍後將討論從 web 服務傳回的還原序列化資料。

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

除了 Mono 基類庫（BCL）中可用的類別之外，例如`HttpWebRequest`，和協力廠商C#程式庫（例如 RestSharp），也可以使用 web 服務。 例如，在 iOS 中， `NSUrlConnection`可以使用和`NSMutableUrlRequest`類別。

下列程式碼範例顯示如何呼叫美國使用 iOS 類別的國際醫學 web 服務程式庫：

```csharp
var rxcui = "198440";
var request = new NSMutableUrlRequest(new NSUrl(string.Format("http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui)),
       NSUrlRequestCachePolicy.ReloadRevalidatingCacheData, 20);
request["Accept"] = "application/json";

var connectionDelegate = new RxTermNSURLConnectionDelegate();
var connection = new NSUrlConnection(request, connectionDelegate);
connection.Start();

public class RxTermNSURLConnectionDelegate : NSUrlConnectionDelegate
{
       StringBuilder _ResponseBuilder;
       public bool IsFinishedLoading { get; set; }
       public string ResponseContent { get; set; }

       public RxTermNSURLConnectionDelegate()
               : base()
       {
               _ResponseBuilder = new StringBuilder();
       }

       public override void ReceivedData(NSUrlConnection connection, NSData data)
       {
               if(data != null) {
                       _ResponseBuilder.Append(data.ToString());
               }
       }
       public override void FinishedLoading(NSUrlConnection connection)
       {
               IsFinishedLoading = true;
               ResponseContent = _ResponseBuilder.ToString();
       }
}
```

一般來說，用於取用 web 服務的平臺特定類別應該僅限於原生程式碼要移植到C#的案例。 可能的話，web 服務存取程式碼應該是可移植的，使其可跨平臺共用。

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

呼叫 web 服務的另一個選項是[服務堆疊](http://www.servicestack.net/)程式庫。 例如，下列程式碼顯示如何使用服務堆疊的`IServiceClient.GetAsync`方法來發出服務要求：

```csharp
client.GetAsync<CustomersResponse>("",
          (response) => {
               foreach(var c in response.Customers) {
                       Console.WriteLine(c.CompanyName);
               }
       },
       (response, ex) => {
               Console.WriteLine(ex.Message);
       });
```

> [!IMPORTANT]
> 雖然 Servicestack.redis 時和 RestSharp 之類的工具可讓您輕鬆地呼叫和取用 REST 服務，但有時使用不符合標準_DataContract_序列化慣例的 XML 或 JSON 並不容易。 如有必要，請使用下列所述的 Servicestack.redis 時，叫用要求並明確處理適當的序列化。


<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>使用 RESTful 資料

RESTful web 服務通常會使用 JSON 訊息傳回給用戶端的資料。 JSON 是以文字為基礎的資料交換格式，它會產生 compact 承載，而這會導致傳送資料時的頻寬需求降低。 在本節中，將會檢查在 JSON 和一般-XML （POX）中使用 RESTful 回應的機制。

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

Xamarin 平臺隨附了現成的 JSON 支援。 藉由使用`JsonObject`，可以抓取結果，如下列程式碼範例所示：

```csharp
var obj = JsonObject.Parse(json);
var properties = obj["rxtermsProperties"];
term.BrandName = properties["brandName"];
term.DisplayName = properties["displayName"];
term.Synonym = properties["synonym"];
term.FullName = properties["fullName"];
term.FullGenericName = properties["fullGenericName"];
term.Strength = properties["strength"];
```

不過，請務必注意`System.Json` ，工具會將整個資料載入記憶體中。

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

[NewtonSoft JSON.NET 程式庫](http://www.newtonsoft.com/json)是廣泛使用的程式庫，用於序列化和還原序列化 JSON 訊息。 下列程式碼範例示範如何使用 JSON.NET 將 JSON 訊息還原序列化為C#物件：

```csharp
var term = new RxTerm();
var properties = JObject.Parse(json)["rxtermsProperties"];
term.BrandName = properties["brandName"].Value<string>();
term.DisplayName = properties["displayName"].Value<string>();
term.Synonym = properties["synonym"].Value<string>();;
term.FullName = properties["fullName"].Value<string>();;
term.FullGenericName = properties["fullGenericName"].Value<string>();;
term.Strength = properties["strength"].Value<string>();
term.RxCUI = properties["rxcui"].Value<string>();
```

<a name="Using_ServiceStack.Text" />

### <a name="servicestacktext"></a>ServiceStack.Text

Servicestack.redis 時是一種 JSON 序列化程式庫，設計用來與 Servicestack.redis 時程式庫搭配使用。 下列程式碼範例顯示如何使用`ServiceStack.Text.JsonObject`來剖析 JSON：

```csharp
var result = JsonObject.Parse(json).Object("rxtermsProperties")
       .ConvertTo(x => new RxTerm {
               BrandName = x.Get("brandName"),
               DisplayName = x.Get("displayName"),
               Synonym = x.Get("synonym"),
               FullName = x.Get("fullName"),
               FullGenericName = x.Get("fullGenericName"),
               Strength = x.Get("strength"),
               RxTermDoseForm = x.Get("rxtermsDoseForm"),
               Route = x.Get("route"),
               RxCUI = x.Get("rxcui"),
               RxNormDoseForm = x.Get("rxnormDoseForm"),
       });
```

<a name="Using_System.Xml.Linq" />

### <a name="systemxmllinq"></a>System.Xml.Linq

如果使用以 XML 為基礎的 REST web 服務，LINQ to XML 可用來剖析 XML 並以內嵌方式填入C#物件，如下列程式碼範例所示：

```csharp
var doc = XDocument.Parse(xml);
var result = doc.Root.Descendants("rxtermsProperties")
.Select(x=> new RxTerm()
       {
               BrandName = x.Element("brandName").Value,
               DisplayName = x.Element("displayName").Value,
               Synonym = x.Element("synonym").Value,
               FullName = x.Element("fullName").Value,
               FullGenericName = x.Element("fullGenericName").Value,
               //bind more here...
               RxCUI = x.Element("rxcui").Value,
       });
```

<a name="asmx" />

## <a name="aspnet-web-service-asmx"></a>ASP.NET Web 服務（.ASMX）

.ASMX 提供建立 web 服務的功能，可使用簡單的物件存取通訊協定（SOAP）來傳送訊息。 SOAP 是平台和語言無關的通訊協定，來建置，並存取 web 服務。 ASMX 服務的取用者不需要知道任何關於平台、 物件模型或用來實作服務的程式設計語言。 它們只需要了解如何傳送和接收 SOAP 訊息。

SOAP 訊息是 XML 文件包含下列項目：

- 名為根項目*信封*可識別為 SOAP 訊息的 XML 文件。
- 選擇性*標頭*元素，其中包含應用程式特定資訊，例如驗證資料。 如果*標頭*項目存在於它必須是第一個子項目*信封*項目。
- 必要*主體*元素，其中包含適用於收件者的 SOAP 訊息。
- 選擇性*錯誤*項目，用來指出錯誤訊息。 如果*錯誤*項目已存在，它必須是子項目*主體*項目。

SOAP 可透過不同的傳輸通訊協定，包括 HTTP、 SMTP、 TCP 和 UDP 操作。 不過，ASMX 服務僅透過 HTTP 操作。 Xamarin 平台支援標準的 SOAP 1.1 實作透過 HTTP，而這包括支援許多標準的 ASMX 服務組態。

### <a name="generating-a-proxy"></a>產生 Proxy

必須產生*proxy*才能使用 .asmx 服務，這可讓應用程式連接到服務。 Proxy 的建構方式取用的服務中繼資料定義的方法和相關聯的服務組態。 此中繼資料會公開為 web 服務所產生的 Web 服務描述語言（WSDL）檔。 Proxy 的建立方式是使用 Visual Studio for Mac 或 Visual Studio，將 web 服務的 web 參考加入至平臺特定專案。

Web 服務 URL 可以是可透過`file:///`路徑前置詞存取的託管遠端來源或本機檔案系統資源，例如：

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "Web 服務 URL 可以是可透過檔案路徑前置詞存取的託管遠端來源或本機檔案系統資源")](images/add-webreference-dialog.png#lightbox)

這會在專案的 [Web] 或 [服務參考] 資料夾中產生 proxy。 由於 proxy 是產生的程式碼，因此不應該修改。

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>手動將 Proxy 新增至專案

如果您有使用相容工具產生的現有 proxy，則當您的專案包含時，可以使用此輸出。 在 Visual Studio for Mac 中，使用 [**新增檔案 ...** ] 用來新增 proxy 的功能表選項。 此外，這需要使用 [**加入參考 ...** ] 明確參考*system.web* 。 .

### <a name="consuming-the-proxy"></a>使用 Proxy

產生的 proxy 類別會提供適用於使用 web 服務使用非同步程式設計模型 (APM) 設計模式的方法。 在此模式中非同步作業會實作為兩個方法，名為*BeginOperationName*並*EndOperationName*，其開始和結束非同步作業。

*BeginOperationName*方法開始非同步作業，並傳回該物件會實作`IAsyncResult`介面。 之後呼叫*BeginOperationName*，應用程式可以繼續呼叫的執行緒上執行指令，而非同步作業會在執行緒集區執行緒。

每次呼叫*BeginOperationName*，應用程式也應該呼叫*EndOperationName*取得作業的結果。 傳回值*EndOperationName*同步 web 服務方法所傳回的類型相同。 下列程式碼範例示範此作法的範例：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

Task Parallel Library (TPL) 可以簡化使用 APM begin/end 方法組，藉由將封裝中相同的非同步作業的程序`Task`物件。 此封裝由多個多載提供`Task.Factory.FromAsync`方法。 這個方法會建立`Task`一個，它`TodoService.EndGetTodoItems`會在`TodoService.BeginGetTodoItems`方法完成時執行方法， `null`並使用參數指出沒有資料會傳遞至`BeginGetTodoItems`委派。 最後，windows 7`TaskCreationOptions`列舉會指定應建立和執行工作的預設行為。

如需有關 APM 的詳細資訊，請參閱 MSDN 上的[非同步程式設計模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)和[TPL 和傳統 .NET Framework 非同步程式設計](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)。

如需使用 .ASMX 服務的詳細資訊，請參閱[使用 ASP.NET Web 服務（.asmx）](~/xamarin-forms/data-cloud/web-services/asmx.md)。

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF 是 Microsoft 用來建立服務導向應用程式的統一架構。 它可讓開發人員建置安全、 可靠、 交易，且可互通的分散式應用程式。

WCF 說明各種不同的合約，其中包含下列服務：

- **資料合約**– 定義訊息內容的基礎資料結構。
- **訊息合約**– 撰寫從現有的資料合約的訊息。
- **錯誤合約**– 允許指定自訂 SOAP 錯誤。
- **服務合約**– 指定服務支援的作業和訊息所需的互動與每個作業。 它們也會指定可以與每個服務上的作業相關聯的任何自訂的錯誤行為。

ASP.NET Web 服務 (ASMX) 和 WCF 之間的差異，但請務必了解 WCF 支援同一個提供的功能，ASMX – 透過 HTTP 的 SOAP 訊息。

> [!IMPORTANT]
> WCF 的 Xamarin 平臺支援是透過 HTTP/HTTPS 使用`BasicHttpBinding`類別，限制為文字編碼的 SOAP 訊息。 此外，WCF 支援需要使用工具，僅適用於以產生 proxy 的 Windows 環境。

### <a name="generating-a-proxy"></a>產生 Proxy

A *proxy*必須產生取用 WCF 服務，可讓應用程式連接至服務。 Proxy 的建構方式取用的服務中繼資料定義的方法和相關聯的服務組態。 此中繼資料會產生 web 服務的 Web 服務描述語言 (WSDL) 文件的形式公開。 您可以使用 Visual Studio 2017 中的 Microsoft WCF Web Service Reference Provider 來建立 proxy，將 Web 服務的服務參考新增至 .NET Standard 程式庫。

建立 Visual Studio 2017 中使用 Microsoft WCF Web Service Reference Provider proxy 的替代方式是使用 ServiceModel Metadata Utility Tool (svcutil.exe)。 如需詳細資訊，請參閱 < [ServiceModel Metadata Utility Tool (Svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)。

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>設定 Proxy

在初始化期間，設定產生的 proxy 通常會採用兩個設定引數（取決於 SOAP 1.1/.asmx 或`EndpointAddress` WCF）：和/或相關聯的系結資訊，如下列範例所示：

```csharp
var binding = new BasicHttpBinding () {
       Name= "basicHttpBinding",
       MaxReceivedMessageSize = 67108864,
};

binding.ReaderQuotas = new System.Xml.XmlDictionaryReaderQuotas() {
       MaxArrayLength = 2147483646,
       MaxStringContentLength = 5242880,
};

var timeout = new TimeSpan(0,1,0);
binding.SendTimeout= timeout;
binding.OpenTimeout = timeout;
binding.ReceiveTimeout = timeout;

client = new Service1Client (binding, new EndpointAddress ("http://192.168.1.100/Service1.svc"));
```

繫結用來指定傳輸、 編碼及所需的應用程式和服務彼此通訊的通訊協定詳細資料。 `BasicHttpBinding`指定文字編碼的 SOAP 訊息會透過 HTTP 傳輸通訊協定傳送。 指定端點位址可讓應用程式連接到 WCF 服務的不同執行個體，前提是有多個已發行的執行個體。

### <a name="consuming-the-proxy"></a>使用 Proxy

產生的 proxy 類別會提供方法來取用 web 服務使用非同步程式設計模型 (APM) 設計模式。 在此模式中，非同步作業會實作為兩個方法，名為*BeginOperationName*並*EndOperationName*，其開始和結束非同步作業。

*BeginOperationName*方法開始非同步作業，並傳回該物件會實作`IAsyncResult`介面。 之後呼叫*BeginOperationName*，應用程式可以繼續呼叫的執行緒上執行指令，而非同步作業會在執行緒集區執行緒。

每次呼叫*BeginOperationName*，應用程式也應該呼叫*EndOperationName*取得作業的結果。 傳回值*EndOperationName*同步 web 服務方法所傳回的類型相同。 下列程式碼範例示範此作法的範例：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

Task Parallel Library (TPL) 可以簡化使用 APM begin/end 方法組，藉由將封裝中相同的非同步作業的程序`Task`物件。 此封裝由多個多載提供`Task.Factory.FromAsync`方法。 這個方法會建立`Task`一個，它`TodoServiceClient.EndGetTodoItems`會在`TodoServiceClient.BeginGetTodoItems`方法完成時執行方法， `null`並使用參數指出沒有資料會傳遞至`BeginGetTodoItems`委派。 最後，windows 7`TaskCreationOptions`列舉會指定應建立和執行工作的預設行為。

如需有關 APM 的詳細資訊，請參閱 MSDN 上的[非同步程式設計模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)和[TPL 和傳統 .NET Framework 非同步程式設計](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)。

如需使用 WCF 服務的詳細資訊，請參閱[使用 Windows Communication Foundation （WCF） Web 服務](~/xamarin-forms/data-cloud/web-services/wcf.md)。

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>使用傳輸安全性

WCF 服務可能會採用傳輸層級安全性，以防止攔截訊息。 Xamarin 平臺支援使用 SSL 來採用傳輸層級安全性的系結。 不過，在某些情況下，堆疊可能需要驗證憑證，因而導致無法預期的行為。 您可以在叫用`ServerCertificateValidationCallback`服務之前註冊委派來覆寫驗證，如下列程式碼範例所示：

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

這會維護傳輸加密，同時忽略伺服器端憑證驗證。 不過，這種方法實際上會忽略與憑證相關聯的信任疑慮，而且可能不適用。 如需詳細資訊，請參閱[mono-project.com](https://www.mono-project.com)上的[使用受信任的根地](https://www.mono-project.com/UsingTrustedRootsRespectfully)。

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>使用用戶端認證安全性

WCF 服務也可能需要服務用戶端使用認證進行驗證。 Xamarin 平臺不支援 WS-安全性通訊協定，可讓用戶端在 SOAP 訊息信封內傳送認證。 不過，Xamarin 平臺確實支援藉由指定適當`ClientCredentialType`的來將 HTTP 基本驗證認證傳送到伺服器的功能：

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

然後，您可以指定基本驗證認證：

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

在上述範例中，如果您收到訊息「已用完類型0的 trampolines」，您可以藉由將`–aot “trampolines={number of trampolines}”`引數加入至組建來增加類型 0 trampolines 的數目。 如需詳細資訊，請參閱[疑難排解](~/ios/troubleshooting/troubleshooting.md#trampolines)。

如需 HTTP 基本驗證的詳細資訊，雖然在 REST web 服務的內容中，請參閱[驗證 RESTful Web 服務](~/xamarin-forms/data-cloud/authentication/rest.md)。

## <a name="related-links"></a>相關連結

- [Xamarin 中的 Web 服務](~/xamarin-forms/data-cloud/index.yml)
- [System.servicemodel 中繼資料公用程式工具（svcutil .exe）](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](https://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
