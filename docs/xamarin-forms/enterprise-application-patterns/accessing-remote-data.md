---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7a7482754653e2aecb069afa215b68306ade8a99
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84129576"
---
# <a name="accessing-remote-data"></a>存取遠端資料

許多現代化的 web 解決方案都會使用 web 伺服器所裝載的 web 服務，以提供遠端用戶端應用程式的功能。 Web 服務開放的作業構成 Web API。

用戶端應用程式應該能夠利用 Web API，而不需要知道 API 所公開的資料或作業如何執行。 這需要由通用標準遵守美國 API，讓用戶端應用程式和 web 服務同意所要使用的資料格式，以及用戶端應用程式與 web 服務之間交換的資料結構。

## <a name="introduction-to-representational-state-transfer"></a>具像狀態傳輸簡介

具像狀態傳輸（REST）是用來建立以超媒體為基礎之分散式系統的架構樣式。 REST 模型的主要優點是它是以開放式標準為基礎，並不會系結模型或用戶端應用程式的執行，以存取任何特定的執行。 因此，可以使用 Microsoft ASP.NET Core MVC 來實作為 REST web 服務，而且用戶端應用程式可以使用任何可產生 HTTP 要求及剖析 HTTP 回應的語言和工具組進行開發。

REST 模型使用導覽配置來代表網路上的物件和服務，稱為「資源」。 執行 REST 的系統通常會使用 HTTP 通訊協定來傳輸要求，以存取這些資源。 在這類系統中，用戶端應用程式會以識別資源的 URI 形式提交要求，並以 HTTP 方法（例如 GET、POST、PUT 或 DELETE）表示要在該資源上執行的作業。 HTTP 要求的主體包含執行作業所需的任何資料。

> [!NOTE]
> REST 會定義無狀態的要求模型。 因此，HTTP 要求必須是獨立的，而且可能會以任何順序發生。

來自 REST 要求的回應會使用標準的 HTTP 狀態碼。 例如，傳回有效資料的要求應包含 HTTP 回應碼 200 (良好)，而找不到或無法刪除指定資源的要求應傳回包含 HTTP 狀態碼 404 (找不到) 的回應。

RESTful Web API 會公開一組已連線的資源，並提供核心作業，讓應用程式能夠操控這些資源，並輕鬆地在兩者之間流覽。 基於這個理由，構成典型 RESTful Web API 的 Uri 會導向其公開的資料，並使用 HTTP 所提供的功能來操作此資料。

用戶端應用程式在 HTTP 要求中所包含的資料，以及來自 web 伺服器的對應回應訊息，可能會以各種不同的格式呈現，稱為媒體類型。 當用戶端應用程式傳送的要求會傳回訊息本文中的資料時，它可以在要求的標頭中指定可處理的媒體類型 `Accept` 。 如果 web 伺服器支援此媒體類型，它可以使用包含標頭的回應來回複，而此 `Content-Type` 標頭會指定訊息主體中的資料格式。 接著，用戶端應用程式必須負責剖析回應訊息，並適當地解讀訊息本文中的結果。

如需 REST 的詳細資訊，請參閱[api 設計](/azure/architecture/best-practices/api-design/)和[api 執行](/azure/architecture/best-practices/api-implementation/)。

## <a name="consuming-restful-apis"></a>使用 RESTful Api

EShopOnContainers 行動應用程式會使用模型 ViewModel （MVVM）模式，而模式的模型元素則代表應用程式中使用的領域實體。 EShopOnContainers 參考應用程式中的控制器和存放庫類別會接受並傳回許多這些模型物件。 因此，它們會當做資料傳輸物件（Dto）使用，以保存行動應用程式與容器化微服務之間傳遞的所有資料。 使用 Dto 將資料傳遞至 web 服務並從中接收資料的主要優點是，藉由在單一遠端呼叫中傳輸更多資料，應用程式可以減少需要進行的遠端呼叫數目。

### <a name="making-web-requests"></a>提出 Web 要求

EShopOnContainers 行動應用程式會使用 `HttpClient` 類別，透過 HTTP 提出要求，並使用 JSON 做為媒體類型。 這個類別會提供以非同步方式傳送 HTTP 要求，以及從 URI 識別的資源接收 HTTP 回應的功能。 `HttpResponseMessage`類別代表在發出 HTTP 要求之後從 REST API 接收的 HTTP 回應訊息。 其中包含回應的相關資訊，包括狀態碼、標頭和任何主體。 `HttpContent`類別代表 HTTP 主體和內容標頭，例如 `Content-Type` 和 `Content-Encoding` 。 您可以使用任何 `ReadAs` 方法（例如和）來讀取內容， `ReadAsStringAsync` `ReadAsByteArrayAsync` 視資料的格式而定。

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>提出 GET 要求

