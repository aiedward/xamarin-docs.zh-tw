---
title: 使用 Azure Cosmos DB 文件資料庫和 Xamarin 驗證使用者。
description: 本文介紹如何將存取控制與 Azure Cosmos DB 分區集合相結合,以便使用者只能在 Xamarin.Forms 應用程式中訪問自己的文檔。
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 6e79e647d64103b6d257de7233f488899bcaff40
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388599"
---
# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>使用 Azure Cosmos DB 文件資料庫和 Xamarin 驗證使用者。

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)

_Azure Cosmos DB 文件資料庫支援分區集合,該集合可以跨多個伺服器和分區,同時支援無限的存儲和輸送量。本文介紹如何將訪問控制與分區集合相結合,以便使用者只能在 Xamarin.Forms 應用程式中訪問自己的文檔。_

## <a name="overview"></a>概觀

創建分區集合時必須指定分區鍵,具有相同分區鍵的文件將存儲在同一分區中。 因此,將使用者標識指定為分區鍵將導致分區集合僅存儲該用戶的文檔。 這還可確保 Azure Cosmos DB 文件資料庫將隨著使用者和項數量的增加而擴展。

必須授予任何集合存取權限,SQL API 存取控制模型定義兩種類型的存取構構:

- **主金鑰**允許對 Cosmos DB 帳戶中的所有資源進行完全管理訪問,並在創建 Cosmos DB 帳戶時創建。
- **資源權杖**擷取資料庫用戶與使用者對特定Cosmos DB資源(如集合或文檔)的許可權之間的關係。

公開主密鑰會打開Cosmos DB帳戶,使其有可能惡意或疏忽使用。 但是,Azure Cosmos DB 資源權杖提供了一種安全機制,允許客戶端根據授予的許可權讀取、寫入和刪除 Azure Cosmos DB 帳戶中的特定資源。

請求、生成和向移動應用程式傳遞資源權杖的典型方法是使用資源權杖代理。 下圖顯示了範例應用程式如何使用資源權杖代理來管理對文件資料庫資料的存取的進階概述:

![](azure-cosmosdb-auth-images/documentdb-authentication.png "Document Database Authentication Process")

資源權杖代理是一個中端 Web API 服務,託管在 Azure 應用服務中,該服務具有 Cosmos DB 帳戶的主金鑰。 範例應用程式使用資源權杖代理管理對文件資料庫資料的存取,如下所示:

1. 登錄時,Xamarin.Forms 應用程式會與 Azure 應用服務聯繫以啟動身份驗證流。
1. Azure 應用服務使用 Facebook 執行 OAuth 身份驗證流。 身份驗證流完成後,Xamarin.Forms 應用程式將接收訪問權杖。
1. Xamarin.Forms 應用程式使用訪問權杖從資源權杖代理請求資源權杖。
1. 資源令牌代理使用訪問權杖從Facebook請求使用者的身份。 然後,使用者的身份用於從Cosmos DB請求資源權杖,該權杖用於授予對經過身份驗證的使用者分區集合的讀/寫存取許可權。
1. Xamarin.Forms 應用程式使用資源權杖直接存取Cosmos DB資源,並具有資源權杖定義的許可權。

> [!NOTE]
> 當資源令牌過期時,後續文檔資料庫請求將收到 401 未經授權的異常。 此時,Xamarin.Forms 應用程式應重新建立標識並請求新的資源權杖。

有關 Cosmos DB 分割區的詳細資訊,請參閱[如何在 Azure Cosmos DB 中分區和縮放](/azure/cosmos-db/partition-data/)。 有關Cosmos DB存取控制的詳細資訊,請參閱在SQL API中[保護對Cosmos DB資料和](/azure/cosmos-db/secure-access-to-data/)[存取控制項](/rest/api/documentdb/access-control-on-documentdb-resources/)的存取。

## <a name="setup"></a>安裝程式

將資源權杖代理整合到 Xamarin.Forms 應用程式的過程如下所示:

