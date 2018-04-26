---
title: 使用進度指標
description: 本文涵蓋設計和使用 Xamarin.tvOS 應用程式內的進度指標。
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/25/2018
ms.openlocfilehash: d512dfddb3a6c81767f937272a4ffb1ab1a35372
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="working-with-progress-indicators"></a>使用進度指標

_本文涵蓋設計和使用 Xamarin.tvOS 應用程式內的進度指標。_

可能會有有時候 Xamarin.tvOS 應用程式需要載入新的內容或執行時間較長的處理作業。 在這段時間，您應該提供活動指示器或進度列，讓使用者知道應用程式仍在執行，並給予一些可用來指示工作正在執行的長度。

![範例進度指標](progress-indicators-images/intro01.png "範例進度指標")

## <a name="about-activity-indicators"></a>關於活動指示器

活動指示器呈現為旋轉齒輪，而且用來代表工作的不明的長度。 當工作啟動，當工作完成時，就會消失時，會顯示指標。

Apple 已使用的活動指標的下列建議：

- **您應該盡可能改用進度列**-因為需要來選擇的任何意見反應長處理序正在執行的使用者，活動指示器提供一律使用進度列，如果長度 （例如，若要下載的檔案中的多少個位元組）。
- **保留指標動畫**位使用者與不動活動指示器已停止的應用程式中，因此您應該一律以動畫顯示指標時，它會顯示。
- **描述工作正在處理**-只顯示活動指示器本身並不足夠; 使用者必須能夠得知它們要等待的程序。 包含有意義標籤 （通常為單一、 完整句子），以清楚地定義工作。

## <a name="about-progress-bars"></a>關於進度列

進度列顯示為線條可填入色彩來表示狀態和長度耗時的工作。 工作的長度為已知或可計算時，應該永遠使用進度列。

Apple 具有進度列所使用的下列建議：

- **正確地回報進度**-進度列應永遠存在的精確表示完成工作所需的時間。 永遠不會無法正確地表示時間讓出現忙碌的應用程式。
- **用於定義完善的持續時間**-橫條圖不應該只顯示所花的時間較長的工作的進度，但會授與使用者和工作中有多少已完成的指示，然後估計剩餘時間。

## <a name="progress-indicators-and-storyboards"></a>進度指標和分鏡腳本

使用進度列指示器 Xamarin.tvOS 應用程式中的最簡單方式是將它加入至應用程式的 UI 使用 iOS 設計工具。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
    
1. 在**方案板**，連按兩下**Main.storyboard**檔案，並開啟它進行編輯。

2. 拖曳**活動指示器**從**工具箱**並將它放在檢視上： 

    ![活動指示器](progress-indicators-images/activity01.png "活動指示器")

3. 在**Widget**  索引標籤**屬性板**，您可以調整活動指示器的數個屬性，例如其**樣式**，**行為**，和**名稱**: 

    ![活動指示器的小工具 索引標籤](progress-indicators-images/activity02.png "活動指示器的小工具索引標籤")
    
    **名稱**決定表示在 C# 程式碼中的活動指標的屬性名稱。

4. 拖曳**進行檢視**從**工具箱**並將它放在檢視上： 

    ![進行檢視](progress-indicators-images/activity03.png "進行檢視")

5. 在**Widget**  索引標籤**屬性總管**，您可以調整進行檢視的數個屬性，例如其**樣式**，**進度**（完成百分比），和**名稱**: 

    ![小工具 索引標籤，進行檢視](progress-indicators-images/activity04.png "進行檢視的 Widget 索引標籤")
    
    **名稱**決定屬性代表在 C# 程式碼中的進行檢視的名稱。

6. 儲存您的變更。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. 在**方案總管 中**，連按兩下**Main.storyboard**檔案，並開啟它進行編輯。

2. 拖曳**活動指示器**從**工具箱**並將它放在檢視上： 

    ![活動指示器](progress-indicators-images/activity01-vs.png
    "活動指示器")

3. 在**Widget**  索引標籤**屬性總管**，您可以調整活動指示器的數個屬性，例如其**樣式**，**行為**，和**名稱**: 

    ![活動指示器的小工具 索引標籤](progress-indicators-images/activity02-vs.png "活動指示器的小工具索引標籤")

    **名稱**決定表示在 C# 程式碼中的活動指標的屬性名稱。

4. 拖曳**進行檢視**從**工具箱**並將它放在檢視上： 

   ![進行檢視](progress-indicators-images/activity03-vs.png "進行檢視")

5. 在**Widget**  索引標籤**屬性總管**，您可以調整進行檢視的數個屬性，例如其**樣式**，**進度**（完成百分比），和**名稱**: 

    ![小工具 索引標籤，進行檢視](progress-indicators-images/activity04-vs.png "進行檢視的 Widget 索引標籤")
    
    **名稱**決定屬性代表在 C# 程式碼中的進行檢視的名稱。

6. 儲存您的變更。

-----

如需使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。 

## <a name="working-with-activity-indicators"></a>使用活動的指標

如同前面所述，當您的應用程式正在執行的不定長度較長的處理序時，應該會顯示活動指示器。

在任何時間點，您可以看到活動指示器為動畫形式藉由檢查其`IsAnimating`屬性。 如果`HidesWhenStopped`屬性是`true`，它的動畫停止時，將會自動隱藏活動指示器。

若要啟動動畫，您可以使用下列程式碼： 

```csharp
ActivityIndicator.StartAnimating();
```

下列命令會停止動畫：

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> 這些程式碼片段假設活動指示器的**名稱**設**ActivityIndicator**中**Widget** iOS 設計工具 索引標籤。

## <a name="working-with-progress-bars"></a>使用進度列

同樣地，進度列應該用於您的應用程式正在執行長時間執行的工作，已知的持續任何的時間時間。 

`Progress`屬性用來設定的工作已完成從 0%到 100%(0.0 到 1.0)。 使用`ProgressTintColor`屬性來設定量完成列的色彩和`TrackTintColor`屬性來設定背景色彩 （未完成的數量）。

## <a name="summary"></a>總結

這篇文章已涵蓋設計和使用 Xamarin.tvOS 應用程式內的進度指標。

## <a name="related-links"></a>相關的連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
