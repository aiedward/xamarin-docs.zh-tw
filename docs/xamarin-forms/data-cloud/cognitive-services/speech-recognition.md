---
title: "使用 Bing 語音 API 的語音辨識"
description: "Bing 語音 API 是以雲端為基礎的 API，可讓演算法，以處理所使用的語言。 本文說明如何使用 Bing 語音辨識 REST API 將音訊轉換成 Xamarin.Forms 應用程式中的文字。"
ms.topic: article
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 186ea6277ec7bd4ceb52855186e6fd88344b1b86
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="speech-recognition-using-the-bing-speech-api"></a>使用 Bing 語音 API 的語音辨識

_Bing 語音 API 是以雲端為基礎的 API，可讓演算法，以處理所使用的語言。本文說明如何使用 Bing 語音辨識 REST API 將音訊轉換成 Xamarin.Forms 應用程式中的文字。_

![](~/media/shared/preview.png "這個 API 已發行目前前")

> [!NOTE]
> Bing 語音 API 仍處於預覽狀態。 那里可能中斷變更，最終發行前的 api。

## <a name="overview"></a>總覽

Bing 語音 API 有兩個元件：

- 將所說的文字轉換成文字語音辨識應用程式開發介面。 可透過 REST API、 用戶端程式庫或服務程式庫執行語音辨識。
- 文字轉換語音 API 將文字轉換成所說的文字。 透過 REST API 執行文字轉換語音轉換。

本文著重在執行語音辨識透過 REST API。 雖然用戶端和服務的程式庫支援傳回部分結果，REST API 只能傳回單一的辨識結果，沒有任何部分結果。

