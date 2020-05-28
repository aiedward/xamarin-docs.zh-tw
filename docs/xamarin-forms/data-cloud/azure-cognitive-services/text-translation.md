---
title: ''
description: Microsoft Translator API 可透過 REST API 用來轉譯語音和文字。 本文說明如何使用 Microsoft 翻譯工具文字 API，在應用程式中將文字翻譯成另一種語言 Xamarin.Forms 。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f0f43f8f2113b6bd0a800ed3e0bd96b641575b1c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139278"
---
# <a name="text-translation-using-the-translator-api"></a>使用翻譯工具 API 進行文字翻譯

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft Translator API 可透過 REST API 用來轉譯語音和文字。本文說明如何使用 Microsoft 翻譯工具文字 API，在應用程式中將文字翻譯成另一種語言 Xamarin.Forms 。_

## <a name="overview"></a>概觀

Translator API 有兩個元件：

- 文字翻譯 REST API 將文字從一種語言翻譯成另一種語言的文字。 API 會自動偵測在翻譯之前所傳送文字的語言。
- 語音翻譯 REST API 將語音從一種語言轉譯成另一種語言的文字。 此 API 也會整合文字轉換語音功能，以說出翻譯的文字。

本文著重于使用翻譯工具文字 API，將文字翻譯成另一種語言。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

必須取得 API 金鑰，才能使用翻譯工具文字 API。 這可以在[如何註冊 Microsoft 翻譯工具文字 API](/azure/cognitive-services/translator/translator-text-how-to-signup/)中取得。

如需有關 Microsoft 翻譯工具文字 API 的詳細資訊，請參閱[翻譯工具文字 API 檔](/azure/cognitive-services/translator/)。

## <a name="authentication"></a>驗證

對翻譯工具文字 API 提出的每個要求都需要 JSON Web 權杖（JWT）存取權杖，其可從的認知服務權杖服務取得 `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` 。 您可以對權杖服務提出 POST 要求來取得權杖，並指定 `Ocp-Apim-Subscription-Key` 包含 API 金鑰做為其值的標頭。

下列程式碼範例顯示如何從權杖服務要求存取權杖：

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

傳回的存取權杖，也就是 Base64 文字，其到期時間為10分鐘。 因此，範例應用程式每隔9分鐘就會更新存取權杖。

必須在每個翻譯工具文字 API 呼叫中指定存取權杖，做為 `Authorization` 標頭前面加上字串的標題 `Bearer` ，如下列程式碼範例所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

如需認知服務權杖服務的詳細資訊，請參閱[驗證](/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="performing-text-translation"></a>執行文字轉譯

藉由對 API 提出 GET 要求，即可完成文字轉譯 `translate` `https://api.microsofttranslator.com/v2/http.svc/translate` 。 在範例應用程式中，方法會叫用 `TranslateTextAsync` 文字轉譯程式：

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

`TranslateTextAsync`方法會產生要求 URI，並從權杖服務取得存取權杖。 然後，文字翻譯要求會傳送至 `translate` API，傳回包含結果的 XML 回應。 會剖析 XML 回應，並將轉譯結果傳回給顯示的呼叫方法。

如需文字翻譯 REST Api 的詳細資訊，請參閱[翻譯工具文字 API](/azure/cognitive-services/translator/reference/v3-0-reference)。

### <a name="configuring-text-translation"></a>設定文字翻譯

您可以藉由指定 HTTP 查詢參數來設定文字翻譯程式：

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

這個方法會設定要轉譯的文字，以及要翻譯文字的語言。 如需 Microsoft Translator 支援的語言清單，請參閱[microsoft 翻譯工具文字 API 中支援的語言](/azure/cognitive-services/translator/languages/)。

> [!NOTE]
> 如果應用程式需要知道文字所在的語言， `Detect` 可以呼叫 API 來偵測文字字串的語言。

### <a name="sending-the-request"></a>傳送要求

`SendRequestAsync`方法會對文字翻譯提出 GET 要求 REST API 並傳迴響應：

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

這個方法會建立 GET 要求，方法是將存取權杖新增至 `Authorization` 標頭，並在前面加上字串 `Bearer` 。 然後，GET 要求會傳送至 `translate` API，要求 URL 會指定要轉譯的文字，以及要翻譯文字的語言。 然後會讀取回應，並將其傳回給呼叫方法。

`translate`如果要求有效，此 API 會在回應中傳送 HTTP 狀態碼200（確定），這表示要求成功，且要求的資訊在回應中。 如需可能的錯誤回應清單，請參閱[取得轉譯](/azure/cognitive-services/translator/reference/v3-0-translate)時的回應訊息。

### <a name="processing-the-response"></a>處理回應

API 回應會以 XML 格式傳回。 下列 XML 資料顯示一般成功的回應訊息：

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

在範例應用程式中，會將 XML 回應剖析成 `XDocument` 實例，並將 xml 根值傳回給顯示的呼叫方法，如下列螢幕擷取畫面所示：

![](text-translation-images/text-translation.png "Text Translation to German")

## <a name="summary"></a>摘要

本文說明如何使用 Microsoft 翻譯工具文字 API，在應用程式中將文字翻譯成另一種語言的文字 Xamarin.Forms 。 除了翻譯文字，Microsoft Translator API 也可以將語音從一種語言轉譯成另一種語言的文字。

## <a name="related-links"></a>相關連結

- [Translator Text API 文件](/azure/cognitive-services/translator/)
- [使用 RESTful Web 服務](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Todo 認知服務（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Translator Text API](/azure/cognitive-services/translator/reference/v3-0-reference)
