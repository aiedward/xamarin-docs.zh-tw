---
title: "存取遠端資料"
ms.topic: article
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0eec51a6c95894482a57bfe3bb1f95aec2045af4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="accessing-remote-data"></a>存取遠端資料

許多新式 web 為基礎的解決方案進行的遠端用戶端應用程式提供功能的網頁伺服器所裝載的 web 服務使用。 Web 服務公開的作業會構成 web API。

用戶端應用程式應該要能夠利用 web API，而不需要知道如何實作資料或應用程式開發介面會公開的作業。 這需要應用程式開發介面遵守啟用同意使用和用戶端應用程式與 web 服務之間交換的資料結構的資料格式的用戶端應用程式與 web 服務的通用標準。

## <a name="introduction-to-representational-state-transfer"></a>具像狀態傳輸簡介

Representational State Transfer (REST) 是建置分散式的系統超媒體為基礎的架構樣式。 其他模型的主要優點是它已根據開放標準，並不會繫結模型或存取任何特定實作的用戶端應用程式的實作。 因此，無法使用 Microsoft ASP.NET Core MVC 中，實作 REST web 服務和用戶端應用程式無法使用任何語言和工具組，可以產生 HTTP 要求和剖析 HTTP 回應開發。

REST 模型使用巡覽配置表示透過網路，稱為資源的物件與服務。 實作 REST 通常系統會使用 HTTP 通訊協定傳輸存取這些資源的要求。 在這類系統中，用戶端應用程式會提交的要求中識別資源的 URI 和 HTTP 方法 （例如 GET、 POST、 PUT 或 DELETE），表示該資源上執行作業的形式。 HTTP 要求的主體會包含執行作業所需的任何資料。

> [!NOTE]
> REST 定義無狀態的要求模型。 因此，HTTP 要求必須是獨立，而且可能會以任何順序出現。

其他回應要求會使用標準 HTTP 狀態碼。 比方說，傳回有效的資料的要求應該包含 HTTP 回應碼 200 （確定），來尋找或刪除指定的資源失敗的要求應該傳回的回應包括 HTTP 狀態碼 404 （找不到）。

RESTful web 應用程式開發介面會公開一組連接的資源，並提供可讓應用程式管理這些資源，並輕鬆地巡覽其間的核心作業。 基於這個理由，是它所公開的資料，並使用提供的功能是透過 HTTP 來操作此資料導向構成一般 RESTful web API 的 Uri。

在 HTTP 要求，並從 web 伺服器，對應的回應訊息用戶端應用程式所包含的資料無法呈現各種不同的格式，稱為媒體類型。 當用戶端應用程式傳送訊息的本文中傳回資料的要求時，它可以指定的媒體類型，它可以處理在`Accept`要求標頭。 如果網頁伺服器支援此媒體類型，它可以回覆回應，其中包含`Content-Type`標頭，指定訊息的本文中的資料格式。 然後會剖析回應訊息，並適當地解譯訊息本文中的結果用戶端應用程式的責任。

如需其他詳細資訊，請參閱[API 設計](/azure/architecture/best-practices/api-design/)和[API 實作](/azure/architecture/best-practices/api-implementation/)。

## <a name="consuming-restful-apis"></a>使用 rest 式應用程式開發介面

EShopOnContainers 行動裝置應用程式會使用該模型-檢視-ViewModel (MVVM) 模式，而應用程式中使用之網域實體的模式代表的模型項目。 EShopOnContainers 參考應用程式的控制站和儲存機制類別接受並傳回多個模型物件。 因此，它們會使用為資料傳輸物件 (Dto) 的行動裝置應用程式與容器化的 microservices 之間傳遞的所有資料。 使用 DTOs 傳送資料，並從 web 服務接收資料的主要優點是由傳輸更多的資料，單一的遠端呼叫中，應用程式將可以減少需要進行的遠端呼叫的數目。

### <a name="making-web-requests"></a>提出 Web 要求

