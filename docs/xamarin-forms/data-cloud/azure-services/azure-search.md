---
title: 使用 Azure 搜尋服務和 Xamarin 來搜尋資料
description: 本文示範如何使用 Microsoft Azure 搜尋程式庫，將 Azure 搜尋服務整合到 Xamarin. Forms 應用程式中。
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
ms.openlocfilehash: cd5aaac0f41ee6e4afd79397a77635e66abad219
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489787"
---
# <a name="search-data-with-azure-search-and-xamarinforms"></a>使用 Azure 搜尋服務和 Xamarin 來搜尋資料

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)

_Azure 搜尋服務是一種雲端服務，可為上傳的資料提供索引編制和查詢功能。這會移除傳統上與在應用程式中執行搜尋功能相關聯的基礎結構需求和搜尋演算法複雜性。本文示範如何使用 Microsoft Azure 搜尋程式庫，將 Azure 搜尋服務整合到 Xamarin. Forms 應用程式中。_

## <a name="overview"></a>概觀

資料會以索引和檔的形式儲存在 Azure 搜尋服務中。 「*索引*」（index）是可由 Azure 搜尋服務服務搜尋的資料存放區，在概念上類似于資料庫資料表。 *檔*是索引中可搜尋資料的單一單位，在概念上與資料庫資料列類似。 上傳檔並提交搜尋查詢到 Azure 搜尋服務時，會向搜尋服務中的特定索引提出要求。

對 Azure 搜尋服務提出的每個要求都必須包含服務的名稱和 API 金鑰。 API 金鑰有兩種類型：

- 系統*管理金鑰*會授與所有作業的完整許可權。 這包括管理服務、建立和刪除索引，以及資料來源。
- *查詢金鑰*會授與索引和檔的唯讀存取權，而且應該由發出搜尋要求的應用程式使用。

最常見的 Azure 搜尋服務要求是執行查詢。 有兩種類型的查詢可以提交：

- *搜尋*查詢會在索引的所有可搜尋欄位中搜尋一或多個專案。 搜尋查詢會使用簡化的語法或 Lucene 查詢語法來建立。 如需詳細資訊，請參閱 Azure 搜尋服務中的[簡單查詢語法](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/)和[Azure 搜尋服務中的 Lucene 查詢語法](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/)。
- *篩選*查詢會對索引中的所有可篩選欄位評估布林運算式。 篩選查詢是使用 OData 篩選語言的子集來建立。 如需詳細資訊，請參閱[Azure 搜尋服務的 OData 運算式語法](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/)。

搜尋查詢和篩選查詢可以單獨或一起使用。 搭配使用時，篩選查詢會先套用至整個索引，然後在篩選查詢的結果上執行搜尋查詢。

