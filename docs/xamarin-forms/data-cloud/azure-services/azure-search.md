---
title: 使用 Azure 搜尋服務來搜尋資料 Xamarin.Forms
description: 本文示範如何使用 Microsoft Azure 搜尋程式庫，將 Azure 搜尋服務整合到 Xamarin.Forms 應用程式中。
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 14d26c1360c1c1b7997598ef1263e3dd62e3c013
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561777"
---
# <a name="search-data-with-azure-search-and-no-locxamarinforms"></a>使用 Azure 搜尋服務來搜尋資料 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)

_Azure 搜尋服務是一項雲端服務，可為上傳的資料提供索引編制和查詢功能。這會移除傳統上與在應用程式中執行搜尋功能相關聯的基礎結構需求和搜尋演算法複雜性。本文示範如何使用 Microsoft Azure 搜尋程式庫，將 Azure 搜尋服務整合到 Xamarin.Forms 應用程式中。_

## <a name="overview"></a>概觀

資料會以索引和檔的形式儲存在 Azure 搜尋服務中。 *索引*是 Azure 搜尋服務可搜尋的資料存放區，在概念上類似于資料庫資料表。 *檔*是索引中可搜尋資料的單一單位，在概念上類似于資料庫資料列。 上傳檔並將搜尋查詢提交至 Azure 搜尋服務時，會對搜尋服務中的特定索引提出要求。

對 Azure 搜尋服務所提出的每個要求都必須包含服務的名稱和 API 金鑰。 API 金鑰有兩種類型：

- *管理金鑰* 會授與所有作業的完整許可權。 這包括管理服務、建立和刪除索引，以及資料來源。
- *查詢金鑰* 會授與索引和檔的唯讀存取權，且應由發出搜尋要求的應用程式使用。

最常見的 Azure 搜尋要求是執行查詢。 可以提交的查詢類型有兩種：

- *搜尋*查詢會在索引的所有可搜尋欄位中搜尋一個或多個專案。 搜尋查詢是使用簡化的語法或 Lucene 查詢語法來建立。 如需詳細資訊，請參閱 Azure 搜尋服務中的 [簡單查詢語法](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/)，以及 [azure 搜尋服務中的 Lucene 查詢語法](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/)。
- *篩選*查詢會對索引中所有可篩選的欄位評估布林運算式。 篩選查詢是使用 OData 篩選器語言的子集建立的。 如需詳細資訊，請參閱 [Azure 搜尋服務的 OData 運算式語法](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/)。

搜尋查詢和篩選查詢可以單獨使用或一起使用。 搭配使用時，會先將篩選查詢套用到整個索引，然後再對篩選查詢的結果執行搜尋查詢。