EShopOnContainers 行動裝置應用程式會使用`HttpClient`提出要求，透過 HTTP 使用 JSON 所做的媒體類型的類別。 這個類別會提供功能以非同步方式傳送 HTTP 要求和接收 HTTP 回應從 URI 所識別的資源。 `HttpResponseMessage`類別表示已經發出 HTTP 要求之後，從 REST API 接收 HTTP 回應訊息。 它包含回應，包括狀態碼、 標頭和任何內文的相關資訊。 `HttpContent`類別可代表 HTTP 內容和內容標頭，例如`Content-Type`和`Content-Encoding`。 可以使用任何讀取內容`ReadAs`方法，例如`ReadAsStringAsync`和`ReadAsByteArrayAsync`，端視資料的格式。

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>提出 GET 要求

`CatalogService`類別用來管理資料擷取程序從類別目錄的微服務。 在`RegisterDependencies`方法中的`ViewModelLocator`類別`CatalogService`類別登錄為依據的型別對應`ICatalogService`Autofac 相依性插入容器的型別。 然後，執行個體時`CatalogViewModel`類別，建立其建構函式接受`ICatalogService`輸入，其中 Autofac 解析，傳回的執行個體`CatalogService`類別。 如需相依性插入的詳細資訊，請參閱[簡介相依性插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

圖 10-1 顯示從顯示的類別目錄微服務讀取目錄資料的類別互動`CatalogView`。

[![](accessing-remote-data-images/catalogdata.png "從類別目錄的微服務擷取資料")](accessing-remote-data-images/catalogdata-large.png#lightbox "從類別目錄的微服務擷取資料")

**圖 10 1**： 從目錄微服務擷取資料

當`CatalogView`巡覽，`OnInitialize`方法中的`CatalogViewModel`類別稱為。 這個方法會從目錄微服務擷取目錄資料，如下列程式碼範例所示：

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

這個方法會呼叫`GetCatalogAsync`方法`CatalogService`已插入的執行個體`CatalogViewModel`由 Autofac。 下列程式碼範例示範`GetCatalogAsync`方法：

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

這個方法建置 URI 識別之資源的要求會傳送，並使用`RequestProvider`類別叫用的資源上的 GET HTTP 方法，傳回結果之前`CatalogViewModel`。 `RequestProvider`類別包含會提交要求，以識別資源時，表示要在該資源上執行作業的 HTTP 方法的 URI 形式的功能，而且執行作業所需的內文包含任何資料。 如需有關資訊`RequestProvider`類別插入`CatalogService class`，請參閱[簡介相依性插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

下列程式碼範例示範`GetAsync`方法中的`RequestProvider`類別：

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

這個方法會呼叫`CreateHttpClient`方法，傳回的執行個體`HttpClient`具有適當的標頭的集合類別。 然後提交非同步 GET 要求與回應儲存在由 URI 所識別的資源`HttpResponseMessage`執行個體。 `HandleResponse`然後叫用方法時，這會擲回例外狀況，如果回應不包含成功 HTTP 狀態碼。 然後回應讀取為字串時，從 JSON 轉換`CatalogRoot`物件，並傳回給`CatalogService`。

`CreateHttpClient`方法以下列程式碼範例所示：

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

這個方法建立的新執行個體`HttpClient`類別，並設定`Accept`所做的任何要求標頭`HttpClient`執行個體`application/json`，表示它需要的任何回應使用 JSON 格式化的內容。 接著，如果存取權杖傳遞的引數作為`CreateHttpClient`方法，它會加入至`Authorization`所做的任何要求標頭`HttpClient`執行個體，加上字串`Bearer`。 如需有關授權的詳細資訊，請參閱[授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

當`GetAsync`方法中的`RequestProvider`類別會呼叫`HttpClient.GetAsync`、`Items`方法中的`CatalogController`Catalog.API 專案中的類別叫用時，這下列程式碼範例所示：

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

這個方法擷取目錄資料從 SQL database 使用 EntityFramework，它做為回應訊息，其中包含成功 HTTP 狀態碼，並傳回集合的 JSON 格式化`CatalogItem`執行個體。

#### <a name="making-a-post-request"></a>發出 POST 要求

`BasketService`類別用來管理資料擷取，並以購物籃微服務更新程序。 在`RegisterDependencies`方法中的`ViewModelLocator`類別`BasketService`類別登錄為依據的型別對應`IBasketService`Autofac 相依性插入容器的型別。 然後，執行個體時`BasketViewModel`類別，建立其建構函式接受`IBasketService`輸入，其中 Autofac 解析，傳回的執行個體`BasketService `類別。 如需相依性插入的詳細資訊，請參閱[簡介相依性插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

圖 10-2 顯示的傳送所顯示的購物籃資料的類別互動`BasketView`，以購物籃微服務。

[![](accessing-remote-data-images/basketdata.png "將資料傳送至購物籃微服務")](accessing-remote-data-images/basketdata-large.png#lightbox "將資料傳送至購物籃微服務")

**圖 10 2**： 將資料傳送至購物籃微服務

當項目加入至購物籃，`ReCalculateTotalAsync`方法中的`BasketViewModel`類別呼叫。 這個方法會更新購物籃中的項目總計的值，並將購物籃資料傳送至購物籃微服務，如下列程式碼範例所示：

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

這個方法會呼叫`UpdateBasketAsync`方法`BasketService`已插入的執行個體`BasketViewModel`由 Autofac。 下列方法示範`UpdateBasketAsync`方法：

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

這個方法建置 URI 識別之資源的要求會傳送，並使用`RequestProvider`類別傳回的結果之前叫用 POST HTTP 方法，在資源上的`BasketViewModel`。 請注意，驗證程序期間 IdentityServer 取得存取權杖，才能授權籃微服務的要求。 如需有關授權的詳細資訊，請參閱[授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下列程式碼範例顯示的其中一個`PostAsync`方法`RequestProvider`類別：

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

這個方法會呼叫`CreateHttpClient`方法，傳回的執行個體`HttpClient`具有適當的標頭的集合類別。 然後提交 JSON 格式，並儲存在回應中傳送序列化的籃資料與由 URI 所識別的資源的非同步 POST 要求`HttpResponseMessage`執行個體。 `HandleResponse`然後叫用方法時，這會擲回例外狀況，如果回應不包含成功 HTTP 狀態碼。 然後，為字串時，從 JSON 轉換讀取回應`CustomerBasket`物件，並傳回給`BasketService`。 如需有關`CreateHttpClient`方法，請參閱[進行的 GET 要求](#making_a_get_request)。

當`PostAsync`方法中的`RequestProvider`類別會呼叫`HttpClient.PostAsync`、`Post`方法中的`BasketController`Basket.API 專案中的類別叫用時，這下列程式碼範例所示：

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

這個方法會使用的執行個體`RedisBasketRepository`類別至購物籃資料保存到 Redis 快取，並將其傳回為回應訊息，其中包含成功 HTTP 狀態碼和 JSON 格式化`CustomerBasket`執行個體。

#### <a name="making-a-delete-request"></a>提出刪除要求

圖 10-3 顯示的類別，可刪除購物籃資料從購物籃微服務的互動`CheckoutView`。

![](accessing-remote-data-images/checkoutdata.png "購物籃微服務拒資料")

**圖 10-3**： 購物籃微服務刪除資料

叫用在結帳程序時，`CheckoutAsync`方法中的`CheckoutViewModel`類別呼叫。 這個方法會建立新的訂單之前清除購物籃，如下列程式碼範例所示,：

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

這個方法會呼叫`ClearBasketAsync`方法`BasketService`已插入的執行個體`CheckoutViewModel`由 Autofac。 下列方法示範`ClearBasketAsync`方法：

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

這個方法建置 URI 識別之資源的要求會傳送至，並使用`RequestProvider`類別叫用 DELETE HTTP 方法，在資源上的。 請注意，驗證程序期間 IdentityServer 取得存取權杖，才能授權籃微服務的要求。 如需有關授權的詳細資訊，請參閱[授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下列程式碼範例示範`DeleteAsync`方法中的`RequestProvider`類別：

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

這個方法會呼叫`CreateHttpClient`方法，傳回的執行個體`HttpClient`具有適當的標頭的集合類別。 然後提交由 URI 所識別之資源的非同步 DELETE 要求。 如需有關`CreateHttpClient`方法，請參閱[進行的 GET 要求](#making_a_get_request)。

當`DeleteAsync`方法中的`RequestProvider`類別會呼叫`HttpClient.DeleteAsync`、`Delete`方法中的`BasketController`Basket.API 專案中的類別叫用時，這下列程式碼範例所示：

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

這個方法會使用的執行個體`RedisBasketRepository`籃資料刪除 Redis 快取的類別。

## <a name="caching-data"></a>快取資料

應用程式的效能來快取經常存取的資料來快速位於關閉的儲存體便可改善應用程式。 如果快速存放位於更接近應用程式，比原始來源，則快取可大幅改善回應逾時擷取資料。

最常見的快取的形式會貫穿快取，其中應用程式藉由參考快取中擷取資料。 如果資料不在快取，它已從資料存放區擷取，並新增至快取。 應用程式可以實作貫穿另行快取模式快取。 此模式會決定是否在項目處於目前快取。 如果項目不在快取中，已從資料存放區讀取並快取中加入。 如需詳細資訊，請參閱[另行快取](/azure/architecture/patterns/cache-aside/)模式。

> [!TIP]
> 經常讀取的且不常變更，就會快取資料。 此資料可以加入至要求第一次應用程式所擷取的快取。 這表示應用程式需要從資料存放區提取資料一次，而且後續存取都可藉由使用快取。

分散式應用程式，例如 eShopOnContainers 參考應用程式，應該提供一個或兩個下列快取：

-   共用快取中，可由多個處理程序或電腦存取。
-   私用快取中，資料在本機保留在執行應用程式的裝置。

EShopOnContainers 行動裝置應用程式會使用私用快取，資料在本機保留的裝置正在執行的應用程式執行個體。 如需 eShopOnContainers 參考應用程式所使用的快取資訊，請參閱[.NET Microservices： 容器化的.NET 應用程式的架構](https://aka.ms/microservicesebook)。

> [!TIP]
> 快取視為無法在任何時間消失的暫時性資料存放區。 請確定資料會保留在原始的資料存放區，以及快取。 如果快取變成無法使用然後最小化遺失資料的機會。

### <a name="managing-data-expiration"></a>管理資料到期

很實用的快取的資料一定會與原始的資料一致。 原始的資料存放區中的資料可能會變更後已快取後，造成快取的資料已過時。 因此，應用程式應該實作一種策略，可協助確保快取中的資料是盡可能最新，但可以也偵測和處理發生時快取中的資料已經過時的情況。 最快取機制啟用快取設定為過期的資料，並因此的期限減少，使其資料可能過期。

> [!TIP]
> 設定預設到期設定快取的時間。 許多快取實作，使資料無效，並從快取移除不在指定期間內存取的到期。 不過，必須小心選擇的逾期期限時。 如果它由太短，資料將會太快過期，而且會降低快取的好處。 如果它由太長，根據成為過時的資料風險。 因此，到期時間應該符合的應用程式將使用該資料的存取模式。

當快取的資料過期時，應該移除快取中，而應用程式必須擷取與原始資料的資料存放區，並將它放回快取。

此外，也可以快取可能會填滿，如果允許資料太長時間維持的狀態。 因此，可能需要將新項目新增至快取要求這道程序中移除一些項目*收回*。 快取服務通常會收回基礎少最近使用的資料。 不過，有其他的收回原則，包括大部分最近使用過的和先進先出。如需詳細資訊，請參閱[快取指引](/azure/architecture/best-practices/caching/)。

<a name="caching_images" />

### <a name="caching-images"></a>快取映像

EShopOnContainers 行動裝置應用程式會使用遠端產品映像可從快取中獲益。 這些映像會顯示[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)控制項，而`CachedImage`所提供的控制項[FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/)程式庫。

Xamarin.Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)控制項支援的下載映像快取。 快取會依預設，會啟用，並將儲存在本機的 24 小時的映像。 此外，設定到期時間，與[ `CacheValidity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CacheValidity/)屬性。 如需詳細資訊，請參閱[下載映像快取](~/xamarin-forms/user-interface/images.md#Image_Caching)。

FFImageLoading 的`CachedImage`控制項是 Xamarin.Forms 取代[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)控制項，提供啟用增補功能的其他屬性。 在這項功能，控制會提供可設定快取，同時支援錯誤，並載入影像的預留位置。 下列程式碼範例示範如何 eShopOnContainers 行動裝置應用程式使用`CachedImage`控制`ProductTemplate`，它是所使用的資料範本[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)控制`CatalogView`:

```xaml
<ffimageloading:CachedImage
    Grid.Row="0"
    Source="{Binding PictureUri}"     
    Aspect="AspectFill">
    <ffimageloading:CachedImage.LoadingPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="default_campaign" />
            <On Platform="UWP, WinRT, WinPhone" Value="Assets/default_campaign.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.LoadingPlaceholder>
    <ffimageloading:CachedImage.ErrorPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="noimage" />
            <On Platform="UWP, WinRT, WinPhone" Value="Assets/noimage.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.ErrorPlaceholder>
</ffimageloading:CachedImage>
```

`CachedImage`控制集`LoadingPlaceholder`和`ErrorPlaceholder`屬性，以特定平台映像。 `LoadingPlaceholder`屬性指定的影像時所指定的影像顯示`Source`擷取屬性，而`ErrorPlaceholder`屬性會指定要嘗試擷取映像時，發生錯誤時顯示影像所指定`Source`屬性。

正如其名，`CachedImage`控制會在裝置上的遠端影像快取的值所指定的時間`CacheDuration`屬性。 當這個屬性值未明確設定時，會套用預設值 30 天。

## <a name="increasing-resilience"></a>增加彈性

遠端服務與資源進行通訊的所有應用程式必須為機密的暫時性錯誤。 暫時性錯誤包括瞬間遺失網路連線到服務、 服務或服務正忙於時會遇到的等候逾時的暫時無法使用。 這些錯誤通常會自行修正，而且如果適合的延遲之後重複動作很可能會成功。

暫時性錯誤可以有巨大的衝擊，認知的應用程式的品質，即使它已徹底測試所有可預見的情況下。 若要確保與遠端服務通訊的應用程式能夠可靠地運作，必須能夠執行下列各項：

-   當使用者發生，並判斷是否可能是暫時性錯誤時，偵測到錯誤。
-   如果它決定在錯誤可能是暫時性的並且追蹤已重試此作業的次數，請再次嘗試操作。
-   使用適當的重試策略，其指定數目的重試、 嘗試失敗之後要採取的每一次嘗試，與動作之間的延遲。

此暫時性錯誤處理可藉由包裝所有嘗試存取遠端服務中實作重試模式的程式碼。

### <a name="retry-pattern"></a>重試一次模式

如果應用程式偵測到失敗，嘗試將要求傳送至遠端服務時，它可以下列方式處理失敗：

-   重試此作業。 應用程式無法立即重試失敗的要求。
-   重試此作業的延遲之後。 應用程式應等待在適合一段時間後再重試要求。
-   取消作業。 應用程式應該取消作業，並報告例外狀況。

重試策略應該調整以符合商務需求的應用程式。 例如，務必將最佳化重試計數以及重試間隔所嘗試的操作。 如果作業是使用者互動的一部分，重試間隔應該短和只有幾個重試嘗試避免進行使用者等待回應。 如果作業是長時間執行工作流程的一部分，其中取消或重新啟動工作流程是昂貴或費時，適合再次嘗試間等候與重試次數。

> [!NOTE]
> 嘗試與大量的重試之間的延遲最少的積極重試策略可能會降低執行接近或達到容量的遠端服務。 此外，重試策略可能也會影響應用程式的回應能力如果持續嘗試執行失敗的作業。

如果要求仍失敗後重試的數目，最好是應用程式以防止移至相同資源的進一步要求，以及報告失敗。 然後之後設定期間，, 應用程式可以進行一個或多個要求的資源，以查看其是否成功。 如需詳細資訊，請參閱[斷路器模式](#circuit_breaker_pattern)。

> [!TIP]
> 永遠不會實作無止盡的重試機制。 使用有限數目的重試，或實作[斷路器](/azure/architecture/patterns/circuit-breaker/)模式，以允許復原服務。

EShopOnContainers 行動裝置應用程式未目前實作重試模式進行 RESTful web 要求時。 不過，`CachedImage`所提供的控制項[FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/)程式庫支援映像載入重試的暫時性錯誤處理。 如果載入的映像失敗，將不進行進一步的嘗試。 所指定的嘗試次數`RetryCount`屬性，然後重試次數之後將會發生指定的延遲`RetryDelay`屬性。 如果這些屬性值未明確設定，其預設值會套用 – 3`RetryCount`屬性，並針對 250ms年`RetryDelay`屬性。 如需有關`CachedImage`控制，請參閱[快取映像](#caching_images)。

EShopOnContainers 參考應用程式確實有實作重試模式。 如需詳細資訊，包括如何結合以重試 」 模式的討論`HttpClient`類別，請參閱[.NET Microservices： 容器化的.NET 應用程式的架構](https://aka.ms/microservicesebook)。

如需重試 」 模式的詳細資訊，請參閱[重試](/azure/architecture/patterns/retry/)模式。

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>斷路器模式

在某些情況下，可能是因為預期的事件，花長時間來修正錯誤。 這些錯誤的範圍可從部分連線中斷服務的完整錯誤。 在這些情況下，它是無意義的應用程式重試不太可能成功，並改為應該接受作業失敗，並據以處理此失敗的作業。

斷路器模式可以防止應用程式的重複嘗試執行的作業可能會失敗，同時也可讓應用程式能夠偵測是否已解決的錯誤。

> [!NOTE]
> 斷路器模式的目的與不同的重試模式。 重試模式可讓應用程式的重試作業，就會成功且預期。 斷路器模式會防止應用程式執行的作業可能會失敗。

斷路器會做為 proxy 的作業，可能會失敗。 Proxy 應該監視最近發生的失敗數目，並使用此資訊來決定是否要允許此作業，若要繼續，或立即傳回例外狀況。

EShopOnContainers 行動裝置應用程式目前未實作斷路器模式。 不過，沒有 eShopOnContainers。 如需詳細資訊，請參閱[.NET Microservices： 容器化的.NET 應用程式的架構](https://aka.ms/microservicesebook)。

> [!TIP]
> 結合的重試和斷路器模式。 應用程式可以結合的重試和斷路器模式來叫用作業，斷路器透過使用重試模式。 不過，重試邏輯應該為機密斷路器所傳回的任何例外狀況，而且如果斷路器會指出錯誤並非暫時性放棄重試次數。

如需斷路器模式的詳細資訊，請參閱[斷路器](/azure/architecture/patterns/circuit-breaker/)模式。

## <a name="summary"></a>總結

許多新式 web 為基礎的解決方案進行的遠端用戶端應用程式提供功能的網頁伺服器所裝載的 web 服務使用。 Web 服務公開的作業組成的 web 應用程式開發介面，和用戶端應用程式應該要能夠利用 web API，而不需要知道如何實作資料或應用程式開發介面會公開的作業。

應用程式的效能來快取經常存取的資料來快速位於關閉的儲存體便可改善應用程式。 應用程式可以實作貫穿另行快取模式快取。 此模式會決定是否在項目處於目前快取。 如果項目不在快取中，已從資料存放區讀取並快取中加入。

當 web 應用程式開發介面與伺服器通訊，應用程式必須以區分暫時性錯誤。 暫時性錯誤包括瞬間遺失網路連線到服務、 服務或服務正忙於時會遇到的等候逾時的暫時無法使用。 這些錯誤通常會自行修正，以及如果適合的延遲之後重複動作時，則可能會成功。 因此，應用程式，應嘗試存取 web API 實作暫時性失敗處理機制的程式碼中所有換行。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
