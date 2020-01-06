---
title: 使用 Microsoft Speech API 的語音辨識
description: Microsoft Speech API 是以雲端為基礎的 API，可提供用來處理語音語言的演算法。 本文說明如何使用 Microsoft 語音辨識 REST API，將音訊轉換成 Xamarin. Forms 應用程式中的文字。
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: eca079972f4e46c0cf60c4749658ff9a7fe1791b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489800"
---
# <a name="speech-recognition-using-the-microsoft-speech-api"></a>使用 Microsoft Speech API 的語音辨識

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft Speech API 是以雲端為基礎的 API，可提供用來處理語音語言的演算法。本文說明如何使用 Microsoft 語音辨識 REST API，將音訊轉換成 Xamarin. Forms 應用程式中的文字。_

## <a name="overview"></a>概觀

Microsoft Speech API 有兩個元件：

- 語音辨識 API，用來將說話文字轉換成文字。 語音辨識可以透過 REST API、用戶端程式庫或服務程式庫來執行。
- 文字轉換語音 API，用來將文字轉換為語音文字。 文字轉換語音是透過 REST API 來執行。

本文著重于透過 REST API 執行語音辨識。 雖然用戶端和服務程式庫支援傳回部分結果，但 REST API 只會傳回單一辨識結果，而不會有任何部分結果。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

必須取得 API 金鑰，才能使用 Microsoft Speech API。 這可從 Azure[入口網站](https://portal.azure.com/)取得。 如需詳細資訊，請參閱[在 Azure 入口網站中建立認知服務帳戶](/azure/cognitive-services/cognitive-services-apis-create-account)。

如需 Microsoft Speech API 的詳細資訊，請參閱[Microsoft SPEECH Api 檔](/azure/cognitive-services/speech/home/)。

## <a name="authentication"></a>驗證  (可能為英文網頁)

對 Microsoft Speech REST API 提出的每個要求都需要 JSON Web 權杖（JWT）存取權杖，可從 `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`的認知服務權杖服務取得。 您可以對權杖服務提出 POST 要求來取得權杖，並指定 `Ocp-Apim-Subscription-Key` 標頭，其中包含 API 金鑰做為其值。

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

您必須在每個 Microsoft 語音 REST API 呼叫中指定存取權杖，做為 `Authorization` 標頭，並在前面加上字串 `Bearer`，如下列程式碼範例所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

無法將有效的存取權杖傳遞給 Microsoft Speech REST API 將會產生403回應錯誤。

## <a name="performing-speech-recognition"></a>執行語音辨識

在 `https://speech.platform.bing.com/speech/recognition/`對 `recognition` API 提出 POST 要求，即可達到語音辨識。 單一要求不能包含超過10秒的音訊，而且要求持續時間總計不得超過14秒。

音訊內容必須以 wav 格式放在要求的 POST 主體中。

在範例應用程式中，`RecognizeSpeechAsync` 方法會叫用語音辨識進程：

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

音訊會在每個平臺特定專案中記錄為 PCM wav 資料，而 `RecognizeSpeechAsync` 方法會使用 `PCLStorage` NuGet 封裝，以資料流程的形式開啟音訊檔案。 系統會產生語音辨識要求 URI，並從權杖服務中取出存取權杖。 語音辨識要求會張貼至 `recognition` API，以傳回包含結果的 JSON 回應。 JSON 回應會還原序列化，並將結果傳回給要顯示的呼叫方法。

### <a name="configuring-speech-recognition"></a>設定語音辨識

您可以藉由指定 HTTP 查詢參數來設定語音辨識程式：

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

`GenerateRequestUri` 方法所執行的主要設定，是設定音訊內容的地區設定。 如需支援的地區設定清單，請參閱[支援的語言](/azure/cognitive-services/speech/api-reference-rest/supportedlanguages/)。

### <a name="sending-the-request"></a>傳送要求

`SendRequestAsync` 方法會對 Microsoft Speech REST API 提出 POST 要求，並傳迴響應：

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

這個方法會藉由下列方式建立 POST 要求：

- 將音訊串流包裝在 `StreamContent` 實例中，以提供以資料流程為基礎的 HTTP 內容。
- 將要求的 `Content-Type` 標頭設定為 `audio/wav; codec="audio/pcm"; samplerate=16000`。
- 將存取權杖新增至 `Authorization` 標頭，並在前面加上字串 `Bearer`。

然後，POST 要求會傳送至 `recognition` API。 然後會讀取回應，並將其傳回給呼叫方法。

`recognition` API 會在回應中傳送 HTTP 狀態碼200（確定），前提是該要求是有效的，這表示要求成功，且要求的資訊在回應中。 如需可能的錯誤回應清單，請參閱[疑難排解](/azure/cognitive-services/speech/troubleshooting)。

### <a name="processing-the-response"></a>處理回應

API 回應會以 JSON 格式傳回，且已辨識的文字會包含在 `name` 標記中。 下列 JSON 資料顯示一般成功的回應訊息：

```json
{  
   "RecognitionStatus":"Success",
   "DisplayText":"Go shopping tomorrow.",
   "Offset":16000000,
   "Duration":17100000
}
```

在範例應用程式中，JSON 回應會還原序列化為 `SpeechResult` 實例，並將結果傳回給顯示的呼叫方法，如下列螢幕擷取畫面所示：

![](speech-recognition-images/speech-recognition.png "Speech Recognition")

## <a name="summary"></a>總結

本文說明如何使用 Microsoft Speech REST API，將音訊轉換成 Xamarin. Forms 應用程式中的文字。 除了執行「語音辨識」，Microsoft 語音 API 也可以將文字轉換成語音文字。

## <a name="related-links"></a>相關連結

- [Microsoft SPEECH API 檔](/azure/cognitive-services/speech/home/)。
- [使用 RESTful Web 服務](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Todo 認知服務（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
