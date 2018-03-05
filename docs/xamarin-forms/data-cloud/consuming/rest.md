---
title: "使用 RESTful Web 服務"
description: "將 web 服務整合到應用程式是常見的案例。 這篇文章會示範如何使用 RESTful web 服務從 Xamarin.Forms 應用程式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: f8b748ad1b57218d1e8aab11bdc1037cf3cfa14c
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="consuming-a-restful-web-service"></a>使用 RESTful Web 服務

_將 web 服務整合到應用程式是常見的案例。這篇文章會示範如何使用 RESTful web 服務從 Xamarin.Forms 應用程式。_

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

RESTful web 服務通常會使用 JSON 訊息傳回給用戶端的資料。 JSON 是以文字為基礎的資料交換格式產生 compact 裝載，因而降低頻寬需求，將資料傳送時。 範例應用程式會使用開放原始碼[NewtonSoft JSON.NET 文件庫](http://www.newtonsoft.com/json)序列化和還原序列化訊息。

其餘的簡易性有助於讓存取行動應用程式中的 web 服務的主要方法。

REST 服務的設定指示可以找到範例應用程式隨附的讀我檔案。 不過，執行範例應用程式時，它會連接到 Xamarin 裝載的 REST 服務，提供唯讀存取資料，如下列螢幕擷取畫面所示：

![](rest-images/portal.png "範例應用程式")

> [!NOTE]
> 在 iOS 9 和更新版本中，應用程式傳輸安全性 (ATS) 會強制執行安全連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，因此可以防止意外洩漏機密資訊。 由於 AT 預設會在 ios 9 建置的應用程式啟用時，所有連線將都會受限於 AT 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
>
>如果無法使用，可以超出選擇 AT **HTTPS**通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-the-web-service"></a>使用 Web 服務

REST 服務會使用 ASP.NET Core 寫入，並提供下列作業：

<table>
  <thead>
    <tr>
      <th>運算</th>
      <th>HTTP 方法</th>
      <th>相對 URI</th>
      <th>參數</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>取得待辦項目清單</td>
      <td>GET</td>
      <td>/api/todoitems/</td>
      <td></td>
    </tr>
    <tr>
      <td>建立新的待辦項目</td>
      <td>POST</td>
      <td>/api/todoitems/</td>
      <td>JSON 格式化 <code>TodoItem</code></td>
    </tr>
    <tr>
      <td>更新 待辦項目</td>
      <td>PUT</td>
      <td>/api/todoitems/</td>
      <td>JSON 格式化 <code>TodoItem</code></td>
    </tr>
    <tr>
      <td>刪除待辦項目</td>
      <td>DELETE</td>
      <td>/api/todoitems/{id}</td>
      <td></td>
    </tr>
  </tbody>
</table>

大部分的 Uri 包含`TodoItem`路徑中的識別碼。 例如，若要刪除`TodoItem`其識別碼是`6bb8a868-dba1-4f1a-93b7-24ebce87e243`，用戶端傳送的 DELETE 要求`http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`。 如需使用範例應用程式中的資料模型的詳細資訊，請參閱[將資料模型化](~/xamarin-forms/data-cloud/walkthrough.md)。

當 Web API framework 收到要求路由傳送要求至動作。 這些動作是只是公用的方法中`TodoItemsController`類別。 架構會使用路由表來判斷何種動作要叫用，以回應要求，下列程式碼範例所示：

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

路由表含有路由範本，和 Web API framework 收到 HTTP 要求時，會嘗試比對針對路由表中的路由範本的 URI。 如果相符路由找不到用戶端收到 404 （找不到） 錯誤。 如果找到相符的路由，Web API 會選取控制器和動作，如下所示：

- 若要尋找控制器，Web API 將 「 控制器 」 加入至值*{控制器}*變數。
- 若要尋找此動作，Web API 的 HTTP 方法並使用相同的 HTTP 方法，做為屬性裝飾的控制器動作會查看。
- *{Id}*預留位置變數會對應至動作參數。

REST 服務會使用基本驗證。 如需詳細資訊，請參閱[驗證 RESTful web 服務](~/xamarin-forms/data-cloud/authentication/rest.md)。 如需 ASP.NET Web API 路由的詳細資訊，請參閱[中 ASP.NET Web API 的路由](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api)ASP.NET 網站上。 如需有關如何建置使用 ASP.NET Core 的 REST 服務的詳細資訊，請參閱[原生行動應用程式建立後端服務](/aspnet/core/mobile/native-mobile-backend/)。

> [!NOTE]
> 範例應用程式使用 Xamarin 裝載的 REST 服務，提供唯讀存取 web 服務。 因此，建立、 更新和刪除資料的作業不會改變應用程式中使用的資料。 不過，可裝載的 REST 服務版本，可以在**TodoRESTService**資料夾中隨附[範例程式碼](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)。
> 如果您自行主控 REST 服務，它可允許完整建立、 更新、 讀取和刪除資料的存取權。

`HttpClient`類別用來傳送和接收 HTTP 要求。 它提供功能來傳送 HTTP 要求和接收 HTTP 回應從 URI 所識別的資源。 每個要求會以非同步作業傳送。 如需有關非同步作業的詳細資訊，請參閱[非同步支援概觀](~/cross-platform/platform/async.md)。

`HttpResponseMessage`類別表示已經發出 HTTP 要求之後，從 web 服務收到 HTTP 回應訊息。 它包含回應，包括狀態碼、 標頭和任何內文的相關資訊。 `HttpContent`類別可代表 HTTP 內容和內容標頭，例如`Content-Type`和`Content-Encoding`。 可以使用任何讀取內容`ReadAs`方法，例如`ReadAsStringAsync`和`ReadAsByteArrayAsync`，視資料的格式。

### <a name="creating-the-httpclient-object"></a>建立 HTTPClient 物件

`HttpClient`執行個體宣告類別層級，讓物件存留的只要應用程式必須提出 HTTP 要求，如下列程式碼範例所示：

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

`HttpClient.MaxResponseContentBufferSize`屬性用來指定要讀取的 HTTP 回應訊息中的內容時緩衝的位元組數目上限。 這個屬性的預設大小是整數的大小上限。 因此，屬性是設定為較小的值，做為保護措施，為限制的應用程式將會接受做為從 web 服務回應的資料量。

### <a name="retrieving-data"></a>擷取資料

`HttpClient.GetAsync`方法用來將 GET 要求傳送至 web 服務 URI，指定，然後從 web 服務接收回應，如下列程式碼範例所示：

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

REST 服務傳送 HTTP 狀態碼`HttpResponseMessage.IsSuccessStatusCode`屬性，以表示 HTTP 要求是否成功或失敗。 這項作業的 REST 服務會回應表示要求成功，而且所要求的資訊回應中傳送 HTTP 狀態碼 200 （確定）。

如果成功，HTTP 作業讀取回應內容時，顯示。 `HttpResponseMessage.Content`屬性表示的 HTTP 回應，內容和`HttpContent.ReadAsStringAsync`方法以非同步方式將輸出寫入 HTTP 內容字串。 此內容接著會從 JSON 轉換`List`的`TodoItem`執行個體。

### <a name="creating-data"></a>建立資料

`HttpClient.PostAsync`方法用來傳送 POST 要求到 URI 所指定的 web 服務，然後從 web 服務接收的回應，如下列程式碼範例所示：

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
    Debug.WriteLine (@"             TodoItem successfully saved.");

  }
  ...
}
```

`TodoItem`執行個體轉換成 JSON 裝載傳送至 web 服務。 此裝載會接著會將傳送到 web 服務中之前隨即提出要求, 的 HTTP 內容主體中內嵌`PostAsync`方法。

REST 服務傳送 HTTP 狀態碼`HttpResponseMessage.IsSuccessStatusCode`屬性，以表示 HTTP 要求是否成功或失敗。 一般針對這項作業的回應為：

- **201 （已建立）** – 要求導致回應已傳送之前建立新的資源。
- **400 （不正確的要求）** -伺服器不了解要求。
- **409 （衝突）** – 要求無法進行。 因為伺服器上的衝突。

### <a name="updating-data"></a>更新資料

`HttpClient.PutAsync`方法用來傳送 PUT 要求到由 URI 所指定的 web 服務，然後從 web 服務接收回應，如下列程式碼範例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await client.PutAsync (uri, content);
  ...
}
```
作業`PutAsync`方法等同於`PostAsync`方法，用於建立 web 服務中的資料。 但是，因為可能從 web 服務傳送的回應。

