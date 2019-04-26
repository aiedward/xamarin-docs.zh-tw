---
title: 使用 Microsoft Speech API 的語音辨識
description: Microsoft Speech API 是以雲端為基礎的 API，可口語的語言處理演算法。 這篇文章說明如何使用 Microsoft 語音辨識的 REST API 將轉換成在 Xamarin.Forms 應用程式中的文字。
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: dbcb86c6076d717966639a8c6117bc59c17e86bf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330218"
---
# <a name="speech-recognition-using-the-microsoft-speech-api"></a>使用 Microsoft Speech API 的語音辨識

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

_Microsoft Speech API 是以雲端為基礎的 API，可口語的語言處理演算法。這篇文章說明如何使用 Microsoft 語音辨識的 REST API 將轉換成在 Xamarin.Forms 應用程式中的文字。_

## <a name="overview"></a>總覽

Microsoft Speech API 有兩個元件：

- 將語音轉換成文字語音辨識 API。 可透過 REST API、 用戶端程式庫或服務程式庫執行語音辨識。
- 文字轉換語音 API 轉換為語音的文字。 透過 REST API 執行文字轉換語音。

本文著重於執行透過 REST API 的語音辨識。 雖然用戶端和服務的程式庫支援傳回部分結果，REST API 可以只會傳回單一的辨識結果，而不需要任何部分的結果。

若要使用 Microsoft Speech API，必須取得 API 金鑰。 這可從 Azure 取得[入口網站](https://portal.azure.com/)。 如需詳細資訊，請參閱 <<c0> [ 在 Azure 入口網站中建立認知服務帳戶](/azure/cognitive-services/cognitive-services-apis-create-account)。

如需有關 Microsoft Speech API 的詳細資訊，請參閱[Microsoft Speech API 文件](/azure/cognitive-services/speech/home/)。

## <a name="authentication"></a>驗證

Microsoft Speech REST API 的每個要求需要 JSON Web 權杖 (JWT) 存取權杖，可從認知服務權杖服務在`https://api.cognitive.microsoft.com/sts/v1.0/issueToken`。 POST 要求給權杖的服務，也可以取得語彙基元指定`Ocp-Apim-Subscription-Key`包含 API 金鑰，做為其值的標頭。

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

存取權杖必須指定每個 Microsoft Speech REST API 呼叫作為`Authorization`加上字串的標頭`Bearer`，如下列程式碼範例所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

若要將有效的存取權杖傳遞至 Microsoft Speech REST API 將會導致 403 回應錯誤。

## <a name="performing-speech-recognition"></a>執行語音辨識

語音辨識，來進行 POST 要求來達成`recognition`API `https://speech.platform.bing.com/speech/recognition/`。 單一要求不能包含超過 10 秒的音訊，與總要求持續時間不能超過 14 秒。

音訊的內容必須放在 POST 要求的主體 wav 格式。

在範例應用程式中，`RecognizeSpeechAsync`方法會叫用語音辨識程序：

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

音訊會記錄為 PCM wav 資料，每個平台專屬專案中，`RecognizeSpeechAsync`方法會使用`PCLStorage`開啟音訊檔案做為資料流的 NuGet 套件。 語音辨識要求 URI 的產生和存取權杖擷取自權杖的服務。 語音辨識要求張貼至`recognition`API，它會傳回包含結果的 JSON 回應。 JSON 回應還原序列化時，傳回給呼叫的方法，以便顯示結果。

### <a name="configuring-speech-recognition"></a>設定語音辨識

您可以藉由指定 HTTP 查詢參數設定語音辨識程序：

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    // To build a request URL, you should follow:
    // https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest
    string requestUri = speechEndpoint;
    requestUri += @"dictation/cognitiveservices/v1?";
    requestUri += @"language=en-us";
    requestUri += @"&format=simple";
    System.Diagnostics.Debug.WriteLine(requestUri.ToString());
    return requestUri;
}
```

所執行的主要組態`GenerateRequestUri`方法是將音訊內容的地區設定。 如需支援的地區設定的清單，請參閱 <<c0> [ 支援的語言](/azure/cognitive-services/speech/api-reference-rest/supportedlanguages/)。

### <a name="sending-the-request"></a>傳送要求

`SendRequestAsync`方法對 Microsoft Speech REST API 的 POST 要求，並傳回回應：

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

這個方法會建置的 POST 要求：

- 文繞圖中的音訊資料流`StreamContent`執行個體，它提供資料流為基礎的 HTTP 內容。
- 設定`Content-Type`對要求標頭`audio/wav; codec="audio/pcm"; samplerate=16000`。
- 加入的存取權杖`Authorization`標頭，加上字串`Bearer`。

POST 要求然後傳送至`recognition`API。 然後回應是閱讀，並傳回至呼叫的方法。

`recognition` API 會在回應中，提供要求是有效的表示要求成功，且要求的資訊會在回應中傳送 HTTP 狀態碼 200 （確定）。 如需可能的錯誤回應的清單，請參閱 <<c0> [ 疑難排解](/azure/cognitive-services/speech/troubleshooting)。

### <a name="processing-the-response"></a>處理回應

API 回應會傳回 JSON 格式，與已辨識的文字中包含`name`標記。 下列 JSON 資料會顯示一般的成功回應訊息：

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

這篇文章說明如何使用 Microsoft Speech REST API 將轉換成在 Xamarin.Forms 應用程式中的文字。 除了執行語音辨識，Microsoft Speech API 可以也將文字轉換成語音的文字。

## <a name="related-links"></a>相關連結

- [Microsoft Speech API 文件](/azure/cognitive-services/speech/home/)。
- [使用 RESTful Web 服務](~/xamarin-forms/data-cloud/consuming/rest.md)
- [待辦事項 Cognitive Services （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
