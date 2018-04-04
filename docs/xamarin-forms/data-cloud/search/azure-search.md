---
title: 使用 Azure 搜尋搜尋資料
description: Azure 搜尋是雲端服務可提供索引和查詢功能上傳的資料。 這會移除基礎結構需求和傳統上相關聯的應用程式中實作搜尋功能搜尋演算法的複雜度。 本文示範如何使用 Azure 搜尋整合 Xamarin.Forms 應用程式的 Microsoft Azure 搜尋程式庫。
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
ms.openlocfilehash: b0542b330e54a41a0cbe6ffe364def78ab6386b8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="searching-data-with-azure-search"></a>使用 Azure 搜尋搜尋資料

_Azure 搜尋是雲端服務可提供索引和查詢功能上傳的資料。這會移除基礎結構需求和傳統上相關聯的應用程式中實作搜尋功能搜尋演算法的複雜度。本文示範如何使用 Azure 搜尋整合 Xamarin.Forms 應用程式的 Microsoft Azure 搜尋程式庫。_

## <a name="overview"></a>總覽

資料會儲存在 Azure 搜尋中，做為索引和文件。 *索引*是 Azure 搜尋服務，可搜尋的資料存放區，並在概念上類似資料庫資料表。 A*文件*是單一單位的可搜尋索引中資料，並在概念上類似於資料庫資料列。 當上傳文件提交至 Azure 搜尋的搜尋查詢，便會要求對特定索引中搜尋服務。

對 Azure 搜尋每個要求必須包含服務名稱，以及 API 金鑰。 有兩種類型的 API 金鑰：

- *系統管理金鑰*授與所有作業的完整權限。 這包括管理服務、 建立和刪除索引和資料來源。
- *查詢索引鍵*唯讀存取索引和文件，以及發出搜尋要求的應用程式應該使用。

Azure 搜尋最常見的要求正在執行查詢。 有兩種可以提交的查詢：

- A*搜尋*查詢會搜尋搜尋索引中的所有可搜尋欄位中的一個或多個項目。 搜尋查詢，會使用簡化的語法或 Lucene 的查詢語法所建置的。 如需詳細資訊，請參閱[Azure 搜尋中的簡單查詢語法](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/)，和[Lucene 的查詢語法，在 Azure 搜尋](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/)。
- A*篩選*查詢會針對索引中的所有可篩選欄位評估的布林運算式。 使用 OData 篩選器語言的子集建立篩選查詢。 如需詳細資訊，請參閱[Azure 搜尋的 OData 運算式語法](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/)。

搜尋查詢及篩選查詢可以分開使用或一起。 篩選查詢一起使用時，會先套用到整個索引，然後搜尋查詢不會對篩選條件查詢的結果。

