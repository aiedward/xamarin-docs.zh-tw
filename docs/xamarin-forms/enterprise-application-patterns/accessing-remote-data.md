---
title: 存取遠端資料
description: 本章說明 eShopOnContainers 行動應用程式如何從容器化微服務存取資料。
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b21fecf139262a88fbbe3de3ea8129cc0f9b211e
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373350"
---
# <a name="accessing-remote-data"></a>存取遠端資料

> [!NOTE]
> 此電子書已在2017的春季發行，且自那時起尚未更新。 本書中有很多工具價值，但有些材質已過期。

許多新式的 web 型解決方案都利用 web 伺服器所裝載的 web 服務，來提供遠端用戶端應用程式的功能。 Web 服務開放的作業構成 Web API。

用戶端應用程式應該能夠利用 web API，而不需要知道 API 所公開的資料或作業如何實行。 這需要 API 遵守一般標準，讓用戶端應用程式和 web 服務同意要使用的資料格式，以及在用戶端應用程式與 web 服務之間交換的資料結構。

## <a name="introduction-to-representational-state-transfer"></a>具象狀態傳輸簡介

具象狀態傳輸 (REST) 是根據超媒體建立分散式系統的架構樣式。 REST 模型的主要優點是它是以開放標準為基礎，且不會系結模型的執行，或是將它存取任何特定執行的用戶端應用程式。 因此，您可以使用 Microsoft ASP.NET Core MVC 來執行 REST web 服務，而且用戶端應用程式可以使用任何可產生 HTTP 要求及剖析 HTTP 回應的語言和工具組來進行開發。

REST 模型使用導覽配置來代表網路上的物件和服務，稱為資源。 執行 REST 的系統通常會使用 HTTP 通訊協定來傳輸要求，以存取這些資源。 在這類系統中，用戶端應用程式會以識別資源的 URI 形式來提交要求，而 HTTP 方法 (例如 GET、POST、PUT 或 DELETE) ，表示要在該資源上執行的作業。 HTTP 要求的主體包含執行作業所需的任何資料。

> [!NOTE]
> REST 會定義無狀態的要求模型。 因此，HTTP 要求必須是獨立的，而且可能會以任何順序發生。

REST 要求的回應會使用標準 HTTP 狀態碼。 例如，傳回有效資料的要求應包含 HTTP 回應碼 200 (良好)，而找不到或無法刪除指定資源的要求應傳回包含 HTTP 狀態碼 404 (找不到) 的回應。

RESTful web API 會公開一組已連接的資源，並提供可讓應用程式管理這些資源並輕鬆在兩者之間流覽的核心作業。 基於這個理由，構成一般 RESTful web API 的 Uri 會導向其公開的資料，並使用 HTTP 提供的功能來操作此資料。

用戶端應用程式在 HTTP 要求中所包含的資料，以及來自 web 伺服器的對應回應訊息，可能會以各種格式呈現，也就是所謂的媒體類型。 當用戶端應用程式傳送可在訊息本文中傳回資料的要求時，它可以在要求的標頭中指定可處理的媒體類型 `Accept` 。 如果 web 伺服器支援此媒體類型，它可以使用包含標頭的回應來回複，此 `Content-Type` 標頭會指定訊息主體中的資料格式。 然後，用戶端應用程式會負責剖析回應訊息，並適當地在訊息本文中解讀結果。

如需 REST 的詳細資訊，請參閱 [api 設計](/azure/architecture/best-practices/api-design/) 和 [api 實行](/azure/architecture/best-practices/api-implementation/)。

## <a name="consuming-restful-apis"></a>使用 RESTful Api

EShopOnContainers 行動應用程式會使用 ViewModel (MVVM) 模式，而模式的模型元素則代表應用程式中使用的網域實體。 EShopOnContainers 參考應用程式中的控制器和儲存機制類別會接受並傳回許多這些模型物件。 因此，它們會用來做為資料傳輸物件 (Dto) ，以保存在行動應用程式和容器化微服務之間傳遞的所有資料。 使用 Dto 將資料傳遞至 web 服務並從中接收資料的主要優點是，藉由在單一遠端呼叫中傳輸更多資料，應用程式可以減少需要進行的遠端呼叫數目。

