---
title: 存取遠端資料
description: 本章說明 eShopOnContainers mobile 應用程式如何從容器化微服務存取資料。
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9c793f4d5f0cda5bff2dedef5e4e5e5bdfca69e5
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "70770811"
---
# <a name="accessing-remote-data"></a>存取遠端資料

許多現代化的 web 解決方案都會使用 web 伺服器所裝載的 web 服務，以提供遠端用戶端應用程式的功能。 Web 服務所公開的作業構成 Web API。

用戶端應用程式應該能夠利用 Web API，而不需要知道 API 所公開的資料或作業如何執行。 這需要由通用標準遵守美國 API，讓用戶端應用程式和 web 服務同意所要使用的資料格式，以及用戶端應用程式與 web 服務之間交換的資料結構。

## <a name="introduction-to-representational-state-transfer"></a>具像狀態傳輸簡介

具像狀態傳輸（REST）是用來建立以超媒體為基礎之分散式系統的架構樣式。 REST 模型的主要優點是它是以開放式標準為基礎，並不會系結模型或用戶端應用程式的執行，以存取任何特定的執行。 因此，可以使用 Microsoft ASP.NET Core MVC 來實作為 REST web 服務，而且用戶端應用程式可以使用任何可產生 HTTP 要求及剖析 HTTP 回應的語言和工具組進行開發。

REST 模型使用導覽配置來代表網路上的物件和服務，稱為「資源」。 執行 REST 的系統通常會使用 HTTP 通訊協定來傳輸要求，以存取這些資源。 在這類系統中，用戶端應用程式會以識別資源的 URI 形式提交要求，並以 HTTP 方法（例如 GET、POST、PUT 或 DELETE）表示要在該資源上執行的作業。 HTTP 要求的主體包含執行作業所需的任何資料。

> [!NOTE]
> REST 會定義無狀態的要求模型。 因此，HTTP 要求必須是獨立的，而且可能會以任何順序發生。

來自 REST 要求的回應會使用標準的 HTTP 狀態碼。 例如，傳回有效資料的要求應該包含 HTTP 回應碼200（確定），而找不到或無法刪除指定資源的要求應該會傳回包含 HTTP 狀態碼404（找不到）的回應。

RESTful Web API 會公開一組已連線的資源，並提供核心作業，讓應用程式能夠操控這些資源，並輕鬆地在兩者之間流覽。 基於這個理由，構成典型 RESTful Web API 的 Uri 會導向其公開的資料，並使用 HTTP 所提供的功能來操作此資料。

用戶端應用程式在 HTTP 要求中所包含的資料，以及來自 web 伺服器的對應回應訊息，可能會以各種不同的格式呈現，稱為媒體類型。 當用戶端應用程式傳送的要求會傳回訊息本文中的資料時，它可以在要求的 `Accept` 標頭中指定可處理的媒體類型。 如果 web 伺服器支援此媒體類型，它可以使用包含 `Content-Type` 標頭的回應來回複，以指定訊息主體中的資料格式。 接著，用戶端應用程式必須負責剖析回應訊息，並適當地解讀訊息本文中的結果。

如需 REST 的詳細資訊，請參閱[api 設計](/azure/architecture/best-practices/api-design/)和[api 執行](/azure/architecture/best-practices/api-implementation/)。

## <a name="consuming-restful-apis"></a>使用 RESTful Api

EShopOnContainers 行動應用程式會使用模型 ViewModel （MVVM）模式，而模式的模型元素則代表應用程式中使用的領域實體。 EShopOnContainers 參考應用程式中的控制器和存放庫類別會接受並傳回許多這些模型物件。 因此，它們會當做資料傳輸物件（Dto）使用，以保存行動應用程式與容器化微服務之間傳遞的所有資料。 使用 Dto 將資料傳遞至 web 服務並從中接收資料的主要優點是，藉由在單一遠端呼叫中傳輸更多資料，應用程式可以減少需要進行的遠端呼叫數目。