Azure 搜尋服務也支援根據搜尋輸入來抓取建議。 如需詳細資訊，請參閱[建議查詢](#suggestions)。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

## <a name="setup"></a>安裝程式

將 Azure 搜尋服務整合到 Xamarin. Forms 應用程式的流程如下所示：

1. 建立 Azure 搜尋服務服務。 如需詳細資訊，請參閱[使用 Azure 入口網站建立 Azure 搜尋服務服務](/azure/search/search-create-service-portal/)。
1. 從 Xamarin. Forms 解決方案的可移植類別庫（PCL）中移除 Silverlight 做為目標架構。 這可以藉由將 PCL 設定檔變更為支援跨平臺開發的設定檔來完成，但不支援 Silverlight，例如設定檔151或設定檔92。
1. 將 [ [Microsoft Azure 搜尋程式庫](https://www.nuget.org/packages/Microsoft.Azure.Search)] NuGet 封裝新增至 [Xamarin] 表單方案中的 PCL 專案。

執行這些步驟之後，您可以使用 Microsoft 搜尋程式庫 API 來管理搜尋索引和資料來源、上傳和管理檔，以及執行查詢。

## <a name="creating-the-azure-search-index"></a>建立 Azure Search 索引

必須定義索引架構，以對應至要搜尋之資料的結構。 這可以在 Azure 入口網站中完成，或以程式設計方式使用 `SearchServiceClient` 類別。 這個類別會管理 Azure 搜尋服務的連接，而且可以用來建立索引。 下列程式碼範例示範如何建立此類別的實例：

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

`SearchServiceClient` 的函式多載會將搜尋服務名稱和 `SearchCredentials` 物件當做引數，並將 `SearchCredentials` 物件包裝 Azure 搜尋服務服務的*管理金鑰*。 建立索引時需要*管理金鑰*。

> [!NOTE]
> 應用程式中應該使用單一 `SearchServiceClient` 實例，以避免開啟太多的 Azure 搜尋服務連接。

索引是由 `Index` 物件所定義，如下列程式碼範例所示：

```csharp
static void CreateSearchIndex()
{
  var index = new Index()
  {
    Name = Constants.Index,
    Fields = new[]
    {
      new Field("id", DataType.String) { IsKey = true, IsRetrievable = true },
      new Field("name", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("location", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("details", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSearchable = true },
      new Field("imageUrl", DataType.String) { IsRetrievable = true }
    },
    Suggesters = new[]
    {
      new Suggester("nameSuggester", SuggesterSearchMode.AnalyzingInfixMatching, new[] { "name" })
    }
  };

  searchClient.Indexes.Create(index);
}
```

`Index.Name` 屬性應該設定為索引的名稱，而且 `Index.Fields` 屬性應該設定為 `Field` 物件的陣列。 每個 `Field` 實例都會指定名稱、類型和任何屬性，以指定欄位的使用方式。 這些屬性包括：

- `IsKey` –指出欄位是否為索引的索引鍵。 索引中只有一個欄位（類型為 `DataType.String`）必須指定為索引鍵欄位。
- `IsFacetable` –指出是否可以在此欄位上執行多面向導覽。 預設值為 `false`。
- `IsFilterable` –指出欄位是否可用於篩選查詢中。 預設值為 `false`。
- `IsRetrievable` –指出是否可在搜尋結果中抓取欄位。 預設值為 `true`。
- `IsSearchable` –指出欄位是否包含在全文檢索搜尋中。 預設值為 `false`。
- `IsSortable` –指出欄位是否可用於 `OrderBy` 運算式中。 預設值為 `false`。

> [!NOTE]
> 在其部署之後變更索引，需要重建和重載資料。

`Index` 物件可以選擇性地指定 `Suggesters` 屬性，以定義索引中用來支援自動完成或搜尋建議查詢的欄位。 `Suggesters` 屬性應該設定為 `Suggester` 物件的陣列，以定義用來建立搜尋建議結果的欄位。

建立 `Index` 物件之後，會藉由呼叫 `SearchServiceClient` 實例上的 `Indexes.Create` 來建立索引。

> [!NOTE]
> 從必須保持回應的應用程式建立索引時，請使用 `Indexes.CreateAsync` 方法。

如需詳細資訊，請參閱[使用 .NET SDK 建立 Azure 搜尋服務索引](/azure/search/search-create-index-dotnet/)。

## <a name="deleting-the-azure-search-index"></a>正在刪除 Azure 搜尋服務索引

您可以藉由呼叫 `SearchServiceClient` 實例上的 `Indexes.Delete` 來刪除索引：

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>將資料上傳至 Azure 搜尋服務索引

定義索引之後，您可以使用下列兩個模型的其中一種，將資料上傳至它：

- **提取模型**–資料會從 Azure 虛擬機器中裝載的 Azure Cosmos DB、Azure SQL Database、Azure Blob 儲存體或 SQL Server 定期進行內嵌。
- **推送模型**–資料會以程式設計方式傳送至索引。 這是本文采用的模型。

必須建立 `SearchIndexClient` 實例，才能將資料匯入至索引。 這可以藉由呼叫 `SearchServiceClient.Indexes.GetClient` 方法來完成，如下列程式碼範例所示：

```csharp
static void UploadDataToSearchIndex()
{
  var indexClient = searchClient.Indexes.GetClient(Constants.Index);

  var monkeyList = MonkeyData.Monkeys.Select(m => new
  {
    id = Guid.NewGuid().ToString(),
    name = m.Name,
    location = m.Location,
    details = m.Details,
    imageUrl = m.ImageUrl
  });

  var batch = IndexBatch.New(monkeyList.Select(IndexAction.Upload));
  try
  {
    indexClient.Documents.Index(batch);
  }
  catch (IndexBatchException ex)
  {
    // Sometimes when the Search service is under load, indexing will fail for some
    // documents in the batch. Compensating actions like delaying and retrying should be taken.
    // Here, the failed document keys are logged.
    Console.WriteLine("Failed to index some documents: {0}",
      string.Join(", ", ex.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
  }
}
```

要匯入至索引的資料會封裝為 `IndexBatch` 物件，以封裝 `IndexAction` 物件的集合。 每個 `IndexAction` 實例都包含一個檔，以及一個屬性，告訴 Azure 搜尋服務要在檔上執行的動作。 在上述的程式碼範例中，會指定 `IndexAction.Upload` 動作，這會導致檔插入至索引（如果是新的），或已取代（如果已經存在）。 然後，`IndexBatch` 物件會藉由在 `SearchIndexClient` 物件上呼叫 `Documents.Index` 方法，傳送至索引。 如需其他索引動作的詳細資訊，請參閱[決定要使用的索引動作](/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)。

> [!NOTE]
> 只有1000檔可以包含在單一索引編制要求中。

請注意，在上述程式碼範例中，`monkeyList` 集合是從 `Monkey` 物件的集合中建立為匿名物件。 這會建立 `id` 欄位的資料，並將 Pascal 大小寫 `Monkey` 屬性名稱的對應解析成 camel case 搜尋索引功能變數名稱。 或者，也可以藉由將 `[SerializePropertyNamesAsCamelCase]` 屬性加入至 `Monkey` 類別來完成這項對應。

如需詳細資訊，請參閱[使用 .NET SDK 將資料上傳至 Azure 搜尋服務](/azure/search/search-import-data-dotnet/)。

## <a name="querying-the-azure-search-index"></a>查詢 Azure 搜尋服務索引

必須建立 `SearchIndexClient` 實例，才能查詢索引。 當應用程式執行查詢時，建議您遵循最低許可權的原則，並直接建立 `SearchIndexClient`，傳遞*查詢索引鍵*做為引數。 這可確保使用者具有索引和檔的唯讀存取權。 這個方法會在下列程式碼範例中示範：

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

`SearchIndexClient` 的函式多載會使用搜尋服務名稱、索引名稱和 `SearchCredentials` 物件做為引數，並將 `SearchCredentials` 物件包裝 Azure 搜尋服務服務的*查詢索引鍵*。

### <a name="search-queries"></a>搜尋查詢

藉由在 `SearchIndexClient` 實例上呼叫 `Documents.SearchAsync` 方法，即可查詢索引，如下列程式碼範例所示：

```csharp
async Task AzureSearch(string text)
{
  Monkeys.Clear();

  var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text);
  foreach (SearchResult<Monkey> result in searchResults.Results)
  {
    Monkeys.Add(new Monkey
    {
      Name = result.Document.Name,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

`SearchAsync` 方法會接受搜尋文字引數，以及可用於進一步精簡查詢的選擇性 `SearchParameters` 物件。 搜尋查詢會指定為搜尋文字引數，而篩選查詢可以藉由設定 `SearchParameters` 引數的 `Filter` 屬性來指定。 下列程式碼範例會示範這兩種查詢類型：

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

此篩選查詢會套用至整個索引，並從結果中移除 [`location`] 欄位不等於 [中國] 而不等於 [越南] 的檔。 篩選之後，會在篩選查詢的結果上執行搜尋查詢。

> [!NOTE]
> 若要篩選而不搜尋，請傳遞 `*` 做為搜尋文字引數。

`SearchAsync` 方法會傳回包含查詢結果的 `DocumentSearchResult` 物件。 系統會列舉此物件，並將每個 `Document` 物件建立為 `Monkey` 物件，並加入至 `Monkeys` `ObservableCollection` 以供顯示。 下列螢幕擷取畫面顯示從 Azure 搜尋服務傳回的搜尋查詢結果：

![](azure-search-images/search.png "Search Results")

如需搜尋和篩選的詳細資訊，請參閱[使用 .NET SDK 查詢您的 Azure 搜尋服務索引](/azure/search/search-query-dotnet/)。

<a name="suggestions" />

### <a name="suggestion-queries"></a>建議查詢

Azure 搜尋服務可在 `SearchIndexClient` 實例上呼叫 `Documents.SuggestAsync` 方法，以根據搜尋查詢來要求建議。 這會在下列程式碼範例中示範：

```csharp
async Task AzureSuggestions(string text)
{
  Suggestions.Clear();

  var parameters = new SuggestParameters()
  {
    UseFuzzyMatching = true,
    HighlightPreTag = "[",
    HighlightPostTag = "]",
    MinimumCoverage = 100,
    Top = 10
  };

  var suggestionResults =
    await indexClient.Documents.SuggestAsync<Monkey>(text, "nameSuggester", parameters);

  foreach (var result in suggestionResults.Results)
  {
    Suggestions.Add(new Monkey
    {
      Name = result.Text,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

`SuggestAsync` 方法會接受搜尋文字引數、要使用的建議工具名稱（定義于索引中），以及可用於進一步精簡查詢的選擇性 `SuggestParameters` 物件。 `SuggestParameters` 實例會設定下列屬性：

- `UseFuzzyMatching` –設定為 [`true`] 時，Azure 搜尋服務將會尋找建議，即使搜尋文字中有替代或遺漏的字元也一樣。
- `HighlightPreTag` –前面加上建議點擊的標記。
- `HighlightPostTag` –附加至建議點擊的標記。
- `MinimumCoverage` –代表必須涵蓋的索引百分比，以便讓查詢回報成功的建議查詢。 預設值為80。
- `Top` –要取得的建議數目。 它必須是介於1到100之間的整數，預設值為5。

整體效果是，會傳回索引的前10個結果，並出現反白顯示，而結果會包含包含類似拼寫之搜尋詞彙的檔。

`SuggestAsync` 方法會傳回包含查詢結果的 `DocumentSuggestResult` 物件。 系統會列舉此物件，並將每個 `Document` 物件建立為 `Monkey` 物件，並加入至 `Monkeys` `ObservableCollection` 以供顯示。 下列螢幕擷取畫面顯示從 Azure 搜尋服務傳回的建議結果：

![](azure-search-images/suggest.png "Suggestion Results")

請注意，在範例應用程式中，只有在使用者完成輸入搜尋字詞時，才會叫用 `SuggestAsync` 方法。 不過，它也可以藉由在每個按鍵上執行，來支援自動完成搜尋查詢。

## <a name="summary"></a>總結

本文示範如何使用 Microsoft Azure 搜尋程式庫，將 Azure 搜尋服務整合到 Xamarin. Forms 應用程式中。 Azure 搜尋服務是一種雲端服務，可為上傳的資料提供索引編制和查詢功能。 這會移除傳統上與在應用程式中執行搜尋功能相關聯的基礎結構需求和搜尋演算法複雜性。

## <a name="related-links"></a>相關連結

- [Azure 搜尋服務（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)
- [Azure 搜尋服務檔](/azure/search/)
- [Microsoft Azure 搜尋程式庫](https://www.nuget.org/packages/Microsoft.Azure.Search/)
