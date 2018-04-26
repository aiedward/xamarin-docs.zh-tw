---
title: Web 服務簡介
description: 本指南示範如何使用不同的網頁服務技術。 涵蓋的主題包括 REST 服務、 SOAP 服務與 Windows Communication Foundation 服務進行通訊。
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6de16095d9e1267587a050e8081f87f896f3153e
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="introduction-to-web-services"></a>Web 服務簡介

_本指南示範如何使用不同的網頁服務技術。涵蓋的主題包括 REST 服務、 SOAP 服務與 Windows Communication Foundation 服務進行通訊。_

若要正確運作，許多行動應用程式依存於雲端，然後讓行動裝置應用程式中整合 web 服務是常見的案例。 Xamarin 平台支援使用不同的網頁服務技術，並包含內建和協力廠商使用 RESTful、 ASMX、 和 Windows Communication Foundation (WCF) 服務的支援。

本文將討論下列主題：

- [REST 服務](#rest)
- [ASP.Net Web 服務 (ASMX)](#asmx)
- [WCF 服務](#wcf)

使用 Xamarin.Forms 客戶，都有完整的範例使用每個這些技術[Xamarin.Forms Web 服務](~/xamarin-forms/data-cloud/index.md)文件。

> [!IMPORTANT]
> IOS 9，應用程式傳輸安全性 (ATS) 會強制執行安全連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，因此可以防止意外洩漏機密資訊。
> 由於 AT 預設會在 ios 9 建置的應用程式啟用時，所有連線將都會受限於 AT 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。

您可以退出 AT 如果無法使用`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。



<a name="rest" />

## <a name="rest"></a>REST

Representational State Transfer (REST) 為架構的樣式，來建置 web 服務。 REST 要求都是透過 HTTP 使用網頁瀏覽器使用來擷取 web 網頁，並將資料傳送至伺服器的相同 HTTP 動詞命令。 動詞命令是：

- **取得**– 這項作業用來從 web 服務擷取資料。
- **POST** – 這項作業用來建立 web 服務上的資料的新項目。
- **PUT** – 這項作業用來更新資料 web 服務上的項目。
- **修補程式**– 這項作業用來更新資料 web 服務上的項目會描述一組指令的相關項目應該如何修改。 範例應用程式中不使用這個動詞命令。
- **刪除**– 這項作業用來刪除項目上的 web 服務的資料。

Web 服務遵守 REST Api 呼叫 rest 式 Api，並使用定義：

- 基底 URI。
- HTTP 方法，例如 GET、 POST、 PUT、 PATCH 或 DELETE。
- 對於這些資料，例如 JavaScript Object Notation (JSON) 的媒體類型。

其餘的簡易性有助於讓存取行動應用程式中的 web 服務的主要方法。

## <a name="consuming-rest-services"></a>使用 REST 服務

有一些程式庫和類別，可以用來取用 REST 服務，並進行下列各小節討論。 如需有關如何使用 REST 服務的詳細資訊，請參閱[使用 RESTful Web 服務](~/xamarin-forms/data-cloud/consuming/rest.md)。

### <a name="httpclient"></a>HttpClient

[Microsoft HTTP Client Libraries](https://www.nuget.org/packages/Microsoft.Net.Http)提供`HttpClient`類別，用來傳送和接收 HTTP 要求。 它提供傳送 HTTP 要求並從 URI 所識別的資源接收 HTTP 回應功能。 每個要求會以非同步作業傳送。 如需有關非同步作業的詳細資訊，請參閱[非同步支援概觀](~/cross-platform/platform/async.md)。

`HttpResponseMessage`類別表示已經發出 HTTP 要求之後，從 web 服務收到 HTTP 回應訊息。 它包含回應，包括狀態碼、 標頭和主體的相關資訊。 `HttpContent`類別可代表 HTTP 內容和內容標頭，例如`Content-Type`和`Content-Encoding`。 可以使用任何讀取內容`ReadAs`方法，例如`ReadAsStringAsync`和`ReadAsByteArrayAsync`，視資料的格式。

如需有關`HttpClient`類別，請參閱[建立 HTTPClient 物件](~/xamarin-forms/data-cloud/consuming/rest.md)。

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

呼叫 web 服務與`HTTPWebRequest`牽涉到：

-  建立特定 URI 要求執行個體。
-  要求執行個體上設定不同的 HTTP 內容。
-  擷取`HttpWebResponse`要求中。
-  讀取回應中的資料。

例如，下列程式碼會擷取資料由美國國家 （地區） 的程式庫的醫藥 web 服務：

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

上述範例會建立`HttpWebRequest`，就會傳回已格式化為 JSON 的資料。 傳回資料`HttpWebResponse`，從中`StreamReader`可讀取的資料。

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

若要使用 REST 服務的另一種方法使用[RestSharp](http://restsharp.org/)程式庫。 RestSharp 封裝 HTTP 要求，包括支援擷取結果做為原始字串內容或已還原序列化的 C# 物件。 例如，下列程式碼提出要求到美國國家 （地區） 的程式庫的醫藥 web 服務，並擷取結果為 JSON 格式化字串：

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` 是一種方法將從原始的 JSON 字串`RestSharp.RestResponse.Content`屬性並將它轉換成 C# 物件。 本文稍後所討論從 web 服務傳回的資料還原序列化。

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

除了類別用於單聲道的基底類別庫 (BCL)，例如`HttpWebRequest`，和第三方 C# 程式庫，例如 RestSharp，平台專屬的類別，也有提供使用 web 服務。 例如，在 iOS、`NSUrlConnection`和`NSMutableUrlRequest`類別可以用。

下列程式碼範例示範如何呼叫在美國太平洋時區使用 iOS 類別國家 （地區） 程式庫的醫藥 web 服務：

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

一般而言，取用 web 服務的平台專屬類別應該只能使用案例，其中原生程式碼移植到 C#。 如果可行，web 服務存取程式碼也能移植，使它可以是共用的跨平台。

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

呼叫 web 服務的另一個選項是[服務堆疊](http://www.servicestack.net/)程式庫。 例如，下列程式碼示範如何使用服務堆疊`IServiceClient.GetAsync`發行的服務要求的方法：

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
> 雖然工具 ServiceStack 和 RestSharp 可以輕鬆呼叫並取用 REST 服務，可能會很重要取用 XML 或不符合標準的 JSON _DataContract_序列化慣例。 如有必要，叫用要求和處理適當的序列化使用明確 ServiceStack.Text 文件庫如下所示。


<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>使用 RESTful 資料

RESTful web 服務通常會使用 JSON 訊息傳回給用戶端的資料。 JSON 是文字型資料交換格式，產生壓縮裝載，而導致降低的頻寬需求，將資料傳送時。 在本節中，會檢查使用 RESTful 回應 JSON 和純舊 XML (POX) 的機制。

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

Xamarin 平台隨附現成提供的 JSON 支援。 使用`JsonObject`，可以擷取結果，如下列程式碼範例所示：

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

不過，請務必留意，`System.Json`工具載入至記憶體中資料的全部內容。

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

[NewtonSoft JSON.NET 文件庫](http://www.newtonsoft.com/json)是廣泛使用的程式庫來序列化和還原序列化 JSON 訊息。 下列程式碼範例示範如何使用 JSON.NET 的 C# 物件還原序列化 JSON 訊息：

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

ServiceStack.Text 是設計來搭配 ServiceStack 程式庫的 JSON 序列化程式庫。 下列程式碼範例顯示如何剖析 JSON 使用`ServiceStack.Text.JsonObject`:

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

萬一發生耗用的 XML 為基礎的 REST web 服務，LINQ to XML 可以用來剖析 XML 並填入的 C# 內嵌物件，如下列程式碼範例所示：

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

ASMX 提供建立使用簡易物件存取通訊協定 (SOAP) 來傳送訊息的 web 服務的能力。 SOAP 是平台和語言無關的通訊協定，來建置和存取 web 服務。 ASMX 服務的取用者不需要了解平台、 物件模型或用來實作服務的程式設計語言的任何項目。 它們只需要了解如何傳送和接收 SOAP 訊息。

SOAP 訊息是 XML 文件包含下列項目：

- 根元素，名為*信封*，識別 XML 文件做為 SOAP 訊息。
- 選擇性*標頭*包含應用程式特定資訊，例如驗證資料的項目。 如果*標頭*項目存在於它必須是第一個子元素的*信封*項目。
- 必要*主體*包含適用於收件者的 SOAP 訊息的項目。
- 選擇性*錯誤*項目，用來指出錯誤訊息。 如果*錯誤*項目不存在，它必須是目的子項目*主體*項目。

SOAP 可在許多傳輸通訊協定，包括 HTTP、 SMTP、 TCP 及 UDP 上操作。 不過，ASMX 服務僅透過 HTTP 操作。 Xamarin 平台支援透過 HTTP 的標準 SOAP 1.1 實作，而且這包括支援許多標準的 ASMX 服務組態。

### <a name="generating-a-proxy"></a>產生 Proxy

A *proxy*必須產生至耗用 ASMX 服務，可讓應用程式連接到服務。 Proxy 的建構方式取用的服務中繼資料定義的方法和相關聯的服務組態。 此中繼資料會公開為 Web 服務描述語言 (WSDL) 文件所產生的 web 服務。 Proxy 是使用 Visual Studio for Mac 或 Visual Studio 將 web 服務的 web 參考新增至特定平台專案所建立。

Web 服務 URL 可以是裝載遠端來源或可透過存取本機檔案系統資源`file:///`路徑前置詞，例如：

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "Web 服務 URL 可以是裝載遠端來源或 1 tb 的檔案路徑前置詞的本機檔案系統資源")](images/add-webreference-dialog.png#lightbox)

此專案的 Web 或服務參考 資料夾中產生的 proxy。 由於產生 proxy 程式碼，它不能修改。

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>手動將 Proxy 加入至專案

如果您有現有的 proxy 使用相容的工具已經產生，就可以取用這個輸出時納入您的專案的一部分。 在 Visual Studio for Mac 使用**新增檔案...** 若要將 proxy 加入的功能表選項。 此外，這需要*System.Web.Services.dll*使用明確參考**加入參考...** 對話方塊。

### <a name="consuming-the-proxy"></a>使用 Proxy

產生的 proxy 類別會提供使用非同步程式設計模型 (APM) 設計模式來使用 web 服務方法。 在此模式的非同步作業會實作為兩個方法，名為*BeginOperationName*和*EndOperationName*，其開始和結束非同步作業。

*BeginOperationName*方法開始非同步作業，並傳回該物件會實作`IAsyncResult`介面。 在呼叫*BeginOperationName*，應用程式可以繼續呼叫的執行緒上執行指令，而非同步作業會在執行緒集區執行緒。

每次呼叫*BeginOperationName*，應用程式也應該呼叫*EndOperationName*取得作業的結果。 傳回值*EndOperationName*同步 web 服務方法所傳回的類型相同。 下列程式碼範例會顯示此動作的範例：

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

工作平行程式庫 (TPL) 可以簡化耗用 APM begin/end 方法組，它會封裝在相同的非同步作業的程序`Task`物件。 此封裝由多個多載`Task.Factory.FromAsync`方法。 這個方法會建立`Task`執行`TodoService.EndGetTodoItems`方法一次`TodoService.BeginGetTodoItems`方法完成時，與`null`指出沒有資料會傳遞至參數`BeginGetTodoItems`委派。 最後，值`TaskCreationOptions`列舉值會指定應建立和執行工作的預設行為。

如需 APM 的詳細資訊，請參閱[非同步程式設計模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)和[TPL 和傳統.NET Framework 非同步程式設計](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)MSDN 上。

如需使用 ASMX 服務的詳細資訊，請參閱[取用 ASP.NET Web 服務 (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md)。

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF 是 Microsoft 的統一的架構建置服務導向應用程式。 它可讓開發人員建置安全、 可靠、 交易，以及可互通的分散式應用程式。

WCF 說明各種不同的合約，其中包含下列服務：

- **資料合約**– 定義會促使內訊息的內容的資料結構。
- **訊息合約**– 撰寫從現有的資料合約的訊息。
- **錯誤合約**– 允許指定自訂 SOAP 錯誤。
- **服務合約**– 指定服務支援的作業和訊息所需的互動與每個作業。 它們也會指定可以與每個服務上的作業相關聯的任何自訂的錯誤行為。

ASP.NET Web 服務 (ASMX) 和 WCF 之間的差異，但請務必了解 WCF 支援相同的功能，提供 ASMX – 透過 HTTP SOAP 訊息。

一般情況下，Xamarin 平台都支援相同隨附 Silverlight 執行階段的 WCF 用戶端子集。 這包括 WCF 的最常見編碼和通訊協定實作，透過 HTTP 的文字編碼 SOAP 訊息傳輸通訊協定使用`BasicHttpBinding`類別。 此外，WCF 支援需要使用工具僅適用於 Windows 的環境，以產生 proxy。

如需有關使用 Xamarin 平台來取用 WCF web 服務與`BasicHttpBinding`類別，請參閱[逐步解說-使用 WCF](walkthrough-working-with-wcf.md)。

### <a name="generating-a-proxy"></a>產生 Proxy

A *proxy*取用 WCF 服務，允許應用程式連接到服務，必須產生。 Proxy 的建構方式取用的服務中繼資料定義的方法和相關聯的服務組態。 產生 web 服務的 Web 服務描述語言 (WSDL) 文件的形式公開此中繼資料。 在 Visual Studio 2017 使用 Microsoft WCF Web 服務參考提供者，將 web 服務的服務參考新增至.NET 標準程式庫，您可以建立 proxy。

建立使用 Microsoft WCF Web 服務參考提供者，在 Visual Studio 2017 proxy 的替代方式是使用 ServiceModel Metadata Utility Tool (svcutil.exe)。 如需詳細資訊，請參閱[ServiceModel Metadata Utility Tool (Svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)。

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>設定 Proxy

設定產生的 proxy 通常會 （根據 SOAP 1.1/ASMX 或 WCF） 的兩個組態引數初始化期間：`EndpointAddress`和 （或) 關聯的繫結資訊，如下列範例所示：

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

繫結用來指定傳輸、 編碼和通訊協定詳細資料所需的應用程式和服務來與對方進行通訊。 `BasicHttpBinding`指定，會透過 HTTP 傳輸通訊協定傳送文字編碼 SOAP 訊息。 指定端點位址可讓應用程式連接到不同的執行個體的 WCF 服務，前提是有多個已發行的執行個體。

### <a name="consuming-the-proxy"></a>使用 Proxy

產生的 proxy 類別會提供方法來使用 web 服務使用非同步程式設計模型 (APM) 的設計模式。 在此模式中，非同步作業會實作為兩個方法，名為*BeginOperationName*和*EndOperationName*，其開始和結束非同步作業。

*BeginOperationName*方法開始非同步作業，並傳回該物件會實作`IAsyncResult`介面。 在呼叫*BeginOperationName*，應用程式可以繼續呼叫的執行緒上執行指令，而非同步作業會在執行緒集區執行緒。

每次呼叫*BeginOperationName*，應用程式也應該呼叫*EndOperationName*取得作業的結果。 傳回值*EndOperationName*同步 web 服務方法所傳回的類型相同。 下列程式碼範例會顯示此動作的範例：

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

工作平行程式庫 (TPL) 可以簡化耗用 APM begin/end 方法組，它會封裝在相同的非同步作業的程序`Task`物件。 此封裝由多個多載`Task.Factory.FromAsync`方法。 這個方法會建立`Task`執行`TodoServiceClient.EndGetTodoItems`方法一次`TodoServiceClient.BeginGetTodoItems`方法完成時，與`null`指出沒有資料會傳遞至參數`BeginGetTodoItems`委派。 最後，值`TaskCreationOptions`列舉值會指定應建立和執行工作的預設行為。

如需 APM 的詳細資訊，請參閱[非同步程式設計模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)和[TPL 和傳統.NET Framework 非同步程式設計](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)MSDN 上。

如需有關如何使用 WCF 服務的詳細資訊，請參閱[使用 Windows Communication Foundation (WCF) Web 服務](~/xamarin-forms/data-cloud/consuming/wcf.md)。

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>使用傳輸安全性

WCF 服務可能會採用傳輸層級安全性以防止攔截的訊息。 Xamarin 平台支援使用傳輸層級安全性使用 SSL 的繫結。 不過，可能情況下，堆疊可能會需要驗證憑證，會導致非預期的行為。 驗證可覆寫註冊`ServerCertificateValidationCallback`委派之前叫用服務，如下列程式碼範例所示：

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

這會保留並忽略用戶端憑證驗證的傳輸加密。 不過，這種方法有效地略過與憑證相關聯的信任問題，而且可能不適當。 如需詳細資訊，請參閱[使用受信任的根 Respectfully](http://www.mono-project.com/UsingTrustedRootsRespectfully)上[mono project.com](http://www.mono-project.com)。

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>使用用戶端認證安全性

WCF 服務也可能需要使用認證進行驗證的服務用戶端。 Xamarin 平台不支援 WEB 服務安全性通訊協定，可讓用戶端傳送 SOAP 訊息封套內的認證。 不過，Xamarin 平台支援的 HTTP 基本驗證認證傳送至伺服器，藉由指定適當的能力`ClientCredentialType`:

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

然後，您可以指定基本驗證認證：

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

在上述範例中，如果您收到訊息 「 已用盡 trampolines 類型 0 之 「 您可以輸入 0 trampolines 數目加入增加`–aot “trampolines={number of trampolines}”`組建的引數。 如需詳細資訊，請參閱[疑難排解](~/ios/troubleshooting/troubleshooting.md#trampolines)。

如需有關 HTTP 基本驗證，不過在 REST web 服務的內容，請參閱[驗證 RESTful Web 服務](~/xamarin-forms/data-cloud/authentication/rest.md)。

## <a name="summary"></a>總結

本指南示範了如何使用不同的網頁服務技術。 涵蓋的主題包括 REST 服務、 SOAP 服務與 Windows Communication Foundation 服務進行通訊。

## <a name="related-links"></a>相關連結

- [Web 服務範例](https://developer.xamarin.com/samples/mobile/WebServices/WebServiceSamples/)
- [Xamarin.Forms 中的 web 服務](~/xamarin-forms/data-cloud/index.md)
- [ServiceModel Metadata Utility Tool (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](http://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
