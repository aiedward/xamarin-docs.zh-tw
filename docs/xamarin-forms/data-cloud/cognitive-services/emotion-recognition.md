---
title: 使用應用程式開發介面的字體情緒辨識
description: 字體 API 臉部運算式在映像做為輸入，然後傳回情感的映像中的一組包含信心層級的資料。 本文說明如何使用朝 API 來辨識情緒、 加以分級 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 4dc04cb077b894b255eb496b2cb2983626573897
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="emotion-recognition-using-the-face-api"></a>使用應用程式開發介面的字體情緒辨識

_字體 API 臉部運算式在映像做為輸入，然後傳回情感的映像中的一組包含信心層級的資料。本文說明如何使用朝 API 來辨識情緒、 加以分級 Xamarin.Forms 應用程式。_

## <a name="overview"></a>總覽

字體 API 可以執行情緒偵測來偵測 anger、 contempt、 disgust、 擔心、 快樂、 中性、 sadness，以及屬於意外、 臉部運算式中。 這些情感是通用和其中溝通透過相同的基本臉部表情。 傳回運算式臉部表情結果，以及朝 API 也可以傳回偵測到的字體的週框方塊。 請注意，必須使用的字體 API 取得 API 金鑰。 這可在取得[再試一次認知服務](https://azure.microsoft.com/try/cognitive-services/?api=face-api)。

透過用戶端程式庫，並透過 REST API，可以執行情緒辨識。 本文著重在執行情緒辨識透過 REST API。 如需 REST API 的詳細資訊，請參閱[朝 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

字體 API 也可用來辨識視訊中的人員臉部運算式，並可傳回其情感的摘要。 如需詳細資訊，請參閱[如何分析視訊即時](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/)。

如需朝 API 的詳細資訊，請參閱[朝 API](/azure/cognitive-services/face/overview/)。

## <a name="authentication"></a>驗證

字體 API 的每個要求需要的 API 金鑰，應該指定的值為`Ocp-Apim-Subscription-Key`標頭。 下列程式碼範例示範如何將加入的 API 金鑰`Ocp-Apim-Subscription-Key`要求標頭：

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

將有效的 API 金鑰表面 API 失敗會導致 401 回應時發生錯誤。

## <a name="performing-emotion-recognition"></a>執行情緒辨識

情緒辨識會藉由包含影像的 POST 要求執行`detect`API `https://[location].api.cognitive.microsoft.com/face/v1.0`，其中`[location]]`是您用來取得 API 金鑰的地區。 選擇性的要求參數如下：

- `returnFaceId` – 是否傳回 faceIds 的偵測到的字體。 預設值是 `true`。
- `returnFaceLandmarks` -是否要傳回的偵測到面朝地標。 預設值是 `false`。
- `returnFaceAttributes` -是否要分析並傳回一個或多個指定面臨屬性。 支援的字體屬性包括`age`， `gender`， `headPose`， `smile`， `facialHair`， `glasses`， `emotion`， `hair`， `makeup`， `occlusion`， `accessories`， `blur`， `exposure`，和`noise`。 請注意，朝屬性分析計算和時間的額外成本。

必須為 URL 或二進位資料的 POST 要求主體中放映像內容。

> [!NOTE]
> 支援的影像檔案格式為 JPEG、 PNG、 GIF 和 BMP、，允許的檔案大小為 1 KB 到 4 MB。

範例應用程式、 情緒辨識程序會叫用呼叫`DetectAsync`方法：

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

這個方法呼叫會指定包含影像資料，faceIds 應傳回，，就不應該傳回朝地標，以及映像的情緒應該要分析的資料流。 它也會指定的陣列，會傳回結果`Face`物件。 接著，`DetectAsync`方法會叫用`detect`REST API 執行情緒辨識：

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

這個方法會產生要求 URI，然後將要求傳送給`detect`應用程式開發介面透過`SendRequestAsync`方法。

> [!NOTE]
> 為您用來取得您的訂用帳戶金鑰的字體 API 呼叫中，您必須使用相同的區域。 例如，如果您取得您訂用帳戶的金鑰從`westus`區域中，將會朝偵測端點`https://westus.api.cognitive.microsoft.com/face/v1.0/detect`。

### <a name="sending-the-request"></a>傳送要求

`SendRequestAsync`方法對朝 API 的 POST 要求，並傳回結果做為`Face`陣列：

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

如果影像透過資料流提供，方法是 POST 要求文繞圖中的影像資料流`StreamContent`執行個體，這會提供 HTTP 內容資料流為基礎。 或者，如果透過 URL 提供映像，則方法會建置 POST 要求藉由包裝中的 URL`StringContent`執行個體，這會提供 HTTP 內容字串為基礎。

然後會在 POST 要求傳送至`detect`應用程式開發介面。 回應是讀取、 還原序列化，並傳回至呼叫的方法。

`detect` API 回應，提供該要求是否有效，表示要求成功，並要求的資訊回應中傳送 HTTP 狀態碼 200 （確定）。 如需可能的錯誤回應的清單，請參閱[朝 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

### <a name="processing-the-response"></a>處理回應

API 回應是以 JSON 格式傳回。 下列 JSON 資料會顯示一般的成功回應訊息會提供範例應用程式所要求的資料：

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

成功的回應訊息包含陣列的字體項目，並依照依遞減順序，而空的回應，表示沒有偵測到的面朝矩形大小。 每個辨識朝包含選擇性朝屬性所指定的一系列`returnFaceAttributes`引數`DetectAsync`方法。

在範例應用程式中，JSON 回應會還原序列化成陣列`Face`物件。 解譯結果從朝 API 時, 偵測到的情緒應解譯為以分數最高情緒分數會正規化為其中一個的總和。 因此，範例應用程式會顯示與最大偵測到的一面分數最高可辨識的情緒映像中。 下列程式碼會完成這個動作：

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

下列螢幕擷取畫面顯示情緒辨識程序的結果範例應用程式中：

![](emotion-recognition-images/emotion-recognition.png "情緒辨識")

## <a name="summary"></a>總結

本文說明如何使用朝 API 來辨識情緒、 加以分級 Xamarin.Forms 應用程式。 字體 API 臉部運算式在映像做為輸入，然後傳回情感的映像中的一組包含信賴度的資料。

## <a name="related-links"></a>相關連結

- [面對 API](/azure/cognitive-services/face/overview/)。
- [Todo 認知服務 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [字體 REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
