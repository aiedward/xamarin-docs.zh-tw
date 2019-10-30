---
title: 在 Xamarin 中使用 tvOS 進度指示器
description: 本檔說明如何在以 Xamarin 建立的 tvOS 應用程式中使用進度指示器。 同時討論進度列和活動指示器。
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 9d346dc52437b5c079d499e142036e94cfaef65d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030433"
---
# <a name="working-with-tvos-progress-indicators-in-xamarin"></a>在 Xamarin 中使用 tvOS 進度指示器

_本文涵蓋在 tvOS 應用程式內設計和使用進度指示器。_

有時候，您的 tvOS 應用程式可能需要載入新的內容，或執行冗長的處理作業。 在這段期間，您應該會顯示活動指示器或進度列，讓使用者知道應用程式仍在執行中，並提供一些指示來指出所執行之工作的長度。

![範例進度指示器](progress-indicators-images/intro01.png "範例進度指示器")

## <a name="about-activity-indicators"></a>關於活動指示器

活動指標會呈現為旋轉齒輪，用來表示不確定長度的工作。 當工作開始時，即會顯示指標，而當工作完成時，則會消失。

Apple 對於使用活動指示器有下列建議：

- **可能的話，請改為使用進度**列，因為活動指示器會提供使用者對執行的程式所需時間的任何意見反應，如果知道長度（例如，要在檔案中下載多少個位元組），請一律使用進度列。
- **將指標保持動畫**-使用者會將固定的活動指示器與停止的應用程式產生關聯，因此，您應該一律在顯示指標時製作動畫。
- **描述正在處理的工作**-只顯示活動指示器本身還不夠;使用者必須知道他們正在等候的進程。 包含清楚定義工作的有意義標籤（通常是單一、完整的句子）。

## <a name="about-progress-bars"></a>關於進度列

進度列會顯示為填滿色彩的線條，以指出耗時工作的狀態和長度。 當已知的工作長度或可以計算時，應該一律使用進度列。

Apple 具有下列使用進度列的建議：

- **正確地報告進度**列：進度列應一律呈現完成工作所需時間的精確表示。 絕對不要將讓應用程式顯示為忙碌的時間。
- 用於**定義良好的持續**時間-進度列不應該只會顯示有較長的工作正在進行中，而是讓使用者和指示工作的完成程度和剩餘時間的估計。

## <a name="progress-indicators-and-storyboards"></a>進度指示器和分鏡腳本

在 tvOS 應用程式中使用進度列指示器最簡單的方式，就是使用 iOS 設計工具將它新增至應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在  **Solution Pad**中，按兩下**主要**的分鏡腳本檔案，並將其開啟以進行編輯。

2. 從 [**工具箱**] 將**活動指標**拖曳至 [view]： 

    ![活動指標](progress-indicators-images/activity01.png "活動指標")

3. 在  **Properties Pad**的  **Widget**  索引標籤中，您可以調整活動指標的數個屬性，例如其**樣式**、**行為**和**名稱**： 

    ![活動指標的 [Widget] 索引標籤](progress-indicators-images/activity02.png "活動指標的 [Widget] 索引標籤")
    
    **名稱**會決定以C#程式碼表示活動指標的屬性名稱。

4. 從 [**工具箱**] 拖曳**進度視圖**，並將其放置在視圖上： 

    ![進度視圖](progress-indicators-images/activity03.png "進度視圖")

5. 在 [**屬性瀏覽器**] 的 [ **Widget** ] 索引標籤中，您可以調整進度視圖的數個屬性，例如其**樣式**、**進度**（完成百分比）和**名稱**： 

    ![進度視圖的 [Widget] 索引標籤](progress-indicators-images/activity04.png "進度視圖的 [Widget] 索引標籤")
    
    **名稱**會決定以C#程式碼表示進度視圖的屬性名稱。

6. 儲存您的變更。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 **方案總管**中，按兩下**主要**的分鏡腳本檔案，並將其開啟以進行編輯。

2. 從 [**工具箱**] 將**活動指標**拖曳至 [view]： 

    ![活動指標](progress-indicators-images/activity01-vs.png
    "活動指標")

3. 在 [**屬性] Explorer**的 [ **Widget** ] 索引標籤中，您可以調整活動指標的數個屬性，例如其**樣式**、**行為**和**名稱**： 

    ![活動指標的 [Widget] 索引標籤](progress-indicators-images/activity02-vs.png "活動指標的 [Widget] 索引標籤")

    **名稱**會決定以C#程式碼表示活動指標的屬性名稱。

4. 從 [**工具箱**] 拖曳**進度視圖**，並將其放置在視圖上： 

   ![進度視圖](progress-indicators-images/activity03-vs.png "進度視圖")

5. 在 [**屬性瀏覽器**] 的 [ **Widget** ] 索引標籤中，您可以調整進度視圖的數個屬性，例如其**樣式**、**進度**（完成百分比）和**名稱**： 

    ![進度視圖的 [Widget] 索引標籤](progress-indicators-images/activity04-vs.png "進度視圖的 [Widget] 索引標籤")
    
    **名稱**會決定以C#程式碼表示進度視圖的屬性名稱。

6. 儲存您的變更。

-----

如需使用分鏡腳本的詳細資訊，請參閱我們的[Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。 

## <a name="working-with-activity-indicators"></a>使用活動指示器

如上所述，當您的應用程式執行長時間不定的進程時，應該會顯示活動指示器。

在任何時間點，您都可以藉由檢查其 `IsAnimating` 屬性來查看活動指標是否為動畫。 如果 `HidesWhenStopped` 屬性為 `true`，則活動指標會在其動畫停止時自動隱藏。

您可以使用下列程式碼來啟動動畫： 

```csharp
ActivityIndicator.StartAnimating();
```

而下列動作會停止動畫：

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> 這些程式碼片段假設活動指示器的**名稱**在 IOS 設計工具的 [ **Widget** ] 索引標籤中設定為 [ **ActivityIndicator** ]。

## <a name="working-with-progress-bars"></a>使用進度列

同樣地，每當您的應用程式執行已知持續時間的長時間執行工作時，都應該使用進度列。 

`Progress` 屬性是用來設定已完成的工作量（從0% 到100% （0.0 至1.0））。 使用 [`ProgressTintColor`] 屬性來設定 [已完成的數量] 列和 [`TrackTintColor`] 屬性的色彩，以設定背景色彩（未完成的金額）。

## <a name="summary"></a>總結

本文涵蓋在 tvOS 應用程式內設計和使用進度指示器。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
