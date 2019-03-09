---
title: 使用 Azure 搜尋服務來搜尋資料
description: 這篇文章會示範如何使用 Microsoft Azure 搜尋服務程式庫，將 Azure 搜尋服務整合至 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
ms.openlocfilehash: 640d50a45b4b7f61113b609fa6a5e4a04566b360
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667531"
---
# <a name="searching-data-with-azure-search"></a>使用 Azure 搜尋服務來搜尋資料

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)

_Azure 搜尋服務是雲端服務可編製索引和查詢上傳資料的功能。這會移除傳統上相關聯的應用程式中實作搜尋功能的搜尋演算法複雜度與基礎結構需求。這篇文章會示範如何使用 Microsoft Azure 搜尋服務程式庫，將 Azure 搜尋服務整合至 Xamarin.Forms 應用程式。_

## <a name="overview"></a>總覽

資料會儲存在 Azure 搜尋服務中，為索引和文件。 *Index*是 Azure 搜尋服務，可搜尋的資料存放區，並在概念上類似資料庫資料表。 A*文件*是單一單元的可搜尋的資料，在索引中，並在概念上類似於資料庫資料列。 當上傳文件和提交到 Azure 搜尋服務的搜尋查詢，便會要求對搜尋服務中特定索引。

每個 Azure 搜尋服務所提出的要求必須包含服務和 API 金鑰的名稱。 有兩種類型的 API 金鑰：

- *系統管理金鑰*授與所有作業的完整權限。 這包括管理服務、 建立和刪除索引和資料來源。
- *查詢金鑰*授與唯讀存取索引和文件，並發出搜尋要求的應用程式應該使用。

最常見的要求，Azure 搜尋服務是執行查詢。 有兩種可以提交的查詢：

- A*搜尋*查詢搜尋索引中的所有可搜尋欄位中的一或多個項目。 搜尋查詢會使用簡化的語法或 Lucene 查詢語法來建置。 如需詳細資訊，請參閱 < [Azure 搜尋服務中的簡單查詢語法](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/)，並[在 Azure 搜尋服務的 Lucene 查詢語法](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/)。
- A*篩選*查詢針對索引中的所有可篩選欄位評估布林運算式。 篩選查詢，會使用 OData 篩選語言的子集所建置的。 如需詳細資訊，請參閱 < [Azure 搜尋服務的 OData 運算式語法](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/)。

合併或分開，可以使用搜尋查詢和篩選查詢。 篩選查詢一起使用時，會先套用到整個索引，並接著搜尋查詢執行篩選查詢的結果。