Azure 搜尋服務也支援根據搜尋輸入來抓取建議。 如需詳細資訊，請參閱 [建議查詢](#suggestion-queries)。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

## <a name="setup"></a>安裝程式

將 Azure 搜尋服務整合到應用程式中的程式如下所示 Xamarin.Forms ：

1. 建立 Azure 搜尋服務。 如需詳細資訊，請參閱 [使用 Azure 入口網站建立 azure 搜尋服務](/azure/search/search-create-service-portal/)。
1. 從 Xamarin.Forms 解決方案的便攜類別庫 (PCL) 移除 Silverlight 做為目標架構。 這可以藉由將 PCL 設定檔變更為任何支援跨平臺開發的設定檔來完成，但不支援 Silverlight，例如設定檔151或設定檔92。
1. 將 [Microsoft Azure 搜尋程式庫](https://www.nuget.org/packages/Microsoft.Azure.Search) NuGet 套件新增至方案中的 PCL 專案 Xamarin.Forms 。

執行這些步驟之後，您可以使用 Microsoft 搜尋程式庫 API 來管理搜尋索引和資料來源、上傳和管理檔，以及執行查詢。

## <a name="creating-the-azure-search-index"></a>建立 Azure Search 索引

必須定義索引架構，以對應至要搜尋的資料結構。 這可以在 Azure 入口網站中完成，或使用類別以程式設計方式完成 `SearchServiceClient` 。 此類別會管理與 Azure 搜尋服務的連線，而且可以用來建立索引。 下列程式碼範例示範如何建立這個類別的實例：

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

函式多載 `SearchServiceClient` 會採用搜尋服務名稱和 `SearchCredentials` 物件做為引數，而 `SearchCredentials` 物件會包裝 Azure 搜尋服務的 *管理金鑰* 。 需要系統 *管理金鑰* 才能建立索引。

> [!NOTE]
> 您 `SearchServiceClient` 應該在應用程式中使用單一實例，以避免開啟太多與 Azure 搜尋服務的連線。

索引是由物件所定義 `Index` ，如下列程式碼範例所示：

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

`Index.Name`屬性應該設定為索引的名稱，而 `Index.Fields` 屬性應設定為物件的陣列 `Field` 。 每個 `Field` 實例都會指定名稱、類型和任何屬性，以指定欄位的使用方式。 這些屬性包括：

- `IsKey` –表示欄位是否為索引的索引鍵。 索引中只有一個類型的欄位 `DataType.String` 必須指定為索引鍵欄位。
- `IsFacetable` –指出是否可以對此欄位執行多面向導覽。 預設值是 `false`。
- `IsFilterable` –表示欄位是否可用於篩選查詢中。 預設值是 `false`。
- `IsRetrievable` –指出是否可以在搜尋結果中取出欄位。 預設值是 `true`。
- `IsSearchable` –表示欄位是否包含在全文檢索搜尋中。 預設值是 `false`。
- `IsSortable` –表示欄位是否可在運算式中使用 `OrderBy` 。 預設值是 `false`。

> [!NOTE]
> 在部署之後變更索引包含重建和重載資料。

`Index`物件可以選擇性地指定 `Suggesters` 屬性，此屬性會定義索引中的欄位，以用來支援自動完成或搜尋建議查詢。 `Suggesters`屬性應設定為物件的陣列 `Suggester` ，這些物件會定義用來建立搜尋建議結果的欄位。

建立物件之後 `Index` ，會 `Indexes.Create` 在實例上呼叫來建立索引 `SearchServiceClient` 。

> [!NOTE]
> 從必須保持回應的應用程式建立索引時，請使用 `Indexes.CreateAsync` 方法。

如需詳細資訊，請參閱 [使用 .NET SDK 建立 Azure 搜尋服務索引](/azure/search/search-create-index-dotnet/)。

## <a name="deleting-the-azure-search-index"></a>刪除 Azure 搜尋服務索引

您可以 `Indexes.Delete` 在實例上呼叫來刪除索引 `SearchServiceClient` ：

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>將資料上傳至 Azure 搜尋服務索引

在定義索引之後，您可以使用下列兩個模型其中之一，將資料上傳至該索引：

- **提取模型** –資料會從 Azure 虛擬機器中裝載的 Azure Cosmos DB、Azure SQL Database、Azure Blob 儲存體或 SQL Server 定期內嵌。
- **推送模型** –資料會以程式設計的方式傳送至索引。 這是本文中採用的模型。

`SearchIndexClient`必須建立實例，才能將資料匯入索引。 您可以藉由呼叫方法來完成這 `SearchServiceClient.Indexes.GetClient` 項作業，如下列程式碼範例所示：

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

要匯入至索引的資料會封裝為 `IndexBatch` 物件，該物件會封裝物件的集合 `IndexAction` 。 每個 `IndexAction` 實例都包含一個檔，以及一個屬性，告訴 Azure 搜尋服務要對該檔執行哪些動作。 在上述程式碼範例中， `IndexAction.Upload` 會指定動作，這會導致檔插入索引（如果有的話），如果已存在，則會予以取代。 `IndexBatch`然後藉由呼叫物件上的方法，將物件傳送至索引 `Documents.Index` `SearchIndexClient` 。 如需其他編制索引動作的詳細資訊，請參閱 [決定要使用的索引動作](/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)。

> [!NOTE]
> 只有1000的檔可以包含在單一索引要求中。

請注意，在上述程式碼範例中， `monkeyList` 會將集合建立為物件集合中的匿名物件 `Monkey` 。 這會建立欄位的資料 `id` ，並將 Pascal 案例屬性名稱的對應解析 `Monkey` 成 camel 大小寫搜尋索引功能變數名稱。 或者，您也可以藉由將屬性加入至類別來完成這項對應 `[SerializePropertyNamesAsCamelCase]` `Monkey` 。

如需詳細資訊，請參閱 [使用 .NET SDK 將資料上傳至 Azure 搜尋](/azure/search/search-import-data-dotnet/)服務。

## <a name="querying-the-azure-search-index"></a>查詢 Azure 搜尋服務索引

您 `SearchIndexClient` 必須建立實例來查詢索引。 當應用程式執行查詢時，建議您遵循最低許可權的原則，並 `SearchIndexClient` 直接建立 *查詢金鑰* 做為引數。 這可確保使用者具有索引和檔的唯讀存取權。 下列程式碼範例會示範這種方法：

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

函式多載 `SearchIndexClient` 會採用搜尋服務名稱、索引名稱和 `SearchCredentials` 物件做為引數，而 `SearchCredentials` 物件會包裝 Azure 搜尋服務的 *查詢金鑰* 。

### <a name="search-queries"></a>搜尋查詢

您可以呼叫實例上的方法來查詢索引 `Documents.SearchAsync` `SearchIndexClient` ，如下列程式碼範例所示：

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

`SearchAsync`方法會接受搜尋文字引數，以及 `SearchParameters` 可用於進一步精簡查詢的選擇性物件。 搜尋查詢會指定為搜尋文字引數，而篩選查詢則可以藉由設定 `Filter` 引數的屬性來指定 `SearchParameters` 。 下列程式碼範例會示範這兩種查詢類型：

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

此篩選查詢會套用至整個索引，並從 `location` 欄位不等於中國且不等於越南的結果中移除檔。 篩選之後，會在篩選查詢的結果上執行搜尋查詢。

> [!NOTE]
> 若要篩選而不搜尋，請 `*` 以搜尋文字引數的形式傳遞。

`SearchAsync`方法 `DocumentSearchResult` 會傳回包含查詢結果的物件。 系統會列舉這個物件，並將每個 `Document` 物件建立為 `Monkey` 物件，並新增至以 `Monkeys` `ObservableCollection` 供顯示。 下列螢幕擷取畫面顯示從 Azure 搜尋服務傳回的搜尋查詢結果：

![搜尋結果](azure-search-images/search.png)

如需搜尋和篩選的詳細資訊，請參閱 [使用 .NET SDK 查詢 Azure 搜尋服務索引](/azure/search/search-query-dotnet/)。

### <a name="suggestion-queries"></a>建議查詢

Azure 搜尋服務可讓您在實例上呼叫方法，根據搜尋查詢要求建議 `Documents.SuggestAsync` `SearchIndexClient` 。 下列程式碼範例會示範這種情況：

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

`SuggestAsync`方法會採用搜尋文字引數、在索引) 中定義 (的建議工具名稱，以及 `SuggestParameters` 可用於進一步精簡查詢的選擇性物件。 `SuggestParameters`實例會設定下列屬性：

- `UseFuzzyMatching` –當設定為時 `true` ，即使搜尋文字中有替代字元或遺失字元，Azure 搜尋服務仍會尋找建議。
- `HighlightPreTag` –建議點擊之前的標記。
- `HighlightPostTag` –附加至建議點擊的標記。
- `MinimumCoverage` –表示必須由建議查詢所涵蓋的索引百分比，以便讓查詢回報成功。 預設值為80。
- `Top` –要取出的建議數目。 它必須是介於1到100之間的整數，預設值為5。

整體效果是會傳回索引的前10個結果，並顯示搜尋結果，而且結果將包含包含類似拼寫之搜尋字詞的檔。

`SuggestAsync`方法 `DocumentSuggestResult` 會傳回包含查詢結果的物件。 系統會列舉這個物件，並將每個 `Document` 物件建立為 `Monkey` 物件，並新增至以 `Monkeys` `ObservableCollection` 供顯示。 下列螢幕擷取畫面顯示 Azure 搜尋服務所傳回的建議結果：

![建議結果](azure-search-images/suggest.png)

請注意，在範例應用程式中， `SuggestAsync` 只有當使用者完成輸入搜尋字詞時，才會叫用方法。 不過，它也可以在每個按鍵上執行時，用來支援自動完成的搜尋查詢。

## <a name="summary"></a>摘要

本文示範如何使用 Microsoft Azure 搜尋程式庫，將 Azure 搜尋服務整合到 Xamarin.Forms 應用程式中。 Azure 搜尋服務是一項雲端服務，可為上傳的資料提供索引編制和查詢功能。 這會移除傳統上與在應用程式中執行搜尋功能相關聯的基礎結構需求和搜尋演算法複雜性。

## <a name="related-links"></a>相關連結

- [Azure 搜尋服務 (範例) ](/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)
- [Azure 搜尋服務檔](/azure/search/)
- [Microsoft Azure 搜尋程式庫](https://www.nuget.org/packages/Microsoft.Azure.Search/)