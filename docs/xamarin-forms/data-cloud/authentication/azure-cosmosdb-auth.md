---
title: 使用 Azure Cosmos DB 文件資料庫和 Xamarin.Forms 進行使用者驗證
description: 這篇文章說明如何結合 Azure Cosmos DB 進行資料分割集合的存取控制，讓使用者只能存取自己的文件中的 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 6e55b3b9b0f204992de684ba09f3d9ff2552ce00
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832354"
---
# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>使用 Azure Cosmos DB 文件資料庫和 Xamarin.Forms 進行使用者驗證

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDBAuth/)

_Azure Cosmos DB 文件資料庫支援資料分割的集合，可跨越多部伺服器和資料分割，同時支援無限制的儲存體和輸送量。這篇文章說明如何結合資料分割的集合，存取控制，讓使用者只能存取自己的文件中的 Xamarin.Forms 應用程式。_

## <a name="overview"></a>總覽

建立資料分割的集合時，就必須指定資料分割索引鍵，並具有相同的資料分割索引鍵的文件會儲存在相同的資料分割。 因此，指定使用者的身分識別做為資料分割索引鍵會導致資料分割的集合，只會儲存該使用者的文件。 這也可確保 Azure Cosmos DB 文件資料庫會隨著使用者數目，和增加的項目。

必須授與至任何集合，存取和 SQL API 的存取控制模型會定義兩種類型的存取建構：

- **主要金鑰**啟用完整的系統管理存取 Cosmos DB 帳戶內的所有資源，並建立 Cosmos DB 帳戶時建立。
- **資源權杖**擷取資料庫使用者與使用者所具備之特定 Cosmos DB 資源，例如集合或文件的權限之間的關聯性。

公開主要金鑰，便會開啟惡意或粗心使用的可能性的 Cosmos DB 帳戶。 不過，Azure Cosmos DB 資源權杖提供的安全機制，允許讀取、 寫入和刪除 Azure Cosmos DB 帳戶，根據授與的權限中的特定資源的用戶端。

要求的典型方法，產生，並將資源權杖傳遞給行動應用程式是使用資源權杖訊息代理程式。 下圖顯示範例應用程式如何使用資源權杖訊息代理程式來管理文件資料庫資料的存取權的高階概觀：

![](azure-cosmosdb-auth-images/documentdb-authentication.png "文件資料庫驗證程序")

資源權杖訊息代理程式是裝載在 Azure App Service 中擁有 Cosmos DB 帳戶的主要金鑰的中介層 Web API 服務。 範例應用程式會使用資源權杖訊息代理程式管理的文件資料庫資料的存取，如下所示：

1. 在登入時，Xamarin.Forms 應用程式，請連絡 Azure App Service，以起始驗證流程。
1. Azure App Service 會執行與 Facebook OAuth 驗證流程。 驗證流程完成後，Xamarin.Forms 應用程式就會收到一個存取權杖。
1. Xamarin.Forms 應用程式會使用存取權杖，向資源權杖訊息代理程式要求資源權杖。
1. 資源權杖訊息代理程式會使用存取權杖來向 Facebook 要求使用者的身分識別。 使用者的身分識別則用來要求資源權杖從 Cosmos DB，用來授與讀取/寫入存取權已驗證的使用者資料分割的集合中。
1. Xamarin.Forms 應用程式會使用資源權杖，利用資源權杖所定義的權限直接存取 Cosmos DB 資源。

> [!NOTE]
> 資源權杖過期時，後續的文件資料庫要求會收到 401 未經授權的例外狀況。 此時，Xamarin.Forms 應用程式應該重新建立身分識別，並要求新的資源權杖。

如需有關 Cosmos DB 資料分割的詳細資訊，請參閱 <<c0> [ 如何以資料分割和調整 Azure Cosmos DB 中的](/azure/cosmos-db/partition-data/)。 如需有關 Cosmos DB 的存取控制的詳細資訊，請參閱[安全存取 Cosmos DB 資料](/azure/cosmos-db/secure-access-to-data/)並[SQL API 中的存取控制](/rest/api/documentdb/access-control-on-documentdb-resources/)。

## <a name="setup"></a>安裝程式

整合 Xamarin.Forms 應用程式中的資源權杖訊息代理程式的程序如下所示：