必須使用 Bing 語音辨識 API 來取得 API 金鑰。 這可在取得[開始免費使用](https://www.microsoft.com/cognitive-services/sign-up?ReturnUrl=/cognitive-services/subscriptions?productId=%2fproducts%2fBing.Speech.Preview)microsoft.com 上。

如需 Bing 語音 API 的詳細資訊，請參閱[Bing 語音文件](https://www.microsoft.com/cognitive-services/Speech-api/documentation/overview)microsoft.com 上。

## <a name="authentication"></a>驗證

Bing 語音辨識 REST API 的每個要求都需要可以從位於的認知的服務權杖服務取得 JSON Web Token (JWT) 存取語彙基元`https://api.cognitive.microsoft.com/sts/v1.0/issueToken`。 可以取得 token，藉由 POST 要求權杖的服務，指定`Ocp-Apim-Subscription-Key`標頭，其中包含做為其值的 API 金鑰。

下列程式碼範例示範如何要求存取權杖從權杖的服務：

```csharp
async Task<string> FetchTokenAsync(string fetchUri, string apiKey)
{
  using (var client = new HttpClient())
  {
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";

    var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
  }
}
```

傳回的存取權杖中，是 Base64 文字時，有到期時間為 10 分鐘。 因此，範例應用程式更新存取權杖每隔 9 分鐘。

必須指定每個 Bing 語音辨識 REST API 存取權杖呼叫做為`Authorization`加上字串的標頭`Bearer`，如下列程式碼範例所示：

```csharp
using (var httpClient = new HttpClient())
{
  httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
  ...
}  
```

若將有效的存取語彙基元傳遞至 Bing 語音辨識 REST API 將會導致 403 回應錯誤。

## <a name="performing-speech-recognition"></a>執行語音辨識

語音辨識達成方式是在 POST 要求`recognize`API `https://speech.platform.bing.com/recognize`。 單一要求不能包含超過 10 秒的音訊和總計要求持續時間不能超過 14 的秒數。

音訊內容必須放置在 POST 要求的主體 wav 格式。 支援的轉碼器的相關資訊，請參閱[支援的轉碼器](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#supported-codecs)microsoft.com 上。

範例應用程式、`RecognizeSpeechAsync`方法會叫用語音辨識程序：

```csharp
public async Task<SpeechResult> RecognizeSpeechAsync(string filename)
{
    ...

    // Read audio file to a stream
    var file = await PCLStorage.FileSystem.Current.LocalStorage.GetFileAsync(filename);
    var fileStream = await file.OpenAsync(PCLStorage.FileAccess.Read);

    // Send audio stream to Bing and deserialize the response
    string requestUri = GenerateRequestUri(Constants.SpeechRecognitionEndpoint);
    string accessToken = authenticationService.GetAccessToken();
    var response = await SendRequestAsync(fileStream, requestUri, accessToken, Constants.AudioContentType);
    var speechResults = JsonConvert.DeserializeObject<SpeechResults>(response);

    fileStream.Dispose();
    return speechResults.results.FirstOrDefault();
}
```

音訊會記錄每個平台專屬專案中做為 PCM wav 資料，而`RecognizeSpeechAsync`方法會使用`PCLStorage`NuGet 封裝來開啟資料流的形式的音訊檔案。 語音辨識要求 URI 會產生存取權杖會從權杖的服務。 語音辨識要求公佈到`recognize`API，其會傳回包含結果的 JSON 回應。 JSON 回應還原序列化時，傳回給呼叫的方法，以便顯示的結果。

### <a name="configuring-speech-recognition"></a>設定語音辨識

指定 HTTP 查詢參數，可以設定語音辨識程序。 有強制和選擇性參數，使用下列方法會顯示必須設定的強制參數：

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    string requestUri = speechEndpoint;
    requestUri += @"?scenarios=ulm";                                    // websearch is the other option
    requestUri += @"&appid=D4D52672-91D7-4C74-8AD8-42B1D98141A5";       // You must use this ID
    requestUri += @"&locale=en-US";                                     // Other languages supported
    requestUri += string.Format("&device.os={0}", operatingSystem);     // Open field
    requestUri += @"&version=3.0";                                      // Required value
    requestUri += @"&format=json";                                      // Required value
    requestUri += @"&instanceid=fe34a4de-7927-4e24-be60-f0629ce1d808";  // GUID for device making the request
    requestUri += @"&requestid=" + Guid.NewGuid().ToString();           // GUID for the request
    return requestUri;
}
```

所執行的主要組態`GenerateRequestUri`方法是將音訊內容的地區設定。 如需支援的地區設定的清單，請參閱[支援的地區設定](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#supported-locales)microsoft.com 上。

如需有關強制參數的可能值的詳細資訊，請參閱[必要參數](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#required-parameters)microsoft.com 上。選擇性參數的相關資訊，請參閱[選擇性參數](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition)microsoft.com 上。

### <a name="sending-the-request"></a>傳送要求

`SendRequestAsync`方法對 Bing 語音辨識 REST API 的 POST 要求，並傳回回應：

```csharp
async Task<string> SendRequestAsync(Stream fileStream, string url, string bearerToken, string contentType)
{
    var content = new StreamContent(fileStream);
    content.Headers.TryAddWithoutValidation("Content-Type", contentType);

    using (var httpClient = new HttpClient())
    {
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
        var response = await httpClient.PostAsync(url, content);

        return await response.Content.ReadAsStringAsync();
    }
}
```

這個方法建置的 POST 要求：

- 換行中的音訊資料流`StreamContent`執行個體，這會提供 HTTP 內容資料流為基礎。
- 設定`Content-Type`以要求標頭`audio/wav; codec="audio/pcm"; samplerate=16000`。
- 加入存取語彙基元`Authorization`標頭，加上字串`Bearer`。

然後會在 POST 要求傳送至`recognize`應用程式開發介面。 回應然後讀取，並傳回至呼叫的方法。

`recognize` API 回應，提供該要求是否有效，表示要求成功，並要求的資訊回應中傳送 HTTP 狀態碼 200 （確定）。 如需可能的錯誤回應的清單，請參閱[錯誤回應](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#error-responses)microsoft.com 上。

### <a name="processing-the-response"></a>處理回應

API 回應會以 JSON 格式傳回與已辨識的文字中包含`name`標記。 下列 JSON 資料會顯示一般的成功回應訊息：

```csharp
{
  "version": "3.0",
  "header": {
    "status": "success",
    "scenario": "ulm",
    "name": "go shopping tomorrow",
    "lexical": "go shopping tomorrow",
    "properties": {
      "requestid": "e06c059d-fa37-4bb1-843f-4914350279a8",
      "HIGHCONF": "1"
    }
  },
  "results": [
    {
      "scenario": "ulm",
      "name": "go shopping tomorrow",
      "lexical": "go shopping tomorrow",
      "confidence": "0.9493451",
      "properties": {
        "HIGHCONF": "1"
      }
    }
  ]
}
```

在範例應用程式中，JSON 回應還原序列化為`SpeechResult`執行個體，而結果傳回給呼叫的方法，以便顯示，如下列螢幕擷取畫面所示：

![](speech-recognition-images/speech-recognition.png "語音辨識")

每個 JSON 標記值的相關資訊，請參閱[語音辨識回應](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#speech-recognition-responses)microsoft.com 上。

## <a name="summary"></a>總結

本文說明如何使用 Bing 語音辨識 REST API 將音訊轉換成 Xamarin.Forms 應用程式中的文字。 除了執行語音辨識，Bing 語音 API 可以也將文字轉換為語音。



## <a name="related-links"></a>相關連結

- [Bing 語音文件](https://www.microsoft.com/cognitive-services/Speech-api/documentation/overview)
- [使用 RESTful Web 服務](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Todo 認知服務 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Bing 語音辨識 REST API](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition)