### <a name="making-web-requests"></a>提出 Web 要求

EShopOnContainers 行動應用程式會使用 `HttpClient` 類別，透過 HTTP 提出要求，並使用 JSON 做為媒體類型。 這個類別會提供以非同步方式傳送 HTTP 要求，以及從 URI 識別的資源接收 HTTP 回應的功能。 @No__t_0 類別代表在發出 HTTP 要求之後從 REST API 接收的 HTTP 回應訊息。 其中包含回應的相關資訊，包括狀態碼、標頭和任何主體。 @No__t_0 類別代表 HTTP 主體和內容標頭，例如 `Content-Type` 和 `Content-Encoding`。 您可以使用任何 `ReadAs` 方法（例如 `ReadAsStringAsync` 和 `ReadAsByteArrayAsync`）來讀取內容，視資料的格式而定。

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>提出 GET 要求

@No__t_0 類別是用來從目錄微服務管理資料抓取程式。 在 `ViewModelLocator` 類別的 `RegisterDependencies` 方法中，會使用 Autofac 相依性插入容器，將 `CatalogService` 類別註冊為 `ICatalogService` 類型的類型對應。 然後，當建立 `CatalogViewModel` 類別的實例時，它的函式會接受一個 `ICatalogService` 型別，它會 Autofac 解析，傳回 `CatalogService` 類別的實例。 如需相依性插入的詳細資訊，請參閱相依性[插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

圖10-1 顯示從目錄微服務讀取目錄資料以供 `CatalogView` 顯示之類別的互動。

[![](accessing-remote-data-images/catalogdata.png "Retrieving data from the catalog microservice")](accessing-remote-data-images/catalogdata-large.png#lightbox "Retrieving data from the catalog microservice")

**圖 10-1**：從目錄微服務中抓取資料

流覽 `CatalogView` 時，會呼叫 `CatalogViewModel` 類別中的 `OnInitialize` 方法。 這個方法會從目錄微服務抓取目錄資料，如下列程式碼範例所示：

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

這個方法會呼叫由 Autofac 插入 `CatalogViewModel` 中 `CatalogService` 實例的 `GetCatalogAsync` 方法。 下列程式碼範例示範 `GetCatalogAsync` 方法：

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

這個方法會建立 URI，以識別要求將傳送至的資源，並使用 `RequestProvider` 類別來叫用資源上的 GET HTTP 方法，然後再將結果傳回給 `CatalogViewModel`。 @No__t_0 類別所包含的功能，會以識別資源的 URI 形式提交要求、指定要在該資源上執行之作業的 HTTP 方法，以及包含執行作業所需之任何資料的主體。 如需如何將 `RequestProvider` 類別插入 `CatalogService class` 的詳細資訊，請參閱相依性[插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

下列程式碼範例顯示 `RequestProvider` 類別中的 `GetAsync` 方法：

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

這個方法會呼叫 `CreateHttpClient` 方法，它會傳回已設定適當標頭之 `HttpClient` 類別的實例。 然後，它會將非同步 GET 要求提交至 URI 所識別的資源，並將回應儲存在 `HttpResponseMessage` 實例中。 然後會叫用 `HandleResponse` 方法，如果回應未包含成功的 HTTP 狀態碼，則會擲回例外狀況。 然後，回應會讀取為字串，從 JSON 轉換成 `CatalogRoot` 物件，並傳回給 `CatalogService`。

下列程式碼範例顯示 `CreateHttpClient` 方法：

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

這個方法會建立 `HttpClient` 類別的新實例，並將 `HttpClient` 實例所提出之任何要求的 `Accept` 標頭設定為 `application/json`，這表示它預期會使用 JSON 來格式化任何回應的內容。 然後，如果將存取權杖當做引數傳遞給 `CreateHttpClient` 方法，它就會加入至 `HttpClient` 實例所提出之任何要求的 `Authorization` 標頭，並在前面加上字串 `Bearer`。 如需授權的詳細資訊，請參閱[授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

當 `RequestProvider` 類別中的 `GetAsync` 方法呼叫 `HttpClient.GetAsync` 時，會叫用目錄. API 專案中 `CatalogController` 類別的 `Items` 方法，如下列程式碼範例所示：

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

這個方法會使用 EntityFramework 從 SQL 資料庫中抓取目錄資料，並將其傳回為包含成功 HTTP 狀態碼的回應訊息，以及 JSON 格式 `CatalogItem` 實例的集合。

#### <a name="making-a-post-request"></a>提出 POST 要求

@No__t_0 類別是用來管理購物籃微服務的資料抓取和更新程式。 在 `ViewModelLocator` 類別的 `RegisterDependencies` 方法中，會使用 Autofac 相依性插入容器，將 `BasketService` 類別註冊為 `IBasketService` 類型的類型對應。 然後，當建立 `BasketViewModel` 類別的實例時，它的函式會接受一個 `IBasketService` 型別，它會 Autofac 解析，傳回 `BasketService` 類別的實例。 如需相依性插入的詳細資訊，請參閱相依性[插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

圖10-2 顯示將 `BasketView` 所顯示的購物籃資料傳送至購物籃微服務的類別互動。

[![](accessing-remote-data-images/basketdata.png "Sending data to the basket microservice")](accessing-remote-data-images/basketdata-large.png#lightbox "Sending data to the basket microservice")

**圖 10-2**：將資料傳送至購物籃微服務

將專案新增至購物籃時，會呼叫 `BasketViewModel` 類別中的 `ReCalculateTotalAsync` 方法。 這個方法會更新購物籃中專案的總計值，並將購物籃資料傳送至購物籃微服務，如下列程式碼範例所示：

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

這個方法會呼叫由 Autofac 插入 `BasketViewModel` 中 `BasketService` 實例的 `UpdateBasketAsync` 方法。 下列方法會顯示 `UpdateBasketAsync` 方法：

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

這個方法會建立 URI，以識別要求將傳送至的資源，並使用 `RequestProvider` 類別在資源上叫用 POST HTTP 方法，然後再將結果傳回給 `BasketViewModel`。 請注意，在驗證程式期間，必須從 IdentityServer 取得存取權杖，才能授權對購物籃微服務的要求。 如需授權的詳細資訊，請參閱[授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下列程式碼範例顯示 `RequestProvider` 類別中的其中一個 `PostAsync` 方法：

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

這個方法會呼叫 `CreateHttpClient` 方法，它會傳回已設定適當標頭之 `HttpClient` 類別的實例。 然後，它會將非同步 POST 要求提交至 URI 所識別的資源，並以 JSON 格式傳送序列化購物籃資料，並將回應儲存在 `HttpResponseMessage` 實例中。 然後會叫用 `HandleResponse` 方法，如果回應未包含成功的 HTTP 狀態碼，則會擲回例外狀況。 然後，回應會讀取為字串，從 JSON 轉換成 `CustomerBasket` 物件，並傳回給 `BasketService`。 如需 `CreateHttpClient` 方法的詳細資訊，請參閱[提出 GET 要求](#making_a_get_request)。

當 `RequestProvider` 類別中的 `PostAsync` 方法呼叫 `HttpClient.PostAsync` 時，會叫用購物籃中 `BasketController` 類別的 `Post` 方法，如下列程式碼範例所示：

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

這個方法會使用 `RedisBasketRepository` 類別的實例，將購物籃資料保存到 Redis 快取，並將其傳回為包含成功 HTTP 狀態碼的回應訊息，以及 JSON 格式的 `CustomerBasket` 實例。

#### <a name="making-a-delete-request"></a>提出刪除要求

圖10-3 顯示從購物籃微服務刪除購物籃資料之類別的互動，適用于 `CheckoutView`。

![](accessing-remote-data-images/checkoutdata.png "Deleteing data from the basket microservice")

**圖 10-3**：刪除購物籃微服務中的資料

叫用結帳進程時，會呼叫 `CheckoutViewModel` 類別中的 `CheckoutAsync` 方法。 在清除購物籃之前，此方法會建立新的訂單，如下列程式碼範例所示：

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

這個方法會呼叫由 Autofac 插入 `CheckoutViewModel` 中 `BasketService` 實例的 `ClearBasketAsync` 方法。 下列方法會顯示 `ClearBasketAsync` 方法：

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

下列程式碼範例顯示 `RequestProvider` 類別中的 `DeleteAsync` 方法：

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

這個方法會呼叫 `CreateHttpClient` 方法，它會傳回已設定適當標頭之 `HttpClient` 類別的實例。 然後，它會將非同步刪除要求提交給 URI 所識別的資源。 如需 `CreateHttpClient` 方法的詳細資訊，請參閱[提出 GET 要求](#making_a_get_request)。

當 `RequestProvider` 類別中的 `DeleteAsync` 方法呼叫 `HttpClient.DeleteAsync` 時，會叫用購物籃中 `BasketController` 類別的 `Delete` 方法，如下列程式碼範例所示：

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

這個方法會使用 `RedisBasketRepository` 類別的實例，從 Redis 快取中刪除購物籃資料。

## <a name="caching-data"></a>快取資料

將經常存取的資料快取到靠近應用程式的快速儲存體，可以改善應用程式的效能。 如果快速儲存體的位置比原始來源更接近應用程式，則快取可以大幅改善抓取資料時的回應時間。

最常見的快取形式是讀取快取，其中應用程式會藉由參考快取來抓取資料。 如果資料不在快取中，就會從資料存放區中取出並新增至快取。 應用程式可以使用另行快取模式來執行讀取快取。 此模式會判斷專案目前是否在快取中。 如果專案不在快取中，則會從資料存放區讀取並新增至快取。 如需詳細資訊，請參閱另行快[取模式。](/azure/architecture/patterns/cache-aside/)

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

EShopOnContainers 行動應用程式會使用從快取中獲益的遠端產品映射。 這些影像會由[`Image`](xref:Xamarin.Forms.Image)控制項顯示，以及由[FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/)程式庫提供的 `CachedImage` 控制項。

[@No__t_1](xref:Xamarin.Forms.Image)控制項的 Xamarin 可支援快取下載的影像。 預設會啟用快取，並在本機將映射儲存24小時。 此外，您還可以使用[`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity)屬性來設定到期時間。 如需詳細資訊，請參閱[下載的影像](~/xamarin-forms/user-interface/images.md#downloaded-image-caching)快取。

FFImageLoading 的 `CachedImage` 控制項取代了 Xamarin [`Image`](xref:Xamarin.Forms.Image)控制項，提供額外的屬性來啟用補充功能。 在此功能中，控制項會提供可設定的快取，同時支援錯誤和載入影像預留位置。 下列程式碼範例示範 eShopOnContainers 行動應用程式如何使用 `ProductTemplate` 中的 `CachedImage` 控制項，這是 `CatalogView` 中的[`ListView`](xref:Xamarin.Forms.ListView)控制項所使用的資料範本：

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

@No__t_0 控制項會將 `LoadingPlaceholder` 和 `ErrorPlaceholder` 屬性設定為平臺特定的影像。 [@No__t_0] 屬性會指定抓取 `Source` 屬性所指定的影像時要顯示的影像，而 [`ErrorPlaceholder`] 屬性會指定在嘗試抓取所指定的影像時，如果發生錯誤時要顯示的影像 `Source`property.

如其名稱所示，`CachedImage` 控制項會在裝置上快取遠端影像，時間是由 `CacheDuration` 屬性值所指定。 若未明確設定此屬性值，則會套用預設值30天。

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
> 絕對不要執行無止盡的重試機制。 使用有限的重試次數，或執行[斷路](/azure/architecture/patterns/circuit-breaker/)器模式以允許服務復原。

EShopOnContainers 行動應用程式目前不會在提出 RESTful web 要求時，執行重試模式。 不過， [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/)程式庫所提供的 `CachedImage` 控制項，可透過重試影像載入來支援暫時性錯誤處理。 如果映射載入失敗，將會進行進一步的嘗試。 嘗試次數是由 `RetryCount` 屬性指定，而重試會在 `RetryDelay` 屬性指定的延遲之後發生。 如果未明確設定這些屬性值，則會套用預設值–3表示 `RetryCount` 屬性，而 `RetryDelay` 屬性則會套用250毫秒。 如需 `CachedImage` 控制項的詳細資訊，請參閱快取[影像](#caching_images)。

EShopOnContainers reference 應用程式會執行重試模式。 如需詳細資訊，包括如何結合重試模式與 `HttpClient` 類別的討論，請參閱[.Net 微服務：容器化 .Net 應用程式的架構](https://aka.ms/microservicesebook)。

如需重試模式的詳細資訊，請參閱[重試](/azure/architecture/patterns/retry/)模式。

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>斷路器模式

在某些情況下，可能會因為預期的事件需要較長的修正而發生錯誤。 這些錯誤的範圍可從部分連線中斷到服務的完全失敗。 在這些情況下，應用程式重試不太可能會成功的作業，而應接受作業失敗並據此處理此失敗的情況，是無意義的。

斷路器模式可防止應用程式重複嘗試執行可能失敗的作業，同時也能讓應用程式偵測是否已解決錯誤。

> [!NOTE]
> 斷路器模式的目的與重試模式不同。 重試模式可讓應用程式在預期成功的情況下重試操作。 斷路器模式可防止應用程式執行可能失敗的作業。

斷路器會作為可能會失敗之作業的 proxy。 Proxy 應該會監視最近發生的失敗次數，並使用這項資訊來決定是否允許作業繼續，或立即傳回例外狀況。

EShopOnContainers 行動應用程式目前不會執行斷路器模式。 不過，eShopOnContainers 會執行。 如需詳細資訊，請參閱[.Net 微服務：容器化 .Net 應用程式的架構](https://aka.ms/microservicesebook)。

> [!TIP]
> 結合重試和斷路器模式。 應用程式可以結合重試和斷路器模式，方法是使用重試模式透過斷路器叫用操作。 不過，重試邏輯應該會受到斷路器所傳回之任何例外狀況的影響，而且如果斷路器指出錯誤不是暫時性的，則放棄重試嘗試。

如需斷路器模式的詳細資訊，請參閱[斷路器模式。](/azure/architecture/patterns/circuit-breaker/)

## <a name="summary"></a>總結

許多現代化的 web 解決方案都會使用 web 伺服器所裝載的 web 服務，以提供遠端用戶端應用程式的功能。 Web 服務所公開的作業會構成 Web API，而且用戶端應用程式應該能夠利用 Web API，而不需要知道 API 所公開的資料或作業如何執行。

將經常存取的資料快取到靠近應用程式的快速儲存體，可以改善應用程式的效能。 應用程式可以使用另行快取模式來執行讀取快取。 此模式會判斷專案目前是否在快取中。 如果專案不在快取中，則會從資料存放區讀取並新增至快取。

與 web Api 通訊時，應用程式必須受到暫時性錯誤的影響。 暫時性的錯誤包括暫時失去服務的網路連線、暫時無法使用服務，或是服務忙碌時所發生的超時。 這些錯誤通常會自行修正，如果動作在適當的延遲後重複，可能會成功。 因此，應用程式應該將存取 Web API 的所有嘗試包裝在程式碼中，以執行暫時性錯誤處理機制。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)
