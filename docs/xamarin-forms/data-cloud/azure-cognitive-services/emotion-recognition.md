---
title: 使用臉部 API 的認知表情辨識
description: 臉部 API 會以影像中的臉部運算式做為輸入，並傳回在影像中每個臉部的一組表情上包含信賴等級的資料。 本文說明如何使用臉部 API 來辨識表情，以對應用程式進行評分 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ac7b90fb3e70fd07fcafe78a68136338469862e0
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936362"
---
# <a name="perceived-emotion-recognition-using-the-face-api"></a>使用臉部 API 的認知表情辨識

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

臉部 API 可執行表情偵測，以根據人類半截所認知的注釋，在臉部運算式中偵測生氣、藐視、厭惡、恐懼、快樂、中性、悲傷和驚喜。 不過，請務必注意，臉部運算式本身可能不一定代表人員的內部狀態。

除了傳回臉部運算式的表情結果以外，臉部 API 也可以針對偵測到的臉部傳回周框方塊。

表情辨識可以透過用戶端程式庫和 REST API 來執行。 本文著重于透過 REST API 執行表情辨識。 如需 REST API 的詳細資訊，請參閱[臉部 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

臉部 API 也可以用來辨識影片中人員的表情運算式，並可傳回其表情的摘要。 如需詳細資訊，請參閱[如何即時分析](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/)影片。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

必須取得 API 金鑰，才能使用臉部 API。 這可以在[試用認知服務](https://azure.microsoft.com/try/cognitive-services/?api=face-api)取得。

如需臉部 API 的詳細資訊，請參閱[臉部 api](/azure/cognitive-services/face/overview/)。

## <a name="authentication"></a>驗證

對臉部 API 所提出的每個要求，都需要一個應該指定為標頭值的 API 金鑰 `Ocp-Apim-Subscription-Key` 。 下列程式碼範例顯示如何將 API 金鑰新增至要求的 `Ocp-Apim-Subscription-Key` 標頭：

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

若無法將有效的 API 金鑰傳遞至臉部 API，將會產生401回應錯誤。

## <a name="perform-emotion-recognition"></a>執行表情辨識

表情辨識的執行方式是將包含影像的 POST 要求放到 `detect` API `https://[location].api.cognitive.microsoft.com/face/v1.0` （位於），其中 `[location]]` 是您用來取得 api 金鑰的區域。 選擇性的要求參數為：

- `returnFaceId`–是否要傳回偵測到之臉部的 faceIds。 預設值是 `true`。
- `returnFaceLandmarks`–是否要傳回所偵測到臉部的臉部地標。 預設值是 `false`。
- `returnFaceAttributes`–是否要分析並傳回一或多個指定的臉部屬性。 支援的臉部屬性包括 `age` 、、、、、、、、、、、、 `gender` `headPose` `smile` `facialHair` `glasses` `emotion` `hair` `makeup` `occlusion` `accessories` `blur` `exposure` 和 `noise` 。 請注意，臉部屬性分析有額外的計算和時間成本。

影像內容必須放在 POST 要求的主體中，做為 URL 或二進位資料。

> [!NOTE]
> 支援的影像檔案格式為 JPEG、PNG、GIF 和 BMP，而允許的檔案大小為1KB 到4MB。

在範例應用程式中，表情辨識程式是藉由呼叫方法來叫用 `DetectAsync` ：

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

這個方法呼叫會指定包含影像資料的資料流程、應傳回的 faceIds、不應傳回臉部地標，以及應該分析影像的表情。 它也會指定以物件陣列的形式傳回結果 `Face` 。 接著， `DetectAsync` 方法會叫用 `detect` 執行表情辨識的 REST API：

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
> 當您用來取得訂用帳戶金鑰時，您必須在臉部 API 呼叫中使用相同的區域。 例如，如果您從區域取得訂用帳戶金鑰 `westus` ，臉部偵測端點會是 `https://westus.api.cognitive.microsoft.com/face/v1.0/detect` 。

### <a name="send-the-request"></a>傳送要求

`SendRequestAsync`方法會對臉部 API 發出 POST 要求，並以陣列的形式傳回結果 `Face` ：

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

如果透過資料流程提供影像，方法會將影像資料流程包裝在實例中，藉此建立 POST 要求 `StreamContent` ，以提供以資料流程為基礎的 HTTP 內容。 或者，如果透過 URL 提供影像，方法就會將 URL 包裝在實例中，以提供以字串為 `StringContent` 基礎的 HTTP 內容，藉以建立 POST 要求。

然後，POST 要求會傳送至 `detect` API。 回應會讀取、還原序列化，並傳回給呼叫方法。

`detect`如果要求有效，此 API 會在回應中傳送 HTTP 狀態碼200（確定），這表示要求成功，且要求的資訊在回應中。 如需可能的錯誤回應清單，請參閱[臉部 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

### <a name="process-the-response"></a>處理回應

API 回應會以 JSON 格式傳回。 下列 JSON 資料顯示一般成功的回應訊息，它會提供範例應用程式所要求的資料：

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

成功的回應訊息包含以臉部矩形大小以遞減順序排序的臉部專案陣列，而空的回應則表示未偵測到臉部。 每個辨識的臉部都包含一系列的選擇性臉部屬性，由 `returnFaceAttributes` 方法的引數所指定 `DetectAsync` 。

在範例應用程式中，JSON 回應會還原序列化為物件的陣列 `Face` 。 當您從臉部 API 解讀結果時，偵測到的表情應該會以最高分的表情來解讀，因為分數會正規化為加總為1。 因此，範例應用程式會針對影像中最大偵測到的臉部，顯示具有最高分數的可辨識表情。 使用下列程式碼即可達成：

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

下列螢幕擷取畫面顯示範例應用程式中表情辨識程式的結果：

![表情辨識](emotion-recognition-images/emotion-recognition.png)

## <a name="related-links"></a>相關連結

- [臉部 API](/azure/cognitive-services/face/overview/)。
- [Todo 認知服務（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [臉部 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
