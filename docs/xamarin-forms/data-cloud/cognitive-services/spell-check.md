---
title: "拼字檢查使用 Bing 拼字檢查應用程式開發介面"
description: "Bing 拼字檢查，會執行內容的拼字檢查的文字，提供內嵌拼錯字的建議。 本文說明如何使用 Bing 拼字檢查 REST API 來更正 Xamarin.Forms 應用程式中的拼字錯誤。"
ms.topic: article
ms.prod: xamarin
ms.assetid: B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: ad2bdf27323fd7d7e108a25387cd6aea6d442098
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>拼字檢查使用 Bing 拼字檢查應用程式開發介面

_Bing 拼字檢查，會執行內容的拼字檢查的文字，提供內嵌拼錯字的建議。本文說明如何使用 Bing 拼字檢查 REST API 來更正 Xamarin.Forms 應用程式中的拼字錯誤。_

## <a name="overview"></a>總覽

Bing 拼字檢查 REST API 有兩種作業模式，並提出要求的應用程式開發介面時，就必須指定一種模式：

- `Spell` 修正不需要變更任何大小寫的簡短文字 （最多 9 個字）。
- `Proof` 更正長文字、 大小寫的更正和基本的標點符號，提供且會抑制積極的更正。

必須使用 Bing 拼字檢查 API 來取得 API 金鑰。 這可在取得[開始免費使用](https://www.microsoft.com/cognitive-services/sign-up?ReturnUrl=/cognitive-services/subscriptions?productId=%2fproducts%2fBing.Speech.Preview)microsoft.com 上。

如需 Bing 拼字檢查 API 所支援的語言，請參閱[語言支援](https://www.microsoft.com/cognitive-services/Bing-Spell-check-API/documentation#language-support)microsoft.com 上。如需 Bing 拼字檢查 API 的詳細資訊，請參閱[Bing 拼字檢查 API](https://www.microsoft.com/cognitive-services/bing-spell-check-api/documentation) microsoft.com 上。

## <a name="authentication"></a>驗證

每個要求對 Bing 拼字檢查 API 需要 API 金鑰，應該指定的值為`Ocp-Apim-Subscription-Key`標頭。 下列程式碼範例示範如何將加入的 API 金鑰`Ocp-Apim-Subscription-Key`要求標頭：

```csharp
using (var httpClient = new HttpClient())
{
  httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
  ...
}
```

若要將有效的 API 金鑰傳遞給 Bing 拼字檢查 API 失敗會導致 401 回應時發生錯誤。

## <a name="performing-spell-checking"></a>執行拼字檢查

拼字檢查可藉由在 GET 或 POST 要求`SpellCheck`API `https://api.cognitive.microsoft.com/bing/v5.0/SpellCheck`。 提出 GET 要求時，會傳送要檢查拼字的文字，做為查詢參數。 POST 要求時，要檢查拼字的文字會傳送要求主體中。 GET 要求受限於拼字檢查 1500年個字元，因為查詢參數的字串長度限制。 因此，除非短字串正在進行拼字檢查，將通常發出 POST 要求。

範例應用程式、`SpellCheckTextAsync`方法會叫用拼字檢查處理程序：

```csharp
public async Task<SpellCheckResult> SpellCheckTextAsync(string text)
{
  string requestUri = GenerateRequestUri(Constants.BingSpellCheckEndpoint, text, SpellCheckMode.Spell);
  var response = await SendRequestAsync(requestUri, Constants.BingSpellCheckApiKey);
  var spellCheckResults = JsonConvert.DeserializeObject<SpellCheckResult>(response);
  return spellCheckResults;
}
```

`SpellCheckTextAsync`方法會產生要求 URI，然後將傳送的要求`SpellCheck`API，其會傳回包含結果的 JSON 回應。 JSON 回應還原序列化時，傳回給呼叫的方法，以便顯示的結果。

如需 Bing 拼字檢查 REST API 的詳細資訊，請參閱[拼字檢查 API](https://dev.cognitive.microsoft.com/docs/services/56e73033cf5ff80c2008c679/operations/57855119bca1df1c647bc358) microsoft.com 上。

### <a name="configuring-spell-checking"></a>設定拼字檢查

指定 HTTP 查詢參數，可以設定拼字檢查處理程序。 有強制和選擇性參數，使用下列方法會顯示必須設定取得要求的強制參數：

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

這個方法會設定為拼字檢查，且拼字檢查模式的文字。

如需強制和選擇性參數的詳細資訊，請參閱[拼字檢查 API](https://dev.cognitive.microsoft.com/docs/services/56e73033cf5ff80c2008c679/operations/57855119bca1df1c647bc358) microsoft.com 上。

### <a name="sending-the-request"></a>傳送要求

`SendRequestAsync`方法對 Bing 拼字檢查 REST API 的 GET 要求，並傳回回應：

```csharp
async Task<string> SendRequestAsync(string url, string apiKey)
{
  using (var httpClient = new HttpClient())
  {
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
  }
}
```

這個方法會加入做為值的應用程式開發介面索引鍵，建置 GET 要求`Ocp-Apim-Subscription-Key`標頭。 然後會傳送 GET 要求至`SpellCheck`拼字檢查模式的要求 URL 指定的文字轉譯，與應用程式開發介面。 回應然後讀取，並傳回至呼叫的方法。

`SpellCheck` API 回應，提供該要求是否有效，表示要求成功，並要求的資訊回應中傳送 HTTP 狀態碼 200 （確定）。 如需可能的錯誤回應的清單，請參閱 < 在回應[拼字檢查 API](https://dev.cognitive.microsoft.com/docs/services/56e73033cf5ff80c2008c679/operations/57855119bca1df1c647bc358) microsoft.com 上。

### <a name="processing-the-response"></a>處理回應

API 回應是以 JSON 格式傳回。 下列 JSON 資料顯示的拼字錯誤文字的回應訊息`Go shappin tommorow`:

```csharp
{
  "_type": "SpellCheck",
  "flaggedTokens": [
    {
      "offset": 3,
      "token": "shappin",
      "type": "UnknownToken",
      "suggestions": [
        {
          "suggestion": "shopping",
          "score": 1
        }
      ]
    },
    {
      "offset": 11,
      "token": "tommorow",
      "type": "UnknownToken",
      "suggestions": [
        {
          "suggestion": "tomorrow",
          "score": 1
        }
      ]
    }
  ]
}
```

`flaggedTokens`陣列包含陣列的文字中已標示為不正確拼字或語法不正確的字。 陣列會是空白，如果不找到任何拼字或文法錯誤。 陣列中的標記如下：

- `offset` -從文字字串的開頭到標示的文字以零為起始的位移。
- `token` – 拼字不正確或不正確語法的文字字串中的單字。
- `type` – 錯誤造成的單字會加上旗標的類型。 有兩個可能的值 –`RepeatedToken`和`UnknownToken`。
- `suggestions` – 會更正拼字或文法錯誤的文字陣列。 陣列所組成的`suggestion`和`score`，指出建議的更正是正確的信心層級。

在範例應用程式中，JSON 回應還原序列化為`SpellCheckResult`執行個體，以傳回給呼叫的方法，以便顯示結果。 下列程式碼範例示範如何`SpellCheckResult`執行個體都針對顯示處理：

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

此程式碼會逐一`FlaggedTokens`集合並取代任何拼字錯誤或在與第一項建議的原始文字語法不正確的字。 下列螢幕擷取畫面顯示之前和之後的拼字檢查：

![](spell-check-images/before-spell-check.png "拼字檢查之前")

![](spell-check-images/after-spell-check.png "拼字檢查之後")

## <a name="summary"></a>總結

本文說明如何使用 Bing 拼字檢查 REST API 來更正 Xamarin.Forms 應用程式中的拼字錯誤。 Bing 拼字檢查，會執行內容的拼字檢查的文字，提供內嵌拼錯字的建議。



## <a name="related-links"></a>相關連結

- [Bing 拼字檢查文件](https://www.microsoft.com/cognitive-services/bing-spell-check-api/documentation)
- [使用 RESTful Web 服務](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Todo 認知服務 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Bing 拼字檢查應用程式開發介面](https://dev.cognitive.microsoft.com/docs/services/56e73033cf5ff80c2008c679/operations/57855119bca1df1c647bc358)
