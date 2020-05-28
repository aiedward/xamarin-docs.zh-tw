---
title: 使用中的 Azure Cosmos DB 檔資料庫Xamarin.Forms
description: 本文說明如何使用 Azure Cosmos DB .NET Standard 用戶端程式庫，將 Azure Cosmos DB 檔資料庫整合到 Xamarin.Forms 應用程式中。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
ms.custom: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 47b35d394eab339a8e9a1f81880e6de4233f29b6
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127080"
---
# <a name="consume-an-azure-cosmos-db-document-database-in-xamarinforms"></a>使用中的 Azure Cosmos DB 檔資料庫Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)

_Azure Cosmos DB 檔資料庫是一種 NoSQL 資料庫，可提供對 JSON 檔的低延遲存取，為需要順暢調整和全域複寫的應用程式提供快速、高可用性、可擴充的資料庫服務。本文說明如何使用 Azure Cosmos DB .NET Standard 用戶端程式庫，將 Azure Cosmos DB 檔資料庫整合到 Xamarin.Forms 應用程式中。_

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Microsoft Azure Cosmos DB 影片**

您可以使用 Azure 訂用帳戶來布建 Azure Cosmos DB 檔資料庫帳戶。 每個資料庫帳戶都可以有零個或多個資料庫。 Azure Cosmos DB 中的檔資料庫是檔集合和使用者的邏輯容器。

Azure Cosmos DB 檔資料庫可能包含零個或多個檔集合。 每個檔集合可以有不同的效能層級，允許為經常存取的集合指定更多的輸送量，而不常存取的集合則具有較少的輸送量。

每個檔集合都包含零個或多個 JSON 檔。 集合中的檔是無架構的，因此不需要共用相同的結構或欄位。 當檔新增至檔集合時，Cosmos DB 會自動編制它們的索引，而且它們會變成可供查詢。

基於開發目的，檔資料庫也可以透過模擬器取用。 使用模擬器，應用程式可以在本機開發和測試，而不需建立 Azure 訂用帳戶，也不會產生任何費用。 如需模擬器的詳細資訊，請參閱[使用 Azure Cosmos DB 模擬器在本機進行開發](/azure/cosmos-db/local-emulator/)。

本文和隨附的範例應用程式會示範 Todo 清單應用程式，其中的工作會儲存在 Azure Cosmos DB 的檔資料庫中。 如需範例應用程式的詳細資訊，請參閱[瞭解範例](~/xamarin-forms/data-cloud/web-services/introduction.md)。

如需 Azure Cosmos DB 的詳細資訊，請參閱[Azure Cosmos DB 檔](/azure/cosmos-db/)。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

## <a name="setup"></a>安裝程式

將 Azure Cosmos DB 檔資料庫整合到應用程式的流程如下所示 Xamarin.Forms ：

