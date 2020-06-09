---
標題：「使用 Azure Cosmos DB 檔資料庫來驗證使用者」和「 Xamarin.Forms 描述：」本文說明如何結合存取控制與 Azure Cosmos DB 分割的集合，讓使用者只能在應用程式中存取自己的檔 Xamarin.Forms 。」
assetid： 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：06/16/2017 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>使用 Azure Cosmos DB 檔資料庫來驗證使用者，以及Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)

_Azure Cosmos DB 檔資料庫支援分割的集合，可以跨越多個伺服器和資料分割，同時支援無限制的儲存體和輸送量。本文說明如何結合存取控制與分割的集合，讓使用者只能在應用程式中存取自己的檔 Xamarin.Forms 。_

## <a name="overview"></a>概觀

建立資料分割集合時，必須指定分割區索引鍵，而且具有相同資料分割索引鍵的檔會儲存在相同的資料分割中。 因此，將使用者的身分識別指定為分割區索引鍵，將會產生只儲存該使用者檔的資料分割集合。 這也可確保 Azure Cosmos DB 檔資料庫會隨著使用者和專案數目的增加而調整。

必須授與存取權給任何集合，而且 SQL API 存取控制模型會定義兩種類型的存取結構：

- **主要金鑰**會啟用 Cosmos DB 帳戶內所有資源的完整系統管理存取權，並在建立 Cosmos DB 帳戶時建立。
- **資源權杖**會捕捉資料庫使用者與使用者對特定 Cosmos DB 資源（例如集合或檔）所擁有之許可權之間的關聯性。

公開主要金鑰會開啟 Cosmos DB 帳戶，以因應惡意或疏忽使用的可能性。 不過，Azure Cosmos DB 資源權杖會提供一個安全機制，讓用戶端根據授與的許可權，來讀取、寫入和刪除 Azure Cosmos DB 帳戶中的特定資源。

對行動應用程式要求、產生和傳遞資源權杖的一般方法是使用資源權杖訊息代理程式。 下圖顯示範例應用程式如何使用資源權杖代理人來管理對檔資料庫資料之存取的高階總覽：

![](azure-cosmosdb-auth-images/documentdb-authentication.png "Document Database Authentication Process")

資源權杖訊息代理程式是主控于 Azure App Service 中的中介層 Web API 服務，其擁有 Cosmos DB 帳戶的主要金鑰。 範例應用程式會使用資源權杖代理人來管理對檔資料庫資料的存取，如下所示：

1. 在登入時， Xamarin.Forms 應用程式會聯絡 Azure App Service 以起始驗證流程。
1. Azure App Service 會使用 Facebook 執行 OAuth 驗證流程。 驗證流程完成之後， Xamarin.Forms 應用程式會收到存取權杖。
1. Xamarin.Forms應用程式會使用存取權杖來要求來自資源權杖仲介的資源權杖。
1. 資源權杖訊息代理程式會使用存取權杖，向 Facebook 要求使用者的身分識別。 然後，使用者的身分識別會用來向 Cosmos DB 要求資源權杖，用來將讀取/寫入存取權授與已驗證使用者的資料分割集合。
1. Xamarin.Forms應用程式會使用資源權杖，直接使用資源權杖所定義的許可權來存取 Cosmos DB 資源。

> [!NOTE]
> 當資源權杖過期時，後續的檔資料庫要求將會收到401未經授權的例外狀況。 此時， Xamarin.Forms 應用程式應該重新建立身分識別，並要求新的資源權杖。

如需 Cosmos DB 資料分割的詳細資訊，請參閱[如何在 Azure Cosmos DB 中分割和相應縮小](/azure/cosmos-db/partition-data/)。 如需 Cosmos DB 存取控制的詳細資訊，請參閱保護[SQL API 中 Cosmos DB 資料和存取控制的](/rest/api/documentdb/access-control-on-documentdb-resources/)[存取權](/azure/cosmos-db/secure-access-to-data/)。

