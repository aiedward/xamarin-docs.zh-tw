---
title: 使用中的 Azure Cosmos DB 檔資料庫 Xamarin.Forms
description: 本文說明如何使用 Azure Cosmos DB .NET Standard 用戶端程式庫，將 Azure Cosmos DB 檔資料庫整合至 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3755f85b01821238e790bd2fb09f567c4e98d4d8
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368358"
---
# <a name="consume-an-azure-cosmos-db-document-database-in-no-locxamarinforms"></a>使用中的 Azure Cosmos DB 檔資料庫 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)

_Azure Cosmos DB 檔資料庫是一種 NoSQL 資料庫，可對 JSON 檔提供低延遲的存取，為需要無縫調整和全域複寫的應用程式，提供快速、高可用性、可擴充的資料庫服務。本文說明如何使用 Azure Cosmos DB .NET Standard 用戶端程式庫，將 Azure Cosmos DB 檔資料庫整合至 Xamarin.Forms 應用程式。_

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Microsoft Azure Cosmos DB 影片**

您可以使用 Azure 訂用帳戶來布建 Azure Cosmos DB 檔資料庫帳戶。 每個資料庫帳戶都可以有零或多個資料庫。 Azure Cosmos DB 中的檔資料庫是檔集合和使用者的邏輯容器。

Azure Cosmos DB 檔資料庫可能包含零個以上的檔集合。 每個檔集合可以有不同的效能層級，允許為經常存取的集合指定更多輸送量，而且不常存取的集合會有較少的輸送量。

每個檔集合都包含零或多個 JSON 檔。 集合中的檔是無架構的，因此不需要共用相同的結構或欄位。 當檔新增至檔集合時，Cosmos DB 會自動編制它們的索引，並且可供查詢。

針對開發用途，也可以透過模擬器使用檔資料庫。 您可以使用模擬器在本機開發及測試應用程式，而不需建立 Azure 訂用帳戶，也不會產生任何費用。 如需模擬器的詳細資訊，請參閱 [使用 Azure Cosmos DB 模擬器在本機進行開發](/azure/cosmos-db/local-emulator/)。

本文和隨附的範例應用程式會示範 Todo 清單應用程式，其中的工作會儲存在 Azure Cosmos DB 檔資料庫中。 如需範例應用程式的詳細資訊，請參閱 [瞭解範例](~/xamarin-forms/data-cloud/web-services/introduction.md)。

如需 Azure Cosmos DB 的詳細資訊，請參閱 [Azure Cosmos DB 檔](/azure/cosmos-db/)。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

## <a name="setup"></a>設定

將 Azure Cosmos DB 檔資料庫整合至應用程式的程式如下所示 Xamarin.Forms ：