1. 建立會使用存取控制的 Cosmos DB 帳戶。 如需詳細資訊，請參閱 < [Cosmos DB 設定](#cosmosdb_configuration)。
1. 建立 Azure App Service 來裝載資源權杖訊息代理程式。 如需詳細資訊，請參閱 < [Azure App Service 組態](#app_service_configuration)。
1. 建立 Facebook 應用程式來執行驗證。 如需詳細資訊，請參閱 < [Facebook 應用程式設定](#facebook_configuration)。
1. 設定 Azure App Service 來執行簡單的驗證與 Facebook。 如需詳細資訊，請參閱 < [Azure App Service 驗證組態](#app_service_authentication_configuration)。
1. Xamarin.Forms 範例應用程式與 Azure App Service 和 Cosmos DB 通訊設定。 如需詳細資訊，請參閱 < [Xamarin.Forms 應用程式組態](#forms_application_configuration)。

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Azure Cosmos DB 設定

建立會使用存取控制的 Cosmos DB 帳戶的程序如下所示：

1. 建立 Cosmos DB 帳戶。 如需詳細資訊，請參閱 <<c0> [ 建立 Azure Cosmos DB 帳戶](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account)。
1. 在 Cosmos DB 帳戶中，建立名為的新集合`UserItems`，指定資料分割索引鍵的`/userid`。

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Azure App Service 設定

裝載資源權杖訊息代理程式在 Azure App Service 中的程序如下所示：

1. 在 Azure 入口網站中，建立新的 App Service web 應用程式。 如需詳細資訊，請參閱 < [App Service Environment 中建立 web 應用程式](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/)。
1. 在 Azure 入口網站中，開啟 web 應用程式的應用程式設定刀鋒視窗並新增下列設定：
    - `accountUrl` – 此值應該是從 Cosmos DB 帳戶的 [金鑰] 刀鋒視窗的 Cosmos DB 帳戶 URL。
    - `accountKey` – 此值應該是 Cosmos DB 主要金鑰 （主要或次要） 從 Cosmos DB 帳戶的 [金鑰] 刀鋒視窗。
    - `databaseId` – 此值應該是 Cosmos DB 資料庫的名稱。
    - `collectionId` – 此值應該是 Cosmos DB 集合的名稱 (在此情況下， `UserItems`)。
    - `hostUrl` – 此值必須從應用程式服務帳戶的 [概觀] 刀鋒視窗的 web 應用程式的 URL。

    下列螢幕擷取畫面示範這項設定：

    [![](azure-cosmosdb-auth-images/azure-web-app-settings.png "App Service Web 應用程式設定")](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "App Service Web 應用程式設定")

1. 將資源權杖訊息代理程式解決方案發佈至 Azure App Service web 應用程式。

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Facebook 應用程式設定

建立 Facebook 應用程式來執行驗證的程序如下所示：

1. 建立 Facebook 應用程式。 如需詳細資訊，請參閱 <<c0> [ 註冊，並將應用程式設定](https://developers.facebook.com/docs/apps/register)Facebook 開發人員中心。
1. 應用程式中新增 Facebook 登入產品。 如需詳細資訊，請參閱 <<c0> [ 新增至您的應用程式或網站的 Facebook 登入](https://developers.facebook.com/docs/facebook-login)Facebook 開發人員中心。
1. Facebook 登入的設定，如下所示：
   - 啟用用戶端 OAuth 登入。
   - 啟用 Web OAuth 登入。
   - 使用設定的有效的 OAuth 重新導向 URI 的 App Service web 應用程式中，URI`/.auth/login/facebook/callback`附加。

  下列螢幕擷取畫面示範這項設定：

  ![](azure-cosmosdb-auth-images/facebook-oauth-settings.png "Facebook 登入的 OAuth 設定")

如需詳細資訊，請參閱 <<c0> [ 向 Facebook 註冊您的應用程式](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook)。

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Azure App Service 的驗證設定

設定 App Service 輕鬆驗證程序如下所示：

1. 在 Azure 入口網站中，瀏覽至 App Service web 應用程式。
1. 在 Azure 入口網站中，開啟 驗證 / 授權刀鋒視窗並執行下列設定：
    - 應該開啟 app Service 驗證。
    - 當要求未經驗證時要採取的動作應該設定為**使用 Facebook 登入**。

    下列螢幕擷取畫面示範這項設定：

    [![](azure-cosmosdb-auth-images/app-service-authentication-settings.png "App Service Web 應用程式驗證設定")](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "App Service Web 應用程式驗證設定")

App Service web 應用程式也應該設定為與 Facebook 應用程式，若要啟用的驗證流程通訊。 這可藉由選取 Facebook 身分識別提供者，並輸入**應用程式識別碼**並**應用程式祕密**從 Facebook 開發人員中心的 Facebook 應用程式設定的值。 如需詳細資訊，請參閱 <<c0> [ 您的應用程式將 Facebook 資訊](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application)。

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Xamarin.Forms 應用程式組態

設定的 Xamarin.Forms 範例應用程式的程序如下所示：

1. 開啟 Xamarin.Forms 方案。
1. 開啟`Constants.cs`並更新下列常數的值：
    - `EndpointUri` – 此值應該是從 Cosmos DB 帳戶的 [金鑰] 刀鋒視窗的 Cosmos DB 帳戶 URL。
    - `DatabaseName` – 此值應該是文件資料庫的名稱。
    - `CollectionName` – 此值應該是文件資料庫集合的名稱 (在此情況下， `UserItems`)。
    - `ResourceTokenBrokerUrl` – 此值必須從應用程式服務帳戶的 [概觀] 刀鋒視窗的資源權杖訊息代理程式 web 應用程式的 URL。

## <a name="initiating-login"></a>起始的登入

範例應用程式起始登入程序使用 Xamarin.Auth 瀏覽器重新導向至識別提供者的 URL，如下列範例程式碼所示：

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

這會導致 Azure App Service 與 Facebook，它會顯示 Facebook 登入頁面之間起始 OAuth 驗證流程：

![](azure-cosmosdb-auth-images/login.png "Facebook 登入")

會取消登入，請按下**取消**按鈕，在 iOS 上，或按下**回**按鈕在 Android 上，在此情況下，使用者仍未驗證和身分識別提供者使用者介面從螢幕中移除。

如需 Xamarin.Auth 的詳細資訊，請參閱[驗證使用者的身分識別提供者](~/xamarin-forms/data-cloud/authentication/oauth.md)。

## <a name="obtaining-a-resource-token"></a>取得為資源語彙基元

成功驗證之後，`WebRedirectAuthenticator.Completed`引發事件。 下列程式碼範例示範處理這個事件：

```csharp
auth.Completed += async (sender, e) =>
{
  if (e.IsAuthenticated && e.Account.Properties.ContainsKey("token"))
  {
    var easyAuthResponseJson = JsonConvert.DeserializeObject<JObject>(e.Account.Properties["token"]);
    var easyAuthToken = easyAuthResponseJson.GetValue("authenticationToken").ToString();

    // Call the ResourceBroker to get the resource token
    using (var httpClient = new HttpClient())
    {
      httpClient.DefaultRequestHeaders.Add("x-zumo-auth", easyAuthToken);
      var response = await httpClient.GetAsync(Constants.ResourceTokenBrokerUrl + "/api/resourcetoken/");
      var jsonString = await response.Content.ReadAsStringAsync();
      var tokenJson = JsonConvert.DeserializeObject<JObject>(jsonString);
      resourceToken = tokenJson.GetValue("token").ToString();
      UserId = tokenJson.GetValue("userid").ToString();

      if (!string.IsNullOrWhiteSpace(resourceToken))
      {
        client = new DocumentClient(new Uri(Constants.EndpointUri), resourceToken);
        ...
      }
      ...
    }
  }
};
```

在成功驗證的結果是存取權杖，也就是可用`AuthenticatorCompletedEventArgs.Account`屬性。 擷取存取權杖並使用資源權杖訊息代理程式的 GET 要求中`resourcetoken`API。

`resourcetoken` API 會使用存取權杖，向 Facebook，接著用來從 Cosmos DB 要求資源權杖要求使用者的身分識別。 如果使用者在文件資料庫中已經有有效的權限的文件，擷取，並包含在資源權杖的 JSON 文件傳回至 Xamarin.Forms 應用程式。 如果使用者沒有有效的權限的文件，文件資料庫中建立的使用者和權限和資源權杖會擷取從權限的文件，並傳回至 Xamarin.Forms 應用程式中的 JSON 文件。

> [!NOTE]
> 文件資料庫使用者是使用文件資料庫時，相關聯的資源，以及每個資料庫可能包含零個或多個使用者。 文件資料庫權限是與文件資料庫使用者相關聯的資源，以及每位使用者可能包含零個或多個權限。 權限資源會提供存取權的使用者在嘗試存取的資源，例如文件時所需要的安全性權杖。

如果`resourcetoken`API 已成功完成後，它會傳送 HTTP 狀態碼 200 （確定） 回應，以及包含在資源權杖的 JSON 文件。 下列 JSON 資料會顯示一般的成功回應訊息：

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

`WebRedirectAuthenticator.Completed`事件處理常式讀取回應`resourcetoken`API，並擷取資源語彙基元和使用者識別碼。資源權杖接著會傳遞做為引數`DocumentClient`建構函式，其中封裝端點、 認證和用來存取 Cosmos DB 的連線原則，而且用來設定和執行對 Cosmos DB 的要求。 資源權杖來直接存取的資源，每個要求傳送，並指出會授與讀取/寫入存取權已驗證的使用者資料分割的集合。

## <a name="retrieving-documents"></a>擷取文件

擷取文件只隸屬於已驗證的使用者可以藉由建立文件查詢，包含使用者的識別碼，做為資料分割索引鍵，並示範如何在下列程式碼範例：

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink,
                        new FeedOptions
                        {
                          MaxItemCount = -1,
                          PartitionKey = new PartitionKey(UserId)
                        })
          .Where(item => !item.Id.Contains("permission"))
          .AsDocumentQuery();
while (query.HasMoreResults)
{
  Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
}
```

查詢以非同步方式擷取所有文件從指定集合中，屬於已驗證的使用者，並將檔案放在`List<TodoItem>`顯示的集合。

`CreateDocumentQuery<T>`方法指定`Uri`引數表示應該查詢的文件集合和`FeedOptions`物件。 `FeedOptions`物件可讓您指定的查詢，以及使用者的識別碼，做為資料分割索引鍵，可傳回無限的數量的項目。 這可確保只有在使用者的資料分割的集合中的文件才會傳回結果。

> [!NOTE]
> 請注意，權限的文件，會建立資源權杖訊息代理程式，會儲存在 Xamarin.Forms 應用程式所建立之文件相同的文件集合。 因此，包含文件查詢`Where`套用篩選述詞來查詢文件集合的子句。 這個子句可確保傳回的權限的文件時，不是從文件集合。

如需有關擷取的文件集合中的文件的詳細資訊，請參閱[擷取的文件集合文件](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#document_query)。

## <a name="inserting-documents"></a>插入文件

在之前將文件插入至文件集合，`TodoItem.UserId`屬性應做為資料分割索引鍵的值以進行更新，如下列程式碼範例所示：

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

這可確保文件會插入使用者的資料分割的集合。

如需有關插入文件集合中的文件的詳細資訊，請參閱[插入文件集合中的文件](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting_document)。

## <a name="deleting-documents"></a>刪除文件

必須指定資料分割索引鍵值時刪除資料分割的集合，文件做為下列程式碼範例所示,：

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

這可確保 Cosmos DB 知道的資料分割刪除該文件的集合。

如需文件集合中刪除文件的詳細資訊，請參閱[文件集合中刪除文件](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting_document)。

## <a name="summary"></a>總結

這篇文章會說明如何使用資料分割的集合，結合存取控制，讓使用者只能存取自己的文件資料庫文件中的 Xamarin.Forms 應用程式。 指定使用者的身分識別做為資料分割索引鍵，可確保資料分割的集合可以只儲存該使用者的文件。


## <a name="related-links"></a>相關連結

- [Todo Azure Cosmos DB Auth （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDBAuth/)
- [使用 Azure Cosmos DB 文件資料庫](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [安全存取 Azure Cosmos DB 資料](/azure/cosmos-db/secure-access-to-data/)
- [SQL API 中的存取控制](/rest/api/documentdb/access-control-on-documentdb-resources/)。
- [如何在資料分割和調整 Azure Cosmos DB 中](/azure/cosmos-db/partition-data/)
- [Azure Cosmos DB 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [Azure Cosmos DB API](https://msdn.microsoft.com/library/azure/dn948556.aspx)
