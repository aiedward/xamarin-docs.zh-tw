---
title: 使用 RESTful Web 服務
description: 將 web 服務整合至應用程式是常見的情況。 本文示範如何從應用程式使用 RESTful web 服務 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3146d1c1de66c0b2bc2e9c2d86a7a50d406ecd7e
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373363"
---
# <a name="consume-a-restful-web-service"></a>使用 RESTful Web 服務

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_將 web 服務整合至應用程式是常見的情況。本文示範如何從應用程式使用 RESTful web 服務 Xamarin.Forms 。_

具象狀態傳輸 (REST) 是建立 web 服務的架構樣式。 REST 要求是使用網頁瀏覽器用來抓取網頁以及將資料傳送至伺服器的相同 HTTP 動詞命令，透過 HTTP 進行。 動詞命令如下：

- **GET** –這項作業用來從 web 服務取得資料。
- **POST** –這項作業用來在 web 服務上建立新的資料項目目。
- **PUT** –這項作業用來更新 web 服務上的資料項目目。
- **PATCH** –這項作業是用來更新 web 服務上的資料項目目，方法是描述有關如何修改專案的一組指示。 範例應用程式中未使用此動詞。
- **Delete** –這項作業用來刪除 web 服務上的資料項目。

遵守 REST 的 Web 服務 Api 稱為 RESTful Api，並使用下列定義：

- 基底 URI。
- HTTP 方法，例如 GET、POST、PUT、PATCH 或 DELETE。
- 資料的媒體類型，例如 JavaScript 物件標記法 (JSON) 。

RESTful web 服務通常會使用 JSON 訊息，將資料傳回到用戶端。 JSON 是以文字為基礎的資料交換格式，可產生 compact 承載，以降低傳送資料時的頻寬需求。 範例應用程式會使用開放原始碼 [NewtonSoft JSON.NET 程式庫](https://www.newtonsoft.com/json) 來序列化和還原序列化訊息。

REST 的簡潔性讓它成為在行動應用程式中存取 web 服務的主要方法。

執行範例應用程式時，它會連接到本機裝載的 REST 服務，如下列螢幕擷取畫面所示：

![範例應用程式](rest-images/portal.png)

> [!NOTE]
> 在 iOS 9 及更新版本中，應用程式傳輸安全性 (ATS) 會強制網際網路資源之間的安全連線 (例如，應用程式的後端伺服器) 和應用程式，進而防止意外洩漏機密資訊。 因為 ATS 預設會在針對 iOS 9 建立的應用程式中啟用，所以所有連線都必須遵守 ATS 安全性需求。 如果連接不符合這些需求，則會失敗併發生例外狀況。
>
>如果無法使用 **HTTPS** 通訊協定和網際網路資源的安全通訊，則可以選擇不使用 ATS。 這可以藉由更新應用程式的 **Info. plist** 檔案來達成。 如需詳細資訊，請參閱 [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-the-web-service"></a>使用 Web 服務

REST 服務是使用 ASP.NET Core 撰寫的，並提供下列作業：

|作業|HTTP method|相對 URI|參數|
|--- |--- |--- |--- |
|取得待辦事項的清單|GET|/api/todoitems/|
|建立新的待辦事項專案|POST|/api/todoitems/|JSON 格式的 TodoItem|
|更新待辦事項|PUT|/api/todoitems/|JSON 格式的 TodoItem|
|刪除待辦事項|刪除|/api/todoitems/{識別碼}|

大部分的 Uri 都包含 `TodoItem` 路徑中的識別碼。 例如，若要刪除 `TodoItem` 識別碼為的 `6bb8a868-dba1-4f1a-93b7-24ebce87e243` ，用戶端會將刪除要求傳送至 `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243` 。 如需範例應用程式中所使用之資料模型的詳細資訊，請參閱 [建立資料](~/xamarin-forms/data-cloud/web-services/introduction.md)模型。

當 Web API 架構收到要求時，它會將要求路由傳送至動作。 這些動作只是類別中的公用方法 `TodoItemsController` 。 架構會使用路由表來決定要叫用哪個動作以回應要求，如下列程式碼範例所示：

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

路由表包含路由範本，而當 Web API 架構收到 HTTP 要求時，它會嘗試比對 URI 與路由表中的路由範本。 如果找不到相符的路由，用戶端會收到 404 (找不到) 錯誤。 如果找到相符的路由，Web API 會依下列方式選取控制器和動作：

- 為了尋找控制器，Web API 會將「控制器」新增至 *{controller}* 變數的值。
- 為了尋找動作，Web API 會查看 HTTP 方法，並查看以與屬性相同的 HTTP 方法裝飾的控制器動作。
- *{Id}* 預留位置變數會對應至動作參數。

REST 服務會使用基本驗證。 如需詳細資訊，請參閱 [驗證 RESTful web 服務](~/xamarin-forms/data-cloud/authentication/rest.md)。 如需 ASP.NET Web API 路由的詳細資訊，請參閱 ASP.NET 網站上 [ASP.NET Web API 中的路由](https://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) 。 如需使用 ASP.NET Core 建立 REST 服務的詳細資訊，請參閱 [建立原生行動應用程式的後端服務](/aspnet/core/mobile/native-mobile-backend/)。

`HttpClient`類別是用來透過 HTTP 傳送和接收要求。 它提供的功能可用來傳送 HTTP 要求，以及從 URI 識別的資源接收 HTTP 回應。 每個要求都會以非同步作業的方式傳送。 如需非同步作業的詳細資訊，請參閱 [非同步支援總覽](~/cross-platform/platform/async.md)。

`HttpResponseMessage`類別代表發出 HTTP 要求之後，從 web 服務接收的 HTTP 回應訊息。 它包含回應的相關資訊，包括狀態碼、標頭和任何主體。 `HttpContent`類別代表 HTTP 主體和內容標頭，例如 `Content-Type` 和 `Content-Encoding` 。 您可以使用任何 `ReadAs` 方法（例如和）來讀取內容（ `ReadAsStringAsync` 視資料的格式而定） `ReadAsByteArrayAsync` 。

### <a name="creating-the-httpclient-object"></a>建立 HTTPClient 物件

此 `HttpClient` 實例會在類別層級宣告，如此一來，只要應用程式需要發出 HTTP 要求，物件就會存留，如下列程式碼範例所示：

```csharp
public class RestService : IRestService
{
  HttpClient client;
  ...

  public RestService ()
  {
    client = new HttpClient ();
  }
  ...
}
```

### <a name="retrieving-data"></a>擷取資料

`HttpClient.GetAsync`方法是用來將 GET 要求傳送至 URI 所指定的 web 服務，然後接收來自 web 服務的回應，如下列程式碼範例所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  Uri uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));
  ...
  HttpResponseMessage response = await client.GetAsync (uri);
  if (response.IsSuccessStatusCode)
  {
      string content = await response.Content.ReadAsStringAsync ();
      Items = JsonConvert.DeserializeObject <List<TodoItem>> (content);
  }
  ...
}
```

REST 服務會在屬性中傳送 HTTP 狀態碼 `HttpResponseMessage.IsSuccessStatusCode` ，以指出 HTTP 要求是否成功或失敗。 針對這項作業，REST 服務會在回應中傳送 HTTP 狀態碼 200 (確定) ，這表示要求成功，且要求的資訊會在回應中。

如果 HTTP 作業成功，則會讀取回應的內容以供顯示。 `HttpResponseMessage.Content`屬性代表 HTTP 回應的內容，而且方法會以 `HttpContent.ReadAsStringAsync` 非同步方式將 HTTP 內容寫入字串。 然後，此內容會從 JSON 轉換為 `List` `TodoItem` 實例的。

> [!WARNING]
> 使用 `ReadAsStringAsync` 方法來取出大型回應可能會對效能造成負面影響。 在這種情況下，應該直接還原序列化回應，以避免必須完全緩衝。

### <a name="creating-data"></a>建立資料

`HttpClient.PostAsync`方法是用來將 POST 要求傳送至 URI 所指定的 web 服務，然後接收來自 web 服務的回應，如下列程式碼範例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  Uri uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));

  ...
  string json = JsonConvert.SerializeObject (item);
  StringContent content = new StringContent (json, Encoding.UTF8, "application/json");

  HttpResponseMessage response = null;
  if (isNewItem)
  {
    response = await client.PostAsync (uri, content);
  }
  ...

  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully saved.");
  }
  ...
}
```

