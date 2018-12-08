---
title: 使用 RESTful Web 服務
description: 將 web 服務整合到應用程式是常見的案例。 這篇文章會示範如何使用 Xamarin.Forms 應用程式的 RESTful web 服務。
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 5ae425df1d2bf28428c51366de28474a040bf368
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061332"
---
# <a name="consuming-a-restful-web-service"></a>使用 RESTful Web 服務

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)

_將 web 服務整合到應用程式是常見的案例。這篇文章會示範如何使用 Xamarin.Forms 應用程式的 RESTful web 服務。_

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

RESTful web 服務通常會使用 JSON 訊息傳回給用戶端的資料。 JSON 是文字型資料交換格式產生承載精簡，會導致在傳送資料時，會減少頻寬的需求。 範例應用程式會使用開放原始碼[NewtonSoft JSON.NET 程式庫](http://www.newtonsoft.com/json)序列化和還原序列化訊息。

簡單的 REST 也促使它來存取行動應用程式中的 web 服務的主要方法。

REST 服務的設定指示位於讀我檔案所附的範例應用程式。 不過，當執行範例應用程式時，它會連線到 Xamarin 裝載 REST 服務，提供唯讀存取資料，如下列螢幕擷取畫面所示：

![](rest-images/portal.png "範例應用程式")

> [!NOTE]
> 在 iOS 9 和更新版本中，App Transport Security (ATS) 會強制執行安全的連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，藉此防止意外洩漏機密資訊。 針對 iOS 9 所建置的應用程式中的預設會啟用 ATS，因為所有連線將會都受限於 ATS 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
>
>如果無法使用，可以共選擇 ATS **HTTPS**通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的達成**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-the-web-service"></a>使用 Web 服務

REST 服務使用 ASP.NET Core 來撰寫，並提供下列作業：

|運算|HTTP 方法|相對 URI|參數|
|--- |--- |--- |--- |
|取得待辦事項的清單|GET|/api/todoitems /|
|建立新的待辦事項項目|POST|/api/todoitems /|JSON 格式化的 TodoItem|
|更新待辦事項|PUT|/api/todoitems /|JSON 格式化的 TodoItem|
|刪除待辦事項|DELETE|/api/todoitems / {id}|

大部分的 Uri 包含`TodoItem`路徑中的識別碼。 例如，若要刪除`TodoItem`其識別碼是`6bb8a868-dba1-4f1a-93b7-24ebce87e243`，用戶端傳送的 DELETE 要求`http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`。 如需使用範例應用程式中的資料模型的詳細資訊，請參閱[將資料模型化](~/xamarin-forms/data-cloud/walkthrough.md)。

Web API 架構會收到要求時它會將要求路由至動作。 這些動作通常只是公用的方法，在`TodoItemsController`類別。 架構會使用路由表來判斷哪些動作引發以回應要求，下列程式碼範例所示：

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

路由表含有路由範本，以及當 Web API 架構會收到 HTTP 要求，會嘗試比對針對路由表中的路由範本的 URI。 如果相符路由找不在用戶端收到 404 （找不到） 錯誤。 如果找到相符的路由，則 Web API 會選取控制器和動作，如下所示：

- 若要尋找控制器，Web API 會將"controller"的值 *{controller}* 變數。
- 若要尋找此動作，Web API 會查看 HTTP 方法，並探討使用相同的 HTTP 方法，做為屬性裝飾的控制器動作。
- *{Id}* 預留位置變數會對應至動作參數。

REST 服務會使用基本驗證。 如需詳細資訊，請參閱[驗證的 RESTful web 服務](~/xamarin-forms/data-cloud/authentication/rest.md)。 如需有關 ASP.NET Web API 路由的詳細資訊，請參閱 < [ASP.NET Web API 中的路由](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api)ASP.NET 網站上。 如需有關如何建置使用 ASP.NET Core 的 REST 服務的詳細資訊，請參閱 <<c0> [ 建立原生行動應用程式的後端服務](/aspnet/core/mobile/native-mobile-backend/)。

> [!NOTE]
> 範例應用程式會取用提供唯讀存取 web 服務的 Xamarin 裝載 REST 服務。 因此，建立、 更新和刪除資料的作業並不會更改應用程式中使用的資料。 不過，可裝載 REST 服務的版本都可在**TodoRESTService**資料夾中隨附[範例程式碼](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)。
> 如果您自行裝載 REST 服務，它允許完整建立、 更新、 讀取和刪除資料的存取權。

`HttpClient`類別用來傳送和接收 HTTP 要求。 它提供功能來傳送 HTTP 要求和接收 HTTP 回應從 URI 所識別的資源。 每個要求會以非同步作業傳送。 如需有關非同步作業的詳細資訊，請參閱 <<c0> [ 非同步支援概觀](~/cross-platform/platform/async.md)。

`HttpResponseMessage`類別代表進行 HTTP 要求後，從 web 服務收到的 HTTP 回應訊息。 它包含回應，包括狀態碼、 標頭，以及任何內文的相關資訊。 `HttpContent`類別可表示的 HTTP 內容和內容標頭，例如`Content-Type`和`Content-Encoding`。 可以讀取內容，使用任一`ReadAs`方法，例如`ReadAsStringAsync`和`ReadAsByteArrayAsync`，視資料的格式。

### <a name="creating-the-httpclient-object"></a>建立 HTTPClient 物件

`HttpClient` ，讓物件存留的只要應用程式必須提出 HTTP 要求，如下列程式碼範例所示，執行個體在類別層級宣告：

```csharp
public class RestService : IRestService
{
  HttpClient client;
  ...

  public RestService ()
  {
    client = new HttpClient ();
    client.MaxResponseContentBufferSize = 256000;
  }
  ...
}
```

`HttpClient.MaxResponseContentBufferSize`屬性用來指定最大讀取的 HTTP 回應訊息中的內容時所要緩衝的位元組數。 這個屬性的預設大小是一個整數的大小上限。 因此，屬性是設為較小的值，以防萬一，限制的應用程式會接受做為回應來自 web 服務的資料量。

### <a name="retrieving-data"></a>擷取資料

`HttpClient.GetAsync`方法用來將 GET 要求傳送至 URI 所指定的 web 服務，然後從 web 服務接收回應，如下列程式碼範例所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/
  var uri = new Uri (string.Format (Constants.RestUrl, string.Empty));
  ...
  var response = await client.GetAsync (uri);
  if (response.IsSuccessStatusCode) {
      var content = await response.Content.ReadAsStringAsync ();
      Items = JsonConvert.DeserializeObject <List<TodoItem>> (content);
  }
  ...
}
```

REST 服務傳送的 HTTP 狀態碼`HttpResponseMessage.IsSuccessStatusCode`屬性，以指示 HTTP 要求是否成功或失敗。 這項作業的 REST 服務會在回應中，表示要求成功，且要求的資訊會在回應中傳送 HTTP 狀態碼 200 （確定）。

HTTP 作業成功，則讀取回應的內容時，顯示。 `HttpResponseMessage.Content`屬性表示的 HTTP 回應，內容和`HttpContent.ReadAsStringAsync`方法以非同步方式將輸出寫入 HTTP 內容字串。 此內容接著會從 JSON 轉換`List`的`TodoItem`執行個體。

### <a name="creating-data"></a>建立資料

`HttpClient.PostAsync`方法用來將 POST 要求傳送至 URI 所指定的 web 服務，然後從 web 服務接收回應，如下列程式碼範例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/
  var uri = new Uri (string.Format (Constants.RestUrl, string.Empty));

  ...
  var json = JsonConvert.SerializeObject (item);
  var content = new StringContent (json, Encoding.UTF8, "application/json");

  HttpResponseMessage response = null;
  if (isNewItem) {
    response = await client.PostAsync (uri, content);
  }
  ...

  if (response.IsSuccessStatusCode) {
    Debug.WriteLine (@"                TodoItem successfully saved.");

  }
  ...
}
```