REST 服務傳送 HTTP 狀態碼`HttpResponseMessage.IsSuccessStatusCode`屬性，以表示 HTTP 要求是否成功或失敗。 一般針對這項作業的回應為：

- **204 （沒有內容）** – 成功處理要求和回應是刻意保留空白。
- **400 （不正確的要求）** -伺服器不了解要求。
- **404 （找不到）** – 要求的資源不存在伺服器上。

### <a name="deleting-data"></a>刪除資料

`HttpClient.DeleteAsync`方法用來 DELETE 要求傳送至 URI，指定 web 服務，然後從 web 服務接收回應，如下列程式碼範例所示：

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/{0}
  var uri = new Uri (string.Format (Constants.RestUrl, id));
  ...
  var response = await client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode) {
    Debug.WriteLine (@"             TodoItem successfully deleted.");
  }
  ...
}
```

REST 服務傳送 HTTP 狀態碼`HttpResponseMessage.IsSuccessStatusCode`屬性，以表示 HTTP 要求是否成功或失敗。 一般針對這項作業的回應為：

- **204 （沒有內容）** – 成功處理要求和回應是刻意保留空白。
- **400 （不正確的要求）** -伺服器不了解要求。
- **404 （找不到）** – 要求的資源不存在伺服器上。

## <a name="summary"></a>總結

這篇文章檢查如何使用 RESTful web 服務從 Xamarin.Forms 應用程式，使用`HttpClient`類別。 其餘的簡易性有助於讓存取行動應用程式中的 web 服務的主要方法。


## <a name="related-links"></a>相關連結

- [建立原生行動裝置應用程式的後端服務](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
