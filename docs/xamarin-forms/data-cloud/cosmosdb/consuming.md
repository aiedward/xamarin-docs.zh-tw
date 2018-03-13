---
title: "使用 Azure Cosmos DB 文件資料庫"
description: "Azure Cosmos DB 文件資料庫是提供低延遲存取 JSON 文件，提供快速、 高可用性的可擴充資料庫服務，無縫式的小數位數和全域複寫所需要的應用程式的 NoSQL 資料庫。 本文說明如何將 Azure Cosmos DB 文件資料庫整合 Xamarin.Forms 應用程式使用 Microsoft Azure DocumentDB 的用戶端程式庫。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 5013b35828cecc2e38600839f306f3c0fc1366b9
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="consuming-an-azure-cosmos-db-document-database"></a>使用 Azure Cosmos DB 文件資料庫

_Azure Cosmos DB 文件資料庫是提供低延遲存取 JSON 文件，提供快速、 高可用性的可擴充資料庫服務，無縫式的小數位數和全域複寫所需要的應用程式的 NoSQL 資料庫。本文說明如何將 Azure Cosmos DB 文件資料庫整合 Xamarin.Forms 應用程式使用 Microsoft Azure DocumentDB 的用戶端程式庫。_

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Microsoft Azure Cosmos DB、 藉由[Xamarin 大學](https://university.xamarin.com/)**

Azure Cosmos DB 文件資料庫帳戶可以使用 Azure 訂用帳戶佈建。 每個資料庫帳戶可以有零個或多個資料庫。 文件中的資料庫 Azure Cosmos DB 是以文件集合和使用者的邏輯容器。

Azure Cosmos DB 文件資料庫可能包含零個或多個文件集合。 每個文件集合可以有不同的效能層級，允許對於經常存取的集合，指定更多的輸送量，而較少的輸送量，對於不常存取的集合。

每個文件集合是由零或多個 JSON 文件所組成。 集合中的文件是無結構描述，並因此不需要共用相同的結構或欄位。 當文件加入文件集合，Cosmos DB 會自動索引以及可用來查詢它們。

供開發應用程式，文件資料庫可使用透過模擬器。 使用模擬器，應用程式可以是開發及測試在本機，而不需要建立 Azure 訂用帳戶，或支付任何費用。 如需模擬器的詳細資訊，請參閱[使用 Azure Cosmos DB 模擬器在本機開發](/azure/cosmos-db/local-emulator/)。

本文中，以及隨附的範例應用程式，示範 Todo 清單的應用程式，Azure Cosmos DB 文件資料庫中儲存工作。 如需範例應用程式的詳細資訊，請參閱[了解範例](~/xamarin-forms/data-cloud/walkthrough.md)。

> [!NOTE]
> DocumentDB 的用戶端程式庫目前不是與通用 Windows 平台 (UWP) 應用程式相容。 不過，Azure Cosmos DB 文件資料庫可以從 UWP 應用程式使用，藉由建立中間層 web 服務使用 DocumentDB 用戶端程式庫，並叫用此服務，從 UWP 應用程式。

如需 Azure Cosmos DB 的詳細資訊，請參閱[Azure Cosmos DB 文件集](/azure/cosmos-db/)。

## <a name="setup"></a>安裝程式

將 Azure Cosmos DB 文件資料庫整合至 Xamarin.Forms 應用程式的程序如下所示：

1. 建立 Cosmos DB 帳戶。 如需詳細資訊，請參閱[建立 Cosmos DB 帳戶](/azure/cosmos-db/documentdb-dotnetcore-get-started#step-1-create-a-documentdb-account)。
1. 新增[DocumentDB 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)平台專案，在 Xamarin.Forms 方案的 NuGet 封裝。
1. 新增`using`指示詞`Microsoft.Azure.Documents`， `Microsoft.Azure.Documents.Client`，和`Microsoft.Azure.Documents.Linq`命名空間，以存取 Cosmos DB 帳戶的類別。

執行這些步驟之後，DocumentDB 用戶端程式庫可用來設定與執行對文件資料庫要求。

> [!NOTE]
> Azure DocumentDB 的用戶端程式庫只能安裝到平台專案，並不到可攜式類別程式庫 (PCL) 專案。 因此，範例應用程式是共用存取專案 (SAP) 以避免程式碼重複。 不過，`DependencyService`類別可以叫用平台專屬專案中所包含的 Azure DocumentDB 用戶端程式庫程式碼使用 PCL 專案中。

## <a name="consuming-the-azure-cosmos-db-account"></a>使用 Azure Cosmos DB 帳戶

`DocumentClient`類型封裝端點、 認證和連線原則用來存取 Azure Cosmos DB 帳戶，並用來設定與執行對帳戶的要求。 下列程式碼範例示範如何建立此類別的執行個體：

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

Cosmos DB Uri 和主索引鍵必須提供給`DocumentClient`建構函式。 這些可以從 Azure 入口網站取得。 如需詳細資訊，請參閱[連接至 Azure Cosmos DB 帳戶](/azure/cosmos-db/documentdb-dotnetcore-get-started#a-idconnectastep-3-connect-to-an-azure-cosmos-db-account)。

### <a name="creating-a-database"></a>建立資料庫

文件資料庫是使用者以及文件集合的邏輯容器，而且可以是建立在 Azure 入口網站，或以程式設計方式使用`DocumentClient.CreateDatabaseIfNotExistsAsync`方法：

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

`CreateDatabaseIfNotExistsAsync`方法指定`Database`物件做為引數，`Database`物件，指定資料庫名稱，做為其`Id`屬性。 `CreateDatabaseIfNotExistsAsync`方法會建立資料庫，如果它不存在，或如果已經存在，則傳回的資料庫。 不過，範例應用程式會忽略所傳回的任何資料`CreateDatabaseIfNotExistsAsync`方法。

> [!NOTE]
> `CreateDatabaseIfNotExistsAsync`方法會傳回`Task<ResourceResponse<Database>>`物件和回應的狀態碼可以檢查來判斷是否已建立的資料庫，或傳回現有的資料庫。

### <a name="creating-a-document-collection"></a>建立文件的集合

文件集合是 JSON 文件的容器，而且可以是建立在 Azure 入口網站，或以程式設計方式使用`DocumentClient.CreateDocumentCollectionIfNotExistsAsync`方法：

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

`CreateDocumentCollectionIfNotExistsAsync`方法需要兩個強制引數 – 資料庫名稱指定為`Uri`，和`DocumentCollection`物件。 `DocumentCollection`物件代表具有指定名稱的文件集合`Id`屬性。 `CreateDocumentCollectionIfNotExistsAsync`方法會建立文件集合，如果它不存在，或如果已經存在會傳回文件集合。 不過，範例應用程式會忽略所傳回的任何資料`CreateDocumentCollectionIfNotExistsAsync`方法。

> [!NOTE]
> `CreateDocumentCollectionIfNotExistsAsync`方法會傳回`Task<ResourceResponse<DocumentCollection>>`物件和回應的狀態碼可以檢查來判斷是否已建立文件集合，或傳回現有的文件集合。

（選擇性）`CreateDocumentCollectionIfNotExistsAsync`方法也可以指定`RequestOptions`物件，此封裝所指定的 Cosmos DB 帳戶發出的要求選項物件。 `RequestOptions.OfferThroughput`屬性用來定義文件集合的效能層級，並在此範例應用程式中，會設定為 400 要求每秒單位數。 這個值應該增加或減少根據是否會經常或不常存取集合。

> [!IMPORTANT]
> 請注意，`CreateDocumentCollectionIfNotExistsAsync`方法會使用保留的輸送量，具有定價影響建立新的集合。

<a name="document_query" />

### <a name="retrieving-document-collection-documents"></a>擷取文件集合的文件

建立及執行文件查詢可以擷取文件集合的內容。 文件建立一個查詢是以`DocumentClient.CreateDocumentQuery`方法：

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

此查詢以非同步方式從指定集合中擷取所有文件，並將放在文件`List<TodoItem>`顯示的集合。

`CreateDocumentQuery<T>`方法指定`Uri`引數表示應該查詢的文件的集合。 在此範例中，`collectionLink`變數是類別層級的欄位，指定`Uri`，代表要擷取的文件的文件集合：

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

`CreateDocumentQuery<T>`方法建立的查詢執行時同步執行，並傳回`IQueryable<T>`物件。 不過，`AsDocumentQuery`方法轉換`IQueryable<T>`物件`IDocumentQuery<T>`可以非同步執行的物件。 使用執行非同步查詢`IDocumentQuery<T>.ExecuteNextAsync`方法，來擷取下一頁結果文件資料庫中，從具有`IDocumentQuery<T>.HasMoreResults`屬性表示是否要從查詢傳回其他結果。

文件可以是伺服器端篩選包含`Where`子句在查詢中，篩選述詞套用至針對文件集合的查詢：

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

此查詢會擷取所有文件： 集合中其`Done`屬性等於`false`。

<a name="inserting_document" />

### <a name="inserting-a-document-into-a-document-collection"></a>插入文件集合中的文件

文件是使用者定義的 JSON 內容，並可以插入到文件集合與`DocumentClient.CreateDocumentAsync`方法：

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

`CreateDocumentAsync`方法指定`Uri`引數表示應該將，插入文件的集合和`object`引數，表示要插入文件。

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

`ReplaceDocumentAsync`方法指定`Uri`引數表示應該取代集合中的文件和`object`引數代表更新的文件資料。

<a name="deleting_document" />

### <a name="deleting-a-document-from-a-document-collection"></a>從文件集合中刪除文件

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

文件集合可以從資料庫刪除`DocumentClient.DeleteDocumentCollectionAsync`方法：

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

`DeleteDocumentCollectionAsync`方法指定`Uri`引數代表要刪除的文件集合。 請注意，叫用這個方法也會刪除儲存在集合中的文件。

### <a name="deleting-a-database"></a>刪除資料庫

資料庫可以從 Cosmos DB 資料庫帳戶與刪除`DocumentClient.DeleteDatabaesAsync`方法：

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

`DeleteDatabaseAsync`方法指定`Uri`引數，代表要刪除的資料庫。 請注意，叫用這個方法也會刪除儲存在資料庫中，文件集合和文件集合中儲存的文件。

## <a name="summary"></a>總結

本文說明如何將 Azure Cosmos DB 文件資料庫整合 Xamarin.Forms 應用程式使用 Microsoft Azure DocumentDB 的用戶端程式庫。 Azure Cosmos DB 文件資料庫是提供低延遲存取 JSON 文件，提供快速、 高可用性的可擴充資料庫服務，無縫式的小數位數和全域複寫所需要的應用程式的 NoSQL 資料庫。


## <a name="related-links"></a>相關連結

- [TodoDocumentDB (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDB/)
- [Cosmos DB 文件](/azure/cosmos-db/)
- [DocumentDB 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [Azure Cosmos DB API](https://msdn.microsoft.com/library/azure/dn948556.aspx)
