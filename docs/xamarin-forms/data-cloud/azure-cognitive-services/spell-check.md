---
標題：「使用 Bing 拼寫檢查 API 進行拼寫檢查」描述：「Bing 拼寫檢查會執行文字的內容拼寫檢查，並提供拼錯文字的內嵌建議。 本文說明如何使用 Bing 拼寫檢查 REST API 來更正應用程式中的拼寫錯誤 Xamarin.Forms 。」
assetid： B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：02/08/2017 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="spell-checking-using-the-bing-spell-check-api"></a>使用 Bing 拼寫檢查 API 進行拼寫檢查

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Bing 拼寫檢查會執行文字的內容拼寫檢查，為拼錯的字組提供內嵌建議。本文說明如何使用 Bing 拼寫檢查 REST API 來更正應用程式中的拼寫錯誤 Xamarin.Forms 。_

## <a name="overview"></a>概觀

Bing 拼寫檢查 REST API 有兩種作業模式，而在對 API 提出要求時，必須指定模式：

- `Spell`更正短文字（最多9個單字），不需要任何大小寫變更。
- `Proof`更正長文字、提供大小寫更正和基本標點符號，以及抑制嚴格的修正。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

必須取得 API 金鑰，才能使用 Bing 拼寫檢查 API。 這可以在試用時取得[認知服務](https://azure.microsoft.com/try/cognitive-services/)

如需 Bing 拼寫檢查 API 支援的語言清單，請參閱支援的[語言](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/)。 如需 Bing 拼寫檢查 API 的詳細資訊，請參閱[Bing 拼寫檢查檔](/azure/cognitive-services/bing-spell-check/)。

## <a name="authentication"></a>驗證

對 Bing 拼寫檢查 API 提出的每個要求都需要一個應該指定為標頭值的 API 金鑰 `Ocp-Apim-Subscription-Key` 。 下列程式碼範例顯示如何將 API 金鑰新增至要求的 `Ocp-Apim-Subscription-Key` 標頭：

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

若無法將有效的 API 金鑰傳遞給 Bing 拼寫檢查 API，將會產生401回應錯誤。

## <a name="performing-spell-checking"></a>執行拼寫檢查

藉由對 API 發出 GET 或 POST 要求，即可完成拼寫檢查 `SpellCheck` `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck` 。 提出 GET 要求時，要檢查拼寫的文字會以查詢參數的形式傳送。 發出 POST 要求時，會在要求主體中傳送要檢查拼寫的文字。 由於查詢參數字串長度限制，GET 要求僅限於拼寫檢查1500個字元。 因此，除非檢查簡短字串，否則通常應該進行 POST 要求。

在範例應用程式中，方法會叫用 `SpellCheckTextAsync` 拼寫檢查程式：

```csharp
public async Task<SpellCheckResult> SpellCheckTextAsync(string text)
{
    string requestUri = GenerateRequestUri(Constants.BingSpellCheckEndpoint, text, SpellCheckMode.Spell);
    var response = await SendRequestAsync(requestUri);
    var spellCheckResults = JsonConvert.DeserializeObject<SpellCheckResult>(response);
    return spellCheckResults;
}
```

`SpellCheckTextAsync`方法會產生要求 URI，然後將要求傳送至 `SpellCheck` API，這會傳回包含結果的 JSON 回應。 JSON 回應會還原序列化，並將結果傳回給要顯示的呼叫方法。

### <a name="configuring-spell-checking"></a>設定拼寫檢查

您可以藉由指定 HTTP 查詢參數來設定拼寫檢查流程：

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

這個方法會設定要檢查拼寫的文字和拼寫檢查模式。

如需 Bing 拼寫檢查 REST API 的詳細資訊，請參閱[拼寫檢查 API v7 參考](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)。

### <a name="sending-the-request"></a>傳送要求

`SendRequestAsync`方法會對 Bing 拼寫檢查 REST API 提出 GET 要求，並傳迴響應：

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

這個方法會將 GET 要求傳送至 `SpellCheck` API，並附上要求 URL 來指定要轉譯的文字和拼寫檢查模式。 然後會讀取回應，並將其傳回給呼叫方法。

`SpellCheck`如果要求有效，此 API 會在回應中傳送 HTTP 狀態碼200（確定），這表示要求成功，且要求的資訊在回應中。 如需回應物件的清單，請參閱[回應物件](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects)。

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

此 `flaggedTokens` 陣列包含文字中標示為不正確拼寫或語法不正確的單字陣列。 如果找不到任何拼寫或文法錯誤，陣列將會是空的。 陣列中的標記為：

- `offset`–以零為起始的位移，從文字字串的開頭到已加上旗標的單字。
- `token`–文字字串中拼寫不正確或語法不正確的單字。
- `type`–造成文字加上旗標的錯誤類型。 有兩個可能的值– `RepeatedToken` 和 `UnknownToken` 。
- `suggestions`–將更正拼寫或文法錯誤的單字陣列。 陣列是由 `suggestion` 和組成 `score` ，這表示建議更正正確的信心層級。

在範例應用程式中，JSON 回應會還原序列化成 `SpellCheckResult` 實例，並將結果傳回給要顯示的呼叫方法。 下列程式碼範例顯示如何 `SpellCheckResult` 處理實例以進行顯示：

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

此程式碼會逐一查看 `FlaggedTokens` 集合，並以第一個建議取代來源文字中拼錯或不正確的任何字組。 下列螢幕擷取畫面顯示在拼寫檢查之前和之後：

![](spell-check-images/before-spell-check.png "Before Spell Check")

![](spell-check-images/after-spell-check.png "After Spell Check")

> [!NOTE]
> 上述範例使用 `Replace` 來簡化，但在大量的文字中，它可能會取代錯誤的 token。 API 提供的 `offset` 值應該用於生產應用程式，以識別來源文字中要執行更新的正確位置。

## <a name="summary"></a>摘要

本文說明如何使用 Bing 拼寫檢查 REST API 來更正應用程式中的拼寫錯誤 Xamarin.Forms 。 Bing 拼寫檢查會執行文字的內容拼寫檢查，為拼錯的字組提供內嵌建議。

## <a name="related-links"></a>相關連結

- [Bing 拼寫檢查檔](/azure/cognitive-services/bing-spell-check/)
- [使用 RESTful Web 服務](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Todo 認知服務（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Bing 拼字檢查 API v7 參考](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