1. 創建將使用存取控制的Cosmos DB帳戶。 有關詳細資訊,請參閱[宇宙資料庫配置](#cosmosdb_configuration)。
1. 創建 Azure 應用服務以承載資源權杖代理。 有關詳細資訊,請參閱[Azure 應用服務設定](#app_service_configuration)。
1. 創建 Facebook 應用以執行身份驗證。 有關詳細資訊,請參閱[Facebook 應用程式設定](#facebook_configuration)。
1. 將 Azure 應用服務配置為對 Facebook 執行簡單的身份驗證。 有關詳細資訊,請參閱[Azure 應用服務認證設定](#app_service_authentication_configuration)。
1. 配置 Xamarin.Forms 範例應用程式以與 Azure 應用服務和 Cosmos DB 進行通訊。 有關詳細資訊,請參閱[Xamarin.窗體應用程式設定](#forms_application_configuration)。

> [!NOTE]
> 如果沒有[Azure 訂閱](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing),請先創建[一個免費帳戶](https://aka.ms/azfree-docs-mobileapps)。然後開始。

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Azure 宇宙資料庫設定

建立將使用存取控制的 Cosmos DB 帳號的過程如下所示:

1. 創建Cosmos資料庫帳戶。 有關詳細資訊,請參閱創建[Azure 宇宙資料庫帳戶](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account)。
1. 在 Cosmos DB 帳戶中,`UserItems`創建名為 的新`/userid`集合,指定的分區鍵。

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Azure 應用服務設定

在 Azure 應用服務中託管資源權杖代理的過程如下所示:

1. 在 Azure 門戶中,創建新的應用服務 Web 應用。 有關詳細資訊,請參閱[在應用服務環境中建立 Web 應用](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/)。
1. 在 Azure 門戶中,開啟 Web 應用的應用設定邊欄選項卡,並新增以下設定:
    - `accountUrl`• 該值應是來自Cosmos DB帳戶的「密鑰」邊欄選項卡的Cosmos DB 帳戶網址。
    - `accountKey`• 該值應是來自Cosmos DB帳戶的Keys邊欄選項卡的Cosmos DB主金鑰(主金鑰或輔助金鑰)。
    - `databaseId`• 該值應為Cosmos DB資料庫的名稱。
    - `collectionId`• 該值應為Cosmos DB集合的名稱(在本`UserItems`例中為)。
    - `hostUrl`• 該值應是應用服務帳戶的「概述」選項卡中的 Web 應用的 URL。

    以下螢幕擷取此設定:

    [![](azure-cosmosdb-auth-images/azure-web-app-settings.png "App Service Web App Settings")](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "App Service Web App Settings")

1. 將資源令牌代理解決方案發佈到 Azure 應用服務 Web 應用。

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Facebook 應用程式設定

創建 Facebook 應用以執行身份驗證的過程如下:

1. 創建 Facebook 應用。 有關詳細資訊,請參閱在 Facebook 開發人員中心[註冊和配置應用](https://developers.facebook.com/docs/apps/register)。
1. 將 Facebook 登錄產品添加到應用。 有關詳細資訊,請參閱在 Facebook 開發人員中心[將 Facebook 登入到您的應用程式或網站](https://developers.facebook.com/docs/facebook-login)。
1. 按以下設定 Facebook 登入:
   - 啟用用戶端 OAuth 登錄名。
   - 啟用 Web OAuth 登錄。
   - 將有效 OAuth 重定向到應用服務 Web 應用`/.auth/login/facebook/callback`的 URI,並附加。

  以下螢幕擷取此設定:

  ![](azure-cosmosdb-auth-images/facebook-oauth-settings.png "Facebook Login OAuth Settings")

有關詳細資訊,請參閱在[Facebook 註冊您的應用程式](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook)。

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Azure 應用程式服務認證配置

配置應用服務簡單身份驗證的過程如下:

1. 在 Azure 門戶中,導航到應用服務 Web 應用。
1. 在 Azure 門戶中,開啟身份驗證/授權邊欄選項卡並執行以下設定:
    - 應用服務身份驗證應打開。
    - 要求未經過身份驗證時執行的操作應設定為**使用 Facebook 登入**。

    以下螢幕擷取此設定:

    [![](azure-cosmosdb-auth-images/app-service-authentication-settings.png "App Service Web App Authentication Settings")](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "App Service Web App Authentication Settings")

應用服務 Web 應用也應配置為與 Facebook 應用通信,以啟用身份驗證流。 這可以通過選擇 Facebook 標識供應商,並從 Facebook 開發人員中心的 Facebook 應用設置中輸入**應用 ID** **和應用機密**值來實現。 有關詳細資訊,請參閱將[Facebook 資訊加入您的應用程式](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application)。

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Xamarin.表單應用程式設定

設定 Xamarin.Forms 範例應用程式的過程如下:

1. 打開 Xamarin.表單解決方案。
1. 開啟`Constants.cs`並更新以下常數的值:
    - `EndpointUri`• 該值應是來自Cosmos DB帳戶的「密鑰」邊欄選項卡的Cosmos DB 帳戶網址。
    - `DatabaseName`• 該值應為文檔資料庫的名稱。
    - `CollectionName`• 值應為文檔資料庫集合的名稱(在本例`UserItems`中為 )。
    - `ResourceTokenBrokerUrl`• 該值應是應用服務帳戶的"概述"選項卡中資源令牌代理 Web 應用的 URL。

## <a name="initiating-login"></a>啟動登入

範例應用程式透過瀏覽器重定向到識別提供程式網址 來啟動登入過程,如以下範例代碼所示:

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

這將導致在 Azure 應用服務和 Facebook 之間啟動 OAuth 身份驗證流,後者顯示 Facebook 登入頁:

![](azure-cosmosdb-auth-images/login.png "Facebook Login")

可以通過按 iOS 上的 **「取消」** 按鈕或按 Android 上的 **「後退**」按鈕來取消登錄,在這種情況下,使用者將保持未通過身份驗證,並且標識提供程式使用者介面將從螢幕上刪除。

## <a name="obtaining-a-resource-token"></a>取得資源權杖

成功身份驗證后,事件`WebRedirectAuthenticator.Completed`將觸發。 以下代碼範例展示了處理此事件:

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

成功身份驗證的結果是訪問權杖,它是可用的`AuthenticatorCompletedEventArgs.Account`屬性。 訪問權杖被提取並用於資源權杖代理的`resourcetoken`API的GET請求。

`resourcetoken` API 使用存取權杖從Facebook請求使用者的身份,而Facebook又用於從Cosmos DB請求資源權杖。 如果文件資料庫中已存在使用者的有效權限文件,則檢索該文件,並且包含資源權杖的 JSON 文件將返回到 Xamarin.Forms 應用程式。 如果使用者不存在有效的許可權文件,則在文檔資料庫中創建使用者和許可權,並從許可權文檔中提取資源權杖並返回到 JSON 文檔中的 Xamarin.Forms 應用程式。

> [!NOTE]
> 文件資料庫使用者是與文檔資料庫關聯的資源,每個資料庫可能包含零個或多個使用者。 文件資料庫許可權是與文檔資料庫使用者關聯的資源,每個使用者可能包含零個或多個許可權。 許可權資源提供對使用者在嘗試訪問資源(如文檔)時所需的安全權杖的訪問。

如果`resourcetoken`API 成功完成,它將在回應中發送 HTTP 狀態代碼 200 (OK)以及包含資源權杖的 JSON 文件。 以下 JSON 數據顯示了典型的成功回應消息:

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

事件`WebRedirectAuthenticator.Completed`處理程式`resourcetoken`從 API 讀取回應,並提取資源權杖和使用者 ID。然後,資源令牌作為參數傳遞給`DocumentClient`建構函數,該構造函數封裝了用於造訪Cosmos DB的終結點、認證和連接策略,並用於配置和執行針對Cosmos DB的請求。 資源令牌隨每個請求一起發送以直接存取資源,並指示授予對經過身份驗證的使用者分區集合的讀/寫存取許可權。

## <a name="retrieving-documents"></a>檢索文件

以建立包含使用者 ID 作為分區鍵的文件查詢,可以檢索僅屬於經過身份驗證的使用者的文件,並在以下代碼範例中演示:

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

查詢非同步檢索來自指定集合的所有屬於經過身份驗證的用戶的文件,並將它們放在集合中`List<TodoItem>`以顯示。

方法`CreateDocumentQuery<T>`指定一`Uri`個 參數,該參數表示應查詢`FeedOptions`文檔和 物件的集合。 該`FeedOptions`物件指定查詢可以返回無限數量的項,並將用戶的 ID 作為分區鍵返回。 這可確保在結果中僅返回使用者分區集合中的文檔。

> [!NOTE]
> 請注意,由資源權杖代理創建的許可權文件與 Xamarin.Forms 應用程式創建的文件儲存在相同的文件集合中。 因此,文檔查詢包含一`Where`個子句,該子句將篩選謂詞應用於針對文檔集合的查詢。 此子句可確保不會從文檔集合返回許可權文檔。

有關從文件集合檢索文件的詳細資訊,請參閱[檢索文件集合文件](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#document_query)。

## <a name="inserting-documents"></a>插入文件

在將文件插入到文件集合中之前,應使用`TodoItem.UserId`用作分區鍵的值更新該屬性,如下代碼範例所示:

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

這可確保將文檔插入到使用者的分區集合中。

有關將文件插入文件集合的詳細資訊,請參考[文件插入文件集合](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting_document)。

## <a name="deleting-documents"></a>刪除文件

從分割區集合中刪除文件時必須指定分割區鍵值,如下代碼範例所示:

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

這可確保Cosmos DB知道要從哪個分區集合中刪除文檔。

有關從文件集合中刪除文件的詳細資訊,請參閱[從文件集合中刪除文件](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting_document)。

## <a name="summary"></a>總結

本文介紹了如何將訪問控制與分區集合相結合,以便使用者只能在 Xamarin.Forms 應用程式中訪問自己的文檔資料庫文檔。 將使用者標識指定為分區鍵可確保分區集合只能為該使用者儲存文檔。

## <a name="related-links"></a>相關連結

- [Do Azure 宇宙 DB Auth(範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)
- [使用 Azure Cosmos DB 文件資料庫](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [保護對 Azure Cosmos DB 資料的存取](/azure/cosmos-db/secure-access-to-data/)
- [SQL API 中使用控制控制](/rest/api/documentdb/access-control-on-documentdb-resources/)。
- [如何在 Azure Cosmos DB 中進行資料分割和調整](/azure/cosmos-db/partition-data/)
- [Azure 宇宙資料庫用戶端庫](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [Azure Cosmos DB API](https://msdn.microsoft.com/library/azure/dn948556.aspx)
