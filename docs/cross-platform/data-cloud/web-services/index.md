---
title: Web 服務簡介
description: 本指南示範如何使用不同的 web 服務技術。 涵蓋的主題包括與 REST 服務、 SOAP 服務和 Windows Communication Foundation 服務進行通訊。
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: b8edc0141807ead60170ec78b3f6348723ab8e18
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650131"
---
# <a name="introduction-to-web-services"></a>Web 服務簡介

_本指南示範如何使用不同的 web 服務技術。涵蓋的主題包括與 REST 服務、 SOAP 服務和 Windows Communication Foundation 服務進行通訊。_

才能正確運作，許多行動應用程式會相依於雲端，並因此整合至行動應用程式的 web 服務是常見的案例。 Xamarin 平台支援使用不同的 web 服務技術，並包含內建和協力廠商的支援，來使用 Rest、 ASMX 和 Windows Communication Foundation (WCF) 服務。

使用 Xamarin.Forms 的客戶，有完整的範例使用每個這些技術[Xamarin.Forms Web 服務](~/xamarin-forms/data-cloud/index.yml)文件。

> [!IMPORTANT]
> 在 iOS 9 中，App Transport Security (ATS) 會強制執行安全的連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，藉此防止意外洩漏機密資訊。
> 針對 iOS 9 所建置的應用程式中的預設會啟用 ATS，因為所有連線將會都受限於 ATS 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。

您可以退出 ATS 如果無法使用`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的達成**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

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

有許多程式庫和類別，可以用來取用 REST 服務，以及下列小節將討論它們。 如需有關如何使用 REST 服務的詳細資訊，請參閱 <<c0> [ 使用 RESTful Web 服務](~/xamarin-forms/data-cloud/web-services/rest.md)。

### <a name="httpclient"></a>HttpClient

[Microsoft HTTP Client Libraries](https://www.nuget.org/packages/Microsoft.Net.Http)提供`HttpClient`類別，用來傳送和接收 HTTP 要求。 它提供的功能傳送 HTTP 要求和接收的 HTTP 回應，從 URI 所識別的資源。 每個要求會以非同步作業傳送。 如需有關非同步作業的詳細資訊，請參閱 <<c0> [ 非同步支援概觀](~/cross-platform/platform/async.md)。

`HttpResponseMessage`類別代表進行 HTTP 要求後，從 web 服務收到的 HTTP 回應訊息。 它包含回應，包括狀態碼、 標頭和主體的相關資訊。 `HttpContent`類別可表示的 HTTP 內容和內容標頭，例如`Content-Type`和`Content-Encoding`。 可以讀取內容，使用任一`ReadAs`方法，例如`ReadAsStringAsync`和`ReadAsByteArrayAsync`，視資料的格式。

如需詳細資訊`HttpClient`類別，請參閱[HTTPClient 物件的建立](~/xamarin-forms/data-cloud/web-services/rest.md)。

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

呼叫 web 服務與`HTTPWebRequest`牽涉到：

-  建立特定 uri 的要求執行個體。
-  要求的執行個體上設定各種 HTTP 屬性。
-  擷取`HttpWebResponse`要求中。
-  讀取回應的資料。

例如，下列程式碼會擷取資料從美國國家的程式庫的失誤 web 服務：

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

上述範例會建立`HttpWebRequest`，會傳回格式化為 JSON 的資料。 在傳回的資料`HttpWebResponse`，從中`StreamReader`可讀取的資料。

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

若要使用 REST 服務的另一種方法使用[RestSharp](http://restsharp.org/)程式庫。 RestSharp 封裝 HTTP 要求，包括支援擷取結果做為原始字串內容或已還原序列化為C#物件。 例如，下列程式碼會提出要求到美國National 程式庫的失誤 web 服務，並擷取結果為 JSON 格式化字串：

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` 是一種方法會接受未經處理的 JSON 字串，從`RestSharp.RestResponse.Content`屬性並將它轉換成C#物件。 還原序列化從 web 服務傳回的資料會在本文稍後討論。

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

