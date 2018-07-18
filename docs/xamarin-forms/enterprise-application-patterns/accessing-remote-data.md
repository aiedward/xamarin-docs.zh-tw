---
title: 存取遠端資料
description: 本章將說明如何在 eShopOnContainers 的行動應用程式會存取資料的容器化微服務。
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 009a4025bc9df6f657964b7e97e559635ef0a929
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996161"
---
# <a name="accessing-remote-data"></a>存取遠端資料

許多現代化的 web 型解決方案進行的遠端用戶端應用程式提供功能的網頁伺服器所裝載的 web 服務使用。 Web 服務所公開的作業構成 web API。

用戶端應用程式應該能夠利用 web API，而不需要知道如何實作資料或 API 會公開的作業。 這需要 API 遵守常見的標準，讓用戶端應用程式和 web 服務，以便協議的資料格式，以使用和用戶端應用程式與 web 服務之間交換資料的結構。

## <a name="introduction-to-representational-state-transfer"></a>具像狀態傳輸的簡介

Representational State Transfer (REST) 是建置超媒體為基礎的分散式的系統的架構樣式。 REST 模型的主要優點是它已根據開放標準，並不會繫結模型或存取任何特定實作的用戶端應用程式的實作。 因此，無法使用 Microsoft ASP.NET Core MVC 中，實作 REST web 服務，而且無法使用任何語言和工具組，能夠產生 HTTP 要求及剖析 HTTP 回應進行用戶端應用程式的開發。

REST 模型會使用瀏覽配置來呈現物件和服務透過網路，稱為資源。 通常實作 REST 系統會使用 HTTP 通訊協定傳輸來存取這些資源的要求。 在這類系統中，用戶端應用程式會提交要求，以識別資源的 URI 和 HTTP 方法 （例如 GET、 POST、 PUT 或 DELETE），表示要對該資源作業的形式。 HTTP 要求的主體會包含執行作業所需的任何資料。

> [!NOTE]
> REST 能定義無狀態要求模式。 因此，HTTP 要求必須是獨立的而且可能會以任何順序出現。

從 REST 回應要求會使用標準 HTTP 狀態碼。 比方說，傳回有效資料的要求應該包含 HTTP 回應碼 200 （確定），而無法找到或刪除指定的資源要求應該傳回回應，其中包含 HTTP 狀態碼 404 （找不到）。

RESTful web API 會公開一組連接的資源，並提供啟用應用程式操作這些資源，並輕鬆地巡覽其間的核心作業。 基於這個理由，構成典型符合 rest 限制的 web API 的 Uri 會導向的資料，它會公開，並使用設備提供透過 HTTP 直接操作這些資料。

包含在 HTTP 要求和對應的回應訊息，從 web 伺服器，用戶端應用程式的資料可以顯示各種不同的格式，稱為 媒體類型。 當用戶端應用程式傳送的訊息主體中傳回資料的要求時，它可以指定的媒體類型，它可以處理在`Accept`要求標頭。 如果 web 伺服器支援此媒體類型，它可以回覆含回應，其中包含`Content-Type`標頭，訊息主體中指定的資料格式。 然後會剖析回應訊息，並適當地解譯訊息本文中的結果用戶端應用程式的責任。

如需有關 REST 的詳細資訊，請參閱[API 設計](/azure/architecture/best-practices/api-design/)並[API 實作](/azure/architecture/best-practices/api-implementation/)。

## <a name="consuming-restful-apis"></a>使用 RESTful Api

EShopOnContainers 的行動裝置應用程式會使用該 Model View ViewModel (MVVM) 模式，而應用程式中使用的領域實體模式代表的模型項目。 EShopOnContainers 參考應用程式的控制站和儲存機制類別會接受並傳回多個模型物件。 因此，它們會當做保留行動裝置應用程式與容器化微服務之間傳遞的所有資料的資料傳輸物件 (Dto)。 若要將資料傳遞至和接收 web 服務中的資料使用 Dto 的主要優點是藉由傳輸更多的資料，在單一遠端呼叫中，應用程式將可以減少所需的遠端呼叫的數目。