## <a name="setup"></a>安裝程式

將資源權杖代理人整合到應用程式的流程如下所示 Xamarin.Forms ：

1. 建立將會使用存取控制的 Cosmos DB 帳戶。 如需詳細資訊，請參閱[Azure Cosmos DB](#azure-cosmos-db-configuration)設定。
1. 建立 Azure App Service 來裝載資源權杖訊息代理程式。 如需詳細資訊，請參閱[Azure App Service](#azure-app-service-configuration)設定。
1. 建立 Facebook 應用程式以執行驗證。 如需詳細資訊，請參閱[Facebook 應用程式組態](#facebook-app-configuration)。
1. 設定 Azure App Service 以使用 Facebook 執行簡單的驗證。 如需詳細資訊，請參閱[Azure App Service 驗證](#azure-app-service-authentication-configuration)設定。
1. 設定 Xamarin.Forms 範例應用程式以與 Azure App Service 和 Cosmos DB 通訊。 如需詳細資訊，請參閱[ Xamarin.Forms 應用程式](#xamarinforms-application-configuration)設定。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

### <a name="azure-cosmos-db-configuration"></a>Azure Cosmos DB 設定

建立將會使用存取控制的 Cosmos DB 帳戶的程式如下所示：

1. 建立 Cosmos DB 帳戶。 如需詳細資訊，請參閱[建立 Azure Cosmos DB 帳戶](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account)。
1. 在 Cosmos DB 帳戶中，建立名為的新集合，並指定的資料 `UserItems` 分割索引鍵 `/userid` 。

### <a name="azure-app-service-configuration"></a>Azure App Service 設定

在 Azure App Service 中裝載資源權杖代理人的程式如下所示：

1. 在 Azure 入口網站中，建立新的 App Service web 應用程式。 如需詳細資訊，請參閱[在 App Service 環境中建立 web 應用程式](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/)。
1. 在 [Azure 入口網站中，開啟 web 應用程式的 [應用程式設定] 分頁，然後新增下列設定：
    - `accountUrl`–此值應該是來自 Cosmos DB 帳戶之 [金鑰] 分頁的 [Cosmos DB 帳戶 URL]。
    - `accountKey`–此值應該是 Cosmos DB 帳戶的 [金鑰] 分頁中的 Cosmos DB 主要金鑰（主要或次要）。
    - `databaseId`–此值應該是 Cosmos DB 資料庫的名稱。
    - `collectionId`–此值應該是 Cosmos DB 集合的名稱（在此案例中為 `UserItems` ）。
    - `hostUrl`–此值應該是 web 應用程式的 URL，從 App Service 帳戶的 [總覽] 分頁。

    下列螢幕擷取畫面會示範這項設定：

    [![](azure-cosmosdb-auth-images/azure-web-app-settings.png "App Service Web App Settings")](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "App Service Web App Settings")

1. 將資源權杖代理人解決方案發佈至 Azure App Service web 應用程式。

### <a name="facebook-app-configuration"></a>Facebook 應用程式組態

建立 Facebook 應用程式以執行驗證的流程如下所示：

1. 建立 Facebook 應用程式。 如需詳細資訊，請參閱 Facebook 開發人員中心上的[註冊及設定應用程式](https://developers.facebook.com/docs/apps/register)。
1. 將 Facebook 登入產品新增至應用程式。 如需詳細資訊，請參閱 Facebook 開發人員中心上的[將 Facebook 登入新增至您的應用程式或網站](https://developers.facebook.com/docs/facebook-login)。
1. 設定 Facebook 登入，如下所示：
   - 啟用用戶端 OAuth 登入。
   - 啟用 Web OAuth 登入。
   - 將有效的 OAuth 重新導向 URI 設定為 App Service web 應用程式的 URI，並 `/.auth/login/facebook/callback` 附加。

  下列螢幕擷取畫面會示範這項設定：

  ![](azure-cosmosdb-auth-images/facebook-oauth-settings.png "Facebook Login OAuth Settings")

如需詳細資訊，請參閱[使用 Facebook 註冊您的應用程式](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook)。

### <a name="azure-app-service-authentication-configuration"></a>Azure App Service 驗證設定

設定 App Service 簡單驗證的程式如下所示：

1. 在 Azure 入口網站中，流覽至 App Service web 應用程式。
1. 在 Azure 入口網站中，開啟 [驗證/授權] 分頁，然後執行下列設定：
    - 應開啟 App Service 驗證。
    - 未驗證要求時要採取的動作應設定為**使用 Facebook 登入**。

    下列螢幕擷取畫面會示範這項設定：

    [![](azure-cosmosdb-auth-images/app-service-authentication-settings.png "App Service Web App Authentication Settings")](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "App Service Web App Authentication Settings")

App Service web 應用程式也應該設定為與 Facebook 應用程式通訊，以啟用驗證流程。 這可以藉由選取 Facebook 識別提供者，並在 Facebook 開發人員中心的 Facebook 應用程式設定中輸入**應用程式識別碼**和**應用程式秘密**值來完成。 如需詳細資訊，請參閱[將 Facebook 資訊新增至您的應用程式](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application)。

### <a name="xamarinforms-application-configuration"></a>Xamarin.Forms應用程式設定

設定範例應用程式的流程如下所示 Xamarin.Forms ：

1. 開啟 Xamarin.Forms 方案。
1. 開啟 `Constants.cs` 並更新下列常數的值：
    - `EndpointUri`–此值應該是來自 Cosmos DB 帳戶之 [金鑰] 分頁的 [Cosmos DB 帳戶 URL]。
    - `DatabaseName`–此值應該是檔資料庫的名稱。
    - `CollectionName`–此值應該是檔資料庫集合的名稱（在此案例中為 `UserItems` ）。
    - `ResourceTokenBrokerUrl`–此值應該是 App Service 帳戶的 [總覽] 分頁中資源權杖代理人 web 應用程式的 URL。

## <a name="initiating-login"></a>起始登入

範例應用程式會藉由將瀏覽器重新導向至識別提供者 URL 來起始登入程式，如下列程式碼範例所示：

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

這會導致 OAuth 驗證流程在 Azure App Service 和 Facebook 之間起始，這會顯示 Facebook 登入頁面：

![](azure-cosmosdb-auth-images/login.png "Facebook Login")

您可以在 iOS 上按下 [**取消**] 按鈕，或按下 Android 上的 [**上一頁**] 按鈕來取消登入，在此情況下，使用者會保持未驗證，並從畫面中移除識別提供者使用者介面。

## <a name="obtaining-a-resource-token"></a>取得資源權杖

成功驗證之後，就會 `WebRedirectAuthenticator.Completed` 引發事件。 下列程式碼範例示範如何處理此事件：

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

成功驗證的結果是存取權杖，這是可用的 `AuthenticatorCompletedEventArgs.Account` 屬性。 存取權杖會被解壓縮，並用於資源權杖訊息代理程式 API 的 GET 要求中 `resourcetoken` 。

此 `resourcetoken` API 會使用存取權杖來向 Facebook 要求使用者的身分識別，然後用它來要求 Cosmos DB 的資源權杖。 如果檔資料庫中的使用者已經有有效的許可權檔，則會抓取它，並將包含資源 token 的 JSON 檔傳回給 Xamarin.Forms 應用程式。 如果使用者不存在有效的許可權檔，則會在檔資料庫中建立使用者和許可權，並從許可權檔中解壓縮資源權杖，並將其傳回 Xamarin.Forms JSON 檔中的應用程式。

> [!NOTE]
> 檔資料庫使用者是與檔資料庫相關聯的資源，而且每個資料庫可能包含零個或多個使用者。 檔資料庫許可權是與檔資料庫使用者相關聯的資源，而且每個使用者都可以包含零或多個許可權。 許可權資源可存取使用者在嘗試存取資源（例如檔）時所需的安全性權杖。

如果 `resourcetoken` API 成功完成，它會在回應中傳送 HTTP 狀態碼200（確定），連同包含資源 token 的 JSON 檔。 下列 JSON 資料顯示一般成功的回應訊息：

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

`WebRedirectAuthenticator.Completed`事件處理常式會從 API 讀取回應 `resourcetoken` ，並解壓縮資源權杖和使用者識別碼。然後，資源權杖會當做引數傳遞至函式 `DocumentClient` ，以封裝用來存取 Cosmos DB 的端點、認證和連線原則，並用於針對 Cosmos DB 設定和執行要求。 資源權杖會與每個要求一起傳送，以直接存取資源，並指出已授與已驗證使用者之已分割集合的讀取/寫入存取權。

## <a name="retrieving-documents"></a>正在抓取檔

藉由建立包含使用者識別碼做為分割區索引鍵的檔查詢，即可取得僅屬於已驗證使用者的檔，並在下列程式碼範例中示範：

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

查詢會以非同步方式從指定的集合中抓取屬於已驗證使用者的所有檔，並將它們放在 `List<TodoItem>` 集合中以供顯示。

`CreateDocumentQuery<T>`方法會指定 `Uri` 引數，表示應查詢檔的集合，以及 `FeedOptions` 物件。 `FeedOptions`物件指定查詢可以傳回不限數目的專案，以及使用者的識別碼做為分割區索引鍵。 這可確保結果中只會傳回使用者分割的集合中的檔。

> [!NOTE]
> 請注意，資源權杖代理人所建立的許可權檔會儲存在與應用程式所建立檔相同的檔集合中 Xamarin.Forms 。 因此，檔查詢所包含的 `Where` 子句會將篩選述詞套用至檔集合的查詢。 此子句可確保不會從檔集合傳回許可權檔。

如需從檔集合中抓取檔的詳細資訊，請參閱抓取[檔集合檔](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#retrieving-document-collection-documents)。

## <a name="inserting-documents"></a>插入檔

將檔插入檔集合之前， `TodoItem.UserId` 應該使用做為分割區索引鍵的值來更新屬性，如下列程式碼範例所示：

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

這可確保檔會插入使用者的資料分割集合中。

如需將檔插入檔集合的詳細資訊，請參閱將[檔插入檔集合](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting-a-document-into-a-document-collection)。

## <a name="deleting-documents"></a>刪除檔

從分割的集合中刪除檔時，必須指定分割區索引鍵值，如下列程式碼範例所示：

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

這可確保 Cosmos DB 知道要刪除檔的資料分割集合。

如需有關從檔集合中刪除檔的詳細資訊，請參閱[從檔集合中刪除檔](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting-a-document-from-a-document-collection)。

## <a name="summary"></a>總結

本文說明如何結合存取控制與分割的集合，讓使用者只能在應用程式中存取自己的檔資料庫檔案 Xamarin.Forms 。 將使用者的身分識別指定為分割區索引鍵，可確保分割的集合只能儲存該使用者的檔。

## <a name="related-links"></a>相關連結

- [Todo Azure Cosmos DB Auth （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)
- [使用 Azure Cosmos DB 文件資料庫](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [保護對 Azure Cosmos DB 資料的存取](/azure/cosmos-db/secure-access-to-data/)
- [SQL API 中的存取控制](/rest/api/documentdb/access-control-on-documentdb-resources/)。
- [如何在 Azure Cosmos DB 中進行資料分割和調整](/azure/cosmos-db/partition-data/)
- [Azure Cosmos DB 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [Azure Cosmos DB API](https://msdn.microsoft.com/library/azure/dn948556.aspx)
