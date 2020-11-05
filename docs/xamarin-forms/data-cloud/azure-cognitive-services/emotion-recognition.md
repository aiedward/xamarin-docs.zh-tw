---
title: 使用臉部 API 察覺的表情辨識
description: 臉部 API 會將影像中的臉部運算式作為輸入，並傳回在影像中每個臉部的一組表情上包含信賴等級的資料。 本文說明如何使用臉部 API 來辨識表情，以對應用程式進行評分 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5b3a20c621ac76ac3b18ba5c503b8dc2b962ef1b
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366811"
---
# <a name="perceived-emotion-recognition-using-the-face-api"></a>使用臉部 API 察覺的表情辨識

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

臉部 API 可執行表情偵測，在臉部運算式中偵測生氣、藐視、厭惡、恐懼、快樂、中性、悲傷和驚喜，以人類半截的認知注釋為基礎。 不過請務必注意，臉部運算式本身不一定代表人員的內部狀態。

除了傳回臉部運算式的表情結果，臉部 API 也可以傳回所偵測到臉部的周框方塊。

表情辨識可透過用戶端程式庫和 REST API 來執行。 本文著重于透過 REST API 來執行表情辨識。 如需 REST API 的詳細資訊，請參閱 [臉部 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

臉部 API 也可以用來辨識影片中人員的臉部運算式，並可傳回其表情的摘要。 如需詳細資訊，請參閱 [如何即時分析](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/)影片。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

必須取得 API 金鑰，才能使用臉部 API。 這可在 [試用認知服務](https://azure.microsoft.com/try/cognitive-services/?api=face-api)中取得。

如需臉部 API 的詳細資訊，請參閱 [臉部 api](/azure/cognitive-services/face/overview/)。

## <a name="authentication"></a>驗證

對臉部 API 提出的每個要求都需要一個應指定為標頭值的 API 金鑰 `Ocp-Apim-Subscription-Key` 。 下列程式碼範例顯示如何將 API 金鑰新增至要求的 `Ocp-Apim-Subscription-Key` 標頭：

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

若無法將有效的 API 金鑰傳遞至臉部 API，將會導致401回應錯誤。

## <a name="perform-emotion-recognition"></a>執行表情辨識

表情辨識的執行方式是將包含影像的 POST 要求放到 `detect` api `https://[location].api.cognitive.microsoft.com/face/v1.0` ，其中 `[location]]` 是您用來取得 api 金鑰的區域。 選用的要求參數為：

- `returnFaceId` –是否要傳回偵測到之臉部的 faceIds。 預設值是 `true`。
- `returnFaceLandmarks` –是否要傳回所偵測到臉部的臉部地標。 預設值是 `false`。
- `returnFaceAttributes` –是否分析和傳回一或多個指定的臉部屬性。 支援的臉部屬性包括 `age` 、 `gender` 、 `headPose` 、、、、、、、、、、 `smile` `facialHair` `glasses` `emotion` `hair` `makeup` `occlusion` `accessories` `blur` `exposure` 和 `noise` 。 請注意，臉部屬性分析有額外的計算和時間成本。

影像內容必須放在 POST 要求的主體中，以作為 URL 或二進位資料。

> [!NOTE]
> 支援的影像檔案格式包括 JPEG、PNG、GIF 和 BMP，而允許的檔案大小是從1KB 到4MB。

在範例應用程式中，會藉由呼叫方法來叫用表情辨識進程 `DetectAsync` ：

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

這個方法呼叫會指定包含影像資料的資料流程、應該傳回 faceIds、不應傳回臉部地標，以及應分析影像的表情。 它也會指定以物件的陣列傳回結果 `Face` 。 接著，此 `DetectAsync` 方法會叫用 `detect` 執行表情辨識的 REST API：

```csharp
public async Task<Face[]> DetectAsync(Stream imageStream, bool returnFaceId, bool returnFaceLandmarks, IEnumerable<FaceAttributeType> returnFaceAttributes)
{
  var requestUrl =
    $"{Constants.FaceEndpoint}/detect?returnFaceId={returnFaceId}" +
    "&returnFaceLandmarks={returnFaceLandmarks}" +
    "&returnFaceAttributes={GetAttributeString(returnFaceAttributes)}";
  return await SendRequestAsync<Stream, Face[]>(HttpMethod.Post, requestUrl, imageStream);
}
```

這個方法會產生要求 URI，然後透過方法將要求傳送至 `detect` API `SendRequestAsync` 。

> [!NOTE]
> 您必須在臉部 API 呼叫中使用與您用來取得訂用帳戶金鑰相同的區域。 例如，如果您從區域取得訂用帳戶金鑰 `westus` ，臉部偵測端點將會是 `https://westus.api.cognitive.microsoft.com/face/v1.0/detect` 。

### <a name="send-the-request"></a>傳送要求

`SendRequestAsync`方法會對臉部 API 提出 POST 要求，並以陣列的形式傳回結果 `Face` ：

```csharp
async Task<TResponse> SendRequestAsync<TRequest, TResponse>(HttpMethod httpMethod, string requestUrl, TRequest requestBody)
{
  var request = new HttpRequestMessage(httpMethod, Constants.FaceEndpoint);
  request.RequestUri = new Uri(requestUrl);
  if (requestBody != null)
  {
    if (requestBody is Stream)
    {
      request.Content = new StreamContent(requestBody as Stream);
      request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
    }
    else
    {
      // If the image is supplied via a URL
      request.Content = new StringContent(JsonConvert.SerializeObject(requestBody, s_settings), Encoding.UTF8, "application/json");
    }
  }

  HttpResponseMessage responseMessage = await _client.SendAsync(request);
  if (responseMessage.IsSuccessStatusCode)
  {
    string responseContent = null;
    if (responseMessage.Content != null)
    {
      responseContent = await responseMessage.Content.ReadAsStringAsync();
    }
    if (!string.IsNullOrWhiteSpace(responseContent))
    {
      return JsonConvert.DeserializeObject<TResponse>(responseContent, s_settings);
    }
    return default(TResponse);
  }
  else
  {
    ...
  }
  return default(TResponse);
}
```

如果透過資料流程提供映射，方法會將影像串流包裝在實例中以建立 POST 要求 `StreamContent` ，以提供以資料流程為基礎的 HTTP 內容。 或者，如果透過 URL 提供影像，則方法會在實例中包裝 URL 來建立 POST 要求 `StringContent` ，以提供以字串為基礎的 HTTP 內容。

POST 要求接著會傳送至 `detect` API。 回應會讀取、還原序列化，並傳回給呼叫方法。

`detect`API 會在回應中傳送 HTTP 狀態碼 200 (確定) ，但前提是要求是有效的，這表示要求成功，且要求的資訊會在回應中。 如需可能的錯誤回應清單，請參閱 [臉部 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

### <a name="process-the-response"></a>處理回應

API 回應會以 JSON 格式傳回。 下列 JSON 資料顯示一般成功的回應訊息，其中會提供範例應用程式所要求的資料：

```json
[  
   {  
      "faceId":"8a1a80fe-1027-48cf-a7f0-e61c0f005051",
      "faceRectangle":{  
         "top":192,
         "left":164,
         "width":339,
         "height":339
      },
      "faceAttributes":{  
         "emotion":{  
            "anger":0.0,
            "contempt":0.0,
            "disgust":0.0,
            "fear":0.0,
            "happiness":1.0,
            "neutral":0.0,
            "sadness":0.0,
            "surprise":0.0
         }
      }
   }
]
```

成功的回應訊息是由依臉部矩形大小以遞減順序排序的臉部專案陣列所組成，而空的回應則表示未偵測到臉部。 每個辨識的臉部都包含一系列的選擇性臉部屬性，這些屬性是由 `returnFaceAttributes` 方法的引數所指定 `DetectAsync` 。

在範例應用程式中，JSON 回應會還原序列化為物件的陣列 `Face` 。 解讀臉部 API 的結果時，偵測到的表情應解釋為分數最高的表情，因為分數會正規化為加總為1。 因此，範例應用程式會針對影像中偵測到的最大臉部，以最高分分數顯示辨識的表情。 使用下列程式碼即可達成：

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

下列螢幕擷取畫面顯示範例應用程式中表情辨識程式的結果：

![表情辨識](emotion-recognition-images/emotion-recognition.png)

## <a name="related-links"></a>相關連結

- [臉部 API](/azure/cognitive-services/face/overview/)。
- [Todo 認知服務 (範例) ](/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [臉部 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)