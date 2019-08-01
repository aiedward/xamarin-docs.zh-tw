---
title: 使用 Bing 拼字檢查 API 的拼字檢查
description: Bing 拼字檢查，會執行內容的拼字檢查的文字，提供針對拼錯的字組內嵌建議。 這篇文章說明如何使用 Bing 拼字檢查 REST API 來更正 Xamarin.Forms 應用程式中的拼字錯誤。
ms.prod: xamarin
ms.assetid: B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: ed6992f946512cd88b4b2b8cfcf4c826bdd6b837
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645340"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>使用 Bing 拼字檢查 API 的拼字檢查

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Bing 拼字檢查，會執行內容的拼字檢查的文字，提供針對拼錯的字組內嵌建議。這篇文章說明如何使用 Bing 拼字檢查 REST API 來更正 Xamarin.Forms 應用程式中的拼字錯誤。_

## <a name="overview"></a>總覽

Bing 拼字檢查 REST API 有兩種作業模式，並對 API 提出要求時，就必須指定的模式：

- `Spell` 更正簡短的文字 （最多 9 個字），而不需要任何大小寫變更。
- `Proof` 更正長文字、 提供大小寫的更正和基本標點符號，且會抑制積極的修正。

若要使用 Bing 拼字檢查 API，必須取得 API 金鑰。 這可在取得[試用認知服務](https://azure.microsoft.com/try/cognitive-services/)

如需 Bing 拼字檢查 API 所支援的語言的清單，請參閱 <<c0> [ 支援的語言](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/)。 如需 Bing 拼字檢查 API 的詳細資訊，請參閱[Bing 拼字檢查文件](/azure/cognitive-services/bing-spell-check/)。

## <a name="authentication"></a>驗證

對 Bing 拼字檢查 API 的每個要求都需要 API 金鑰，應該指定的值為`Ocp-Apim-Subscription-Key`標頭。 下列程式碼範例示範如何新增 API 金鑰才能`Ocp-Apim-Subscription-Key`的要求標頭：

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

若要將有效的 API 金鑰傳遞給 Bing 拼字檢查 API 的失敗會導致 401 回應時發生錯誤。

## <a name="performing-spell-checking"></a>執行拼字檢查

拼字檢查，即可進行 GET 或 POST 要求來`SpellCheck`API `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck`。 在 GET 要求時，要檢查拼字的文字會傳送做為查詢參數。 在 POST 要求時，要檢查拼字的文字會傳送要求主體中。 GET 要求僅限於拼字檢查 1500 個字元，因為查詢參數的字串長度限制。 因此，除非短字串正在進行拼字檢查，應該通常提出 POST 要求。

在範例應用程式中，`SpellCheckTextAsync`方法會叫用拼字檢查程序：

```csharp
public async Task<SpellCheckResult> SpellCheckTextAsync(string text)
{
    string requestUri = GenerateRequestUri(Constants.BingSpellCheckEndpoint, text, SpellCheckMode.Spell);
    var response = await SendRequestAsync(requestUri);
    var spellCheckResults = JsonConvert.DeserializeObject<SpellCheckResult>(response);
    return spellCheckResults;
}
```

`SpellCheckTextAsync`方法會產生要求 URI，並接著會傳送至要求`SpellCheck`API，它會傳回包含結果的 JSON 回應。 JSON 回應還原序列化時，傳回給呼叫的方法，以便顯示結果。

### <a name="configuring-spell-checking"></a>設定拼字檢查

您可以藉由指定 HTTP 查詢參數設定拼字檢查程序：

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

這個方法會設定拼字檢查，且拼字檢查模式的文字。

如需 Bing 拼字檢查 REST API 的詳細資訊，請參閱[拼字檢查 API v7 參考](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)。

### <a name="sending-the-request"></a>傳送要求

`SendRequestAsync`方法對 Bing 拼字檢查 REST API 的 GET 要求，並傳回回應：

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

這個方法會傳送 GET 要求來`SpellCheck`API，要求 URL 指定的文字轉譯，與拼字檢查模式。 然後回應是閱讀，並傳回至呼叫的方法。

`SpellCheck` API 會在回應中，提供要求是有效的表示要求成功，且要求的資訊會在回應中傳送 HTTP 狀態碼 200 （確定）。 如需回應物件的清單，請參閱 <<c0> [ 回應物件](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects)。

### <a name="processing-the-response"></a>處理回應

API 回應會傳回 JSON 格式。 下列 JSON 資料顯示拼字錯誤文字的回應訊息`Go shappin tommorow`:

```json
{  
   "_type":"SpellCheck",
   "flaggedTokens":[  
      {  
         "offset":3,
         "token":"shappin",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"shopping",
               "score":1
            }
         ]
      },
      {  
         "offset":11,
         "token":"tommorow",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"tomorrow",
               "score":1
            }
         ]
      }
   ],
   "correctionType":"High"
}
```

`flaggedTokens`陣列會包含在文字中標示為不正確拼字或文法不正確的字組的陣列。 陣列會是空白，如果不找到任何拼字或文法錯誤。 陣列中的標記如下：

- `offset` -從文字字串的開頭至標示的文字之以零起始的位移。
- `token` – 正確拼字或文法錯誤的文字字串中的單字。
- `type` – 錯誤造成的單字會加上旗標的類型。 有兩個可能的值 –`RepeatedToken`和`UnknownToken`。
- `suggestions` – 會更正拼字或文法錯誤的文字陣列。 陣列組成`suggestion`和`score`，這表示的建議的修正方法是在正確的信賴等級。

在範例應用程式中，JSON 回應還原序列化為`SpellCheckResult`執行個體，以傳回給呼叫的方法，以便顯示結果。 下列程式碼範例示範如何`SpellCheckResult`執行個體都針對顯示處理：

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

此程式碼會逐一`FlaggedTokens`集合並取代任何拼字錯誤或文法不正確的字，在第一個建議的來源文字。 下列螢幕擷取畫面顯示之前和之後的拼字檢查：

![](spell-check-images/before-spell-check.png "拼字檢查之前")

![](spell-check-images/after-spell-check.png "拼字檢查之後")

> [!NOTE]
> 上述範例使用`Replace`來簡化, 但在大量的文字中, 它可能會取代錯誤的 token。 API 提供的`offset`值應該用於生產應用程式, 以識別來源文字中要執行更新的正確位置。

## <a name="summary"></a>總結

這篇文章說明如何使用 Bing 拼字檢查 REST API 來更正 Xamarin.Forms 應用程式中的拼字錯誤。 Bing 拼字檢查，會執行內容的拼字檢查的文字，提供針對拼錯的字組內嵌建議。

## <a name="related-links"></a>相關連結

- [Bing 拼字檢查文件](/azure/cognitive-services/bing-spell-check/)
- [使用 RESTful Web 服務](~/xamarin-forms/data-cloud/web-services/rest.md)
- [待辦事項 Cognitive Services （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Bing 拼字檢查 API v7 參考](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
