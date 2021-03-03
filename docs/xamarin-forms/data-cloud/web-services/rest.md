---
title: 使用 RESTful web 服務
description: 將 web 服務整合至應用程式是常見的情況。 本文示範如何從應用程式使用 RESTful web 服務 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 27bafeb9ac82ba23128eabdd9f4a15cb5c8c1bf1
ms.sourcegitcommit: 322e7bcf9fb8c1ad52ab8e929bea95d45e280834
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751362"
---
# <a name="consume-a-restful-web-service"></a>使用 RESTful web 服務

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_將 web 服務整合至應用程式是常見的情況。本文示範如何從應用程式使用 RESTful web 服務 Xamarin.Forms 。_

具像狀態傳輸 (REST) 是用來建立 web 服務的架構樣式。 REST 要求是使用網頁瀏覽器用來抓取網頁以及將資料傳送至伺服器的相同 HTTP 動詞命令，透過 HTTP 進行。 動詞命令如下：

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

## <a name="consume-the-web-service"></a>取用 Web 服務

REST 服務是使用 ASP.NET Core 撰寫的，並提供下列作業：

|作業|HTTP method|相對 URI|參數|
|--- |--- |--- |--- |
|取得待辦事項的清單|GET|/api/todoitems/|
|建立新的待辦事項專案|POST|/api/todoitems/|JSON 格式的 TodoItem|
|更新待辦事項|PUT|/api/todoitems/|JSON 格式的 TodoItem|
|刪除待辦事項|刪除|/api/todoitems/{識別碼}|

大部分的 Uri 都包含 `TodoItem` 路徑中的識別碼。 例如，若要刪除 `TodoItem` 識別碼為的 `6bb8a868-dba1-4f1a-93b7-24ebce87e243` ，用戶端會將刪除要求傳送至 `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243` 。 如需範例應用程式中所使用之資料模型的詳細資訊，請參閱 [建立資料](~/xamarin-forms/data-cloud/web-services/introduction.md)模型。

當 Web API 架構收到要求時，它會將要求路由傳送至動作。 這些動作只是類別中的公用方法 `TodoItemsController` 。 架構會使用路由中介軟體來比對傳入要求的 Url，並將其對應至動作。 REST Api 應該使用屬性，將應用程式的功能作為一組資源來路由傳送，其作業是由 HTTP 指令動詞表示。 屬性路由使用一組屬性，將動作直接對應至路由範本。 如需屬性路由的詳細資訊，請參閱 [REST api 的屬性路由](/aspnet/core/mvc/controllers/routing?view=aspnetcore-5.0#ar)。 如需使用 ASP.NET Core 建立 REST 服務的詳細資訊，請參閱 [建立原生行動應用程式的後端服務](/aspnet/core/mobile/native-mobile-backend/)。

`HttpClient`類別是用來透過 HTTP 傳送和接收要求。 它提供的功能可用來傳送 HTTP 要求，以及從 URI 識別的資源接收 HTTP 回應。 每個要求都會以非同步作業的方式傳送。 如需非同步作業的詳細資訊，請參閱 [非同步支援總覽](~/cross-platform/platform/async.md)。

`HttpResponseMessage`類別代表發出 HTTP 要求之後，從 web 服務接收的 HTTP 回應訊息。 它包含回應的相關資訊，包括狀態碼、標頭和任何主體。 `HttpContent`類別代表 HTTP 主體和內容標頭，例如 `Content-Type` 和 `Content-Encoding` 。 您可以使用任何 `ReadAs` 方法（例如和）來讀取內容（ `ReadAsStringAsync` 視資料的格式而定） `ReadAsByteArrayAsync` 。

### <a name="create-the-httpclient-object"></a>建立 HTTPClient 物件

此 `HttpClient` 實例會在類別層級宣告，如此一來，只要應用程式需要發出 HTTP 要求，物件就會存留，如下列程式碼範例所示：

```csharp
public class RestService : IRestService
{
  HttpClient client;
  ...

  public RestService ()
  {
    client = new HttpClient ();
    ...
  }
  ...
}
```

### <a name="retrieve-data"></a>取出資料

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
      Items = JsonSerializer.Deserialize<List<TodoItem>>(content, serializerOptions);
  }
  ...
}
```

REST 服務會在屬性中傳送 HTTP 狀態碼 `HttpResponseMessage.IsSuccessStatusCode` ，以指出 HTTP 要求是否成功或失敗。 針對這項作業，REST 服務會在回應中傳送 HTTP 狀態碼 200 (確定) ，這表示要求成功，且要求的資訊會在回應中。

如果 HTTP 作業成功，則會讀取回應的內容以供顯示。 `HttpResponseMessage.Content`屬性代表 HTTP 回應的內容，而且方法會以 `HttpContent.ReadAsStringAsync` 非同步方式將 HTTP 內容寫入字串。 然後，此內容會從 JSON 還原序列化 `List` 為 `TodoItem` 實例的。

> [!WARNING]
> 使用 `ReadAsStringAsync` 方法來取出大型回應可能會對效能造成負面影響。 在這種情況下，應該直接還原序列化回應，以避免必須完全緩衝。

### <a name="create-data"></a>建立資料

`HttpClient.PostAsync`方法是用來將 POST 要求傳送至 URI 所指定的 web 服務，然後接收來自 web 服務的回應，如下列程式碼範例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  Uri uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));

  ...
  string json = JsonSerializer.Serialize<TodoItem>(item, serializerOptions);
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

`TodoItem`實例會序列化為 JSON 承載，以傳送至 web 服務。 然後，此承載會內嵌在 HTTP 內容主體中，此內容會在使用方法提出要求之前傳送至 web 服務 `PostAsync` 。

REST 服務會在屬性中傳送 HTTP 狀態碼 `HttpResponseMessage.IsSuccessStatusCode` ，以指出 HTTP 要求是否成功或失敗。 這種作業的常見回應如下：

- **201 (建立)** –要求在傳送回應之前，會產生新的資源。
- **400 (錯誤的要求)** –伺服器無法理解要求。
- **409 (衝突)** –因為伺服器發生衝突，所以無法執行要求。

### <a name="update-data"></a>更新資料

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

### <a name="delete-data"></a>刪除資料

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

### <a name="local-development"></a>本機開發

如果您要使用 ASP.NET Core Web API 這類架構在本機開發 REST web 服務，您可以同時進行 web 服務和行動應用程式的偵錯工具。 在此案例中，您必須啟用 iOS simualtor 和 Android 模擬器的純文字 HTTP 流量。 如需設定專案以允許通訊的相關資訊，請參閱 [連接到本機 web 服務](~/cross-platform/deploy-test/connect-to-local-web-services.md)。

## <a name="related-links"></a>相關連結

- [Microsoft 瞭解：在 Xamarin 應用程式中取用 REST web 服務](/learn/modules/consume-rest-services/)
- [Microsoft 瞭解：使用 ASP.NET Core 建立 web API](/learn/modules/build-web-api-aspnet-core/)
- [建立原生行動應用程式的後端服務](/aspnet/core/mobile/native-mobile-backend/)
- [REST Api 的屬性路由](/aspnet/core/mvc/controllers/routing?view=aspnetcore-5.0#ar)
- [TodoREST (範例)](/samples/xamarin/xamarin-forms-samples/webservices-todorest) \(英文\)
- [HttpClient API](xref:System.Net.Http.HttpClient)
- [Android 網路安全性設定](https://devblogs.microsoft.com/xamarin/cleartext-http-android-network-security/)
- [iOS 應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)
- [連線到本機 Web 服務](~/cross-platform/deploy-test/connect-to-local-web-services.md)
