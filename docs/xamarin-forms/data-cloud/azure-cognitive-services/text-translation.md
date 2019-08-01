---
title: 文字轉譯使用 Translator API
description: Microsoft Translator API 可用來將語音及透過 REST API 的文字翻譯。 這篇文章說明如何使用 Microsoft Translator Text API 來轉譯至 Xamarin.Forms 應用程式中的另一種語言的文字。
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 5739246ec7804b58d900ec790f427dab37504b1f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655023"
---
# <a name="text-translation-using-the-translator-api"></a>文字轉譯使用 Translator API

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft Translator API 可用來將語音及透過 REST API 的文字翻譯。這篇文章說明如何使用 Microsoft Translator Text API 來轉譯至 Xamarin.Forms 應用程式中的另一種語言的文字。_

## <a name="overview"></a>總覽

Translator API 有兩個元件：

- 文字轉譯 REST API，將從一種語言的文字翻譯成另一種語言的文字。 API 會自動偵測已傳送之前將它轉譯文字的語言。
- 語音翻譯來轉譯文字中的另一種語言的一種語言的語音功能的 REST API。 API 也整合了傳回翻譯的文字的讀出的文字轉換語音功能。

本文著重於翻譯文字從某種語言到另一個使用 Translator Text API。

若要使用 Translator Text API，必須取得 API 金鑰。 這可在取得[如何註冊 Microsoft Translator Text API](/azure/cognitive-services/translator/translator-text-how-to-signup/)。

如需有關 Microsoft Translator Text API 的詳細資訊，請參閱 < [Translator Text API 文件](/azure/cognitive-services/translator/)。

## <a name="authentication"></a>驗證

對 Translator Text API 的每個要求需要 JSON Web 權杖 (JWT) 存取權杖，可從認知服務權杖服務在`https://api.cognitive.microsoft.com/sts/v1.0/issueToken`。 POST 要求給權杖的服務，也可以取得語彙基元指定`Ocp-Apim-Subscription-Key`包含 API 金鑰，做為其值的標頭。

下列程式碼範例會示範如何要求存取權杖的權杖服務：

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

傳回的存取權杖，也就是 Base64 文字，有 10 分鐘到期時間。 因此，範例應用程式更新的存取權杖每隔 9 分鐘。

存取權杖必須指定每 Translator Text API 呼叫作為`Authorization`加上字串的標頭`Bearer`，如下列程式碼範例所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

如需詳細的認知服務權杖服務的詳細資訊，請參閱[驗證語彙基元 API](http://docs.microsofttranslator.com/oauth-token.html)。

## <a name="performing-text-translation"></a>執行文字翻譯

文字翻譯，即可進行 GET 要求來`translate`API `https://api.microsofttranslator.com/v2/http.svc/translate`。 在範例應用程式中，`TranslateTextAsync`方法會叫用的文字轉譯程序：

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

`TranslateTextAsync`方法會產生要求 URI，並擷取存取權杖，向權杖服務。 文字轉譯要求然後傳送至`translate`API，它會傳回包含結果的 XML 回應。 剖析 XML 回應，並轉譯結果會傳回到呼叫的方法，以便顯示。

如需文字轉譯的 REST Api 的詳細資訊，請參閱[Microsoft Translator Text API](http://docs.microsofttranslator.com/text-translate.html)。

### <a name="configuring-text-translation"></a>設定文字翻譯

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

這個方法會設定要轉譯的文字和翻譯的文字語言。 如需 Microsoft Translator 支援的語言的清單，請參閱 < [Microsoft Translator Text API 中支援的語言](/azure/cognitive-services/translator/languages/)。

> [!NOTE]
> 如果應用程式必須知道何種語言的文字是，`Detect`可以呼叫 API，來偵測文字字串的語言。

### <a name="sending-the-request"></a>傳送要求

`SendRequestAsync`方法到文字翻譯 REST API 提出 GET 要求，並傳回回應：

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

這個方法會藉由新增存取權杖，才能建置 GET 要求`Authorization`標頭，加上字串`Bearer`。 GET 要求然後傳送至`translate`API，要求 URL 指定的文字轉譯，與要翻譯的文字的語言。 然後回應是閱讀，並傳回至呼叫的方法。

`translate` API 會在回應中，提供要求是有效的表示要求成功，且要求的資訊會在回應中傳送 HTTP 狀態碼 200 （確定）。 如需可能的錯誤回應的清單，請參閱在回應訊息[取得翻譯](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate)。

### <a name="processing-the-response"></a>處理回應

API 回應會傳回 XML 格式。 下列 XML 資料會顯示一般的成功回應訊息：

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

範例應用程式中的 XML 回應剖析成`XDocument`執行個體，而 XML 根值傳回給呼叫的方法，以便顯示如下列螢幕擷取畫面所示：

![](text-translation-images/text-translation.png "為德文的文字翻譯")

## <a name="summary"></a>總結

這篇文章說明如何使用 Microsoft Translator Text API，將從一種語言的文字翻譯文字中的 Xamarin.Forms 應用程式中的另一種語言。 除了翻譯文字，Microsoft Translator API 也 ip-pbx 從一種語言的語音文字中的另一種語言。

## <a name="related-links"></a>相關連結

- [Translator Text API 文件](/azure/cognitive-services/translator/)。
- [使用 RESTful Web 服務](~/xamarin-forms/data-cloud/web-services/rest.md)
- [待辦事項 Cognitive Services （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Microsoft Translator Text API](http://docs.microsofttranslator.com/text-translate.html)。
