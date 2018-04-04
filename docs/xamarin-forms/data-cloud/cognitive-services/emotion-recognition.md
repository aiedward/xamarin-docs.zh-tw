---
title: 使用應用程式開發介面的字體情緒辨識
description: 字體 API 臉部運算式在映像做為輸入，然後傳回情感的映像中的一組包含信心層級的資料。 本文說明如何使用朝 API 來辨識情緒、 加以分級 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 49e53425dbaf3aadd74d02ab030929e3311c7c8c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="emotion-recognition-using-the-face-api"></a>使用應用程式開發介面的字體情緒辨識

_字體 API 臉部運算式在映像做為輸入，然後傳回情感的映像中的一組包含信心層級的資料。本文說明如何使用朝 API 來辨識情緒、 加以分級 Xamarin.Forms 應用程式。_

## <a name="overview"></a>總覽

字體 API 可以執行情緒偵測來偵測 anger、 contempt、 disgust、 擔心、 快樂、 中性、 sadness，以及屬於意外、 臉部運算式中。 這些情感是通用和其中溝通透過相同的基本臉部表情。 傳回運算式臉部表情結果，以及朝 API 也可以傳回偵測到的字體的週框方塊。 請注意，必須使用的字體 API 取得 API 金鑰。 這可在取得[再試一次認知服務](https://azure.microsoft.com/try/cognitive-services/?api=face-api)。

透過用戶端程式庫，並透過 REST API，可以執行情緒辨識。 本文著重在執行透過情緒辨識[Microsoft.ProjectOxford.Face](https://www.nuget.org/packages/Microsoft.ProjectOxford.Face/)用戶端程式庫，您可以從 NuGet 下載。

字體 API 也可用來辨識視訊中的人員臉部運算式，並可傳回其情感的摘要。 如需詳細資訊，請參閱[如何分析視訊即時](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/)。

如需朝 API 的詳細資訊，請參閱[朝 API](/azure/cognitive-services/face/overview/)。

## <a name="performing-emotion-recognition"></a>執行情緒辨識

情緒辨識達成朝 api 上傳影像資料流。 影像檔大小應該大於 4 MB，並支援的檔案格式為 JPEG、 PNG、 GIF 和 BMP。

下列程式碼範例示範情緒辨識程序：

```csharp
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;

var faceServiceClient = new FaceServiceClient(Constants.FaceApiKey, Constants.FaceEndpoint);
// e.g. var faceServiceClient = new FaceServiceClient("a3dbe2ed6a5a9231bb66f9a964d64a12", "https://westus.api.cognitive.microsoft.com/face/v1.0/detect");

var faceAttributes = new FaceAttributeType[] { FaceAttributeType.Emotion };
using (var photoStream = photo.GetStream())
{
    Face[] faces = await faceServiceClient.DetectAsync(photoStream, true, false, faceAttributes);
    if (faces.Any())
    {
        // Emotions detected are happiness, sadness, surprise, anger, fear, contempt, disgust, or neutral.
        emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
    }
    // Store emotion as app rating
    ...
}
```

`FaceServiceClient`必須建立執行個體來執行字體應用程式開發介面有索引鍵和端點做為引數傳遞的情緒辨識`FaceServiceClient`建構函式。

> [!NOTE]
> 為您用來取得您的訂用帳戶金鑰的字體 API 呼叫中，您必須使用相同的區域。 例如，如果您取得您訂用帳戶的金鑰從`westus`區域中，將會朝偵測端點`https://westus.api.cognitive.microsoft.com/face/v1.0/detect`。

`DetectAsync`方法，這個方法會在叫用`FaceServiceClient`執行個體，做為上傳映像正面應用程式開發介面， `Stream`。 API 金鑰，在這項作業會叫用會提交至朝 API。 若要提交有效的 API 金鑰將會導致`Microsoft.ProjectOxford.Face.FaceAPIException`擲回，並提供例外狀況訊息，指出已提交的 API 金鑰無效。

`DetectAsync`方法會傳回`Face`陣列，前提是已辨識的字體。 每個傳回的字體包含以指出其位置，加上選擇性的字體屬性所指定的一系列矩形`faceAttributes`引數`DetectAsync`方法。 如果偵測不到任何字體時，空`Face`會傳回陣列。

解譯結果從朝 API 時, 偵測到的情緒應解譯為以分數最高情緒分數會正規化為其中一個的總和。 因此，範例應用程式會顯示與最大偵測到的一面分數最高可辨識的情緒中映像，如下列螢幕擷取畫面所示：

![](emotion-recognition-images/emotion-recognition.png "情緒辨識")

## <a name="summary"></a>總結

本文說明如何使用朝 API 來辨識情緒、 加以分級 Xamarin.Forms 應用程式。 字體 API 臉部運算式在映像做為輸入，然後傳回情感的映像中的一組包含信賴度的資料。

## <a name="related-links"></a>相關連結

- [面對 API](/azure/cognitive-services/face/overview/)。
- [Todo 認知服務 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Microsoft.ProjectOxford.Face](https://www.nuget.org/packages/Microsoft.ProjectOxford.Face/)
