---
title: 使用 tvOS 的進度指示器，在 Xamarin 中
description: 本文件說明如何使用 Xamarin 建置的 tvOS 應用程式中的進度指標使用。 它討論進度列和活動的指標。
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/25/2018
ms.openlocfilehash: cbd2b2de237a5bb22d1dc0242569b96b12bca070
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106672"
---
# <a name="working-with-tvos-progress-indicators-in-xamarin"></a>使用 tvOS 的進度指示器，在 Xamarin 中

_本文章涵蓋設計和使用 Xamarin.tvOS 應用程式內的進度指示器。_

可能有 Xamarin.tvOS 應用程式需要時載入新的內容，或執行耗時的處理作業的時間。 在這段時間，您應該呈現活動指示器或進度列讓使用者知道應用程式仍在執行，並給予一些指示，指出工作正在執行的長度。

![範例的進度指示器](progress-indicators-images/intro01.png "範例進度指標")

## <a name="about-activity-indicators"></a>相關活動的指標

活動指示器呈現旋轉齒輪，並用來表示長度不明的工作。 啟動工作，並在工作完成時就會消失時，會顯示指標。

Apple 會有下列建議使用活動的指標：

- **可能的話，請改用進度列**-因為需要的任何意見反應來選擇長時間執行的程序的使用者，活動指示器提供一律使用進度列，如果長度為已知 （例如，若要下載檔案中的多少個位元組）。
- **保留以動畫顯示的指示器**-使用者與 「 定態的活動指標是已停止的應用程式，因此您應該一律以動畫顯示指標時所顯示。
- **描述工作正在處理**-只顯示本身的活動指標仍嫌不足，使用者必須以瞭解它們要等待的程序。 包含有意義的標籤 （通常是一個單一、 完整句子），以清楚定義的工作。

## <a name="about-progress-bars"></a>關於進度列

進度列顯示為線條，以填滿色彩，以指出狀態和長度耗時的工作。 工作長度已知或可計算時，應該一律使用進度列。

Apple 有進度列所使用的下列建議：

- **準確地回報進度**-進度列應永遠存在的精確表示完成工作所需的時間。 永遠不會無法正確地表示若要讓應用程式出現忙碌的時間。
- **用於定義完善的持續時間**-放置，橫條圖不應該只顯示正在漫長的工作的進度，但讓使用者和指出多少工作已完成，然後估計剩餘時間。

## <a name="progress-indicators-and-storyboards"></a>進度指標和分鏡腳本

使用進度列指示器 Xamarin.tvOS 應用程式中的最簡單方式是將它新增至使用 iOS 設計工具的應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)
    
1. 在  **Solution Pad**，按兩下**Main.storyboard**檔案，並開啟它進行編輯。

2. 拖曳**活動指示器**從**工具箱**並將它放在檢視上： 

    ![活動指示器](progress-indicators-images/activity01.png "活動指示器")

3. 在  **Widget**索引標籤**Properties Pad**，您可以調整活動指示器的數個屬性，例如其**樣式**，**行為**，並**名稱**: 

    ![小工具 索引標籤，為活動指示器](progress-indicators-images/activity02.png "的活動指標的小工具 索引標籤")
    
    **名稱**判斷屬性表示中的活動指標的名稱C#程式碼。

4. 拖曳**進度檢視**從**工具箱**並將它放在檢視上： 

    ![進度檢視](progress-indicators-images/activity03.png "進度檢視")

5. 在 [ **Widget** ] 索引標籤**屬性總管**，您可以調整的進度檢視數個屬性，例如其**樣式**，**進度**（完成的百分比），並**名稱**: 

    ![小工具 索引標籤，進行檢視](progress-indicators-images/activity04.png "進度檢視 索引標籤上的小工具")
    
    **名稱**判斷屬性表示中的 [進度] 檢視的名稱C#程式碼。

6. 儲存您的變更。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. 在 **方案總管**，按兩下**Main.storyboard**檔案，並開啟它進行編輯。

2. 拖曳**活動指示器**從**工具箱**並將它放在檢視上： 

    ![活動指示器](progress-indicators-images/activity01-vs.png
    "活動指示器")

3. 在  **Widget**索引標籤**屬性總管**，您可以調整活動指示器的數個屬性，例如其**樣式**，**行為**，並**名稱**: 

    ![小工具 索引標籤，為活動指示器](progress-indicators-images/activity02-vs.png "的活動指標的小工具 索引標籤")

    **名稱**判斷屬性表示中的活動指標的名稱C#程式碼。

4. 拖曳**進度檢視**從**工具箱**並將它放在檢視上： 

   ![進度檢視](progress-indicators-images/activity03-vs.png "進度檢視")

5. 在 [ **Widget** ] 索引標籤**屬性總管**，您可以調整的進度檢視數個屬性，例如其**樣式**，**進度**（完成的百分比），並**名稱**: 

    ![小工具 索引標籤，進行檢視](progress-indicators-images/activity04-vs.png "進度檢視 索引標籤上的小工具")
    
    **名稱**判斷屬性表示中的 [進度] 檢視的名稱C#程式碼。

6. 儲存您的變更。

-----

如需有關使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。 

## <a name="working-with-activity-indicators"></a>使用活動的指標

如上所述，當您的應用程式正在執行的不定長度較長的處理序時，應該會顯示活動的指標。

在任何時間點，您可以看到一個活動標記為動畫形式藉由檢查其`IsAnimating`屬性。 如果`HidesWhenStopped`屬性是`true`，停止它的動畫時，將會自動隱藏活動指示器。

您可以使用下列程式碼，以啟動動畫： 

```csharp
ActivityIndicator.StartAnimating();
```

與下列命令會停止動畫：

```csharp
ActivityIndicator.StopAnimating();
```

> [!NOTE]
> 這些程式碼片段假設活動指標**名稱**已設為**ActivityIndicator**中**Widget** iOS 設計工具 索引標籤。

## <a name="working-with-progress-bars"></a>使用進度列

同樣地，進度列也應該使用您的應用程式正在執行長時間執行的工作，已知的持續任何的時間時間。 

`Progress`屬性用來設定工作已完成從 0%到 100%(0.0 到 1.0) 的數量。 使用`ProgressTintColor`屬性來設定已完成的金額列的色彩和`TrackTintColor`屬性來設定背景色彩 （未完成的數量）。

## <a name="summary"></a>總結

這篇文章已涵蓋設計和使用 Xamarin.tvOS 應用程式內的進度指示器。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