### <a name="making-web-requests"></a>提出 Web 要求

EShopOnContainers 的行動裝置應用程式會使用`HttpClient`over HTTP，所做的媒體類型的 JSON 時提出要求的類別。 這個類別提供功能以非同步方式傳送 HTTP 要求和接收 HTTP 回應從 URI 所識別的資源。 `HttpResponseMessage`類別代表進行 HTTP 要求後，從 REST API 收到的 HTTP 回應訊息。 它包含回應，包括狀態碼、 標頭，以及任何內文的相關資訊。 `HttpContent`類別可表示的 HTTP 內容和內容標頭，例如`Content-Type`和`Content-Encoding`。 可以讀取內容，使用任一`ReadAs`方法，例如`ReadAsStringAsync`和`ReadAsByteArrayAsync`，取決於資料的格式。

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>提出 GET 要求

`CatalogService`類別用來從目錄微服務中管理的資料擷取程序。 在`RegisterDependencies`方法中的`ViewModelLocator`類別`CatalogService`類別登錄為依據的型別對應`ICatalogService`Autofac 相依性插入容器的型別。 然後，執行個體時`CatalogViewModel`建立類別時，其建構函式接受`ICatalogService`輸入，其中 Autofac 解析時，傳回的執行個體`CatalogService`類別。 如需相依性插入的詳細資訊，請參閱[相依性插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

圖 10-1 顯示的 讀取目錄微服務所顯示的目錄資料的類別互動`CatalogView`。

[![](accessing-remote-data-images/catalogdata.png "擷取目錄微服務中的資料")](accessing-remote-data-images/catalogdata-large.png#lightbox "擷取目錄微服務中的資料")

**圖 10-1**： 擷取目錄微服務中的資料

當`CatalogView`，巡覽`OnInitialize`方法中的`CatalogViewModel`類別稱為。 這個方法會從目錄微服務中，擷取目錄資料，如下列程式碼範例所示：

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

這個方法建置識別此要求會傳送，該資源的 URI，並使用`RequestProvider`類別來叫用 GET HTTP 方法上的資源，再將結果`CatalogViewModel`。 `RequestProvider`類別包含會提交要求，以識別資源時，表示要對該資源，作業的 HTTP 方法的 URI 形式的功能，而且包含的所有資料的主體，才能執行作業。 如需有關的資訊`RequestProvider`類別不會插入`CatalogService class`，請參閱[相依性插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

下列程式碼範例所示`GetAsync`方法中的`RequestProvider`類別：

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

這個方法會呼叫`CreateHttpClient`方法，以傳回的執行個體`HttpClient`類別以適當的標頭集合。 然後提交非同步的 GET 要求與回應儲存在 URI 所識別的資源`HttpResponseMessage`執行個體。 `HandleResponse`然後叫用方法時，它會擲回例外狀況，如果回應沒有包含成功的 HTTP 狀態碼。 然後回應讀取為字串時，從 JSON 轉換`CatalogRoot`物件，並傳回給`CatalogService`。

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

這個方法會建立的新執行個體`HttpClient`類別，並設定`Accept`所做的任何要求標頭`HttpClient`執行個體`application/json`，這表示，它會預期使用 JSON 格式化任何回應的內容。 然後，如果做為引數傳遞存取權杖`CreateHttpClient`方法，它會加入至`Authorization`所做的任何要求標頭`HttpClient`執行個體，加上字串`Bearer`。 如需有關授權的詳細資訊，請參閱 <<c0> [ 授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

當`GetAsync`方法中的`RequestProvider`類別會呼叫`HttpClient.GetAsync`，則`Items`方法中的`CatalogController`Catalog.API 專案中的類別會叫用，這下列程式碼範例所示：

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

此方法會從 SQL 資料庫使用 EntityFramework，擷取目錄資料，並傳回為回應訊息，其中包含成功的 HTTP 狀態碼，和集合的 JSON 格式化`CatalogItem`執行個體。

#### <a name="making-a-post-request"></a>發出 POST 要求

`BasketService`類別用來管理資料擷取，並使用購物籃微服務中更新程序。 在`RegisterDependencies`方法中的`ViewModelLocator`類別`BasketService`類別登錄為依據的型別對應`IBasketService`Autofac 相依性插入容器的型別。 然後，執行個體時`BasketViewModel`建立類別時，其建構函式接受`IBasketService`輸入，其中 Autofac 解析時，傳回的執行個體`BasketService `類別。 如需相依性插入的詳細資訊，請參閱[相依性插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

圖 10-2 顯示互動的類別，可將所顯示的購物籃資料傳送`BasketView`，購物籃微服務。

[![](accessing-remote-data-images/basketdata.png "將資料傳送至購物籃微服務")](accessing-remote-data-images/basketdata-large.png#lightbox "將資料傳送至購物籃微服務")

**圖 10-2**： 將資料傳送至購物籃微服務

項目新增至購物籃時,`ReCalculateTotalAsync`方法中的`BasketViewModel`類別稱為。 這個方法會更新購物籃中的項目總計的值，並將購物籃資料傳送至購物籃微服務，如下列程式碼範例所示：

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

這個方法建置識別此要求會傳送，該資源的 URI，並使用`RequestProvider`類別以叫用傳回的結果前的 POST HTTP 方法，在資源`BasketViewModel`。 請注意，在驗證過程中，取得從 IdentityServer 的存取權杖，才能授權購物籃微服務的要求。 如需有關授權的詳細資訊，請參閱 <<c0> [ 授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下列程式碼範例顯示其中一種`PostAsync`中的方法`RequestProvider`類別：

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

這個方法會呼叫`CreateHttpClient`方法，以傳回的執行個體`HttpClient`類別以適當的標頭集合。 然後提交非同步 POST 要求，以 JSON 格式和儲存在回應中傳送序列化的購物籃資料 URI 所識別的資源`HttpResponseMessage`執行個體。 `HandleResponse`然後叫用方法時，它會擲回例外狀況，如果回應沒有包含成功的 HTTP 狀態碼。 然後，為字串時，從 JSON 轉換，讀取的回應`CustomerBasket`物件，並傳回給`BasketService`。 如需詳細資訊`CreateHttpClient`方法，請參閱 <<c2> [ 提出 GET 要求](#making_a_get_request)。

當`PostAsync`方法中的`RequestProvider`類別會呼叫`HttpClient.PostAsync`，則`Post`方法中的`BasketController`Basket.API 專案中的類別會叫用，這下列程式碼範例所示：

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

這個方法會使用的執行個體`RedisBasketRepository`類別將購物籃資料保存到 Redis 快取，並將它傳回為回應訊息，其中包含成功 HTTP 狀態碼和 JSON 格式化`CustomerBasket`執行個體。

#### <a name="making-a-delete-request"></a>提出刪除要求

圖 10-3 顯示的類別，可刪除購物籃微服務中，從購物籃資料互動`CheckoutView`。

![](accessing-remote-data-images/checkoutdata.png "正在刪除資料，從購物籃微服務")

**圖 10-3**： 刪除購物籃微服務中的資料

叫用簽出程序時，`CheckoutAsync`方法中的`CheckoutViewModel`類別稱為。 這個方法會建立新的訂單之前清除購物籃，如下列程式碼範例所示,：

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

這個方法建置 URI 識別之資源的要求會傳送至，並使用`RequestProvider`叫用 DELETE HTTP 方法，在資源上的類別。 請注意，在驗證過程中，取得從 IdentityServer 的存取權杖，才能授權購物籃微服務的要求。 如需有關授權的詳細資訊，請參閱 <<c0> [ 授權](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下列程式碼範例所示`DeleteAsync`方法中的`RequestProvider`類別：

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

這個方法會呼叫`CreateHttpClient`方法，以傳回的執行個體`HttpClient`類別以適當的標頭集合。 然後送出 URI 所識別之資源的非同步刪除要求。 如需詳細資訊`CreateHttpClient`方法，請參閱 <<c2> [ 提出 GET 要求](#making_a_get_request)。

當`DeleteAsync`方法中的`RequestProvider`類別會呼叫`HttpClient.DeleteAsync`，則`Delete`方法中的`BasketController`Basket.API 專案中的類別會叫用，這下列程式碼範例所示：

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

這個方法會使用的執行個體`RedisBasketRepository`類別以從 Redis 快取刪除購物籃資料。

## <a name="caching-data"></a>快取資料

可以改善應用程式的效能，藉由快取經常存取的資料位於接近的快速儲存體應用程式。 如果快速的儲存體的位置更接近應用程式比原始來源，則快取可大幅改善回應時間擷取資料時。

最常見的快取形式是貫穿式讀取快取，其中應用程式參考快取中擷取資料。 如果資料不在快取，它已從資料存放區擷取，並新增至快取。 應用程式可以實作貫穿式讀取快取使用另行快取模式。 此模式會決定項目是否目前在快取。 如果項目不在快取中，已從資料存放區讀取並快取中加入。 如需詳細資訊，請參閱 <<c0> [ 另行快取](/azure/architecture/patterns/cache-aside/)模式。

> [!TIP]
> 快取經常讀取及不常變更的資料。 這項資料可以新增至應用程式擷取隨選第一次的快取。 這表示應用程式需要從資料存放區擷取資料一次，而且可以使用快取滿足後續存取。

分散式應用程式，例如 eShopOnContainers 參考應用程式，應該提供一個或兩個下列快取：

-   共用快取，可以由多個處理序或電腦存取。
-   私用快取，其中資料會保留在執行應用程式的裝置的本機。

EShopOnContainers 的行動裝置應用程式會使用私人快取，其中資料會保留在裝置執行的應用程式執行個體的本機。 如需使用 eShopOnContainers 參考應用程式的快取資訊，請參閱[.NET 微服務： 容器化.NET 應用程式架構](https://aka.ms/microservicesebook)。

> [!TIP]
> 快取視為可能會隨時消失的暫時性資料存放區。 請確定資料會保留在原始資料存放區，以及快取。 快取變成無法使用時，遺失資料的機會會接著會降到最低。

### <a name="managing-data-expiration"></a>管理資料到期

我們不希望快取的資料一律會與原始資料一致。 之後就會被快取，導致快取的資料變成過時，可能會變更原始資料存放區中的資料。 因此，應用程式應該實作策略，可協助確保快取中的資料是盡可能保持最新，但可以也偵測並處理當快取中的資料變成過時的情況。 啟用快取設定為過期資料，最快取機制，並因此減少為其資料可能過期的期間。

> [!TIP]
> 設定預設的到期時間設定快取。 許多快取實作期限，讓資料無效，並移除快取中，如果它不會在指定期間內存取。 不過，必須小心選擇的逾期期限時。 如果它太短，資料會太快過期，而且會降低快取的優勢。 如果它由太長，而變成過時資料的風險。 因此，到期時間應該符合使用資料的應用程式的存取的模式。

當快取的資料過期時，應該移除快取，而應用程式必須擷取原始資料的資料存放區，並將它放回快取。

此外，也可以快取可能會填滿，如果允許資料太長時間維持的狀態。 因此，可能需要將新的項目新增至快取的要求這道程序中移除一些項目*收回*。 快取服務通常會收回基礎少最近使用的資料。 不過，有其他的收回原則，包括大部分最近使用過的和後進先出。如需詳細資訊，請參閱 <<c0> [ 快取指導方針](/azure/architecture/best-practices/caching/)。

<a name="caching_images" />

### <a name="caching-images"></a>快取映像

EShopOnContainers 的行動裝置應用程式會使用遠端的產品映像快取中獲益。 這些映像會顯示[ `Image` ](xref:Xamarin.Forms.Image)控制項，而`CachedImage`所提供的控制項[FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/)程式庫。

Xamarin.Forms [ `Image` ](xref:Xamarin.Forms.Image)控制項支援快取的已下載的映像。 快取會依預設，會啟用，並將儲存在本機的 24 小時內的映像。 此外，可以使用設定的到期時間[ `CacheValidity` ](xref:Xamarin.Forms.UriImageSource.CacheValidity)屬性。 如需詳細資訊，請參閱 <<c0> [ 下載映像快取](~/xamarin-forms/user-interface/images.md#Image_Caching)。

FFImageLoading`CachedImage`控制項是用來取代 Xamarin.Forms [ `Image` ](xref:Xamarin.Forms.Image)控制項，提供額外的屬性，可讓增補功能。 在這項功能，之間控制項提供可設定快取，同時支援時發生錯誤，並載入影像的預留位置。 下列程式碼範例示範 eShopOnContainers 的行動裝置應用程式的使用方式`CachedImage`在中控制可`ProductTemplate`，這是所使用的資料範本[ `ListView` ](xref:Xamarin.Forms.ListView)控制在`CatalogView`:

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

`CachedImage`控制集`LoadingPlaceholder`和`ErrorPlaceholder`屬性，以特定平台映像。 `LoadingPlaceholder`屬性會指定所指定的影像時要顯示的映像`Source`已擷取屬性，而`ErrorPlaceholder`屬性指定當您嘗試擷取的映像時，發生錯誤時要顯示的映像所指定`Source`屬性。

如同名稱所暗示`CachedImage`的值所指定的時間控制項快取的遠端裝置上的映像`CacheDuration`屬性。 當未明確設定此屬性值時，則會套用預設值 30 天。

## <a name="increasing-resilience"></a>增加彈性

與遠端服務和資源進行通訊的所有應用程式必須能感應暫時性錯誤。 暫時性錯誤包括瞬間失去網路連線到服務、 服務或服務忙碌時所發生的逾時的暫時無法使用。 這些錯誤通常會自行修正，而且如果適當的延遲後再重複此動作可能會成功。

暫時性錯誤可以察覺到品質的應用程式，有很大的影響，即使它已經過徹底測試所有可預見的情況下。 若要確保與遠端服務進行通訊的應用程式可靠地運作，它必須能夠執行下列各項：

-   偵測錯誤發生，並判斷是否可能是暫時性的錯誤。
-   如果經判定錯誤很可能是暫時性的追蹤的作業重試的次數，請重試此作業。
-   使用適當的重試策略，其指定數目的重試次數、 每次嘗試，以及嘗試失敗之後要採取的動作之間的延遲。

藉由包裝所有嘗試存取遠端服務中實作重試模式的程式碼可藉此暫時性錯誤處理。

### <a name="retry-pattern"></a>重試模式

如果嘗試將要求傳送至遠端服務時，應用程式偵測到失敗，它可以在下列任一方式來處理失敗：

-   重試此作業。 應用程式無法立即重試失敗的要求。
-   此作業的延遲之後重試一次。 應用程式應該等待在適合的一段時間再重試要求。
-   取消作業。 應用程式應該取消作業，並報告例外狀況。

重試策略應該加以調整以符合商務需求的應用程式。 比方說，務必最佳化重試計數以及重試間隔所嘗試的操作。 如果作業是使用者互動的一部分，重試間隔應該短，且只有少數重試嘗試以避免讓使用者等待回應。 如果作業是在長時間執行的工作流程，其中取消或重新啟動工作流程是昂貴或耗時，很適當，再嘗試之間等候與重試一次。

> [!NOTE]
> 與意圖，而大量的重試之間的延遲最少的積極重試策略可能會降低執行接近或達到產能的遠端服務。 此外，這類的重試策略也可能會影響應用程式的回應能力如果持續嘗試執行失敗的作業。

如果要求仍失敗重試次數之後，最好是應用程式來防止進一步要求，前往相同的資源，並報告失敗。 然後，在設定期間之後, 應用程式可以進行一或多個要求的資源，以查看它們是否成功。 如需詳細資訊，請參閱 <<c0> [ 斷路器模式](#circuit_breaker_pattern)。

> [!TIP]
> 永遠不要實作無盡的重試機制。 使用有限數目的重試，或實作[斷路器](/azure/architecture/patterns/circuit-breaker/)模式，可允許服務復原。

EShopOnContainers 的行動裝置應用程式目前不會實作重試模式進行 RESTful web 要求。 不過，`CachedImage`所提供的控制項[FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/)程式庫支援暫時性錯誤處理重試映像載入。 如果載入的映像失敗，將不進行進一步的嘗試。 所指定的次數`RetryCount`屬性，然後重試會在指定的延遲之後`RetryDelay`屬性。 如果這些屬性值未明確設定，其預設值便會套用 – 3`RetryCount`屬性，並為 250 毫秒`RetryDelay`屬性。 如需詳細資訊`CachedImage`控制項，請參閱[快取映像](#caching_images)。

EShopOnContainers 參考應用程式會實作重試模式。 如需詳細資訊，包括如何結合使用重試模式的討論`HttpClient`類別，請參閱[.NET 微服務： 容器化.NET 應用程式架構](https://aka.ms/microservicesebook)。

如需有關重試模式的詳細資訊，請參閱 <<c0> [ 重試](/azure/architecture/patterns/retry/)模式。

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>斷路器模式

在某些情況下，可能是因為預期的事件所花費時間修正錯誤。 這些錯誤的範圍可以從失去部分連線到服務完全失敗。 在這些情況下，就毫無意義的應用程式，以重試不太可能成功，並改為應該接受作業失敗，並據以處理此失敗的作業。

斷路器模式可以防止應用程式重複嘗試執行可能失敗，同時也可讓應用程式，以偵測是否已解決錯誤的作業。

> [!NOTE]
> 斷路器模式的目的與重試模式不同。 重試模式可讓應用程式以重試作業，就會成功的期望。 斷路器模式會防止應用程式執行可能失敗的作業。

斷路器做為 proxy 的作業，可能會失敗。 Proxy 應監視最近發生的失敗數目，並使用此資訊來決定是否要讓此作業，若要繼續，或立即傳回例外狀況。

EShopOnContainers 的行動裝置應用程式目前不會實作斷路器模式。 不過，eShopOnContainers 會。 如需詳細資訊，請參閱 < [.NET 微服務： 容器化.NET 應用程式架構](https://aka.ms/microservicesebook)。

> [!TIP]
> 結合的重試和斷路器模式。 應用程式可以使用重試模式叫用透過斷路器的作業來結合的重試和斷路器模式。 不過，重試邏輯應該很容易受斷路器所傳回的任何例外狀況，而且如果斷路器指出錯誤並非暫時性，放棄重試。

如需斷路器模式的詳細資訊，請參閱[斷路器](/azure/architecture/patterns/circuit-breaker/)模式。

## <a name="summary"></a>總結

許多現代化的 web 型解決方案進行的遠端用戶端應用程式提供功能的網頁伺服器所裝載的 web 服務使用。 Web 服務所公開的作業構成 web API，且用戶端應用程式應該要能夠利用 web API，而不需要知道如何實作資料或 API 會公開的作業。

可以改善應用程式的效能，藉由快取經常存取的資料位於接近的快速儲存體應用程式。 應用程式可以實作貫穿式讀取快取使用另行快取模式。 此模式會決定項目是否目前在快取。 如果項目不在快取中，已從資料存放區讀取並快取中加入。

與 web Api 通訊時，應用程式必須能感應暫時性錯誤。 暫時性錯誤包括瞬間失去網路連線到服務、 服務或服務忙碌時所發生的逾時的暫時無法使用。 這些錯誤通常會自行修正，以及如果適當的延遲後再重複此動作，它就可能會成功。 因此，應用程式應該包裝所有嘗試存取 web API 實作暫時性錯誤處理機制的程式碼中。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