Mono 的基底中的可用類別除了類別庫 (BCL)，這類`HttpWebRequest`，以及第三方C#程式庫，例如 RestSharp，平台特定類別也會適用於使用 web 服務。 例如，在 iOS 中，`NSUrlConnection`和`NSMutableUrlRequest`類別可以用。

下列程式碼範例示範如何呼叫美國使用 iOS 類別 national 程式庫的失誤 web 服務：

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

一般而言，使用 web 服務的平台特定類別會受限於案例，其中原生程式碼移植到C#。 可能的話，以便它可以是共用的跨平台，還是應該為可移植執行 web 服務存取程式碼。

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

呼叫 web 服務的另一個選項是[服務堆疊](http://www.servicestack.net/)程式庫。 例如，下列程式碼會示範如何使用服務堆疊`IServiceClient.GetAsync`發出服務要求的方法：

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
> 雖然工具，像是 ServiceStack 和 RestSharp 輕鬆地呼叫，並取用 REST 服務，可能會很重要的取用 XML 或不符合標準的 JSON _DataContract_序列化慣例。 如有必要，請叫用要求，並處理適當的序列化，明確地使用下面討論的 ServiceStack.Text 程式庫。


<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>使用 RESTful 資料

RESTful web 服務通常會使用 JSON 訊息傳回給用戶端的資料。 JSON 是文字型資料交換格式，產生壓縮的承載，會導致較低的頻寬需求，將資料傳送時。 在本節中，會檢查使用 RESTful 回應 JSON 和純舊 XML (POX) 的機制。

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

Xamarin 平台隨附內建 JSON 支援。 使用`JsonObject`，可以擷取結果，如下列程式碼範例所示：

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

不過，請務必留意，`System.Json`工具會載入記憶體中的資料完整。

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

[NewtonSoft JSON.NET 程式庫](http://www.newtonsoft.com/json)是廣泛使用的程式庫來序列化和還原序列化 JSON 訊息。 下列程式碼範例示範如何還原序列化成 JSON 訊息中使用 JSON.NETC#物件：

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

ServiceStack.Text 是設計用於搭配 ServiceStack 程式庫的 JSON 序列化程式庫。 下列程式碼範例顯示如何剖析 JSON 使用`ServiceStack.Text.JsonObject`:

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

發生時使用以 XML 為基礎的 REST web 服務，可以使用 LINQ to XML 來剖析 XML，並填入C#物件內嵌，如下列程式碼範例所示：

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

## <a name="aspnet-web-service-asmx"></a>ASP.NET Web 服務 (ASMX)

ASMX 讓您能夠建立使用簡易物件存取通訊協定 (SOAP) 來傳送訊息的 web 服務。 SOAP 是平台和語言無關的通訊協定，來建置，並存取 web 服務。 ASMX 服務的取用者不需要知道任何關於平台、 物件模型或用來實作服務的程式設計語言。 它們只需要了解如何傳送和接收 SOAP 訊息。

SOAP 訊息是 XML 文件包含下列項目：

- 名為根項目*信封*可識別為 SOAP 訊息的 XML 文件。
- 選擇性*標頭*元素，其中包含應用程式特定資訊，例如驗證資料。 如果*標頭*項目存在於它必須是第一個子項目*信封*項目。
- 必要*主體*元素，其中包含適用於收件者的 SOAP 訊息。
- 選擇性*錯誤*項目，用來指出錯誤訊息。 如果*錯誤*項目已存在，它必須是子項目*主體*項目。

SOAP 可透過不同的傳輸通訊協定，包括 HTTP、 SMTP、 TCP 和 UDP 操作。 不過，ASMX 服務僅透過 HTTP 操作。 Xamarin 平台支援標準的 SOAP 1.1 實作透過 HTTP，而這包括支援許多標準的 ASMX 服務組態。

### <a name="generating-a-proxy"></a>產生 Proxy

A *proxy*必須產生使用 ASMX 服務，可讓應用程式連接至服務。 Proxy 的建構方式取用的服務中繼資料定義的方法和相關聯的服務組態。 此中繼資料會公開為 web 服務所產生的 Web 服務描述語言 (WSDL) 文件。 使用 Visual Studio for Mac 或 Visual Studio 將 web 服務的 web 參考新增至平台專屬專案建置 proxy。

Web 服務 URL 可以是裝載的遠端來源或本機檔案系統資源可透過存取`file:///`路徑前置詞，例如：

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "Web 服務 URL 可以是裝載的遠端來源或檔案路徑前置詞 1 tb 的本機檔案系統資源")](images/add-webreference-dialog.png#lightbox)

此專案的 Web 或服務參考 資料夾中產生的 proxy。 因為在產生 proxy 程式碼中，您應該不會進行修改。

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>手動將 Proxy 加入至專案

如果您有現有的 proxy 使用相容的工具已經產生，就可以取用此輸出包含您專案的一部分時。 在 Visual Studio for Mac 中，使用**新增檔案...** 若要將 proxy 加入的功能表選項。 此外，這需要*System.Web.Services.dll*使用明確參考**新增參考...** 對話方塊。

### <a name="consuming-the-proxy"></a>使用 Proxy

產生的 proxy 類別會提供適用於使用 web 服務使用非同步程式設計模型 (APM) 設計模式的方法。 在此模式中非同步作業會實作為兩個方法，名為*BeginOperationName*並*EndOperationName*，其開始和結束非同步作業。

*BeginOperationName*方法開始非同步作業，並傳回該物件會實作`IAsyncResult`介面。 之後呼叫*BeginOperationName*，應用程式可以繼續呼叫的執行緒上執行指令，而非同步作業會在執行緒集區執行緒。

每次呼叫*BeginOperationName*，應用程式也應該呼叫*EndOperationName*取得作業的結果。 傳回值*EndOperationName*同步 web 服務方法所傳回的類型相同。 下列程式碼範例會顯示這個範例：

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

Task Parallel Library (TPL) 可以簡化使用 APM begin/end 方法組，藉由將封裝中相同的非同步作業的程序`Task`物件。 此封裝由多個多載提供`Task.Factory.FromAsync`方法。 這個方法會建立`Task`，執行`TodoService.EndGetTodoItems`方法一次`TodoService.BeginGetTodoItems`方法完成時，與`null`參數，指出正在將資料傳遞至`BeginGetTodoItems`委派。 最後，windows 7`TaskCreationOptions`列舉會指定應建立和執行工作的預設行為。

如需 APM 的詳細資訊，請參閱[非同步程式設計模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)並[TPL 和傳統.NET Framework 非同步程式設計](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)MSDN 上。

如需有關使用 ASMX 服務的詳細資訊，請參閱[取用 ASP.NET Web 服務 (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)。

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF 是 Microsoft 的統一的架構，用於建置服務導向應用程式。 它可讓開發人員建置安全、 可靠、 交易，且可互通的分散式應用程式。

WCF 說明各種不同的合約，其中包含下列服務：

- **資料合約**– 定義訊息內容的基礎資料結構。
- **訊息合約**– 撰寫從現有的資料合約的訊息。
- **錯誤合約**– 允許指定自訂 SOAP 錯誤。
- **服務合約**– 指定服務支援的作業和訊息所需的互動與每個作業。 它們也會指定可以與每個服務上的作業相關聯的任何自訂的錯誤行為。

ASP.NET Web 服務 (ASMX) 和 WCF 之間的差異，但請務必了解 WCF 支援同一個提供的功能，ASMX – 透過 HTTP 的 SOAP 訊息。

> [!IMPORTANT]
> WCF 的 Xamarin 平台支援僅限於文字編碼的 SOAP 訊息透過 HTTP/HTTPS 使用`BasicHttpBinding`類別。 此外，WCF 支援需要使用工具，僅適用於以產生 proxy 的 Windows 環境。

### <a name="generating-a-proxy"></a>產生 Proxy

A *proxy*必須產生取用 WCF 服務，可讓應用程式連接至服務。 Proxy 的建構方式取用的服務中繼資料定義的方法和相關聯的服務組態。 此中繼資料會產生 web 服務的 Web 服務描述語言 (WSDL) 文件的形式公開。 在 Visual Studio 2017 中使用 Microsoft WCF Web Service Reference Provider，將 web 服務的服務參考新增至.NET Standard 程式庫，可以建立 proxy。

建立 Visual Studio 2017 中使用 Microsoft WCF Web Service Reference Provider proxy 的替代方式是使用 ServiceModel Metadata Utility Tool (svcutil.exe)。 如需詳細資訊，請參閱 < [ServiceModel Metadata Utility Tool (Svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)。

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>設定 Proxy

設定產生的 proxy 會通常會需要在初始化期間的 （根據 SOAP 1.1/ASMX 或 WCF） 的兩個組態引數：`EndpointAddress`和 （或) 關聯的繫結資訊，如下列範例所示：

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

每次呼叫*BeginOperationName*，應用程式也應該呼叫*EndOperationName*取得作業的結果。 傳回值*EndOperationName*同步 web 服務方法所傳回的類型相同。 下列程式碼範例會顯示這個範例：

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

Task Parallel Library (TPL) 可以簡化使用 APM begin/end 方法組，藉由將封裝中相同的非同步作業的程序`Task`物件。 此封裝由多個多載提供`Task.Factory.FromAsync`方法。 這個方法會建立`Task`，執行`TodoServiceClient.EndGetTodoItems`方法一次`TodoServiceClient.BeginGetTodoItems`方法完成時，與`null`參數，指出正在將資料傳遞至`BeginGetTodoItems`委派。 最後，windows 7`TaskCreationOptions`列舉會指定應建立和執行工作的預設行為。

如需 APM 的詳細資訊，請參閱[非同步程式設計模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)並[TPL 和傳統.NET Framework 非同步程式設計](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)MSDN 上。

如需有關如何使用 WCF 服務的詳細資訊，請參閱 <<c0> [ 使用 Windows Communication Foundation (WCF) Web 服務](~/xamarin-forms/data-cloud/web-services/wcf.md)。

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>使用傳輸安全性

WCF 服務可以採用傳輸層級安全性，以防止攔截的訊息。 Xamarin 平台支援採用傳輸層級的安全性，使用 SSL 的繫結。 不過，可能情況下，堆疊可能會需要驗證憑證，會導致非預期的行為。 驗證您可以覆寫註冊`ServerCertificateValidationCallback`委派之前叫用服務，如下列程式碼範例所示：

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

這會維護傳輸加密，但略過伺服器端憑證驗證。 不過，這種方法實際上會忽略與憑證相關聯的信任問題，而且可能不適當。 如需詳細資訊，請參閱 <<c0> [ 使用受信任的根地鞠躬](https://www.mono-project.com/UsingTrustedRootsRespectfully)上[mono-project.com](https://www.mono-project.com)。

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>使用用戶端認證的安全性

WCF 服務也可能需要使用認證進行驗證的服務用戶端。 Xamarin 平台不支援 WS-安全性通訊協定，可讓用戶端傳送 SOAP 訊息信封內的認證。 不過，在 Xamarin 平台支援能夠傳送到伺服器的 HTTP 基本驗證認證，藉由指定適當`ClientCredentialType`:

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

然後，您可以指定基本驗證認證：

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

在上述範例中，如果您收到訊息 「 已用盡 trampolines 類型 0 的 「 您可以提高類型 0 trampolines 數目加上`–aot “trampolines={number of trampolines}”`組建的引數。 如需詳細資訊，請參閱[疑難排解](~/ios/troubleshooting/troubleshooting.md#trampolines)。

如需有關 HTTP 基本驗證，雖然在 REST web 服務的內容，請參閱[驗證的 RESTful Web 服務](~/xamarin-forms/data-cloud/authentication/rest.md)。

## <a name="related-links"></a>相關連結

- [在 Xamarin.Forms 中的 web 服務](~/xamarin-forms/data-cloud/index.yml)
- [ServiceModel Metadata Utility Tool (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](https://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
