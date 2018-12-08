---
title: 使用 Azure Cosmos DB 文件資料庫
description: 這篇文章說明如何使用 Azure Cosmos DB.NET Standard 用戶端程式庫，若要將 Xamarin.Forms 應用程式中整合 Azure Cosmos DB 文件資料庫。
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 79547277b00ae1f1d9b035d5fb08685562cefc79
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052578"
---
# <a name="consuming-an-azure-cosmos-db-document-database"></a>使用 Azure Cosmos DB 文件資料庫

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDB/)

_Azure Cosmos DB 文件資料庫是提供低延遲存取 JSON 文件，提供快速、 高可用性、 可調整的資料庫服務的應用程式，需要能夠順暢調整和全域複寫的 NoSQL 資料庫。這篇文章說明如何使用 Azure Cosmos DB.NET Standard 用戶端程式庫，若要將 Xamarin.Forms 應用程式中整合 Azure Cosmos DB 文件資料庫。_

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Microsoft Azure Cosmos DB、 藉由[Xamarin University](https://university.xamarin.com/)**

Azure Cosmos DB 文件資料庫帳戶可以使用 Azure 訂用帳戶佈建。 每個資料庫帳戶可以有零個或多個資料庫。 Azure Cosmos DB 中的文件資料庫是文件集合和使用者的邏輯容器。

Azure Cosmos DB 文件資料庫可能包含零個或多個文件集合。 每個文件集合可以有不同的效能層級，允許更多的輸送量為經常存取的集合，指定與不常存取的集合的輸送量較少。

每個文件集合是由零或多個 JSON 文件所組成。 集合中的文件是無結構描述，並因此不需要共用相同的結構或欄位。 文件加入文件集合，Cosmos DB 自動編製索引，以及它們會變成可用來查詢。

基於開發目的，文件資料庫可以取用透過模擬器。 使用模擬器，應用程式開發和測試在本機，不需建立 Azure 訂用帳戶，也不會產生任何費用。 如需有關模擬器的詳細資訊，請參閱 <<c0> [ 搭配 Azure Cosmos DB 模擬器在本機開發](/azure/cosmos-db/local-emulator/)。

本文章中，和隨附的範例應用程式，示範 Azure Cosmos DB 文件資料庫中儲存工作的待辦事項清單應用程式。 如需範例應用程式的詳細資訊，請參閱[了解範例](~/xamarin-forms/data-cloud/walkthrough.md)。

如需有關 Azure Cosmos DB 的詳細資訊，請參閱 < [Azure Cosmos DB 文件](/azure/cosmos-db/)。

## <a name="setup"></a>安裝程式

將 Azure Cosmos DB 文件資料庫整合至 Xamarin.Forms 應用程式的程序如下所示：

1. 建立 Cosmos DB 帳戶。 如需詳細資訊，請參閱 <<c0> [ 建立 Azure Cosmos DB 帳戶](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account)。
1. 新增[Azure Cosmos DB.NET Standard 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)平台專案中的 Xamarin.Forms 方案的 NuGet 套件。
1. 新增`using`指示詞`Microsoft.Azure.Documents`， `Microsoft.Azure.Documents.Client`，和`Microsoft.Azure.Documents.Linq`命名空間，以存取 Cosmos DB 帳戶的類別。

執行這些步驟之後，Azure Cosmos DB.NET Standard 用戶端程式庫可用來設定和執行對文件資料庫的要求。

> [!NOTE]
> Azure Cosmos DB.NET Standard 用戶端程式庫只能安裝到平台專案，而不可攜式類別庫 (PCL) 專案。 因此，範例應用程式是共用存取專案 (SAP) 以避免程式碼重複。 不過，`DependencyService`類別可以叫用 Azure Cosmos DB.NET Standard 平台專屬專案中所包含的用戶端程式庫程式碼使用 PCL 專案中。

## <a name="consuming-the-azure-cosmos-db-account"></a>使用 Azure Cosmos DB 帳戶

`DocumentClient`類型封裝端點、 認證和連線原則，用來存取 Azure Cosmos DB 帳戶，並用來設定和執行對帳戶的要求。 下列程式碼範例示範如何建立此類別的執行個體：

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

Cosmos DB Uri 和主索引鍵必須提供給`DocumentClient`建構函式。 這些可以從 Azure 入口網站取得。 如需詳細資訊，請參閱 <<c0> [ 連接到 Azure Cosmos DB 帳戶](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect)。

### <a name="creating-a-database"></a>建立資料庫

文件資料庫是使用者以及文件集合的邏輯容器，而且可以是建立在 Azure 入口網站中，或以程式設計方式使用`DocumentClient.CreateDatabaseIfNotExistsAsync`方法：

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

`CreateDatabaseIfNotExistsAsync`方法指定`Database`物件做為引數，`Database`物件，指定資料庫名稱，做為其`Id`屬性。 `CreateDatabaseIfNotExistsAsync`方法會建立資料庫，如果它不存在，或如果已經存在，會傳回資料庫。 不過，範例應用程式會忽略所傳回的任何資料`CreateDatabaseIfNotExistsAsync`方法。

> [!NOTE]
> `CreateDatabaseIfNotExistsAsync`方法會傳回`Task<ResourceResponse<Database>>`物件和回應的狀態碼可以檢查以判斷是否已建立資料庫，或傳回現有的資料庫。

### <a name="creating-a-document-collection"></a>建立文件集合

文件集合是 JSON 文件的容器，而且可以是建立在 Azure 入口網站中，或以程式設計方式使用`DocumentClient.CreateDocumentCollectionIfNotExistsAsync`方法：

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

`CreateDocumentCollectionIfNotExistsAsync`方法需要兩個強制的引數 – 資料庫名稱指定為`Uri`，和`DocumentCollection`物件。 `DocumentCollection`物件表示文件集合，指定其名稱，但`Id`屬性。 `CreateDocumentCollectionIfNotExistsAsync`方法會建立文件集合，如果它不存在，或如果已經存在，會傳回文件集合。 不過，範例應用程式會忽略所傳回的任何資料`CreateDocumentCollectionIfNotExistsAsync`方法。

> [!NOTE]
> `CreateDocumentCollectionIfNotExistsAsync`方法會傳回`Task<ResourceResponse<DocumentCollection>>`物件和回應的狀態碼可以檢查以判斷是否已建立的文件集合，或傳回現有的文件集合。

（選擇性）`CreateDocumentCollectionIfNotExistsAsync`方法也可以指定`RequestOptions`物件，封裝可以針對至 Cosmos DB 帳戶發出的要求指定的選項。 `RequestOptions.OfferThroughput`屬性用來定義文件集合的效能層級，並在此範例應用程式中，會設定為每秒 400 個要求單位。 此值應該增加或減少取決於是否會頻繁或不常存取集合。

> [!IMPORTANT]
> 請注意，`CreateDocumentCollectionIfNotExistsAsync`方法會建立含有保留輸送量且具有價格含意的新集合。

<a name="document_query" />

### <a name="retrieving-document-collection-documents"></a>擷取文件集合的文件

建立和執行文件查詢可以擷取文件集合的內容。 建立文件查詢`DocumentClient.CreateDocumentQuery`方法：

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

這項查詢以非同步方式從指定的集合中擷取的所有文件，並將放在文件`List<TodoItem>`顯示的集合。

`CreateDocumentQuery<T>`方法指定`Uri`引數表示應該查詢的文件的集合。 在此範例中，`collectionLink`變數是類別層級的欄位，指定`Uri`，表示文件集合來擷取從的文件：

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

`CreateDocumentQuery<T>`方法會建立查詢，會同步執行，並傳回`IQueryable<T>`物件。 不過，`AsDocumentQuery`方法將`IQueryable<T>`物件到`IDocumentQuery<T>`物件以非同步方式執行。 使用執行非同步查詢`IDocumentQuery<T>.ExecuteNextAsync`方法，來擷取下一頁的結果文件資料庫中，指定具有`IDocumentQuery<T>.HasMoreResults`屬性指出是否要從查詢傳回的其他結果。

文件可以是篩選伺服器端包含`Where`在查詢中，套用篩選述詞來查詢文件集合的子句：

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

此查詢會擷取所有文件： 集合中其`Done`屬性等於`false`。

<a name="inserting_document" />

### <a name="inserting-a-document-into-a-document-collection"></a>插入的文件集合中的文件

文件是使用者定義 JSON 內容，並可插入的文件集合`DocumentClient.CreateDocumentAsync`方法：

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

`CreateDocumentAsync`方法指定`Uri`引數，表示文件應插入的集合和`object`引數，表示要插入文件。

### <a name="replacing-a-document-in-a-document-collection"></a>取代文件集合中的文件

文件可以取代文件集合中具有`DocumentClient.ReplaceDocumentAsync`方法：

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

`ReplaceDocumentAsync`方法指定`Uri`引數表示應該取代集合中的文件和`object`代表更新的文件資料的引數。

<a name="deleting_document" />

### <a name="deleting-a-document-from-a-document-collection"></a>從 文件集合中刪除文件

文件可以從集合中刪除文件與`DocumentClient.DeleteDocumentAsync`方法：

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

`DeleteDocumentAsync`方法指定`Uri`引數表示應該刪除集合中的文件。

### <a name="deleting-a-document-collection"></a>刪除文件集合

可以從資料庫刪除的文件集合`DocumentClient.DeleteDocumentCollectionAsync`方法：

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

`DeleteDocumentCollectionAsync`方法指定`Uri`引數，表示要刪除的文件集合。 請注意，叫用這個方法也會刪除儲存在集合中的文件。

### <a name="deleting-a-database"></a>刪除資料庫

資料庫可以從與 Cosmos DB 資料庫帳戶中刪除`DocumentClient.DeleteDatabaesAsync`方法：

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

`DeleteDatabaseAsync`方法指定`Uri`引數，表示要刪除的資料庫。 請注意，叫用這個方法也會刪除儲存在資料庫中，文件集合和文件集合中儲存的文件。

## <a name="summary"></a>總結

這篇文章說明如何使用 Azure Cosmos DB.NET Standard 的用戶端程式庫來整合 Xamarin.Forms 應用程式中的 Azure Cosmos DB 文件資料庫。 Azure Cosmos DB 文件資料庫是提供低延遲存取 JSON 文件，提供快速、 高可用性、 可調整的資料庫服務的應用程式，需要能夠順暢調整和全域複寫的 NoSQL 資料庫。


## <a name="related-links"></a>相關連結

- [Todo Azure Cosmos DB （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDB/)
- [Azure Cosmos DB 文件](/azure/cosmos-db/)
- [Azure Cosmos DB.NET Standard 的用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [Azure Cosmos DB API](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)
