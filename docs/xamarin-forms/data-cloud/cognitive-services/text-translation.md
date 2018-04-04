---
title: 文字轉譯使用轉譯器應用程式開發介面
description: Microsoft 轉譯器 API 可以用來轉譯語音和透過 REST API 的文字。 本文說明如何使用 Microsoft 轉譯程式文字 API 翻譯一種語言到另一個 Xamarin.Forms 應用程式中的文字。
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 5c1001335fb030f9a91ec72456042316864ccf5c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="text-translation-using-the-translator-api"></a>文字轉譯使用轉譯器應用程式開發介面

_Microsoft 轉譯器 API 可以用來轉譯語音和透過 REST API 的文字。本文說明如何使用 Microsoft 轉譯程式文字 API 翻譯一種語言到另一個 Xamarin.Forms 應用程式中的文字。_

## <a name="overview"></a>總覽

轉譯程式 API 有兩個元件：

- 文字轉譯 REST API，將文字從一個語言轉譯成另一種語言的文字。 應用程式開發介面會自動偵測之前將它轉譯已傳送的文字的語言。
- 語音轉譯 REST API，同時從一種語言的語音文字中的另一種語言。 此 API 也整合回翻譯的文字的讀出的文字轉換語音功能。

本文著重在將轉譯到另一個使用轉譯程式文字 API 從一種語言的文字。

必須使用轉譯程式文字 API 來取得 API 金鑰。 這可在取得[如何註冊 Microsoft 轉譯程式文字 API](/azure/cognitive-services/translator/translator-text-how-to-signup/)。

如需 Microsoft 轉譯程式文字 API 的詳細資訊，請參閱[轉譯程式文字 API 文件](/azure/cognitive-services/translator/)。

## <a name="authentication"></a>驗證

轉譯程式文字 API 的每個要求都需要可以從位於的認知的服務權杖服務取得 JSON Web Token (JWT) 存取語彙基元`https://api.cognitive.microsoft.com/sts/v1.0/issueToken`。 可以取得 token，藉由 POST 要求權杖的服務，指定`Ocp-Apim-Subscription-Key`標頭，其中包含做為其值的 API 金鑰。

下列程式碼範例示範如何要求存取權杖從權杖的服務：

```csharp
public AuthenticationService(string apiKey)
{
    subscriptionKey = apiKey;
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
}
...
async Task<string> FetchTokenAsync(string fetchUri)
{
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";
    var result = await httpClient.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
}
```

傳回的存取權杖中，是 Base64 文字時，有到期時間為 10 分鐘。 因此，範例應用程式更新存取權杖每隔 9 分鐘。

必須指定每個轉譯器文字 API 存取權杖呼叫做為`Authorization`加上字串的標頭`Bearer`，如下列程式碼範例所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

如需認知的服務權杖服務的詳細資訊，請參閱[驗證語彙基元 API](http://docs.microsofttranslator.com/oauth-token.html)。

## <a name="performing-text-translation"></a>執行文字轉譯

文字轉譯可藉由在 GET 要求`translate`API `https://api.microsofttranslator.com/v2/http.svc/translate`。 範例應用程式、`TranslateTextAsync`方法會叫用文字轉譯程序：

```csharp
public async Task<string> TranslateTextAsync(string text)
{
  ...
  string requestUri = GenerateRequestUri(Constants.TextTranslatorEndpoint, text, "en", "de");
  string accessToken = authenticationService.GetAccessToken();
  var response = await SendRequestAsync(requestUri, accessToken);
  var xml = XDocument.Parse(response);
  return xml.Root.Value;
}
```

`TranslateTextAsync`方法會產生要求 URI，並從權杖的服務擷取存取權杖。 文字轉譯要求然後傳送至`translate`應用程式開發介面，它會傳回包含結果的 XML 回應。 會剖析 XML 回應，並轉譯結果會傳回到呼叫的方法，以便顯示。

如需文字轉譯 REST Api 的詳細資訊，請參閱[Microsoft 轉譯程式文字 API](http://docs.microsofttranslator.com/text-translate.html)。

### <a name="configuring-text-translation"></a>設定文字轉譯

您可以藉由指定 HTTP 查詢參數設定文字轉譯程序：

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

這個方法會設定要將翻譯的文字和語言翻譯的文字。 如需支援的 Microsoft Translator 語言的清單，請參閱[Microsoft 轉譯程式文字 API 中支援的語言](/azure/cognitive-services/translator/languages/)。

> [!NOTE]
> 如果應用程式必須知道何種語言的文字中，是`Detect`偵測文字字串的語言，可以呼叫 API。

### <a name="sending-the-request"></a>傳送要求

`SendRequestAsync`方法對文字轉譯 REST API 的 GET 要求，並傳回回應：

```csharp
async Task<string> SendRequestAsync(string url, string bearerToken)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

這個方法會將存取權杖，才能建置 GET 要求`Authorization`標頭，加上字串`Bearer`。 然後會傳送 GET 要求至`translate`API，以要求 URL 指定的文字轉譯，以及語言翻譯的文字。 回應然後讀取，並傳回至呼叫的方法。

`translate` API 回應，提供該要求是否有效，表示要求成功，並要求的資訊回應中傳送 HTTP 狀態碼 200 （確定）。 如需可能的錯誤回應的清單，請參閱 < 在回應訊息[取得翻譯](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate)。

### <a name="processing-the-response"></a>處理回應

API 回應是以 XML 格式傳回。 下列 XML 資料會顯示一般的成功回應訊息：

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

範例應用程式中的 XML 回應會剖析成`XDocument`執行個體，而 XML 根值傳回給呼叫的方法，以便顯示在下列螢幕擷取畫面所示：

![](text-translation-images/text-translation.png "設為德文的文字轉譯")

## <a name="summary"></a>總結

本文說明如何使用 Microsoft 轉譯程式文字 API，將文字從一個語言轉譯成 Xamarin.Forms 應用程式中的另一種語言的文字。 除了轉譯文字時，Microsoft 轉譯程式 API 也同時從一種語言的語音文字中的另一種語言。

## <a name="related-links"></a>相關連結

- [轉譯程式文字 API 文件](/azure/cognitive-services/translator/)。
- [使用 RESTful Web 服務](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Todo 認知服務 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Microsoft Translator 文字 API](http://docs.microsofttranslator.com/text-translate.html)。
