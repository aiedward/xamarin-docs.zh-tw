---
標題：「取用 RESTful Web 服務」描述：「將 web 服務整合到應用程式」是常見的案例。 本文示範如何從應用程式使用 RESTful web 服務。」 Xamarin.Forms
assetid： B540910C-9C51-416A-AAB9-057BF76489C3 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：05/28/2020 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="consume-a-restful-web-service"></a>使用 RESTful Web 服務

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_將 web 服務整合到應用程式是常見的案例。本文示範如何從應用程式使用 RESTful web 服務 Xamarin.Forms 。_

具像狀態傳輸（REST）是用來建立 web 服務的架構樣式。 REST 要求是使用 web 瀏覽器用來抓取網頁和將資料傳送到伺服器的相同 HTTP 動詞，透過 HTTP 進行。 動詞：

- **GET** –這是用來從 web 服務抓取資料的作業。
- **POST** –這項作業會用來在 web 服務上建立新的資料項目目。
- **PUT** –此作業用來更新 web 服務上的資料項目。
- **修補程式**–這項作業是用來更新 web 服務上的資料項目，方法是描述有關如何修改專案的一組指示。 範例應用程式中不會使用這個動詞。
- **Delete** –此作業用來刪除 web 服務上的資料項目。

遵守 REST 的 Web 服務 Api 稱為 RESTful Api，並使用來定義：

- 基底 URI。
- HTTP 方法，例如 GET、POST、PUT、PATCH 或 DELETE。
- 資料的媒體類型，例如 JavaScript 物件標記法（JSON）。

