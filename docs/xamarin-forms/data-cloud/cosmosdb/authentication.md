---
title: 驗證使用者以 Azure Cosmos DB 文件資料庫
description: 本文說明如何結合 Azure Cosmos DB 分割集合的存取控制，讓使用者只能存取自己的文件中的 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 031a48e5e10100b2c57ac067a0dda916c93d20da
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241607"
---
# <a name="authenticating-users-with-an-azure-cosmos-db-document-database"></a>驗證使用者以 Azure Cosmos DB 文件資料庫

_Azure Cosmos DB 文件資料庫可支援資料分割的集合、 可以橫跨多部伺服器和資料分割，同時支援無限制的儲存體和輸送量。本文說明如何結合資料分割的集合，存取控制，讓使用者只能存取自己的文件中的 Xamarin.Forms 應用程式。_

## <a name="overview"></a>總覽

建立分割的集合時，就必須指定資料分割索引鍵，並具有相同的資料分割索引鍵的文件會儲存在相同的資料分割。 因此，指定使用者的身分識別做為資料分割索引鍵會導致資料分割的集合，其中只會儲存該使用者的文件。 這也可確保 Azure Cosmos DB 文件資料庫會調整的使用者數目，並增加項目。

必須授與至任何集合，存取和 SQL API 的存取控制模型會定義兩種類型的存取的建構：

- **主要金鑰**啟用完整管理存取權 Cosmos DB 帳戶中的所有資源，並建立 Cosmos DB 帳戶時建立。
- **資源語彙基元**擷取之資料庫的使用者和使用者擁有特定 Cosmos DB 資源，例如集合或文件的權限之間的關聯性。

將主要金鑰的公開會開啟，可能會使用惡意或疏忽 Cosmos DB 帳戶。 不過，Azure Cosmos DB 資源語彙基元提供允許讀取、 寫入和刪除，Azure Cosmos DB 帳戶，根據授與權限的特定資源的用戶端的安全機制。

要求的典型方法，產生，並將資源語彙基元傳遞給行動應用程式是使用資源語彙基元的 broker。 下圖顯示範例應用程式來管理存取權的文件資料庫資料所使用的資源權杖的 broker 的高階概觀：

![](authentication-images/documentdb-authentication.png "文件資料庫驗證程序")

Azure 應用程式服務擁有 Cosmos DB 帳戶的主要金鑰中裝載的中間層 Web API 服務為資源語彙基元 broker。 範例應用程式會使用資源語彙基元 broker 管理的文件資料庫資料的存取，如下所示：

1. 登入，在 Xamarin.Forms 應用程式，請連絡 Azure 應用程式服務，才能起始驗證流程。
1. Azure App Service 會執行與 Facebook OAuth 驗證流程。 驗證流程完成後，Xamarin.Forms 應用程式會收到一個存取權杖。
1. Xamarin.Forms 應用程式會使用存取權杖，向資源語彙基元 broker 要求資源權杖。
1. 資源語彙基元 broker 會使用存取權杖來要求來自 Facebook 使用者的身分識別。 使用者的身分識別接著用於要求的資源語彙基元從 Cosmos DB，用來授與讀取/寫入存取權已驗證的使用者資料分割的集合。
1. Xamarin.Forms 應用程式會使用直接存取 Cosmos DB 資源與資源語彙基元所定義的權限資源語彙基元。

> [!NOTE]
> 當資源權杖到期時，後續的文件資料庫要求將會接收 401 未授權例外狀況。 此時，Xamarin.Forms 應用程式應該重新建立的身分識別，並要求新的資源權杖。

如需 Cosmos DB 的資料分割的詳細資訊，請參閱[如何分割區和 Azure Cosmos DB 中的小數位數](/azure/cosmos-db/partition-data/)。 如需 Cosmos DB 存取控制的詳細資訊，請參閱[保護 Cosmos 資料庫資料的存取](/azure/cosmos-db/secure-access-to-data/)和[中 SQL API 的存取控制](/rest/api/documentdb/access-control-on-documentdb-resources/)。