實例會轉換成 JSON 承載，以傳送 `TodoItem` 至 web 服務。 然後，此承載會內嵌在 HTTP 內容主體中，此內容會在使用方法提出要求之前傳送至 web 服務 `PostAsync` 。

REST 服務會在屬性中傳送 HTTP 狀態碼 `HttpResponseMessage.IsSuccessStatusCode` ，以指出 HTTP 要求是否成功或失敗。 這種作業的常見回應如下：

- **201 (建立)** –要求在傳送回應之前，會產生新的資源。
- **400 (錯誤的要求)** –伺服器無法理解要求。
- **409 (衝突)** –因為伺服器發生衝突，所以無法執行要求。

### <a name="updating-data"></a>更新資料

`HttpClient.PutAsync`方法是用來將 PUT 要求傳送至 URI 所指定的 web 服務，然後接收來自 web 服務的回應，如下列程式碼範例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await client.PutAsync (uri, content);
  ...
}
```

方法的操作與在 `PutAsync` `PostAsync` web 服務中用來建立資料的方法相同。 不過，從 web 服務傳送的可能回應會有所不同。

REST 服務會在屬性中傳送 HTTP 狀態碼 `HttpResponseMessage.IsSuccessStatusCode` ，以指出 HTTP 要求是否成功或失敗。 這種作業的常見回應如下：

- **204 (沒有內容)** -已成功處理要求，而且回應是刻意空白的。
- **400 (錯誤的要求)** –伺服器無法理解要求。
- **404 (找不到)** –要求的資源不存在於伺服器上。

### <a name="deleting-data"></a>刪除資料

`HttpClient.DeleteAsync`方法是用來將 DELETE 要求傳送至 URI 所指定的 web 服務，然後接收來自 web 服務的回應，如下列程式碼範例所示：

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  Uri uri = new Uri (string.Format (Constants.TodoItemsUrl, id));
  ...
  HttpResponseMessage response = await client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully deleted.");
  }
  ...
}
```

REST 服務會在屬性中傳送 HTTP 狀態碼 `HttpResponseMessage.IsSuccessStatusCode` ，以指出 HTTP 要求是否成功或失敗。 這種作業的常見回應如下：

- **204 (沒有內容)** -已成功處理要求，而且回應是刻意空白的。
- **400 (錯誤的要求)** –伺服器無法理解要求。
- **404 (找不到)** –要求的資源不存在於伺服器上。

## <a name="related-links"></a>相關連結

- [建立原生行動應用程式的後端服務](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (範例)](/samples/xamarin/xamarin-forms-samples/webservices-todorest) \(英文\)
- [HttpClient](/dotnet/api/system.net.http.httpclient)