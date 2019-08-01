---
title: 使用人臉識別 API 的表情辨識
description: 人臉識別 API 的臉部表情作為輸入，映像，並傳回資料，包括跨一組影像中每個臉部表情的信心層級。 這篇文章說明如何使用人臉識別 API，可辨識這些表情，若要評比 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 6f03ae1030ef4a69b15c5e219785eee12c4e603b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656569"
---
# <a name="emotion-recognition-using-the-face-api"></a>使用人臉識別 API 的表情辨識

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_人臉識別 API 的臉部表情作為輸入，映像，並傳回資料，包括跨一組影像中每個臉部表情的信心層級。這篇文章說明如何使用人臉識別 API，可辨識這些表情，若要評比 Xamarin.Forms 應用程式。_

## <a name="overview"></a>總覽

人臉識別 API 可以執行情緒偵測來偵測生氣、 藐視、 厭惡、 恐懼、 快樂、 悲傷及驚喜，臉部的運算式中。 這些表情是通用和不同溝通透過相同的基本臉部表情。 傳回運算式臉部表情結果，以及人臉識別 API 也可以傳回偵測到的臉部的週框方塊。 請注意，必須使用人臉識別 API 取得 API 金鑰。 這可在取得[試用認知服務](https://azure.microsoft.com/try/cognitive-services/?api=face-api)。

透過用戶端程式庫，和透過 REST API，您可以執行表情辨識。 本文著重於執行表情辨識透過 REST API。 如需有關 REST API 的詳細資訊，請參閱 <<c0> [ 臉部 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

人臉識別 API 也可用來辨識視訊中的人的臉部表情，並可傳回其表情摘要。 如需詳細資訊，請參閱 <<c0> [ 如何在即時的分析影片](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/)。

更多人臉識別 API 的詳細資訊，請參閱[人臉識別 API](/azure/cognitive-services/face/overview/)。

## <a name="authentication"></a>驗證

人臉識別 API 的每個要求需要 API 金鑰，應該指定的值為`Ocp-Apim-Subscription-Key`標頭。 下列程式碼範例示範如何新增 API 金鑰才能`Ocp-Apim-Subscription-Key`的要求標頭：

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

將有效的 API 金鑰的臉部 api 的失敗會導致 401 回應時發生錯誤。

## <a name="performing-emotion-recognition"></a>執行表情辨識

藉由 POST 要求包含映像以執行表情辨識`detect`API `https://[location].api.cognitive.microsoft.com/face/v1.0`，其中`[location]]`是您用來取得您的 API 金鑰的區域。 選擇性的要求參數如下：

- `returnFaceId` – 是否要傳回 faceIds 的偵測到的臉部。 預設值是 `true`。
- `returnFaceLandmarks` – 是否要傳回臉部特徵的偵測到的臉部。 預設值是 `false`。
- `returnFaceAttributes` – 是否要分析並傳回指定的一或多個臉部屬性。 支援的臉部屬性包括`age`， `gender`， `headPose`， `smile`， `facialHair`， `glasses`， `emotion`， `hair`， `makeup`， `occlusion`， `accessories`， `blur`， `exposure`，和`noise`。 請注意，臉部屬性分析計算和時間的額外成本。

映像內容必須放在 URL 或二進位資料的 POST 要求的主體。

> [!NOTE]
> 支援的影像檔案格式為 JPEG、 PNG、 GIF 及 BMP、，允許的檔案大小為 1 KB 到 4 MB。

範例應用程式中的表情辨識程序會叫用呼叫`DetectAsync`方法：

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

這個方法呼叫中指定資料流，包含影像資料，也應該會傳回 faceIds，，就不應該傳回臉部特徵，並應該分析的映像的表情。 它也會指定結果將會以陣列傳回`Face`物件。 依次`DetectAsync`方法會叫用`detect`REST API 執行情緒辨識：

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

這個方法會產生要求 URI，並接著會傳送至要求`detect`API 透過`SendRequestAsync`方法。

> [!NOTE]
> 為您用來取得您的訂用帳戶金鑰的臉部 API 呼叫中，您必須使用相同的區域。 例如，如果您取得您的訂用帳戶金鑰，從`westus`區域，是臉部偵測端點`https://westus.api.cognitive.microsoft.com/face/v1.0/detect`。

### <a name="sending-the-request"></a>傳送要求

`SendRequestAsync`方法對人臉識別 API 的 POST 要求，並傳回結果做`Face`陣列：

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

如果透過資料流所提供的影像，方法會藉由包裝中的影像資料流建置 POST 要求`StreamContent`執行個體，它提供資料流為基礎的 HTTP 內容。 或者，如果透過 URL 所提供的影像，方法會建置在 POST 要求藉由包裝中的 URL`StringContent`執行個體，它提供一個字串為基礎的 HTTP 內容。

POST 要求然後傳送至`detect`API。 回應是讀取、 還原序列化，並傳回至呼叫的方法。

`detect` API 會在回應中，提供要求是有效的表示要求成功，且要求的資訊會在回應中傳送 HTTP 狀態碼 200 （確定）。 如需可能的錯誤回應的清單，請參閱 <<c0> [ 臉部 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

### <a name="processing-the-response"></a>處理回應

API 回應會傳回 JSON 格式。 下列 JSON 資料會顯示一般的成功回應訊息會提供範例應用程式所要求的資料：

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

成功的回應訊息是由陣列的排序的臉部矩形大小，以遞減順序，而空的回應表示沒有偵測到的面朝項目所組成。 每個辨識臉部包含選擇性的臉部屬性所指定的一系列`returnFaceAttributes`引數`DetectAsync`方法。

在範例應用程式中，JSON 回應還原序列化為陣列`Face`物件。 當解譯結果的人臉識別 API，偵測到的表情應解譯為最高的分數，情緒分數會正規化為其中一個的總和。 因此，範例應用程式會顯示映像中偵測到所面臨的最大的分數最高可辨識的情緒。 做法是以下列程式碼：

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

下列螢幕擷取畫面顯示範例應用程式中的表情辨識程序的結果：

![](emotion-recognition-images/emotion-recognition.png "表情辨識")

## <a name="summary"></a>總結

這篇文章說明如何使用人臉識別 API，可辨識這些表情，若要評比 Xamarin.Forms 應用程式。 人臉識別 API 的臉部表情作為輸入，映像，並傳回資料，包括跨一組影像中每個臉部表情的信心。

## <a name="related-links"></a>相關連結

- [臉部 API](/azure/cognitive-services/face/overview/)。
- [待辦事項 Cognitive Services （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [人臉識別 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
