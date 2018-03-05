---
title: "情緒辨識使用情緒應用程式開發介面"
description: "情緒 API 臉部運算式在映像做為輸入，然後傳回信心層級情感的映像中的一組。 本文說明如何使用情緒 API 來辨識情緒、 加以分級 Xamarin.Forms 應用程式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 159bd1b23eb7505c5d5629570a34d54e0525567e
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="emotion-recognition-using-the-emotion-api"></a>情緒辨識使用情緒應用程式開發介面

_情緒 API 臉部運算式在映像做為輸入，然後傳回信心層級情感的映像中的一組。本文說明如何使用情緒 API 來辨識情緒、 加以分級 Xamarin.Forms 應用程式。_

![](~/media/shared/preview.png "這個 API 已發行目前前")

> [!NOTE]
> 情緒 API 仍處於預覽狀態。 那里可能中斷變更，最終發行前的 api。

## <a name="overview"></a>總覽

情緒 API 可以偵測 anger、 contempt、 disgust、 擔心、 快樂，中性、 sadness，而意外、 臉部運算式中。 這些情感是通用和其中溝通透過相同的基本臉部表情。 傳回運算式臉部表情結果，以及情緒 API 也會傳回偵測到使用朝 API 的字體的週框方塊。 如果使用者已經有呼叫朝 API，它們可以提交朝矩形做為選擇性的輸入。 請注意，必須使用情緒 API 來取得 API 金鑰。 這可在取得[開始免費使用](https://www.microsoft.com/cognitive-services/sign-up)microsoft.com 上。

透過用戶端程式庫，並透過 REST API，可以執行情緒辨識。 本文著重在執行透過情緒辨識[Microsoft.ProjectOxford.Emotion](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/)用戶端程式庫，您可以從 NuGet 下載。

情緒 API 也可用來辨識視訊中的人員臉部運算式，並傳回其情感的摘要。 如需詳細資訊，請參閱[影片情緒](https://www.microsoft.com/cognitive-services/emotion-api/documentation#emotion-in-video)microsoft.com 上。

如需情緒 API 的詳細資訊，請參閱[情緒 API 文件](https://www.microsoft.com/cognitive-services/emotion-api/documentation)microsoft.com 上。

## <a name="performing-emotion-recognition"></a>執行情緒辨識

情緒辨識達成情緒 api 上傳影像資料流。 影像檔大小應該大於 4 MB，並支援的檔案格式為 JPEG、 PNG、 GIF 和 BMP。 映像中，偵測臉大小範圍為 36 x 36 到 4096 x 4096 像素。 將不會偵測到任何字體超出此範圍。

下列程式碼範例示範情緒辨識程序：

```csharp
using Microsoft.ProjectOxford.Emotion;
using Microsoft.ProjectOxford.Emotion.Contract;

var emotionClient = new EmotionServiceClient(Constants.EmotionApiKey);

using (var photoStream = photo.GetStream())
{
  Emotion[] emotionResult = await emotionClient.RecognizeAsync(photoStream);
  if (emotionResult.Any())
  {
    // Emotions detected are happiness, sadness, surprise, anger, fear, contempt, disgust, or neutral.
    emotionResultLabel.Text = emotionResult.FirstOrDefault().Scores.ToRankedList().FirstOrDefault().Key;
  }
  // Store emotion as app rating
  ...
}
```

`EmotionServiceClient`必須建立執行個體來執行情緒辨識，做為引數傳遞的情緒 API 金鑰與`EmotionServiceClient`建構函式。

`RecognizeAsync`方法，這個方法會在叫用`EmotionServiceClient`執行個體，做為上傳映像情緒應用程式開發介面， `Stream`。 API 金鑰，在這項作業會叫用會提交至情緒 API。 若要提交有效的 API 金鑰將會導致`Microsoft.ProjectOxford.Common.ClientException`擲回，並提供例外狀況訊息，指出已提交的 API 金鑰無效。

`RecognizeAsync`方法會傳回`Emotion`陣列，前提是已辨識的字體。 每個映像，可以偵測到的字體的最大數目是 64，，面朝矩形大小，以遞減順序排序次序。 如果偵測不到任何字體時，空`Emotion`會傳回陣列。

解譯結果從情緒 API 時, 偵測到的情緒應解譯為與分數最高情緒分數會正規化為其中一個的總和。 因此，範例應用程式會顯示與最大偵測到的一面分數最高可辨識的情緒中映像，如下列螢幕擷取畫面所示：

![](emotion-recognition-images/emotion-recognition.png "情緒辨識")

## <a name="summary"></a>總結

本文說明如何使用情緒 API 來辨識情緒、 加以分級 Xamarin.Forms 應用程式。 情緒 API 臉部運算式在映像做為輸入，然後傳回信心情感的映像中的一組。


## <a name="related-links"></a>相關連結

- [情緒 API 文件](https://www.microsoft.com/cognitive-services/emotion-api/documentation)
- [Todo 認知服務 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Microsoft.ProjectOxford.Emotion](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/)
- [情緒應用程式開發介面](https://dev.projectoxford.ai/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)