RESTful web 服務通常會使用 JSON 訊息將資料傳回給用戶端。 JSON 是以文字為基礎的資料交換格式，會產生 compact 承載，這會導致傳送資料時的頻寬需求降低。 範例應用程式會使用開放原始碼[NewtonSoft JSON.NET 程式庫](https://www.newtonsoft.com/json)來序列化和還原序列化訊息。

REST 的簡單性讓它成為在行動應用程式中存取 web 服務的主要方法。

執行範例應用程式時，它會連接到本機裝載的 REST 服務，如下列螢幕擷取畫面所示：

![](rest-images/portal.png "Sample Application")

> [!NOTE]
> 在 iOS 9 和更新版本中，應用程式傳輸安全性（ATS）會強制執行網際網路資源（例如應用程式的後端伺服器）與應用程式之間的安全連線，藉此防止意外洩漏機密資訊。 由於預設會在針對 iOS 9 建立的應用程式中啟用 ATS，因此所有連線都將受限於 ATS 安全性需求。 如果連線不符合這些需求，則會失敗並產生例外狀況。
>
>如果無法使用**HTTPS**通訊協定和網際網路資源的安全通訊，可以退出宣告 ATS。 這可以藉由更新應用程式的**plist**檔案來達成。 如需詳細資訊，請參閱[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-the-web-service"></a>使用 Web 服務

REST 服務是使用 ASP.NET Core 撰寫的，並提供下列作業：

|作業|HTTP method|相對 URI|參數|
|--- |--- |--- |--- |
|取得待辦事項的清單|GET|/api/todoitems/|
|建立新的待辦事項專案|POST|/api/todoitems/|JSON 格式的 TodoItem|
|更新待辦事項|PUT|/api/todoitems/|JSON 格式的 TodoItem|
|刪除待辦事項|刪除|/api/todoitems/{id}|

大部分的 Uri 都包含 `TodoItem` 路徑中的識別碼。 例如，若要刪除 `TodoItem` 其識別碼為的 `6bb8a868-dba1-4f1a-93b7-24ebce87e243` ，用戶端會將刪除要求傳送至 `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243` 。 如需範例應用程式中所使用之資料模型的詳細資訊，請參閱[模型化資料](~/xamarin-forms/data-cloud/web-services/introduction.md)。

當 Web API 架構收到要求時，它會將要求路由傳送至動作。 這些動作只是類別中的公用方法 `TodoItemsController` 。 架構會使用路由表來判斷要叫用哪個動作以回應要求，如下列程式碼範例所示：

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

路由表包含路由範本，而當 Web API 架構收到 HTTP 要求時，它會嘗試比對路由表中的路由範本與 URI。 如果找不到相符的路由，用戶端會收到404（找不到）錯誤。 如果找到相符的路由，Web API 會選取控制器和動作，如下所示：

- 若要尋找控制器，Web API 會將 "controller" 新增至 *{controller}* 變數的值。
- 若要尋找此動作，Web API 會查看 HTTP 方法，並查看使用與屬性相同的 HTTP 方法裝飾的控制器動作。
- *{Id}* 預留位置變數會對應至動作參數。

REST 服務會使用基本驗證。 如需詳細資訊，請參閱[驗證 RESTful web 服務](~/xamarin-forms/data-cloud/authentication/rest.md)。 如需有關 ASP.NET Web API 路由的詳細資訊，請參閱 ASP.NET 網站上[ASP.NET Web API 中的路由](https://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api)。 如需使用 ASP.NET Core 建立 REST 服務的詳細資訊，請參閱[建立原生行動應用程式的後端服務](/aspnet/core/mobile/native-mobile-backend/)。

`HttpClient`類別是用來透過 HTTP 傳送和接收要求。 它提供的功能可用於傳送 HTTP 要求，以及從 URI 識別的資源接收 HTTP 回應。 每個要求都會以非同步作業的方式傳送。 如需非同步作業的詳細資訊，請參閱[非同步支援總覽](~/cross-platform/platform/async.md)。

`HttpResponseMessage`類別代表在提出 HTTP 要求之後，從 web 服務接收的 HTTP 回應訊息。 其中包含回應的相關資訊，包括狀態碼、標頭和任何主體。 `HttpContent`類別代表 HTTP 主體和內容標頭，例如 `Content-Type` 和 `Content-Encoding` 。 您可以使用任何 `ReadAs` 方法（例如和）來讀取內容， `ReadAsStringAsync` `ReadAsByteArrayAsync` 視資料的格式而定。

### <a name="creating-the-httpclient-object"></a>建立 HTTPClient 物件

`HttpClient`實例是在類別層級宣告，因此，只要應用程式需要提出 HTTP 要求，物件就會存在，如下列程式碼範例所示：

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

REST 服務會在屬性中傳送 HTTP 狀態碼 `HttpResponseMessage.IsSuccessStatusCode` ，以指出 HTTP 要求成功或失敗。 在這項作業中，REST 服務會在回應中傳送 HTTP 狀態碼200（確定），這表示要求成功，且要求的資訊在回應中。

如果 HTTP 作業成功，則會讀取回應的內容，以供顯示。 `HttpResponseMessage.Content`屬性代表 HTTP 回應的內容，而方法會以 `HttpContent.ReadAsStringAsync` 非同步方式將 HTTP 內容寫入字串。 然後，此內容會從 JSON 轉換成 `List` `TodoItem` 實例的。

> [!WARNING]
> 使用 `ReadAsStringAsync` 方法來抓取大型回應可能會對效能造成負面影響。 在這種情況下，回應應該直接還原序列化，以免必須完全緩衝。

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

`TodoItem`實例會轉換成 JSON 承載，以傳送至 web 服務。 然後，此承載會內嵌于 HTTP 內容的主體中，在使用方法提出要求之前，將會傳送至 web 服務 `PostAsync` 。

REST 服務會在屬性中傳送 HTTP 狀態碼 `HttpResponseMessage.IsSuccessStatusCode` ，以指出 HTTP 要求成功或失敗。 這種作業的常見回應為：

- **201 （已建立）** –要求導致在傳送回應之前建立新的資源。
- **400 （不正確的要求）** -伺服器無法理解要求。
- **409 （衝突）** -無法執行要求，因為伺服器上發生衝突。

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

方法的作業與 `PutAsync` `PostAsync` 用來在 web 服務中建立資料的方法相同。 不過，從 web 服務傳送的可能回應會有所不同。

REST 服務會在屬性中傳送 HTTP 狀態碼 `HttpResponseMessage.IsSuccessStatusCode` ，以指出 HTTP 要求成功或失敗。 這種作業的常見回應為：

- **204 （沒有內容）** –已成功處理要求，而且回應是刻意為空白。
- **400 （不正確的要求）** -伺服器無法理解要求。
- **404 （找不到）** -要求的資源不存在於伺服器上。

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

REST 服務會在屬性中傳送 HTTP 狀態碼 `HttpResponseMessage.IsSuccessStatusCode` ，以指出 HTTP 要求成功或失敗。 這種作業的常見回應為：

- **204 （沒有內容）** –已成功處理要求，而且回應是刻意為空白。
- **400 （不正確的要求）** -伺服器無法理解要求。
- **404 （找不到）** -要求的資源不存在於伺服器上。

## <a name="related-links"></a>相關連結

- [建立原生行動應用程式的後端服務](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest) \(英文\)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
