---
title: 使用 Microsoft 語音 API 的語音辨識
description: Microsoft 語音 API 是以雲端為基礎的 API，可讓演算法，以處理所使用的語言。 本文說明如何使用 Microsoft 語音辨識 REST API 將音訊轉換成 Xamarin.Forms 應用程式中的文字。
ms.topic: article
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 2230b11f9553fb779a86d7504ed5507d2e7cbaa7
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="speech-recognition-using-the-microsoft-speech-api"></a>使用 Microsoft 語音 API 的語音辨識

_Microsoft 語音 API 是以雲端為基礎的 API，可讓演算法，以處理所使用的語言。本文說明如何使用 Microsoft 語音辨識 REST API 將音訊轉換成 Xamarin.Forms 應用程式中的文字。_

## <a name="overview"></a>總覽

Microsoft 語音 API 有兩個元件：

- 將所說的文字轉換成文字語音辨識應用程式開發介面。 可透過 REST API、 用戶端程式庫或服務程式庫執行語音辨識。
- 文字轉換語音 API 將文字轉換成所說的文字。 透過 REST API 執行文字轉換語音轉換。

本文著重在執行語音辨識透過 REST API。 雖然用戶端和服務的程式庫支援傳回部分結果，REST API 只能傳回單一的辨識結果，沒有任何部分結果。

必須使用 Microsoft 語音 API 來取得 API 金鑰。 這可在取得[再試一次認知服務](https://azure.microsoft.com/try/cognitive-services/)。

如需 Microsoft 語音 API 的詳細資訊，請參閱[Microsoft 語音 API 文件](/azure/cognitive-services/speech/home/)。

## <a name="authentication"></a>驗證

Microsoft 語音 REST API 的每個要求都需要可以從位於的認知的服務權杖服務取得 JSON Web Token (JWT) 存取語彙基元`https://api.cognitive.microsoft.com/sts/v1.0/issueToken`。 可以取得 token，藉由 POST 要求權杖的服務，指定`Ocp-Apim-Subscription-Key`標頭，其中包含做為其值的 API 金鑰。

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

必須指定每個 Microsoft 語音 REST API 的存取權杖呼叫做為`Authorization`加上字串的標頭`Bearer`，如下列程式碼範例所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

要傳遞至 Microsoft 語音 REST API 的 有效存取權杖失敗會導致 403 回應時發生錯誤。

## <a name="performing-speech-recognition"></a>執行語音辨識

語音辨識達成方式是在 POST 要求`recognition`API `https://speech.platform.bing.com/speech/recognition/`。 單一要求不能包含超過 10 秒的音訊和總計要求持續時間不能超過 14 的秒數。

音訊內容必須放置在 POST 要求的主體 wav 格式。

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
    var speechResult = JsonConvert.DeserializeObject<SpeechResult>(response);

    fileStream.Dispose();
    return speechResult;
}
```

音訊會記錄每個平台專屬專案中做為 PCM wav 資料，而`RecognizeSpeechAsync`方法會使用`PCLStorage`NuGet 封裝來開啟資料流的形式的音訊檔案。 語音辨識要求 URI 會產生存取權杖會從權杖的服務。 語音辨識要求公佈到`recognition`API，其會傳回包含結果的 JSON 回應。 JSON 回應還原序列化時，傳回給呼叫的方法，以便顯示的結果。

### <a name="configuring-speech-recognition"></a>設定語音辨識

您可以藉由指定 HTTP 查詢參數設定語音辨識程序：

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    // To build a request URL, you should follow:
    // https://docs.microsoft.com/en-us/azure/cognitive-services/speech/getstarted/getstartedrest
    string requestUri = speechEndpoint;
    requestUri += @"dictation/cognitiveservices/v1?";
    requestUri += @"language=en-us";
    requestUri += @"&format=simple";
    System.Diagnostics.Debug.WriteLine(requestUri.ToString());
    return requestUri;
}
```

所執行的主要組態`GenerateRequestUri`方法是將音訊內容的地區設定。 如需支援的地區設定的清單，請參閱[支援的語言](/azure/cognitive-services/speech/api-reference-rest/supportedlanguages/)。

### <a name="sending-the-request"></a>傳送要求

`SendRequestAsync`方法對 Microsoft 語音 REST API 的 POST 要求，並傳回回應：

```csharp
async Task<string> SendRequestAsync(Stream fileStream, string url, string bearerToken, string contentType)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var content = new StreamContent(fileStream);
    content.Headers.TryAddWithoutValidation("Content-Type", contentType);
    var response = await httpClient.PostAsync(url, content);
    return await response.Content.ReadAsStringAsync();
}
```

這個方法建置的 POST 要求：

- 換行中的音訊資料流`StreamContent`執行個體，這會提供 HTTP 內容資料流為基礎。
- 設定`Content-Type`以要求標頭`audio/wav; codec="audio/pcm"; samplerate=16000`。
- 加入存取語彙基元`Authorization`標頭，加上字串`Bearer`。

然後會在 POST 要求傳送至`recognition`應用程式開發介面。 回應然後讀取，並傳回至呼叫的方法。

`recognition` API 回應，提供該要求是否有效，表示要求成功，並要求的資訊回應中傳送 HTTP 狀態碼 200 （確定）。 如需可能的錯誤回應的清單，請參閱[疑難排解](/azure/cognitive-services/speech/troubleshooting)。

### <a name="processing-the-response"></a>處理回應

API 回應會以 JSON 格式傳回與已辨識的文字中包含`name`標記。 下列 JSON 資料會顯示一般的成功回應訊息：

```json
{  
   "RecognitionStatus":"Success",
   "DisplayText":"Go shopping tomorrow.",
   "Offset":16000000,
   "Duration":17100000
}
```

在範例應用程式中，JSON 回應還原序列化為`SpeechResult`執行個體，而結果傳回給呼叫的方法，以便顯示，如下列螢幕擷取畫面所示：

![](speech-recognition-images/speech-recognition.png "語音辨識")

## <a name="summary"></a>總結

本文說明如何使用 Microsoft 語音 REST API 將音訊轉換成 Xamarin.Forms 應用程式中的文字。 除了執行語音辨識，Microsoft 語音 API 可以也將文字轉換為語音。

## <a name="related-links"></a>相關連結

- [Microsoft 語音 API 文件](/azure/cognitive-services/speech/home/)。
- [使用 RESTful Web 服務](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Todo 認知服務 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