`CatalogService`類別是用來從目錄微服務管理資料抓取程式。 在 `RegisterDependencies` 類別的方法中 `ViewModelLocator` ， `CatalogService` 類別會註冊為具有 Autofac 相依性插入容器之類型的類型對應 `ICatalogService` 。 然後，在建立類別的實例時 `CatalogViewModel` ，其函式會接受 `ICatalogService` Autofac 解析的類型，以傳回類別的實例 `CatalogService` 。 如需相依性插入的詳細資訊，請參閱相依性[插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

圖10-1 顯示從目錄微服務讀取目錄資料以供顯示的類別互動 `CatalogView` 。

[![](accessing-remote-data-images/catalogdata.png "Retrieving data from the catalog microservice")](accessing-remote-data-images/catalogdata-large.png#lightbox "Retrieving data from the catalog microservice")

**圖 10-1**：從目錄微服務中抓取資料

當 `CatalogView` 流覽至時， `OnInitialize` `CatalogViewModel` 會呼叫類別中的方法。 這個方法會從目錄微服務抓取目錄資料，如下列程式碼範例所示：

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

這個方法會呼叫 `GetCatalogAsync` Autofac 所插入之實例的方法 `CatalogService` `CatalogViewModel` 。 下列程式碼範例示範 `GetCatalogAsync` 方法：

```csharp
public async Task<ObservableCollection<CatalogItem>> GetCatalogAsync()  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.CatalogEndpoint);  
    builder.Path = "api/v1/catalog/items";  
    string uri = builder.ToString();  

    CatalogRoot catalog = await _requestProvider.GetAsync<CatalogRoot>(uri);  
    ...  
    return catalog?.Data.ToObservableCollection();            
}
```

這個方法會建立 URI，以識別要求將傳送至的資源，並使用 `RequestProvider` 類別來叫用資源上的 GET HTTP 方法，然後再將結果傳回給 `CatalogViewModel` 。 `RequestProvider`類別所包含的功能會以識別資源的 URI 形式提交要求、指定要在該資源上執行之作業的 HTTP 方法，以及包含執行作業所需之任何資料的主體。 如需如何將類別插入中的詳細資訊 `RequestProvider` `CatalogService class` ，請參閱相依性[插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

下列程式碼範例顯示 `GetAsync` 類別中的方法 `RequestProvider` ：

```csharp
public async Task<TResult> GetAsync<TResult>(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    HttpResponseMessage response = await httpClient.GetAsync(uri);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>   
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

這個方法會呼叫 `CreateHttpClient` 方法，它會傳回已 `HttpClient` 設定適當標頭之類別的實例。 然後，它會將非同步 GET 要求提交至 URI 所識別的資源，並將回應儲存在 `HttpResponseMessage` 實例中。 `HandleResponse`接著會叫用方法，如果回應未包含成功的 HTTP 狀態碼，則會擲回例外狀況。 然後，回應會讀取為字串，從 JSON 轉換成 `CatalogRoot` 物件，並傳回給 `CatalogService` 。

`CreateHttpClient`方法如下列程式碼範例所示：

```csharp
private HttpClient CreateHttpClient(string token = "")  
{  
    var httpClient = new HttpClient();  
    httpClient.DefaultRequestHeaders.Accept.Add(  
        new MediaTypeWithQualityHeaderValue("application/json"));  

    if (!string.IsNullOrEmpty(token))  
    {  
        httpClient.DefaultRequestHeaders.Authorization =   
            new AuthenticationHeaderValue("Bearer", token);  
    }  
    return httpClient;  
}
```

這個方法會建立類別的新實例 `HttpClient` ，並將 `Accept` 實例所提出之任何要求的標頭設定 `HttpClient` 為 `application/json` ，這表示它預期會使用 JSON 來格式化任何回應的內容。 然後，如果將存取權杖當做引數傳遞給 `CreateHttpClient` 方法，它就會加入至 `Authorization` 實例所提出之任何要求的標頭 `HttpClient` 中，並在前面加上字串 `Bearer` 。 如需授權的詳細資訊，請參閱[授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

當類別中的 `GetAsync` 方法 `RequestProvider` 呼叫時 `HttpClient.GetAsync` ，會叫用 `Items` `CatalogController` 目錄. API 專案中類別的方法，如下列程式碼範例所示：

```csharp
[HttpGet]  
[Route("[action]")]  
public async Task<IActionResult> Items(  
    [FromQuery]int pageSize = 10, [FromQuery]int pageIndex = 0)  
{  
    var totalItems = await _catalogContext.CatalogItems  
        .LongCountAsync();  

    var itemsOnPage = await _catalogContext.CatalogItems  
        .OrderBy(c=>c.Name)  
        .Skip(pageSize * pageIndex)  
        .Take(pageSize)  
        .ToListAsync();  

    itemsOnPage = ComposePicUri(itemsOnPage);  
    var model = new PaginatedItemsViewModel<CatalogItem>(  
        pageIndex, pageSize, totalItems, itemsOnPage);             

    return Ok(model);  
}
```

這個方法會使用 EntityFramework 從 SQL 資料庫中抓取目錄資料，並將其傳回為包含成功 HTTP 狀態碼的回應訊息，以及 JSON 格式化實例的集合 `CatalogItem` 。

#### <a name="making-a-post-request"></a>提出 POST 要求

`BasketService`類別是用來管理購物籃微服務的資料抓取和更新程式。 在 `RegisterDependencies` 類別的方法中 `ViewModelLocator` ， `BasketService` 類別會註冊為具有 Autofac 相依性插入容器之類型的類型對應 `IBasketService` 。 然後，在建立類別的實例時 `BasketViewModel` ，其函式會接受 `IBasketService` Autofac 解析的類型，以傳回類別的實例 `BasketService` 。 如需相依性插入的詳細資訊，請參閱相依性[插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

圖10-2 顯示將所顯示的購物籃資料傳送 `BasketView` 至購物籃微服務的類別互動。

[![](accessing-remote-data-images/basketdata.png "Sending data to the basket microservice")](accessing-remote-data-images/basketdata-large.png#lightbox "Sending data to the basket microservice")

**圖 10-2**：將資料傳送至購物籃微服務

將專案新增至購物籃時， `ReCalculateTotalAsync` 會呼叫類別中的方法 `BasketViewModel` 。 這個方法會更新購物籃中專案的總計值，並將購物籃資料傳送至購物籃微服務，如下列程式碼範例所示：

```csharp
private async Task ReCalculateTotalAsync()  
{  
    ...  
    await _basketService.UpdateBasketAsync(new CustomerBasket  
    {  
        BuyerId = userInfo.UserId,   
        Items = BasketItems.ToList()  
    }, authToken);  
}
```

這個方法會呼叫 `UpdateBasketAsync` Autofac 所插入之實例的方法 `BasketService` `BasketViewModel` 。 下列方法顯示 `UpdateBasketAsync` 方法：

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

這個方法會建立 URI，以識別要求將傳送至的資源，並使用類別在 `RequestProvider` 資源上叫用 POST HTTP 方法，然後再將結果傳回給 `BasketViewModel` 。 請注意，在驗證程式期間，必須從 IdentityServer 取得存取權杖，才能授權對購物籃微服務的要求。 如需授權的詳細資訊，請參閱[授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下列程式碼範例顯示類別中的其中一個 `PostAsync` 方法 `RequestProvider` ：

```csharp
public async Task<TResult> PostAsync<TResult>(  
    string uri, TResult data, string token = "", string header = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    ...  
    var content = new StringContent(JsonConvert.SerializeObject(data));  
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");  
    HttpResponseMessage response = await httpClient.PostAsync(uri, content);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>  
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

這個方法會呼叫 `CreateHttpClient` 方法，它會傳回已 `HttpClient` 設定適當標頭之類別的實例。 然後，它會將非同步 POST 要求提交至 URI 所識別的資源，並以 JSON 格式傳送序列化購物籃資料，並將回應儲存在實例中 `HttpResponseMessage` 。 `HandleResponse`接著會叫用方法，如果回應未包含成功的 HTTP 狀態碼，則會擲回例外狀況。 然後，回應會讀取為字串，從 JSON 轉換成 `CustomerBasket` 物件，並傳回給 `BasketService` 。 如需方法的詳細資訊 `CreateHttpClient` ，請參閱[提出 GET 要求](#making_a_get_request)。

當類別中的 `PostAsync` 方法 `RequestProvider` 呼叫時 `HttpClient.PostAsync` ，會叫用 `Post` `BasketController` 購物籃中類別的方法，如下列程式碼範例所示：

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

這個方法會使用類別的實例， `RedisBasketRepository` 將購物籃資料保存到 Redis 快取，並將其傳回為回應訊息，其中包含成功的 HTTP 狀態碼和 JSON 格式的 `CustomerBasket` 實例。

#### <a name="making-a-delete-request"></a>提出刪除要求

圖10-3 顯示從購物籃微服務刪除購物籃資料之類別的互動 `CheckoutView` 。

![](accessing-remote-data-images/checkoutdata.png "Deleteing data from the basket microservice")

**圖 10-3**：刪除購物籃微服務中的資料

叫用結帳進程時， `CheckoutAsync` `CheckoutViewModel` 會呼叫類別中的方法。 在清除購物籃之前，此方法會建立新的訂單，如下列程式碼範例所示：

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

這個方法會呼叫 `ClearBasketAsync` Autofac 所插入之實例的方法 `BasketService` `CheckoutViewModel` 。 下列方法顯示 `ClearBasketAsync` 方法：

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

這個方法會建立 URI，以識別要求將傳送至的資源，並使用 `RequestProvider` 類別來叫用資源上的 DELETE HTTP 方法。 請注意，在驗證程式期間，必須從 IdentityServer 取得存取權杖，才能授權對購物籃微服務的要求。 如需授權的詳細資訊，請參閱[授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下列程式碼範例顯示 `DeleteAsync` 類別中的方法 `RequestProvider` ：

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

這個方法會呼叫 `CreateHttpClient` 方法，它會傳回已 `HttpClient` 設定適當標頭之類別的實例。 然後，它會將非同步刪除要求提交給 URI 所識別的資源。 如需方法的詳細資訊 `CreateHttpClient` ，請參閱[提出 GET 要求](#making_a_get_request)。

當類別中的 `DeleteAsync` 方法 `RequestProvider` 呼叫時 `HttpClient.DeleteAsync` ，會叫用 `Delete` `BasketController` 購物籃中類別的方法，如下列程式碼範例所示：

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

這個方法會使用類別的實例 `RedisBasketRepository` ，從 Redis 快取中刪除購物籃資料。

## <a name="caching-data"></a>快取資料

將經常存取的資料快取到靠近應用程式的快速儲存體，可以改善應用程式的效能。 如果快速儲存體的位置比原始來源更接近應用程式，則快取可以大幅改善抓取資料時的回應時間。

最常見的快取形式是讀取快取，其中應用程式會藉由參考快取來抓取資料。 如果資料不在快取中，就會從資料存放區擷取，並加入快取。 應用程式可以使用另行快取模式來執行讀取快取。 此模式會判斷專案目前是否在快取中。 如果專案不在快取中，則會從資料存放區讀取並新增至快取。 如需詳細資訊，請參閱另行快[取模式。](/azure/architecture/patterns/cache-aside/)

> [!TIP]
> 快取經常讀取且不常變更的資料。 此資料可以在第一次由應用程式抓取時，視需要新增至快取。 這表示應用程式只需要從資料存放區提取資料一次，而且使用快取可以滿足後續的存取權。

分散式應用程式（例如 eShopOnContainers 參考應用程式）應該提供下列其中一種或兩種快取：

- 共用快取，可由多個進程或電腦存取。
- 私人快取，其中的資料會保留在執行應用程式的本機裝置上。

EShopOnContainers 行動應用程式會使用私人快取，其中的資料會保留在執行應用程式實例的本機裝置上。 如需 eShopOnContainers 參考應用程式所使用之快取的相關資訊，請參閱[.Net 微服務：容器化 .Net 應用程式的架構](https://aka.ms/microservicesebook)。

> [!TIP]
> 將快取視為暫時性資料存放區，可隨時消失。 確保資料會保留在原始資料存放區和快取中。 如果快取變得無法使用，則遺失資料的機會會降到最低。

### <a name="managing-data-expiration"></a>管理資料到期

預期快取的資料一律會與原始資料一致。 原始資料存放區中的資料在快取之後可能會變更，導致快取的資料變成過時。 因此，應用程式應該執行策略，協助確保快取中的資料盡可能保持最新狀態，但也可以偵測並處理快取中的資料已過時時所發生的情況。 大部分的快取機制可讓快取設定為將資料過期，因而減少資料可能已過期的期間。

> [!TIP]
> 設定快取時的預設到期時間。 許多快取會執行過期，這樣會使資料失效，並在指定的期間內未存取時，從快取中移除。 不過，在選擇到期時間時，必須特別小心。 如果變得太短，資料將會很快過期，而快取的優點也會減少。 如果太長，資料風險就會變得過時。 因此，到期時間應該符合使用資料之應用程式的存取模式。

當快取的資料過期時，應從快取中移除，而且應用程式必須從原始資料存放區抓取資料，並將其放回快取中。

如果允許資料保留太長一段時間，可能也會填滿快取。 因此，將新專案加入至快取的要求可能需要移除進程中的某些專案 *，稱為收回。* 快取服務通常會以最不常使用的方式來收回資料。 不過，還有其他收回原則，包括最常使用和先進先出。如需詳細資訊，請參閱快取[指引](/azure/architecture/best-practices/caching/)。

<a name="caching_images" />

### <a name="caching-images"></a>快取影像

EShopOnContainers 行動應用程式會使用從快取中獲益的遠端產品映射。 這些影像會由控制項顯示 [`Image`](xref:Xamarin.Forms.Image) ，以及 `CachedImage` 由[FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/)程式庫提供的控制項。

Xamarin.Forms [`Image`](xref:Xamarin.Forms.Image) 控制項支援快取下載的影像。 預設會啟用快取，並在本機將映射儲存24小時。 此外，您可以使用屬性來設定到期時間 [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) 。 如需詳細資訊，請參閱[下載的影像](~/xamarin-forms/user-interface/images.md#downloaded-image-caching)快取。

FFImageLoading 的 `CachedImage` 控制項是控制項的替代方案 Xamarin.Forms [`Image`](xref:Xamarin.Forms.Image) ，提供額外的屬性來啟用補充功能。 在此功能中，控制項會提供可設定的快取，同時支援錯誤和載入影像預留位置。 下列程式碼範例顯示 eShopOnContainers 行動應用程式如何使用 `CachedImage` 中的控制項 `ProductTemplate` ，這是中的控制項所使用的資料範本 [`ListView`](xref:Xamarin.Forms.ListView) `CatalogView` ：

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

`CachedImage`控制項會將 `LoadingPlaceholder` 和屬性設定 `ErrorPlaceholder` 為平臺特定的影像。 `LoadingPlaceholder`屬性會指定在抓取屬性所指定的影像時要顯示的影像 `Source` ，而 `ErrorPlaceholder` 屬性會指定在嘗試抓取屬性所指定的影像時，如果發生錯誤時要顯示的影像 `Source` 。

正如其名， `CachedImage` 控制項會在裝置上快取遠端影像，以達屬性值所指定的時間 `CacheDuration` 。 若未明確設定此屬性值，則會套用預設值30天。

## <a name="increasing-resilience"></a>提高復原能力

與遠端服務和資源通訊的所有應用程式都必須受到暫時性錯誤的影響。 暫時性的錯誤包括暫時失去服務的網路連線、暫時無法使用服務，或是服務忙碌時所發生的超時。 這些錯誤通常會自行修正，如果動作在適當的延遲後重複，可能會成功。

暫時性錯誤可能會對應用程式的認知品質造成重大影響，即使在所有可預見的情況下已徹底測試過也一樣。 為了確保與遠端服務通訊的應用程式能夠可靠地運作，它必須能夠執行下列所有動作：

- 在錯誤發生時偵測到它們，並判斷錯誤是否可能是暫時性的。
- 如果作業判斷錯誤可能是暫時性的，並持續追蹤作業重試的次數，請重試操作。
- 使用適當的重試策略，指定重試次數、每次嘗試之間的延遲，以及嘗試失敗後要採取的動作。

藉由在程式碼中包裝所有存取遠端服務的嘗試，以執行重試模式，即可達到此暫時性錯誤處理。

### <a name="retry-pattern"></a>重試模式

如果應用程式在嘗試將要求傳送至遠端服務時偵測到失敗，它可以使用下列任何一種方式來處理失敗：

- 正在重試操作。 應用程式可以立即重試失敗的要求。
- 延遲後重試作業。 應用程式應該等候一段適當的時間，然後再重試要求。
- 正在取消作業。 應用程式應該取消作業並報告例外狀況。

應調整重試策略，以符合應用程式的商務需求。 例如，請務必將重試計數和重試間隔優化到嘗試的作業。 如果作業是使用者互動的一部分，則重試間隔應該是短的，而且只會嘗試幾次重試，以避免讓使用者等待回應。 如果作業是長時間執行之工作流程的一部分，而取消或重新開機工作流程的成本很高或非常耗時，則適合在嘗試之間等候較長的時間，然後再重試一次。

> [!NOTE]
> 一種積極的重試策略，每次嘗試之間的延遲最少，以及大量重試，可能會降低執行接近或容量的遠端服務。 此外，這類重試策略也會影響應用程式的回應能力（如果持續嘗試執行失敗的作業）。

如果要求在重試次數之後仍然失敗，應用程式最好避免進一步要求進入相同的資源，並報告失敗。 然後，應用程式可以在一段時間之後，對資源提出一或多個要求，以查看是否成功。 如需詳細資訊，請參閱[斷路器模式](#circuit_breaker_pattern)。

> [!TIP]
> 永遠不要實作無盡的重試機制。 使用有限的重試次數，或執行[斷路](/azure/architecture/patterns/circuit-breaker/)器模式以允許服務復原。

EShopOnContainers 行動應用程式目前不會在提出 RESTful web 要求時，執行重試模式。 不過， `CachedImage` [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/)程式庫提供的控制項可透過重試影像載入來支援暫時性錯誤處理。 如果映射載入失敗，將會進行進一步的嘗試。 嘗試次數是由屬性所指定 `RetryCount` ，而重試會在屬性所指定的延遲之後發生 `RetryDelay` 。 如果未明確設定這些屬性值，則會套用其預設值– 3 `RetryCount` （屬性）和250毫秒（代表 `RetryDelay` 屬性）。 如需控制項的詳細資訊 `CachedImage` ，請參閱快取[影像](#caching_images)。

EShopOnContainers reference 應用程式會執行重試模式。 如需詳細資訊，包括如何將重試模式與類別結合的討論 `HttpClient` ，請參閱[.net 微服務：容器化 .Net 應用程式的架構](https://aka.ms/microservicesebook)。

如需重試模式的詳細資訊，請參閱[重試](/azure/architecture/patterns/retry/)模式。

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>斷路器模式

在某些情況下，可能會因為預期的事件需要較長的修正而發生錯誤。 這些錯誤的範圍可從部分連線中斷到服務的完全失敗。 在這些情況下，應用程式重試不太可能會成功的作業，而應接受作業失敗並據此處理此失敗的情況，是無意義的。

斷路器模式可防止應用程式重複嘗試執行可能失敗的作業，同時也能讓應用程式偵測是否已解決錯誤。

> [!NOTE]
> 斷路器模式的目的與重試模式不同。 重試模式可讓應用程式在預期成功的情況下重試操作。 斷路器模式可防止應用程式執行可能失敗的作業。

針對可能失敗的作業，斷路器會充當 Proxy。 Proxy 應該會監視最近發生的失敗次數，並使用這項資訊來決定是否允許作業繼續，或立即傳回例外狀況。

EShopOnContainers 行動應用程式目前不會執行斷路器模式。 不過，eShopOnContainers 會執行。 如需詳細資訊，請參閱[.Net 微服務：容器化 .Net 應用程式的架構](https://aka.ms/microservicesebook)。

> [!TIP]
> 結合重試和斷路器模式。 應用程式可以結合重試和斷路器模式，方法是使用重試模式透過斷路器叫用操作。 不過，重試邏輯應對斷路器傳回的任何例外狀況十分敏感，並在斷路器指出錯誤並非暫時性時，放棄重試。

如需斷路器模式的詳細資訊，請參閱[斷路器模式。](/azure/architecture/patterns/circuit-breaker/)

## <a name="summary"></a>摘要

許多現代化的 web 解決方案都會使用 web 伺服器所裝載的 web 服務，以提供遠端用戶端應用程式的功能。 Web 服務所公開的作業會構成 Web API，而且用戶端應用程式應該能夠利用 Web API，而不需要知道 API 所公開的資料或作業如何執行。

將經常存取的資料快取到靠近應用程式的快速儲存體，可以改善應用程式的效能。 應用程式可以使用另行快取模式來執行讀取快取。 此模式會判斷專案目前是否在快取中。 如果專案不在快取中，則會從資料存放區讀取並新增至快取。

與 web Api 通訊時，應用程式必須受到暫時性錯誤的影響。 暫時性的錯誤包括暫時失去服務的網路連線、暫時無法使用服務，或是服務忙碌時所發生的超時。 這些錯誤通常會自行修正，如果動作在適當的延遲後重複，可能會成功。 因此，應用程式應該將存取 Web API 的所有嘗試包裝在程式碼中，以執行暫時性錯誤處理機制。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)
