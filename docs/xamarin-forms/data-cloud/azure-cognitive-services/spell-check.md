---
title: 使用 Bing 拼寫檢查 API 的拼寫檢查
description: Bing 拼寫檢查會對文字執行內容相關的拼寫檢查，為拼錯的字組提供內嵌建議。 本文說明如何使用 Bing 拼寫檢查 REST API 更正應用程式中的拼寫錯誤 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4446e92ee1d9b6938464375ba36c6e8e4cdfed6b
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365784"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>使用 Bing 拼寫檢查 API 的拼寫檢查

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Bing 拼寫檢查會對文字執行內容相關的拼寫檢查，為拼錯的字組提供內嵌建議。本文說明如何使用 Bing 拼寫檢查 REST API 更正應用程式中的拼寫錯誤 Xamarin.Forms 。_

## <a name="overview"></a>概觀

Bing 拼寫檢查 REST API 有兩種作業模式，而在對 API 提出要求時必須指定模式：

- `Spell` 更正簡短文字 (最多9個單字) 不會有任何大小寫變更。
- `Proof` 更正長文字、提供大小寫更正和基本標點符號，並隱藏積極的修正。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

您必須取得 API 金鑰，才能使用 Bing 拼寫檢查 API。 這可在[試用認知服務](https://azure.microsoft.com/try/cognitive-services/)中取得

如需 Bing 拼寫檢查 API 所支援的語言清單，請參閱 [支援的語言](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/)。 如需 Bing 拼寫檢查 API 的詳細資訊，請參閱 [Bing 拼寫檢查檔](/azure/cognitive-services/bing-spell-check/)。

## <a name="authentication"></a>驗證

對 Bing 拼寫檢查 API 提出的每個要求都需要一個應指定為標頭值的 API 金鑰 `Ocp-Apim-Subscription-Key` 。 下列程式碼範例顯示如何將 API 金鑰新增至要求的 `Ocp-Apim-Subscription-Key` 標頭：

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

若無法將有效的 API 金鑰傳遞給 Bing 拼寫檢查 API，將會導致401回應錯誤。

## <a name="performing-spell-checking"></a>執行拼寫檢查

您可以對 API 進行 GET 或 POST 要求，以達成拼寫檢查 `SpellCheck` `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck` 。 提出 GET 要求時，會以查詢參數的形式傳送要拼寫檢查的文字。 提出 POST 要求時，會在要求主體中傳送拼寫檢查的文字。 由於查詢參數字串長度的限制，GET 要求受限於拼寫檢查1500個字元。 因此，除非拼寫較短的字串，否則通常應該進行 POST 要求。

在範例應用程式中，此方法會叫用 `SpellCheckTextAsync` 拼寫檢查進程：

```csharp
public async Task<SpellCheckResult> SpellCheckTextAsync(string text)
{
    string requestUri = GenerateRequestUri(Constants.BingSpellCheckEndpoint, text, SpellCheckMode.Spell);
    var response = await SendRequestAsync(requestUri);
    var spellCheckResults = JsonConvert.DeserializeObject<SpellCheckResult>(response);
    return spellCheckResults;
}
```

`SpellCheckTextAsync`方法會產生要求 URI，然後將要求傳送至 `SpellCheck` API，該 API 會傳回包含結果的 JSON 回應。 JSON 回應會還原序列化，並將結果傳回給呼叫的方法以供顯示。

### <a name="configuring-spell-checking"></a>設定拼寫檢查

您可以藉由指定 HTTP 查詢參數來設定拼寫檢查進程：

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

這個方法會設定要拼寫檢查的文字，以及拼寫檢查模式。

如需 Bing 拼寫檢查 REST API 的詳細資訊，請參閱 [拼寫檢查 API v7 參考](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)。

### <a name="sending-the-request"></a>正在傳送要求

`SendRequestAsync`方法會 REST API Bing 拼寫檢查的 GET 要求，並傳迴響應：

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

這個方法會將 GET 要求傳送至 `SpellCheck` API，並以要求 URL 指定要轉譯的文字，以及拼寫檢查模式。 接著會讀取回應，然後將回應傳回給呼叫方法。

`SpellCheck`API 會在回應中傳送 HTTP 狀態碼 200 (確定) ，但前提是要求是有效的，這表示要求成功，且要求的資訊會在回應中。 如需回應物件的清單，請參閱 [回應物件](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects)。

### <a name="processing-the-response"></a>處理回應

API 回應會以 JSON 格式傳回。 下列 JSON 資料顯示拼錯文字的回應訊息 `Go shappin tommorow` ：

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

`flaggedTokens`陣列包含文字中標示為未正確拼寫或語法不正確的單字陣列。 如果找不到拼寫或文法錯誤，陣列就會是空的。 陣列中的標記如下：

- `offset` –從文字字串開頭以零為起始的位移到已加上旗標的單字。
- `token` –文字字串中拼寫不正確或語法不正確的文字。
- `type` –造成字組被加上旗標的錯誤類型。 有兩個可能的值– `RepeatedToken` 和 `UnknownToken` 。
- `suggestions` –將更正拼寫或文法錯誤的單字陣列。 陣列是由和所組成 `suggestion` `score` ，表示建議的更正正確的信賴等級。

在範例應用程式中，JSON 回應會還原序列化為 `SpellCheckResult` 實例，並將結果傳回給呼叫方法以供顯示。 下列程式碼範例顯示如何 `SpellCheckResult` 處理實例以供顯示：

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

這段程式碼會逐一查看 `FlaggedTokens` 集合，並使用第一個建議來取代來源文字中任何拼錯或不正確的字組。 下列螢幕擷取畫面顯示在拼寫檢查之前和之後：

![拼寫檢查之前](spell-check-images/before-spell-check.png)

![拼寫檢查之後](spell-check-images/after-spell-check.png)

> [!NOTE]
> 上述範例使用 `Replace` 的是簡單的，但在大量的文字中，它可能會取代錯誤的標記。 API 提供的 `offset` 值應該用於生產應用程式，以識別來源文字中要執行更新的正確位置。

## <a name="summary"></a>總結

本文說明如何使用 Bing 拼寫檢查 REST API 更正應用程式中的拼寫錯誤 Xamarin.Forms 。 Bing 拼寫檢查會對文字執行內容相關的拼寫檢查，為拼錯的字組提供內嵌建議。

## <a name="related-links"></a>相關連結

- [Bing 拼寫檢查檔](/azure/cognitive-services/bing-spell-check/)
- [使用 RESTful Web 服務](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Todo 認知服務 (範例) ](/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Bing 拼字檢查 API v7 參考](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)