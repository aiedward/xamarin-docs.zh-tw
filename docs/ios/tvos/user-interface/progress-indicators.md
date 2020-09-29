---
title: 在 Xamarin 中使用 tvOS 進度指示器
description: 本檔說明如何在以 Xamarin 建立的 tvOS 應用程式中使用進度指示器。 它會討論進度列和活動指標。
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 4f79fe550966c36a2be15431757fb7443e090ec9
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436402"
---
# <a name="working-with-tvos-progress-indicators-in-xamarin"></a>在 Xamarin 中使用 tvOS 進度指示器

_本文說明如何在 tvOS 應用程式內設計和使用進度指示器。_

您的 tvOS 應用程式有時需要載入新的內容或執行冗長的處理作業。 在這段時間內，您應該呈現活動指標或進度列，讓使用者知道應用程式仍在執行，並提供一些指示給他們執行的工作長度。

![範例進度指示器](progress-indicators-images/intro01.png "範例進度指示器")

## <a name="about-activity-indicators"></a>關於活動指標

活動指標會顯示為旋轉齒輪，用來代表無法確定長度的工作。 當工作開始時，即會顯示指標，當工作完成時就會消失。

Apple 針對使用活動指標有下列建議：

- **可能的話，請改為使用進度** 列，因為活動指標可讓使用者對所執行程式的時間長度沒有任何意見反應，請一律使用進度列（如果已知長度 (），例如，在檔案) 中要下載的位元組數目。
- **將指標保持在動畫** -使用者會將固定的活動指標與停止的應用程式建立關聯，因此在顯示時，您應該一律以動畫顯示指標。
- **描述正在處理的工作** -只顯示活動指示器本身還不夠;使用者必須知道他們正在等待的進程。 包含有意義的標籤 (通常是明確定義工作的單一完整句子) 。

## <a name="about-progress-bars"></a>關於進度列

進度列會顯示為填滿色彩的線條，以指出耗時工作的狀態和長度。 當工作的長度為已知或可計算時，應一律使用進度列。

Apple 針對使用進度列有下列建議：

- **正確地報告進度** -進度列應一律呈現完成工作所需時間的精確表示。 絕對不要讓應用程式看似忙碌的時間。
- **使用於定義完善的持續** 時間：進度列應該不只會顯示冗長的工作，還能讓使用者和指出工作的完成程度，以及剩餘時間的估計。

## <a name="progress-indicators-and-storyboards"></a>進度指示器和分鏡腳本

在 tvOS 應用程式中使用進度指示器最簡單的方式，就是使用 iOS 設計工具將它新增至應用程式的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 **Solution Pad**中，按兩下 **主要** 的分鏡腳本檔案，然後開啟它進行編輯。

2. 從 [**工具箱**] 拖曳**活動指標**，並將它放在視圖上： 

    ![活動指標](progress-indicators-images/activity01.png "活動指標")

3. 在**Properties Pad**的 [ **Widget** ] 索引標籤中，您可以調整活動指標的數個屬性，例如其**樣式**、**行為**和**名稱**： 

    ![活動指標的 [Widget] 索引標籤](progress-indicators-images/activity02.png "活動指標的 [Widget] 索引標籤")
    
    **名稱**會決定代表 c # 程式碼中活動指標的屬性名稱。

4. 從 [**工具箱**] 拖曳**進度視圖**，然後將它放在視圖上： 

    ![進度視圖](progress-indicators-images/activity03.png "進度視圖")

5. 在**屬性瀏覽器**的 [ **Widget** ] 索引標籤中，您可以調整進度視圖的數個屬性，例如其**樣式**、**進度** (完成百分比) 和**名稱**： 

    ![進度視圖的 Widget 索引標籤](progress-indicators-images/activity04.png "進度視圖的 Widget 索引標籤")
    
    **名稱**會決定以 c # 程式碼表示進度視圖的屬性名稱。

6. 儲存變更。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 **方案總管**中，按兩下 **主要** 的分鏡腳本檔案，然後開啟它進行編輯。

2. 從 [**工具箱**] 拖曳**活動指標**，並將它放在視圖上： 

    ![活動指標](progress-indicators-images/activity01-vs.png
    "活動指標")

3. 在 [**屬性] Explorer**的 [ **Widget** ] 索引標籤中，您可以調整活動指標的數個屬性，例如其**樣式**、**行為**和**名稱**： 

    ![活動指標的 [Widget] 索引標籤](progress-indicators-images/activity02-vs.png "活動指標的 [Widget] 索引標籤")

    **名稱**會決定代表 c # 程式碼中活動指標的屬性名稱。

4. 從 [**工具箱**] 拖曳**進度視圖**，然後將它放在視圖上： 

   ![進度視圖](progress-indicators-images/activity03-vs.png "進度視圖")

5. 在**屬性瀏覽器**的 [ **Widget** ] 索引標籤中，您可以調整進度視圖的數個屬性，例如其**樣式**、**進度** (完成百分比) 和**名稱**： 

    ![進度視圖的 Widget 索引標籤](progress-indicators-images/activity04-vs.png "進度視圖的 Widget 索引標籤")
    
    **名稱**會決定以 c # 程式碼表示進度視圖的屬性名稱。

6. 儲存變更。

-----

如需使用分鏡腳本的詳細資訊，請參閱我們的 [Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。 

## <a name="working-with-activity-indicators"></a>使用活動指示器

如上面所述，當您的應用程式執行長時間不定的程式時，應顯示活動指示器。

在任何時間點，您都可以藉由檢查其屬性來查看活動指標是否有動畫效果 `IsAnimating` 。 如果 `HidesWhenStopped` 屬性為 `true` ，則活動指標會在其動畫停止時自動隱藏。

您可以使用下列程式碼來啟動動畫： 

```csharp
ActivityIndicator.StartAnimating();
```

以下將停止動畫：

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> 這些程式碼片段假設活動指標的**名稱**在 IOS 設計工具的 [ **Widget** ] 索引標籤中設定為 [ **ActivityIndicator** ]。

## <a name="working-with-progress-bars"></a>使用進度列

同樣地，當您的應用程式執行已知持續時間的長時間執行工作時，應該使用進度列。 

`Progress`屬性可用來將已完成的工作量（從0% 到 100% (0.0）設定為 1.0) 。 您 `ProgressTintColor` 可以使用屬性來設定 [完成量] 列的色彩，以及將 `TrackTintColor` 背景色彩設定 ([未完成]) 的屬性。

## <a name="summary"></a>摘要

本文涵蓋了在 tvOS 應用程式內設計和使用進度指示器的程式。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)