`TodoItem`執行個體轉換為 JSON 承載，將傳送至 web 服務。 然後會傳送至 web 服務中，再與提出要求的 HTTP 內容主體中內嵌此承載`PostAsync`方法。

REST 服務傳送的 HTTP 狀態碼`HttpResponseMessage.IsSuccessStatusCode`屬性，以指示 HTTP 要求是否成功或失敗。 這項作業的常見回應有：

- **201 （已建立）** -要求導致新的資源建立之前傳送回應。
- **400 （不正確的要求）** -伺服器不了解要求。
- **409 （衝突）** – 要求可能不會執行伺服器上的衝突。

### <a name="updating-data"></a>更新資料

`HttpClient.PutAsync`方法用來將 PUT 要求傳送至 URI 所指定的 web 服務，然後從 web 服務接收回應，如下列程式碼範例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await client.PutAsync (uri, content);
  ...
}
```
營運`PutAsync`方法等同於`PostAsync`用於 web 服務中建立資料的方法。 不過，不同的 web 服務傳送的可能回應。

REST 服務傳送的 HTTP 狀態碼`HttpResponseMessage.IsSuccessStatusCode`屬性，以指示 HTTP 要求是否成功或失敗。 這項作業的常見回應有：

- **204 （沒有內容）** – 已成功處理要求和回應預設為空白。
- **400 （不正確的要求）** -伺服器不了解要求。
- **404 （找不到）** – 要求的資源不存在伺服器上。

### <a name="deleting-data"></a>刪除資料

`HttpClient.DeleteAsync`方法用來將 DELETE 要求傳送至 URI 所指定的 web 服務，然後從 web 服務接收回應，如下列程式碼範例所示：

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/{0}
  var uri = new Uri (string.Format (Constants.RestUrl, id));
  ...
  var response = await client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode) {
    Debug.WriteLine (@"                TodoItem successfully deleted.");
  }
  ...
}
```

REST 服務傳送的 HTTP 狀態碼`HttpResponseMessage.IsSuccessStatusCode`屬性，以指示 HTTP 要求是否成功或失敗。 這項作業的常見回應有：

- **204 （沒有內容）** – 已成功處理要求和回應預設為空白。
- **400 （不正確的要求）** -伺服器不了解要求。
- **404 （找不到）** – 要求的資源不存在伺服器上。

## <a name="summary"></a>總結

這篇文章檢查如何使用 Xamarin.Forms 應用程式，從 RESTful web 服務使用`HttpClient`類別。 簡單的 REST 也促使它來存取行動應用程式中的 web 服務的主要方法。


## <a name="related-links"></a>相關連結

- [建立原生行動裝置應用程式的後端服務](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