Azure 搜尋也支援根據搜尋輸入擷取建議。 如需詳細資訊，請參閱[建議查詢](#suggestions)。

## <a name="setup"></a>安裝程式

Azure 搜尋整合 Xamarin.Forms 應用程式的程序如下所示：

1. 建立 Azure 搜尋服務。 如需詳細資訊，請參閱[建立使用 Azure 入口網站在 Azure 搜尋服務](/azure/search/search-create-service-portal/)。
1. 移除 Silverlight 目標 framework 從 Xamarin.Forms 方案可攜式類別程式庫 (PCL)。 這可以透過變更任何設定檔中，支援跨平台開發，但是不支援 Silverlight，例如 151 或設定檔 92 PCL 設定檔。
1. 新增[Microsoft Azure 搜尋程式庫](https://www.nuget.org/packages/Microsoft.Azure.Search)NuGet 套件加入 PCL 專案，在 Xamarin.Forms 方案中。

執行這些步驟之後，Microsoft 搜尋程式庫 API 可用來管理搜尋的索引和資料來源、 上傳和管理文件，以及執行查詢。

## <a name="creating-the-azure-search-index"></a>建立 Azure 搜尋索引

索引結構描述必須定義對應至要搜尋的資料結構。 這可以是完成在 Azure 入口網站，或以程式設計方式使用`SearchServiceClient`類別。 這個類別會管理連接至 Azure 搜尋中，而且可以用來建立索引。 下列程式碼範例示範如何建立此類別的執行個體：

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

`SearchServiceClient`建構函式多載會採用搜尋服務名稱和`SearchCredentials`物件做為引數，`SearchCredentials`物件包裝*管理金鑰*Azure 搜尋服務。 *管理金鑰*才能建立索引。

> [!NOTE]
>  單一`SearchServiceClient`應該盡量避免開啟太多連線至 Azure 搜尋應用程式中使用執行個體。

索引由定義`Index`物件，如下列程式碼範例所示：

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

`Index.Name`屬性應該設定為索引的名稱和`Index.Fields`屬性應該設定為陣列`Field`物件。 每個`Field`執行個體會指定名稱、 類型和任何屬性，指定如何使用此欄位。 這些屬性包括：

- `IsKey` – 表示欄位是否為索引的索引鍵。 類型的索引中只能有一個欄位`DataType.String`，必須指定為索引鍵欄位。
- `IsFacetable` – 指出是否可以在此欄位上執行多面向導覽。 預設值是 `false`。
- `IsFilterable` – 指出是否可以在篩選查詢中使用欄位。 預設值是 `false`。
- `IsRetrievable` – 指出是否可以在搜尋結果中擷取欄位。 預設值是 `true`。
- `IsSearchable` – 指出欄位是否包含在全文檢索搜尋中。 預設值是 `false`。
- `IsSortable` – 指出是否可以在使用欄位`OrderBy`運算式。 預設值是 `false`。

> [!NOTE]
> 部署後變更索引需要重建並重新載入資料。

`Index`物件可以選擇性地指定`Suggesters`屬性，用來支援自動完成，或搜尋建議查詢在索引中所定義的欄位。 `Suggesters`屬性應該設定為陣列`Suggester`物件，以定義用來建置搜尋建議結果的欄位。

在建立之後`Index`物件，藉由呼叫建立索引`Indexes.Create`上`SearchServiceClient`執行個體。

> [!NOTE]
> 當建立索引從應用程式必須保持回應時，使用`Indexes.CreateAsync`方法。

如需詳細資訊，請參閱[建立 Azure 搜尋索引，使用.NET SDK](/azure/search/search-create-index-dotnet/)。

## <a name="deleting-the-azure-search-index"></a>刪除 Azure 搜尋索引

索引可以刪除藉由呼叫`Indexes.Delete`上`SearchServiceClient`執行個體：

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>將資料上傳至 Azure 搜尋索引

定義索引之後, 可以使用其中兩個模型上傳資料：

- **提取模型**– 資料是定期內嵌從 Azure Cosmos DB、 Azure SQL Database、 Azure Blob 儲存體，或 SQL Server 裝載在 Azure 虛擬機器。
- **發送模型**– 資料會以程式設計方式傳送至索引。 這是本文章中採用的主從模型。

A`SearchIndexClient`必須匯入資料的索引建立執行個體。 這可藉由呼叫`SearchServiceClient.Indexes.GetClient`方法，如下列程式碼範例所示：

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

要匯入之索引的資料會封裝為`IndexBatch`物件，該封裝的集合物件`IndexAction`物件。 每個`IndexAction`執行個體包含文件，以及在文件上執行哪些動作會告訴 Azure 搜尋的屬性。 在上述程式碼範例`IndexAction.Upload`動作指定，而這可能導致正在插入至索引中，如果它是新的在文件或取代已存在時。 `IndexBatch`物件然後傳送至索引藉由呼叫`Documents.Index`方法`SearchIndexClient`物件。 如需其他的索引動作的資訊，請參閱[決定要使用的索引動作](/azure/search/search-import-data-dotnet#ii-decide-which-indexing-action-to-use)。

> [!NOTE]
> 只有 1000年份文件可以包含在單一索引要求。

請注意，在上述程式碼範例`monkeyList`集合會建立為集合中的匿名物件`Monkey`物件。 這會建立資料`id`欄位，並解析 Pascal 大小寫對應`Monkey`camel 命名法的大小寫的屬性名稱搜尋索引欄位名稱。 或者，此對應也可藉由新增`[SerializePropertyNamesAsCamelCase]`屬性`Monkey`類別。

如需詳細資訊，請參閱[資料上傳至 Azure 搜尋.NET SDK](/azure/search/search-import-data-dotnet/)。

## <a name="querying-the-azure-search-index"></a>查詢 Azure 搜尋索引

A`SearchIndexClient`必須建立執行個體，來查詢索引。 當應用程式執行查詢時，建議您最好遵循最低權限原則，並建立`SearchIndexClient`直接傳遞*查詢索引鍵*做為引數。 這可確保使用者具有索引和文件上的唯讀存取。 下列程式碼範例將示範這個方法：

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

`SearchIndexClient`建構函式多載會採用搜尋服務名稱、 索引名稱和`SearchCredentials`物件做為引數，`SearchCredentials`物件包裝*查詢索引鍵*Azure 搜尋服務。

### <a name="search-queries"></a>搜尋查詢

索引可以藉由呼叫查詢`Documents.SearchAsync`方法`SearchIndexClient`執行個體，如下列程式碼範例所示：

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

`SearchAsync`搜尋文字引數和選擇性方法會採用`SearchParameters`可用來進一步精簡查詢的物件。 搜尋查詢指定為搜尋文字引數，而藉由設定可以指定篩選條件查詢`Filter`屬性`SearchParameters`引數。 下列程式碼範例示範查詢類型：

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

此篩選條件查詢套用至整個索引，並從結果移除文件位置`location`欄位不等於中國和不等於越南。 篩選後，搜尋查詢不會對篩選條件查詢的結果。

> [!NOTE]
> 若要篩選而不必搜尋，請傳遞`*`做為搜尋文字引數。

`SearchAsync`方法會傳回`DocumentSearchResult`包含查詢結果的物件。 列舉此物件，與每個`Document`物件建立為`Monkey`物件，並加入至`Monkeys``ObservableCollection`供顯示。 下列螢幕擷取畫面顯示搜尋查詢傳回的結果從 Azure 搜尋：

![](azure-search-images/search.png "搜尋結果")

如需有關搜尋和篩選的詳細資訊，請參閱[查詢您的 Azure 搜尋索引使用.NET SDK](/azure/search/search-query-dotnet/)。

<a name="suggestions" />

### <a name="suggestion-queries"></a>建議的查詢

Azure 搜尋可讓建議要求以搜尋查詢，藉由呼叫`Documents.SuggestAsync`方法`SearchIndexClient`執行個體。 下列程式碼範例所示：

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

`SuggestAsync`方法使用搜尋文字引數，若要使用建議工具名稱 （也就是在索引中定義），以及選用性`SuggestParameters`可用來進一步精簡查詢的物件。 `SuggestParameters`執行個體設定下列屬性：

- `UseFuzzyMatching` -當設定為`true`，Azure 搜尋會尋找建議，即使搜尋文字內取代或遺失的字元。
- `HighlightPreTag` – 的前面加上建議叫用的標記。
- `HighlightPostTag` – 會附加至建議叫用的標記。
- `MinimumCoverage` – 表示必須涵蓋查詢的建議查詢的索引百分比回報成功。 預設為 80。
- `Top` – 擷取建議的數目。 它必須是介於 1 到 100，而預設值是 5 之間的整數。

整體結果是，從索引前的 10 個結果將不會傳回叫用反白顯示，而且結果會包含文件包含同樣使用搜尋詞彙的拼字。

`SuggestAsync`方法會傳回`DocumentSuggestResult`包含查詢結果的物件。 列舉此物件，與每個`Document`物件建立為`Monkey`物件，並加入至`Monkeys``ObservableCollection`供顯示。 下列螢幕擷取畫面顯示從 Azure 搜尋傳回的建議結果：

![](azure-search-images/suggest.png "建議的結果")

請注意，範例應用程式，在`SuggestAsync`使用者完成輸入搜尋詞彙時只叫用方法。 不過，它也可用來支援自動完成查詢執行每個按鍵動作。

## <a name="summary"></a>總結

本文示範如何使用 Azure 搜尋整合 Xamarin.Forms 應用程式的 Microsoft Azure 搜尋程式庫。 Azure 搜尋是雲端服務可提供索引和查詢功能上傳的資料。 這會移除基礎結構需求和傳統上相關聯的應用程式中實作搜尋功能搜尋演算法的複雜度。


## <a name="related-links"></a>相關連結

- [Azure 搜尋 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)
- [Azure Search 文件](/azure/search/)
- [Microsoft Azure 搜尋程式庫](https://www.nuget.org/packages/Microsoft.Azure.Search/)