## <a name="setup"></a>安裝程式

將資源語彙基元 broker 整合至 Xamarin.Forms 應用程式的程序如下所示：

1. 建立會使用存取控制的 Cosmos DB 帳戶。 如需詳細資訊，請參閱[Cosmos 資料庫組態](#cosmosdb_configuration)。
1. 建立 Azure 應用程式服務，以裝載資源語彙基元 broker。 如需詳細資訊，請參閱[Azure 應用程式服務組態](#app_service_configuration)。
1. 建立 Facebook 應用程式來執行驗證。 如需詳細資訊，請參閱[Facebook 應用程式組態](#facebook_configuration)。
1. 設定 Azure 應用程式服務來執行簡單與 Facebook 驗證。 如需詳細資訊，請參閱[Azure 應用程式服務的驗證設定](#app_service_authentication_configuration)。
1. Xamarin.Forms 範例應用程式與 Azure App Service 與 Cosmos DB 通訊設定。 如需詳細資訊，請參閱[Xamarin.Forms 應用程式組態](#forms_application_configuration)。

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Azure Cosmos DB 組態

建立會使用存取控制的 Cosmos DB 帳戶的程序如下所示：

1. 建立 Cosmos DB 帳戶。 如需詳細資訊，請參閱[建立 Azure Cosmos DB 帳戶](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account)。
1. Cosmos DB 帳戶中，建立名為的新集合`UserItems`，指定資料分割索引鍵`/userid`。

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Azure 應用程式服務組態

裝載 Azure App Service 中的資源權杖 broker 的程序如下所示：

1. 在 Azure 入口網站中，建立新的 App Service web 應用程式。 如需詳細資訊，請參閱[App Service 環境中建立 web 應用程式](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/)。
1. 在 Azure 入口網站中，開啟 [應用程式設定] 刀鋒視窗，web 應用程式中，並新增下列設定：
    - `accountUrl` – 這個值應該是從 Cosmos DB 帳戶中的 [金鑰] 刀鋒視窗的 Cosmos DB 帳戶 URL。
    - `accountKey` – 這個值應該是 Cosmos 資料庫主要金鑰 （主要或次要） 從 Cosmos DB 帳戶中的 [金鑰] 刀鋒視窗。
    - `databaseId` – 值應該是 Cosmos DB 資料庫的名稱。
    - `collectionId` – 值應該是 Cosmos DB 集合的名稱 (在此情況下， `UserItems`)。
    - `hostUrl` – 這個值應該是從應用程式服務帳戶的 [概觀] 刀鋒視窗的 web 應用程式的 URL。

    下列螢幕擷取畫面會示範這項設定：

    [![](authentication-images/azure-web-app-settings.png "App Service Web 應用程式設定")](authentication-images/azure-web-app-settings-large.png#lightbox "App Service Web 應用程式設定")

1. 將資源語彙基元 broker 方案發行至 Azure App Service web 應用程式。

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Facebook 應用程式組態

建立 Facebook 應用程式來執行驗證的程序如下所示：

1. 建立 Facebook 應用程式。 如需詳細資訊，請參閱[註冊和設定應用程式](https://developers.facebook.com/docs/apps/register)Facebook 開發人員中心。
1. 將 Facebook 登入產品加入至應用程式。 如需詳細資訊，請參閱[新增 Facebook 登入您的應用程式或網站](https://developers.facebook.com/docs/facebook-login)Facebook 開發人員中心。
1. 設定 Facebook 登入，如下所示：
  - 啟用用戶端 OAuth 登入。
  - 啟用 Web OAuth 登入。
  - 設定有效的 OAuth 重新導向至 App Service web 應用程式中，URI 的 URI 與`/.auth/login/facebook/callback`附加。

  下列螢幕擷取畫面會示範這項設定：

  ![](authentication-images/facebook-oauth-settings.png "Facebook 登入的 OAuth 設定")

如需詳細資訊，請參閱[註冊您的應用程式與 Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook)。

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Azure App Service 的驗證設定

用於設定應用程式服務的簡單驗證程序如下所示：

1. 在 Azure 入口網站中，巡覽至 App Service web 應用程式。
1. 在 Azure 入口網站中，開啟 驗證 / 授權刀鋒視窗並執行下列設定：
  - 應該開啟應用程式服務驗證。
  - 當要求未經驗證時要採取的動作應該設定為**使用 Facebook 登入**。

  下列螢幕擷取畫面會示範這項設定：

  [![](authentication-images/app-service-authentication-settings.png "App Service Web 應用程式驗證設定")](authentication-images/app-service-authentication-settings-large.png#lightbox "App Service Web 應用程式驗證設定")

App Service web 應用程式也應該設定為與 Facebook 應用程式，以啟用驗證流程通訊。 這可藉由選取 Facebook 身分識別提供者，並輸入**應用程式識別碼**和**應用程式秘鑰**從 Facebook 開發人員中心的 Facebook 應用程式設定的值。 如需詳細資訊，請參閱[新增 Facebook 應用程式的資訊](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application)。

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Xamarin.Forms 應用程式組態

設定 Xamarin.Forms 範例應用程式的程序如下所示：

1. 開啟 Xamarin.Forms 方案。
1. 開啟`Constants.cs`及更新下列常數的值：
  - `EndpointUri` – 這個值應該是從 Cosmos DB 帳戶中的 [金鑰] 刀鋒視窗的 Cosmos DB 帳戶 URL。
  - `DatabaseName` – 值應該是文件資料庫的名稱。
  - `CollectionName` – 值應該是文件資料庫集合的名稱 (在此情況下， `UserItems`)。
  - `ResourceTokenBrokerUrl` – 這個值應該是從應用程式服務帳戶的 [概觀] 刀鋒視窗資源語彙基元 broker web 應用程式的 URL。

## <a name="initiating-login"></a>起始登入

範例應用程式會起始登入程序，利用 Xamarin.Auth 瀏覽器重新導向至識別提供者的 URL，如下列範例程式碼所示：

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

這會導致 Azure App Service 與 Facebook、 Facebook 登入頁面會顯示起始 OAuth 驗證流程：

![](authentication-images/login.png "Facebook 登入")

登入可以藉由按下取消**取消**按鈕在 iOS 上，或按**回**按鈕在 Android 上，在此情況下，使用者仍未驗證和身分識別提供者使用者介面從螢幕中移除。

如需 Xamarin.Auth 的詳細資訊，請參閱[驗證使用者身分識別提供者](~/xamarin-forms/data-cloud/authentication/oauth.md)。

## <a name="obtaining-a-resource-token"></a>取得為資源語彙基元

成功的驗證，下列`WebRedirectAuthenticator.Completed`事件引發。 下列程式碼範例示範處理這個事件：

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

成功驗證的結果是存取權杖，也就是可用`AuthenticatorCompletedEventArgs.Account`屬性。 存取權杖是擷取，而且用於要求的 GET 要求的資源權杖 broker`resourcetoken`應用程式開發介面。

`resourcetoken` API 所使用的存取權杖來要求使用者的身分識別來自 Facebook，接著用來從 Cosmos DB 要求為資源語彙基元。 如果使用者在文件資料庫中已經有有效的權限的文件，它會擷取並包含資源語彙基元的 JSON 文件會傳回到 Xamarin.Forms 應用程式。 如果使用者沒有有效的權限的文件，使用者和權限是在資料庫中建立文件，並從權限的文件擷取資源語彙基元並傳回 JSON 文件中的 Xamarin.Forms 應用程式。

> [!NOTE]
> 文件資料庫使用者是使用文件資料庫時，相關聯的資源，而且每個資料庫可能包含零個或多個使用者。 文件資料庫權限是與文件資料庫使用者相關聯的資源，而且每一位使用者可能包含零或多個權限。 權限資源提供存取權的使用者在嘗試存取的資源，例如文件時所需要的安全性權杖。

如果`resourcetoken`API 已成功完成，它會傳送 HTTP 狀態碼 200 （確定） 回應，以及包含資源語彙基元的 JSON 文件。 下列 JSON 資料會顯示一般的成功回應訊息：

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

`WebRedirectAuthenticator.Completed`事件處理常式會讀取來自回應`resourcetoken`應用程式開發介面，並擷取資源語彙基元和使用者識別碼。資源權杖接著會傳遞做為引數`DocumentClient`建構函式，將封裝端點、 認證和用來存取 Cosmos DB 的連線原則和用來設定與執行對 Cosmos DB 要求。 資源權杖的直接存取資源時，每個要求傳送，並指出會授與讀取/寫入存取權已驗證的使用者資料分割的集合。

## <a name="retrieving-documents"></a>擷取文件

擷取文件只隸屬於已驗證的使用者可藉由下列程式碼範例建立包含使用者的識別碼，表示為資料分割索引鍵和示範文件查詢：

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

查詢以非同步方式擷取所有屬於已驗證的使用者，從指定集合中的文件，並放在`List<TodoItem>`顯示的集合。

`CreateDocumentQuery<T>`方法指定`Uri`引數表示應該查詢的文件的集合和`FeedOptions`物件。 `FeedOptions`物件指定可傳回無限的數量的項目查詢，而使用者的識別碼，表示為資料分割索引鍵。 這可確保使用者的資料分割集合中的文件會在結果中傳回。

> [!NOTE]
> 請注意，權限的文件，也就建立資源語彙基元 broker 會儲存在相同的文件集合，如 Xamarin.Forms 應用程式所建立之文件。 因此，包含文件查詢`Where`篩選述詞套用至查詢對文件集合的子句。 這個子句可確保權限的文件不傳回的文件集合。

如需文件集合中擷取的文件的詳細資訊，請參閱[擷取文件集合文件](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#document_query)。

## <a name="inserting-documents"></a>插入文件

插入到文件集合中，文件之前`TodoItem.UserId`應該用做為資料分割索引鍵的值與更新屬性，如下列程式碼範例所示：

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

這可確保文件就會插入到使用者的資料分割的集合。

如需插入文件集合中的文件的詳細資訊，請參閱[插入文件集合中的文件](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#inserting_document)。

## <a name="deleting-documents"></a>刪除文件

必須指定資料分割索引鍵值時刪除文件從資料分割的集合，做為下列程式碼範例所示,：

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

這可確保 Cosmos DB 知道的資料分割刪除來自的文件的集合。

如需從文件集合中刪除文件的詳細資訊，請參閱[從文件集合中刪除文件](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#deleting_document)。

## <a name="summary"></a>總結

本文說明如何使用資料分割的集合，結合存取控制，讓使用者只能存取自己的文件資料庫文件中的 Xamarin.Forms 應用程式。 指定使用者的身分識別做為資料分割索引鍵，可確保資料分割的集合可以只儲存文件，該使用者。


## <a name="related-links"></a>相關連結

- [Todo Azure Cosmos DB Auth （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDBAuth/)
- [使用 Azure Cosmos DB 文件資料庫](~/xamarin-forms/data-cloud/cosmosdb/consuming.md)
- [保護 Azure Cosmos 資料庫資料的存取](/azure/cosmos-db/secure-access-to-data/)
- [SQL API 中的存取控制](/rest/api/documentdb/access-control-on-documentdb-resources/)。
- [如何在資料分割和 Azure Cosmos DB 中的小數位數](/azure/cosmos-db/partition-data/)
- [Azure Cosmos DB 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [Azure Cosmos DB 應用程式開發介面](https://msdn.microsoft.com/library/azure/dn948556.aspx)