Azure 搜尋服務也支援根據搜尋輸入擷取建議。 如需詳細資訊，請參閱 <<c0> [ 建議查詢](#suggestions)。

## <a name="setup"></a>安裝程式

將 Azure 搜尋服務整合至 Xamarin.Forms 應用程式的程序如下所示：

1. 建立 Azure 搜尋服務。 如需詳細資訊，請參閱 <<c0> [ 建立 Azure 搜尋服務使用 Azure 入口網站](/azure/search/search-create-service-portal/)。
1. 從 Xamarin.Forms 方案可攜式類別庫 (PCL) 中移除 Silverlight 做為目標架構。 這可以藉由變更任何設定檔中，支援跨平台開發，但不支援 Silverlight，例如 151 或設定檔 92 的 PCL 設定檔來完成。
1. 新增[Microsoft Azure Search 文件庫](https://www.nuget.org/packages/Microsoft.Azure.Search)NuGet 套件加入 PCL 專案中的 Xamarin.Forms 方案。

執行這些步驟之後，Microsoft 搜尋程式庫 API 可用來管理搜尋服務索引和資料來源、 上傳和管理文件，並執行查詢。

## <a name="creating-the-azure-search-index"></a>建立 Azure 搜尋服務索引

索引結構描述必須定義對應至要搜尋的資料結構。 這可以是完成在 Azure 入口網站中，或以程式設計方式使用`SearchServiceClient`類別。 此類別管理連線到 Azure 搜尋服務，並可用來建立索引。 下列程式碼範例示範如何建立此類別的執行個體：

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

`SearchServiceClient`建構函式多載會採用搜尋服務名稱和`SearchCredentials`物件做為引數，`SearchCredentials`物件包裝*管理金鑰*Azure 搜尋服務。 *系統管理金鑰*，才可建立索引。

> [!NOTE]
>  單一`SearchServiceClient`應該盡量避免開啟太多連線到 Azure 搜尋服務應用程式中使用執行個體。

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

`Index.Name`屬性應設為名稱的索引，而`Index.Fields`屬性應設為陣列`Field`物件。 每個`Field`執行個體會指定名稱、 類型和任何屬性，指定欄位的使用方式。 這些屬性包括：

- `IsKey` – 表示欄位是否為索引的索引鍵。 在索引中，型別只能有一個欄位`DataType.String`，必須指定為索引鍵的欄位。
- `IsFacetable` – 指出是否能夠對此欄位中的多面向導覽。 預設值是 `false`。
- `IsFilterable` -指出欄位是否可以用於篩選查詢。 預設值是 `false`。
- `IsRetrievable` -指出欄位是否可以擷取搜尋結果中。 預設值是 `true`。
- `IsSearchable` -指出欄位是否包含全文檢索搜尋中。 預設值是 `false`。
- `IsSortable` -指出欄位是否可用在`OrderBy`運算式。 預設值是 `false`。

> [!NOTE]
> 部署之後，變更索引牽涉到重建和重新載入資料。

`Index`物件可以選擇性地指定`Suggesters`屬性，定義要用來支援自動完成，或搜尋建議查詢的索引中的欄位。 `Suggesters`屬性應設為陣列`Suggester`物件，定義用來建置搜尋建議結果的欄位。

在建立後`Index`物件，藉由呼叫建立索引`Indexes.Create`上`SearchServiceClient`執行個體。

> [!NOTE]
> 當建立索引從應用程式必須保持回應性時，使用`Indexes.CreateAsync`方法。

如需詳細資訊，請參閱 <<c0> [ 建立使用.NET SDK 的 Azure 搜尋服務索引](/azure/search/search-create-index-dotnet/)。

## <a name="deleting-the-azure-search-index"></a>刪除 Azure 搜尋服務索引

來刪除索引，請呼叫`Indexes.Delete`上`SearchServiceClient`執行個體：

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>將資料上傳至 Azure 搜尋服務索引

定義索引之後, 可以使用其中一個模型上傳資料：

- **提取模型**– 資料定期擷取從 Azure Cosmos DB、 Azure SQL Database、 Azure Blob 儲存體，或 SQL Server 裝載在 Azure 虛擬機器。
- **推送模型**– 資料會以程式設計方式傳送至索引。 這是在這篇文章中採用的模型。

A`SearchIndexClient`索引將資料匯入，必須建立執行個體。 這可藉由呼叫`SearchServiceClient.Indexes.GetClient`方法，如下列程式碼範例所示：

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

若要匯入到索引的資料會封裝成`IndexBatch`物件，該封裝的集合物件`IndexAction`物件。 每個`IndexAction`執行個體包含文件，以及告知 Azure 搜尋服務文件上執行哪些動作的屬性。 在上述程式碼範例`IndexAction.Upload`動作指定，而導致新的如果插入索引的文件或它是否已被取代。 `IndexBatch`物件然後藉由呼叫傳送到索引`Documents.Index`方法`SearchIndexClient`物件。 如需其他索引編製動作的資訊，請參閱[決定要使用的索引編製動作](/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)。

> [!NOTE]
> 只有 1000 個文件可以包含在單一的索引編製要求中。

請注意，在上述程式碼範例`monkeyList`集合會建立為集合中的匿名物件`Monkey`物件。 這會建立資料`id`欄位中，並解析 Pascal 大小寫對應`Monkey`屬性名稱為駝峰式命名法大小寫搜尋索引的欄位名稱。 或者，此對應也可藉由新增`[SerializePropertyNamesAsCamelCase]`屬性設定為`Monkey`類別。

如需詳細資訊，請參閱 <<c0> [ 資料上傳至 Azure 搜尋服務中使用.NET SDK](/azure/search/search-import-data-dotnet/)。

## <a name="querying-the-azure-search-index"></a>查詢 Azure 搜尋服務索引

A`SearchIndexClient`必須建立執行個體，來查詢索引。 當應用程式執行查詢時，建議您最好遵循最低權限原則，並建立`SearchIndexClient`直接傳遞*查詢金鑰*做為引數。 這可確保使用者可以索引和文件上的唯讀存取。 這種方法是以下列程式碼範例所示：

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

`SearchIndexClient`建構函式多載會將搜尋服務名稱、 索引名稱，以及`SearchCredentials`物件做為引數，`SearchCredentials`物件包裝*查詢金鑰*Azure 搜尋服務。

### <a name="search-queries"></a>搜尋查詢

可以藉由呼叫查詢索引`Documents.SearchAsync`方法`SearchIndexClient`執行個體，如下列程式碼範例所示：

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

`SearchAsync`方法會接受搜尋文字引數，以及選擇性`SearchParameters`可用來進一步精簡查詢的物件。 搜尋查詢指搜尋文字引數，而可以指定篩選查詢，藉由設定`Filter`屬性`SearchParameters`引數。 下列程式碼範例示範查詢類型：

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

此篩選條件查詢套用至整個索引，並從結果中移除文件其中`location`欄位不等於中國和不等於越南。 篩選後，搜尋查詢會執行篩選查詢的結果。

> [!NOTE]
> 若要篩選而不必搜尋，請傳遞`*`做為搜尋文字引數。

`SearchAsync`方法會傳回`DocumentSearchResult`包含查詢結果的物件。 列舉此物件，與每個`Document`物件所建立時`Monkey`物件，並新增至`Monkeys``ObservableCollection`顯示。 下列螢幕擷取畫面顯示搜尋查詢傳回的結果從 Azure 搜尋服務：

![](azure-search-images/search.png "搜尋結果")

如需有關搜尋和篩選的詳細資訊，請參閱 <<c0> [ 查詢 Azure 搜尋服務索引使用.NET SDK](/azure/search/search-query-dotnet/)。

<a name="suggestions" />

### <a name="suggestion-queries"></a>建議的查詢

Azure 搜尋服務可讓要求建議根據搜尋查詢，藉由呼叫`Documents.SuggestAsync`方法`SearchIndexClient`執行個體。 下列程式碼範例所示：

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

`SuggestAsync`方法會搜尋文字引數，若要使用的建議工具名稱 （也就定義在索引中），以及選用`SuggestParameters`可用來進一步精簡查詢的物件。 `SuggestParameters`執行個體設定下列屬性：

- `UseFuzzyMatching` – 當設定為`true`，Azure 搜尋服務會尋找建議，即使搜尋文字中沒有替代或遺漏的字元。
- `HighlightPreTag` – 建議點擊前面加上標記。
- `HighlightPostTag` – 會附加至建議叫用的標記。
- `MinimumCoverage` – 表示建議查詢的查詢必須涵蓋索引的百分比報告成功。 預設值為 80。
- `Top` – 要擷取的建議數目。 它必須是介於 1 到 100，而預設值為 5 的整數。

整體影響是，索引的前 10 個結果將不會傳回搜尋結果醒目提示，而且結果將會包含文件包含同樣使用搜尋字詞的拼字。

`SuggestAsync`方法會傳回`DocumentSuggestResult`包含查詢結果的物件。 列舉此物件，與每個`Document`物件所建立時`Monkey`物件，並新增至`Monkeys``ObservableCollection`顯示。 下列螢幕擷取畫面顯示 Azure 搜尋服務所傳回的建議結果：

![](azure-search-images/suggest.png "建議結果")

請注意，在範例應用程式，`SuggestAsync`當使用者完成輸入搜尋字詞只叫用方法。 不過，它也可用來支援每個 keypress 上，執行的自動完成的搜尋查詢。

## <a name="summary"></a>總結

這篇文章會示範如何使用 Microsoft Azure 搜尋服務程式庫，將 Azure 搜尋服務整合至 Xamarin.Forms 應用程式。 Azure 搜尋服務是雲端服務可編製索引和查詢上傳資料的功能。 這會移除傳統上相關聯的應用程式中實作搜尋功能的搜尋演算法複雜度與基礎結構需求。


## <a name="related-links"></a>相關連結

- [Azure 搜尋服務 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)
- [Azure 搜尋服務文件](/azure/search/)
- [Microsoft Azure 搜尋服務程式庫](https://www.nuget.org/packages/Microsoft.Azure.Search/)