1. 建立 Cosmos DB 帳戶。 如需詳細資訊，請參閱[建立 Azure Cosmos DB 帳戶](/azure/cosmos-db/sql-api-dotnetcore-get-started#create-an-azure-cosmos-account)。
1. 將[Azure Cosmos DB .NET Standard 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)NuGet 套件新增至方案中的平臺專案 Xamarin.Forms 。
1. 將 `using` `Microsoft.Azure.Documents` 、和命名空間的指示詞新增 `Microsoft.Azure.Documents.Client` `Microsoft.Azure.Documents.Linq` 至將會存取 Cosmos DB 帳戶的類別。

執行這些步驟之後，可以使用 Azure Cosmos DB .NET Standard 用戶端程式庫來設定和執行檔資料庫的要求。

> [!NOTE]
> Azure Cosmos DB .NET Standard 用戶端程式庫只能安裝到平臺專案，而不是可移植的類別庫（PCL）專案中。 因此，範例應用程式是一個共用存取專案（SAP），以避免程式碼重複。 不過，您 `DependencyService` 可以在 PCL 專案中使用類別，以叫用平臺特定專案中所包含的 Azure Cosmos DB .NET Standard 用戶端程式庫程式碼。

## <a name="consuming-the-azure-cosmos-db-account"></a>使用 Azure Cosmos DB 帳戶

`DocumentClient`類型會封裝用來存取 Azure Cosmos DB 帳戶的端點、認證和連線原則，並用於設定及執行帳戶的要求。 下列程式碼範例示範如何建立此類別的實例：

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

必須提供 Cosmos DB Uri 和主要金鑰給此函式 `DocumentClient` 。 這些可以從 Azure 入口網站取得。 如需詳細資訊，請參閱[連接到 Azure Cosmos DB 帳戶](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect)。

### <a name="creating-a-database"></a>建立資料庫

檔資料庫是檔集合和使用者的邏輯容器，而且可以在 Azure 入口網站中建立，或以程式設計方式使用 `DocumentClient.CreateDatabaseIfNotExistsAsync` 方法：

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

方法會將 `CreateDatabaseIfNotExistsAsync` `Database` 物件指定為引數，並使用 `Database` 物件將資料庫名稱指定為其 `Id` 屬性。 `CreateDatabaseIfNotExistsAsync`方法會建立資料庫（如果不存在），或傳回資料庫（如果已經存在的話）。 不過，範例應用程式會忽略方法所傳回的任何資料 `CreateDatabaseIfNotExistsAsync` 。

> [!NOTE]
> `CreateDatabaseIfNotExistsAsync`方法 `Task<ResourceResponse<Database>>` 會傳回物件，而且可以檢查回應的狀態碼，以判斷資料庫是否已建立，或是否已傳回現有的資料庫。

### <a name="creating-a-document-collection"></a>建立檔集合

檔集合是 JSON 檔的容器，可以在 Azure 入口網站中建立，或以程式設計方式使用 `DocumentClient.CreateDocumentCollectionIfNotExistsAsync` 方法：

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

`CreateDocumentCollectionIfNotExistsAsync`方法需要兩個強制引數–指定為的資料庫名稱 `Uri` ，以及 `DocumentCollection` 物件。 `DocumentCollection`物件代表其名稱是以屬性指定的檔集合 `Id` 。 `CreateDocumentCollectionIfNotExistsAsync`方法會建立檔集合（如果不存在），或傳回檔集合（如果已經存在）。 不過，範例應用程式會忽略方法所傳回的任何資料 `CreateDocumentCollectionIfNotExistsAsync` 。

> [!NOTE]
> `CreateDocumentCollectionIfNotExistsAsync`方法 `Task<ResourceResponse<DocumentCollection>>` 會傳回物件，而且可以檢查回應的狀態碼，以判斷是否已建立檔集合，或是否傳回現有的檔集合。

（選擇性）此 `CreateDocumentCollectionIfNotExistsAsync` 方法也可以指定 `RequestOptions` 物件，它會封裝可針對發出給 Cosmos DB 帳戶之要求指定的選項。 `RequestOptions.OfferThroughput`屬性是用來定義檔集合的效能層級，在範例應用程式中，會設定為每秒400個要求單位。 視集合是否經常或不常存取而定，此值應該增加或減少。

> [!IMPORTANT]
> 請注意，此 `CreateDocumentCollectionIfNotExistsAsync` 方法會使用保留的輸送量來建立新的集合，這會影響價格。

<a name="document_query" />

### <a name="retrieving-document-collection-documents"></a>正在抓取檔集合檔

您可以藉由建立和執行檔查詢來抓取檔集合的內容。 使用方法建立檔查詢 `DocumentClient.CreateDocumentQuery` ：

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

`CreateDocumentQuery<T>`方法會指定 `Uri` 引數，表示應查詢檔的集合。 在此範例中， `collectionLink` 變數是類別層級欄位，它會指定 `Uri` 代表要從其中抓取檔的檔集合：

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

`CreateDocumentQuery<T>`方法會建立以同步方式執行的查詢，並傳回 `IQueryable<T>` 物件。 不過， `AsDocumentQuery` 方法會將 `IQueryable<T>` 物件轉換成 `IDocumentQuery<T>` 可以非同步執行的物件。 非同步查詢是以 `IDocumentQuery<T>.ExecuteNextAsync` 方法執行，它會從檔資料庫中抓取下一頁的結果，並使用 `IDocumentQuery<T>.HasMoreResults` 屬性來指出查詢是否有其他結果要傳回。

您可以在查詢中包含子句，以篩選伺服器端的檔 `Where` ，這會將篩選述詞套用至檔集合的查詢：

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

此查詢會從其屬性等於的集合中抓取所有檔 `Done` `false` 。

<a name="inserting_document" />

### <a name="inserting-a-document-into-a-document-collection"></a>將檔插入檔集合

檔是使用者定義的 JSON 內容，可以使用方法插入檔集合 `DocumentClient.CreateDocumentAsync` ：

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

`CreateDocumentAsync`方法會指定 `Uri` 引數，代表要插入檔的集合，以及表示 `object` 要插入之檔的引數。

### <a name="replacing-a-document-in-a-document-collection"></a>取代檔集合中的檔

檔集合中的檔可以使用方法來取代 `DocumentClient.ReplaceDocumentAsync` ：

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

`ReplaceDocumentAsync`方法會指定 `Uri` 引數，表示應取代之集合中的檔，以及 `object` 表示已更新檔資料的引數。

<a name="deleting_document" />

### <a name="deleting-a-document-from-a-document-collection"></a>從檔集合中刪除檔

您可以使用方法，從檔集合中刪除檔 `DocumentClient.DeleteDocumentAsync` ：

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

`DeleteDocumentAsync`方法會指定 `Uri` 引數，表示應刪除之集合中的檔。

### <a name="deleting-a-document-collection"></a>刪除檔集合

您可以使用方法，從資料庫中刪除檔集合 `DocumentClient.DeleteDocumentCollectionAsync` ：

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

`DeleteDocumentCollectionAsync`方法 `Uri` 會指定引數，表示要刪除的檔集合。 請注意，叫用這個方法也會刪除儲存在集合中的檔。

### <a name="deleting-a-database"></a>刪除資料庫

您可以使用方法，從 Cosmos DB 資料庫帳戶中刪除資料庫 `DocumentClient.DeleteDatabaesAsync` ：

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

`DeleteDatabaseAsync`方法 `Uri` 會指定引數，表示要刪除的資料庫。 請注意，叫用這個方法也會刪除儲存在資料庫中的檔集合，以及儲存在檔集合中的檔。

## <a name="summary"></a>摘要

本文說明如何使用 Azure Cosmos DB .NET Standard 用戶端程式庫，將 Azure Cosmos DB 檔資料庫整合到 Xamarin.Forms 應用程式中。 Azure Cosmos DB 檔資料庫是一種 NoSQL 資料庫，可提供對 JSON 檔的低延遲存取，為需要順暢調整和全域複寫的應用程式提供快速、高可用性、可擴充的資料庫服務。

## <a name="related-links"></a>相關連結

- [Todo Azure Cosmos DB （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)
- [Azure Cosmos DB 文件](/azure/cosmos-db/)
- [Azure Cosmos DB .NET Standard 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [Azure Cosmos DB API](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)