1. 建立 Cosmos DB 帳戶。 如需詳細資訊，請參閱 [建立 Azure Cosmos DB 帳戶](/azure/cosmos-db/sql-api-dotnetcore-get-started#create-an-azure-cosmos-account)。
1. 將 [Azure Cosmos DB .NET Standard 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) NuGet 套件新增至方案中的平臺專案 Xamarin.Forms 。
1. 將 `using` `Microsoft.Azure.Documents` 、和命名空間的指示詞加入 `Microsoft.Azure.Documents.Client` `Microsoft.Azure.Documents.Linq` 至將存取 Cosmos DB 帳戶的類別。

執行這些步驟之後，Azure Cosmos DB .NET Standard 用戶端程式庫可以用來設定和執行檔資料庫的要求。

> [!NOTE]
> Azure Cosmos DB .NET Standard 用戶端程式庫只能安裝到平臺專案中，而不能安裝到便攜類別庫 (PCL) 專案中。 因此，範例應用程式是 (SAP) 的共用存取專案，以避免程式碼重複。 不過，此 `DependencyService` 類別可用於 PCL 專案中，以叫用平臺特定專案中包含的 Azure Cosmos DB .NET Standard 用戶端程式庫程式碼。

## <a name="consuming-the-azure-cosmos-db-account"></a>使用 Azure Cosmos DB 帳戶

此 `DocumentClient` 類型會封裝用來存取 Azure Cosmos DB 帳戶的端點、認證和連線原則，並用於設定和執行帳戶的要求。 下列程式碼範例示範如何建立這個類別的實例：

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

Cosmos DB 的 Uri 和主鍵必須提供給此函式 `DocumentClient` 。 這些可以從 Azure 入口網站取得。 如需詳細資訊，請參閱 [連接到 Azure Cosmos DB 帳戶](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect)。

### <a name="creating-a-database"></a>建立資料庫

檔資料庫是檔集合和使用者的邏輯容器，可在 Azure 入口網站中建立，或使用方法以程式設計方式建立 `DocumentClient.CreateDatabaseIfNotExistsAsync` ：

```csharp
public async Task CreateDatabase(string databaseName)
{
  ...
  await client.CreateDatabaseIfNotExistsAsync(new Database
  {
    Id = databaseName
  });
  ...
}
```

方法會將 `CreateDatabaseIfNotExistsAsync` `Database` 物件指定為引數，並將 `Database` 物件指定為資料庫名稱做為其 `Id` 屬性。 `CreateDatabaseIfNotExistsAsync`如果資料庫不存在，方法會建立資料庫，如果資料庫已經存在，則會傳回資料庫。 不過，範例應用程式會忽略方法所傳回的任何資料 `CreateDatabaseIfNotExistsAsync` 。

> [!NOTE]
> `CreateDatabaseIfNotExistsAsync`方法 `Task<ResourceResponse<Database>>` 會傳回物件，且可以檢查回應的狀態碼，以判斷資料庫是否已建立，或是否傳回現有的資料庫。

### <a name="creating-a-document-collection"></a>建立檔集合

檔集合是 JSON 檔的容器，可在 Azure 入口網站中建立，或使用方法以程式設計方式建立 `DocumentClient.CreateDocumentCollectionIfNotExistsAsync` ：

```csharp
public async Task CreateDocumentCollection(string databaseName, string collectionName)
{
  ...
  // Create collection with 400 RU/s
  await client.CreateDocumentCollectionIfNotExistsAsync(
    UriFactory.CreateDatabaseUri(databaseName),
    new DocumentCollection
    {
      Id = collectionName
    },
    new RequestOptions
    {
      OfferThroughput = 400
    });
  ...
}
```

`CreateDocumentCollectionIfNotExistsAsync`方法需要兩個強制引數–指定為的資料庫名稱 `Uri` 和 `DocumentCollection` 物件。 `DocumentCollection`物件代表其名稱是以屬性指定的檔集合 `Id` 。 `CreateDocumentCollectionIfNotExistsAsync`方法會建立檔集合（如果不存在），或傳回檔集合（如果已經存在的話）。 不過，範例應用程式會忽略方法所傳回的任何資料 `CreateDocumentCollectionIfNotExistsAsync` 。

> [!NOTE]
> `CreateDocumentCollectionIfNotExistsAsync`方法 `Task<ResourceResponse<DocumentCollection>>` 會傳回物件，並且可以檢查回應的狀態碼，以判斷是否已建立檔集合，或是否傳回現有的檔集合。

`CreateDocumentCollectionIfNotExistsAsync`方法也可以選擇性地指定 `RequestOptions` 物件，該物件會封裝可針對發給 Cosmos DB 帳戶的要求指定的選項。 `RequestOptions.OfferThroughput`屬性是用來定義檔集合的效能層級，而在範例應用程式中，會設定為每秒400個要求單位。 您應該根據集合經常存取或不常存取，來增加或減少此值。

> [!IMPORTANT]
> 請注意，此 `CreateDocumentCollectionIfNotExistsAsync` 方法將會使用保留的輸送量來建立新的集合，其具有價格含意。

### <a name="retrieving-document-collection-documents"></a>正在抓取檔集合檔

您可以藉由建立及執行檔查詢來取出檔集合的內容。 使用方法建立檔查詢 `DocumentClient.CreateDocumentQuery` ：

```csharp
public async Task<List<TodoItem>> GetTodoItemsAsync()
{
  ...
  var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
            .AsDocumentQuery();
  while (query.HasMoreResults)
  {
    Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
  }
  ...
}
```

此查詢會以非同步方式從指定的集合中抓取所有檔，並將檔放在 `List<TodoItem>` 集合中以供顯示。

`CreateDocumentQuery<T>`方法會指定一個 `Uri` 引數，表示應該查詢檔的集合。 在此範例中， `collectionLink` 變數是類別層級的欄位，可指定 `Uri` 代表要從中抓取檔的檔集合：

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

`CreateDocumentQuery<T>`方法會建立同步執行的查詢，並傳回 `IQueryable<T>` 物件。 不過，此 `AsDocumentQuery` 方法會將 `IQueryable<T>` 物件轉換成 `IDocumentQuery<T>` 可非同步執行的物件。 非同步查詢會使用 `IDocumentQuery<T>.ExecuteNextAsync` 方法來執行，這個方法會從檔資料庫抓取下一頁結果，而此 `IDocumentQuery<T>.HasMoreResults` 屬性會指出是否要從查詢傳回其他結果。

您可以在查詢中加入子句，以篩選伺服器端的檔 `Where` ，這會針對檔集合將篩選述詞套用至查詢：

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

此查詢會從其屬性等於的集合中取出所有檔 `Done` `false` 。

### <a name="inserting-a-document-into-a-document-collection"></a>將檔插入檔集合中

檔是使用者定義的 JSON 內容，而且可以使用方法插入檔集合中 `DocumentClient.CreateDocumentAsync` ：

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

`CreateDocumentAsync`方法指定的 `Uri` 引數代表應插入檔的集合，以及 `object` 表示要插入之檔的引數。

### <a name="replacing-a-document-in-a-document-collection"></a>取代檔集合中的檔

檔集合中的檔可以使用 `DocumentClient.ReplaceDocumentAsync` 方法取代：

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

`ReplaceDocumentAsync`方法會指定 `Uri` 引數，表示應取代的集合中的檔，以及 `object` 表示更新檔資料的引數。

### <a name="deleting-a-document-from-a-document-collection"></a>從檔集合中刪除檔

您可以使用下列方法，從檔集合中刪除檔 `DocumentClient.DeleteDocumentAsync` ：

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

`DeleteDocumentAsync`方法指定的 `Uri` 引數代表集合中應該刪除的檔。

### <a name="deleting-a-document-collection"></a>刪除檔集合

您可以使用下列方法，從資料庫中刪除檔集合 `DocumentClient.DeleteDocumentCollectionAsync` ：

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

`DeleteDocumentCollectionAsync`方法 `Uri` 會指定引數，表示要刪除的檔集合。 請注意，叫用這個方法也會刪除儲存在集合中的檔。

### <a name="deleting-a-database"></a>刪除資料庫

您可以使用下列方法，從 Cosmos DB 資料庫帳戶中刪除資料庫 `DocumentClient.DeleteDatabaesAsync` ：

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

`DeleteDatabaseAsync`方法會指定一個 `Uri` 引數，表示要刪除的資料庫。 請注意，叫用這個方法也會刪除儲存在資料庫中的檔集合，以及儲存在檔集合中的檔。

## <a name="summary"></a>總結

本文說明如何使用 Azure Cosmos DB .NET Standard 用戶端程式庫，將 Azure Cosmos DB 檔資料庫整合至 Xamarin.Forms 應用程式。 Azure Cosmos DB 檔資料庫是一種 NoSQL 資料庫，可對 JSON 檔提供低延遲的存取，為需要無縫調整和全域複寫的應用程式，提供快速、高可用性、可擴充的資料庫服務。

## <a name="related-links"></a>相關連結

- [Todo Azure Cosmos DB (範例) ](/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)
- [Azure Cosmos DB 文件](/azure/cosmos-db/)
- [Azure Cosmos DB .NET Standard 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [Azure Cosmos DB API](/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)