### <a name="making-web-requests"></a>提出 Web 要求

EShopOnContainers 行動應用程式會使用 `HttpClient` 類別，透過 HTTP 提出要求，並使用 JSON 作為媒體類型。 此類別提供的功能可讓您以非同步方式傳送 HTTP 要求，並從 URI 識別的資源接收 HTTP 回應。 `HttpResponseMessage`類別代表發出 HTTP 要求之後，從 REST API 接收的 HTTP 回應訊息。 它包含回應的相關資訊，包括狀態碼、標頭和任何主體。 `HttpContent`類別代表 HTTP 主體和內容標頭，例如 `Content-Type` 和 `Content-Encoding` 。 您可以使用任何 `ReadAs` 方法（例如和）來讀取內容（ `ReadAsStringAsync` 視資料的格式而定） `ReadAsByteArrayAsync` 。

#### <a name="making-a-get-request"></a>提出 GET 要求

`CatalogService`類別是用來管理目錄微服務中的資料抓取進程。 在 `RegisterDependencies` 類別的方法中 `ViewModelLocator` ， `CatalogService` 會使用 Autofac 相依性插入容器，將類別註冊為類型的類型對應 `ICatalogService` 。 然後，當建立類別的實例時，它的函式 `CatalogViewModel` 會接受 `ICatalogService` Autofac 解析的型別，並傳回類別的實例 `CatalogService` 。 如需相依性插入的詳細資訊，請參閱相依性 [插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction-to-dependency-injection)。

圖10-1 顯示類別的互動，這些類別會從目錄微服務讀取目錄資料以供顯示 `CatalogView` 。

[![從目錄微服務中取出資料](accessing-remote-data-images/catalogdata.png)](accessing-remote-data-images/catalogdata-large.png#lightbox "從目錄微服務中取出資料")

**圖 10-1** ：從目錄微服務中取出資料

當 `CatalogView` 流覽至時， `OnInitialize` `CatalogViewModel` 會呼叫類別中的方法。 這個方法會從目錄微服務中取出類別目錄資料，如下列程式碼範例所示：

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

這個方法會呼叫透過 `GetCatalogAsync` `CatalogService` Autofac 插入的實例方法 `CatalogViewModel` 。 下列程式碼範例示範 `GetCatalogAsync` 方法：

```csharp
public async Task<ObservableCollection<CatalogItem>> GetCatalogAsync()  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.CatalogEndpoint);  
    builder.Path = "api/v1/catalog/items";  
    string uri = builder.ToString();  

    CatalogRoot catalog = await _requestProvider.GetAsync<CatalogRoot>(uri);  
    ...  
    return catalog?.Data.ToObservableCollection();            
}
```

這個方法會建立 URI 來識別要求將傳送至的資源，並使用 `RequestProvider` 類別在資源上叫用 GET HTTP 方法，然後將結果傳回給 `CatalogViewModel` 。 `RequestProvider`類別包含的功能，會以識別資源的 URI 形式來提交要求、指出在該資源上執行之作業的 HTTP 方法，以及包含執行作業所需之任何資料的主體。 如需如何將 `RequestProvider` 類別插入的詳細資訊 `CatalogService class` ，請參閱相依性 [插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction-to-dependency-injection)。

下列程式碼範例顯示 `GetAsync` 類別中的方法 `RequestProvider` ：

```csharp
public async Task<TResult> GetAsync<TResult>(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    HttpResponseMessage response = await httpClient.GetAsync(uri);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>   
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

這個方法會呼叫 `CreateHttpClient` 方法，此方法會傳回已 `HttpClient` 設定適當標頭的類別實例。 然後，它會將非同步 GET 要求提交至 URI 所識別的資源，並將回應儲存在 `HttpResponseMessage` 實例中。 `HandleResponse`然後叫用方法，如果回應未包含成功的 HTTP 狀態碼，則會擲回例外狀況。 然後，回應會以字串形式讀取，從 JSON 轉換為 `CatalogRoot` 物件，並傳回至 `CatalogService` 。

`CreateHttpClient`下列程式碼範例顯示方法：

```csharp
private HttpClient CreateHttpClient(string token = "")  
{  
    var httpClient = new HttpClient();  
    httpClient.DefaultRequestHeaders.Accept.Add(  
        new MediaTypeWithQualityHeaderValue("application/json"));  

    if (!string.IsNullOrEmpty(token))  
    {  
        httpClient.DefaultRequestHeaders.Authorization =   
            new AuthenticationHeaderValue("Bearer", token);  
    }  
    return httpClient;  
}
```

這個方法會建立類別的新實例 `HttpClient` ，並將 `Accept` 實例所提出之任何要求的標頭設定 `HttpClient` 為 `application/json` ，這表示它預期會使用 JSON 來格式化任何回應的內容。 然後，如果存取權杖當做引數傳遞至 `CreateHttpClient` 方法，則會將它加入至 `Authorization` 實例所提出之任何要求的標頭 `HttpClient` 中，並在前面加上字串 `Bearer` 。 如需授權的詳細資訊，請參閱 [授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

當類別中的 `GetAsync` 方法 `RequestProvider` 呼叫時 `HttpClient.GetAsync` ，會叫用 `Items` `CatalogController` Catalog. API 專案中類別的方法，如下列程式碼範例所示：

```csharp
[HttpGet]  
[Route("[action]")]  
public async Task<IActionResult> Items(  
    [FromQuery]int pageSize = 10, [FromQuery]int pageIndex = 0)  
{  
    var totalItems = await _catalogContext.CatalogItems  
        .LongCountAsync();  

    var itemsOnPage = await _catalogContext.CatalogItems  
        .OrderBy(c=>c.Name)  
        .Skip(pageSize * pageIndex)  
        .Take(pageSize)  
        .ToListAsync();  

    itemsOnPage = ComposePicUri(itemsOnPage);  
    var model = new PaginatedItemsViewModel<CatalogItem>(  
        pageIndex, pageSize, totalItems, itemsOnPage);             

    return Ok(model);  
}
```

這個方法會使用 EntityFramework 從 SQL 資料庫中取出類別目錄資料，並將其傳回為回應訊息，其中包含成功的 HTTP 狀態碼和 JSON 格式化的 `CatalogItem` 實例集合。

#### <a name="making-a-post-request"></a>提出 POST 要求

`BasketService`類別可用來管理購物籃微服務的資料抓取和更新程式。 在 `RegisterDependencies` 類別的方法中 `ViewModelLocator` ， `BasketService` 會使用 Autofac 相依性插入容器，將類別註冊為類型的類型對應 `IBasketService` 。 然後，當建立類別的實例時，它的函式 `BasketViewModel` 會接受 `IBasketService` Autofac 解析的型別，並傳回類別的實例 `BasketService` 。 如需相依性插入的詳細資訊，請參閱相依性 [插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction-to-dependency-injection)。

[圖 10-2] 顯示將所顯示的購物籃資料傳送 `BasketView` 至購物籃微服務之類別的互動。

[![將資料傳送至購物籃微服務](accessing-remote-data-images/basketdata.png)](accessing-remote-data-images/basketdata-large.png#lightbox "將資料傳送至購物籃微服務")

**圖 10-2** ：將資料傳送至購物籃微服務

將專案新增至購物籃時， `ReCalculateTotalAsync` 會呼叫類別中的方法 `BasketViewModel` 。 這個方法會更新購物籃中專案的總計值，並將購物籃資料傳送至購物籃微服務，如下列程式碼範例所示：

```csharp
private async Task ReCalculateTotalAsync()  
{  
    ...  
    await _basketService.UpdateBasketAsync(new CustomerBasket  
    {  
        BuyerId = userInfo.UserId,   
        Items = BasketItems.ToList()  
    }, authToken);  
}
```

這個方法會呼叫透過 `UpdateBasketAsync` `BasketService` Autofac 插入的實例方法 `BasketViewModel` 。 下列方法會顯示 `UpdateBasketAsync` 方法：

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

這個方法會建立 URI 來識別要求將傳送至的資源，並使用 `RequestProvider` 類別在資源上叫用 POST HTTP 方法，然後將結果傳回給 `BasketViewModel` 。 請注意，在驗證程式期間從 IdentityServer 取得的存取權杖，必須授權購物籃微服務的要求。 如需授權的詳細資訊，請參閱 [授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下列程式碼範例顯示類別中的其中一個 `PostAsync` 方法 `RequestProvider` ：

```csharp
public async Task<TResult> PostAsync<TResult>(  
    string uri, TResult data, string token = "", string header = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    ...  
    var content = new StringContent(JsonConvert.SerializeObject(data));  
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");  
    HttpResponseMessage response = await httpClient.PostAsync(uri, content);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>  
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

這個方法會呼叫 `CreateHttpClient` 方法，此方法會傳回已 `HttpClient` 設定適當標頭的類別實例。 然後，它會將非同步 POST 要求提交至 URI 所識別的資源，並以 JSON 格式傳送序列化的購物籃資料，並將回應儲存在 `HttpResponseMessage` 實例中。 `HandleResponse`然後叫用方法，如果回應未包含成功的 HTTP 狀態碼，則會擲回例外狀況。 然後，回應會以字串形式讀取，從 JSON 轉換為 `CustomerBasket` 物件，並傳回至 `BasketService` 。 如需此方法的詳細資訊 `CreateHttpClient` ，請參閱 [提出 GET 要求](#making-a-get-request)。

當類別中的 `PostAsync` 方法 `RequestProvider` 呼叫時 `HttpClient.PostAsync` ，會叫用 `Post` `BasketController` 購物籃. API 專案中類別的方法，如下列程式碼範例所示：

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

這個方法會使用類別的實例， `RedisBasketRepository` 將購物籃資料保存至 Redis 快取，並將其傳回為包含成功 HTTP 狀態碼和 JSON 格式化實例的回應訊息 `CustomerBasket` 。

#### <a name="making-a-delete-request"></a>提出刪除要求

圖10-3 顯示從購物籃微服務刪除購物籃資料之類別的互動 `CheckoutView` 。

![從購物籃微服務 Deleteing 資料](accessing-remote-data-images/checkoutdata.png)

**圖 10-3** ：從購物籃微服務刪除資料

當叫用結帳進程時， `CheckoutAsync` `CheckoutViewModel` 會呼叫類別中的方法。 此方法會在清除購物籃之前建立新訂單，如下列程式碼範例所示：

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

這個方法會呼叫透過 `ClearBasketAsync` `BasketService` Autofac 插入的實例方法 `CheckoutViewModel` 。 下列方法會顯示 `ClearBasketAsync` 方法：

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

這個方法會建立 URI，以識別要將要求傳送至的資源，並使用類別在 `RequestProvider` 資源上叫用 DELETE HTTP 方法。 請注意，在驗證程式期間從 IdentityServer 取得的存取權杖，必須授權購物籃微服務的要求。 如需授權的詳細資訊，請參閱 [授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下列程式碼範例顯示 `DeleteAsync` 類別中的方法 `RequestProvider` ：

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

這個方法會呼叫 `CreateHttpClient` 方法，此方法會傳回已 `HttpClient` 設定適當標頭的類別實例。 然後，它會將非同步刪除要求提交至 URI 所識別的資源。 如需此方法的詳細資訊 `CreateHttpClient` ，請參閱 [提出 GET 要求](#making-a-get-request)。

當類別中的 `DeleteAsync` 方法 `RequestProvider` 呼叫時 `HttpClient.DeleteAsync` ，會叫用 `Delete` `BasketController` 購物籃. API 專案中類別的方法，如下列程式碼範例所示：

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

這個方法會使用類別的實例 `RedisBasketRepository` ，從 Redis 快取中刪除購物籃資料。

## <a name="caching-data"></a>快取資料

將經常存取的資料快取到靠近應用程式的快速儲存體，即可改善應用程式的效能。 如果快速儲存體的位置比原始來源更接近應用程式，則快取可大幅改善在抓取資料時的回應時間。

最常見的快取形式是讀取快取，其中應用程式會藉由參考快取來抓取資料。 如果資料不在快取中，就會從資料存放區擷取，並加入快取。 應用程式可以使用另行快取模式來執行讀取快取。 此模式會判斷專案目前是否在快取中。 如果專案不在快取中，則會從資料存放區讀取，並新增至快取。 如需詳細資訊，請參閱另行快[取模式。](/azure/architecture/patterns/cache-aside/)

> [!TIP]
> 快取經常讀取且不常變更的資料。 當應用程式第一次抓取此資料時，就可以視需要將此資料新增至快取。 這表示應用程式只需要從資料存放區提取資料一次，而且可以使用快取來滿足後續的存取權。

分散式應用程式（例如 eShopOnContainers 參考應用程式）應提供下列其中一種或兩個快取：

- 共用快取，可由多個進程或電腦存取。
- 私用快取，其中的資料會在執行應用程式的本機裝置上保存。

EShopOnContainers 行動應用程式使用私用快取，其中的資料會在執行應用程式實例的本機裝置上保存。 如需 eShopOnContainers 參考應用程式所使用之快取的相關資訊，請參閱 [.Net 微服務：容器化 .Net 應用程式的架構](https://aka.ms/microservicesebook)。

> [!TIP]
> 您可以將快取視為暫時性資料存放區，而這可能會在任何時間消失。 確定資料會在原始資料存放區和快取中維護。 如果快取變成無法使用，則遺失資料的機會會降至最低。

### <a name="managing-data-expiration"></a>管理資料到期

預期快取的資料一律會與原始資料一致。 原始資料存放區中的資料在快取之後可能會變更，導致快取的資料變成過時。 因此，應用程式應該執行一個策略，協助確保快取中的資料盡可能保持最新狀態，但也可以偵測和處理快取中的資料已過時時所發生的情況。 大部分的快取機制可讓快取設定為過期資料，因而減少資料可能過期的期間。

> [!TIP]
> 設定快取時設定預設到期時間。 許多快取會執行過期，這會使資料失效，並在指定的期間內未存取時將資料從快取中移除。 不過，在選擇到期期間時必須特別小心。 如果變得太短，資料將會太快過期，而快取的優點將會降低。 如果時間太長，資料風險將會變得過時。 因此，到期時間應符合使用該資料之應用程式的存取模式。

快取的資料過期時，應從快取中移除，而且應用程式必須從原始資料存放區取出資料，並將其放回快取中。

如果允許的資料長達一段時間，也可能會填滿快取。 因此，將新專案加入至快取的要求可能需要移除進程中的某些 *專案，稱為* 「收回」。 快取服務通常會以最少最近使用的方式來收回資料。 不過，還有其他的收回原則，包括最近使用和先進先出。如需詳細資訊，請參閱快取 [指引](/azure/architecture/best-practices/caching/)。

### <a name="caching-images"></a>快取影像

EShopOnContainers 行動應用程式會取用可受益于快取的遠端產品影像。 控制項會顯示這些影像 [`Image`](xref:Xamarin.Forms.Image) ，以及 `CachedImage` [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) 程式庫所提供的控制項。

Xamarin.Forms [`Image`](xref:Xamarin.Forms.Image) 控制項支援快取下載的影像。 預設會啟用快取，並在本機將映射儲存24小時。 此外，您可以使用屬性來設定到期時間 [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) 。 如需詳細資訊，請參閱 [下載的映射](~/xamarin-forms/user-interface/images.md#downloaded-image-caching)快取。

FFImageLoading 的 `CachedImage` 控制項是控制項的替代方案 Xamarin.Forms [`Image`](xref:Xamarin.Forms.Image) ，提供可啟用補充功能的其他屬性。 在此功能中，控制項會提供可設定的快取，同時支援錯誤和載入影像預留位置。 下列程式碼範例示範 eShopOnContainers 行動應用程式如何使用 `CachedImage` 中的控制項 `ProductTemplate` ，也就是中的控制項所使用的資料範本 [`ListView`](xref:Xamarin.Forms.ListView) `CatalogView` ：

```xaml
<ffimageloading:CachedImage
    Grid.Row="0"
    Source="{Binding PictureUri}"     
    Aspect="AspectFill">
    <ffimageloading:CachedImage.LoadingPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="default_campaign" />
            <On Platform="UWP" Value="Assets/default_campaign.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.LoadingPlaceholder>
    <ffimageloading:CachedImage.ErrorPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="noimage" />
            <On Platform="UWP" Value="Assets/noimage.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.ErrorPlaceholder>
</ffimageloading:CachedImage>
```

`CachedImage`控制項會將 `LoadingPlaceholder` 和屬性設定 `ErrorPlaceholder` 為平臺特定的影像。 `LoadingPlaceholder`屬性會指定在抓取屬性指定的影像時要顯示的影像 `Source` ，而且 `ErrorPlaceholder` 當嘗試抓取屬性指定的影像時，屬性會指定要顯示的影像 `Source` 。

顧名思義， `CachedImage` 控制項會將裝置上的遠端影像快取為屬性值所指定的時間 `CacheDuration` 。 如果未明確設定此屬性值，則會套用預設值30天。

## <a name="increasing-resilience"></a>提高復原能力

與遠端服務和資源通訊的所有應用程式必須區分暫時性錯誤。 暫時性錯誤包括與服務的網路連線暫時中斷、服務暫時無法使用，或服務忙碌時所發生的超時。 這些錯誤通常會自行修正，如果動作在適當的延遲後重複，可能會成功。

暫時性錯誤可能會對應用程式的認知品質有很大的影響，即使已在所有可預見的情況下徹底測試也一樣。 為了確保與遠端服務通訊的應用程式能可靠地運作，它必須能夠執行下列所有動作：

- 偵測到錯誤發生時，判斷錯誤是否可能是暫時性的。
- 如果判斷錯誤可能是暫時性的，請重試作業，並追蹤作業重試的次數。
- 使用適當的重試策略，以指定重試次數、每次嘗試之間的延遲，以及嘗試失敗之後要採取的動作。

您可以藉由在程式碼中包裝所有存取遠端服務的嘗試，以執行重試模式，來達到這個暫時性錯誤處理。

### <a name="retry-pattern"></a>重試模式

如果應用程式在嘗試將要求傳送至遠端服務時偵測到失敗，則可以使用下列任何一種方式來處理失敗：

- 正在重試作業。 應用程式可以立即重試失敗的要求。
- 在延遲後重試作業。 應用程式應該等候一段適當的時間，再重試要求。
- 正在取消作業。 應用程式應該取消作業並報告例外狀況。

重試策略應進行調整，以符合應用程式的商務需求。 例如，請務必將重試計數和重試間隔優化，以進行嘗試的作業。 如果作業是使用者互動的一部分，則重試間隔應該很短，而且只會重試幾次，以避免讓使用者等待回應。 如果作業是長時間執行之工作流程的一部分，而取消或重新開機工作流程的成本很高或需要花費很多時間，則在嘗試之間等待更長的時間，然後重試。

> [!NOTE]
> 在嘗試之間有最少延遲和大量重試的積極重試策略，可能會降低執行接近或達到容量的遠端服務。 此外，這類重試策略也可能會影響應用程式的回應能力（如果持續嘗試執行失敗的作業）。

如果要求在重試次數之後仍失敗，則應用程式最好避免進一步要求前往相同的資源，並回報失敗。 然後，在設定的一段時間之後，應用程式就可以對資源提出一或多個要求，以確認是否成功。 如需詳細資訊，請參閱 [斷路器模式](#circuit-breaker-pattern)。

> [!TIP]
> 永遠不要實作無盡的重試機制。 使用有限的重試次數，或執行 [斷路](/azure/architecture/patterns/circuit-breaker/) 器模式以允許服務復原。

EShopOnContainers 行動裝置應用程式目前在提出 RESTful web 要求時，不會執行重試模式。 不過， `CachedImage` [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) 程式庫所提供的控制項可透過重試映射載入來支援暫時性錯誤處理。 如果映射載入失敗，將會進行進一步的嘗試。 嘗試的次數是由 `RetryCount` 屬性指定，而重試將會在屬性指定的延遲之後進行 `RetryDelay` 。 如果未明確設定這些屬性值，則會套用其預設值–3表示 `RetryCount` 屬性，並針對屬性250毫秒 `RetryDelay` 。 如需控制項的詳細資訊 `CachedImage` ，請參閱快取 [影像](#caching-images)。

EShopOnContainers 參考應用程式會執行重試模式。 如需詳細資訊，包括如何將重試模式與類別合併的討論 `HttpClient` ，請參閱 [.net 微服務：容器化 .Net 應用程式的架構](https://aka.ms/microservicesebook)。

如需重試模式的詳細資訊，請參閱 [重試](/azure/architecture/patterns/retry/) 模式。

### <a name="circuit-breaker-pattern"></a>斷路器模式

在某些情況下，可能會因為預期的事件需要較長時間來修正而發生錯誤。 這些錯誤的範圍可能從部分連接中斷到服務的完整失敗。 在這些情況下，應用程式無法重試不太可能成功的作業，而應接受作業失敗，並據此處理此失敗。

斷路器模式可防止應用程式重複嘗試執行可能失敗的作業，同時讓應用程式偵測是否已解決錯誤。

> [!NOTE]
> 斷路器模式的目的與重試模式不同。 重試模式可讓應用程式在預期成功的情況下重試操作。 斷路器模式可防止應用程式執行可能失敗的作業。

針對可能失敗的作業，斷路器會充當 Proxy。 Proxy 應監視最近發生的失敗次數，並使用這項資訊來決定是否允許作業繼續，或立即傳回例外狀況。

EShopOnContainers 行動裝置應用程式目前不會執行斷路器模式。 不過，eShopOnContainers 會執行。 如需詳細資訊，請參閱 [.Net 微服務：容器化 .Net 應用程式的架構](https://aka.ms/microservicesebook)。

> [!TIP]
> 結合重試和斷路器模式。 應用程式可以使用重試模式，透過斷路器叫用作業來結合重試和斷路器模式。 不過，重試邏輯應對斷路器傳回的任何例外狀況十分敏感，並在斷路器指出錯誤並非暫時性時，放棄重試。

如需斷路器模式的詳細資訊，請參閱 [斷路](/azure/architecture/patterns/circuit-breaker/) 器模式。

## <a name="summary"></a>總結

許多新式的 web 型解決方案都利用 web 伺服器所裝載的 web 服務，來提供遠端用戶端應用程式的功能。 Web 服務所公開的作業構成了 web API，而用戶端應用程式應該能夠利用 web API，而不需要知道 API 所公開的資料或作業如何實行。

將經常存取的資料快取到靠近應用程式的快速儲存體，即可改善應用程式的效能。 應用程式可以使用另行快取模式來執行讀取快取。 此模式會判斷專案目前是否在快取中。 如果專案不在快取中，則會從資料存放區讀取，並新增至快取。

使用 web Api 進行通訊時，應用程式必須區分暫時性錯誤。 暫時性錯誤包括與服務的網路連線暫時中斷、服務暫時無法使用，或服務忙碌時所發生的超時。 這些錯誤通常會自行修正，如果動作在適當的延遲後重複，則可能會成功。 因此，應用程式應該將所有嘗試存取 web API 的套裝程式裝在程式碼中，以實行暫時性的錯誤處理機制。

## <a name="related-links"></a>相關連結

- [下載電子書 (2Mb PDF) ](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub)  (範例) ](https://github.com/dotnet-architecture/eShopOnContainers)
