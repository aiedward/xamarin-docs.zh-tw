---
title: "使用進度指標"
description: "本文涵蓋設計與進度指標 Xamarin.tvOS 應用程式內使用。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 582B6D0C-1F16-4299-A9A6-5651E76009FE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: b5d3a03324e73b06bd3defe7e6610163c3d1b26d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-progress-indicators"></a>使用進度指標

_本文涵蓋設計與進度指標 Xamarin.tvOS 應用程式內使用。_


可能會有有時候 Xamarin.tvOS 應用程式需要載入新的內容或執行時間較長的處理作業。 在這段時間，您應該提供將活動指示器或進度列，讓使用者知道應用程式仍在執行，以及讓他們工作正在執行的一些指示。

[ ![](progress-indicators-images/intro01.png "範例進度指標")](progress-indicators-images/intro01.png)

<a name="About-Activity-Indicators" />

## <a name="about-activity-indicators"></a>關於活動指示器

活動指示器以視覺化方式呈現為旋轉齒輪，而且用來代表工作的不明的長度。 當工作啟動，當工作完成時，就會消失時，會顯示指標。

Apple 已使用的活動指標的下列建議：

- **您應該盡可能改用進度列**活動指標可讓使用者處理序正在執行大約需要多久，沒有回應，因為如果長度知道 （例如，若要下載的檔案中的多少個位元組），請一律使用進度列。
- **保留指標動畫**位使用者與靜態狀態的活動指示器已停止的應用程式，您都應該有指標時才會顯示。
- **描述工作正在處理**-只顯示活動指示器單獨使用時，也不夠，使用者必須能夠得知它們要等待的程序。 包含有意義標籤 （通常為單一、 完整句子），以清楚地定義工作。

<a name="Summary" />

## <a name="about-progress-bars"></a>關於進度列

進度列顯示為線條可填入色彩來表示狀態和長度耗時的工作。 一定會知道工作的長度，或可以計算時，可以用進度列。

Apple 具有進度列所使用的下列建議：

- **正確地報告進度**-應一律進度列的精確表示完成工作所需的時間。 永遠不會無法正確地表示時間讓出現忙碌的應用程式。
- **使用 Well-Defined 工期**-進度列不應該只顯示所花的時間較長的工作，但會讓使用者和工作中有多少已完成的指示，然後估計剩餘時間。

<a name="Progress-Indicators-and-Storyboards" />

## <a name="progress-indicators-and-storyboards"></a>進度指標和分鏡腳本

使用進度列指示器 Xamarin.tvOS 應用程式中的最簡單方式是將它們新增到應用程式的 UI 使用 iOS 設計工具。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
    
1. 在**方案板**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**活動指示器**從**工具箱**並將它放在檢視上： 

    [ ![](progress-indicators-images/activity01.png "活動指示器")](progress-indicators-images/activity01.png)
1. 在**Widget 索引標籤**的**屬性板**，您可以調整活動指示器的數個屬性，例如其**樣式**和**行為**: 

    [ ![](progress-indicators-images/activity02.png "[小工具] 索引標籤 ")](progress-indicators-images/activity02.png)
1. 拖曳**進行檢視**從**工具箱**並將它放在檢視上： 

    [ ![](progress-indicators-images/activity03.png "進行檢視")](progress-indicators-images/activity03.png)
1. 在**Widget 索引標籤**的**屬性總管**，您可以調整進行檢視的數個屬性，例如其**樣式**和**進度**（完成百分比）： 

    [ ![](progress-indicators-images/activity04.png "[小工具] 索引標籤")](progress-indicators-images/activity04.png)
1. 最後，指派**名稱**控制項，讓您可以在 C# 程式碼回應給他們。 例如:  

    [ ![](progress-indicators-images/activity05.png "指派的名稱")](progress-indicators-images/activity05.png)
1. 儲存您的變更。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. 在**方案總管 中**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**活動指示器**從**工具箱**並將它放在檢視上： 

    [ ![](progress-indicators-images/activity01-vs.png "活動指示器")](progress-indicators-images/activity01-vs.png)
1. 在**Widget 索引標籤**的**屬性總管**，您可以調整活動指示器的數個屬性，例如其**樣式**和**行為**: 

    [ ![](progress-indicators-images/activity02-vs.png "[小工具] 索引標籤")](progress-indicators-images/activity02-vs.png)
1. 拖曳**進行檢視**從**工具箱**並將它放在檢視上： 

    [ ![](progress-indicators-images/activity03-vs.png "進行檢視")](progress-indicators-images/activity03-vs.png)
1. 在**Widget 索引標籤**的**屬性總管**，您可以調整進行檢視的數個屬性，例如其**樣式**和**進度**（完成百分比）： 

    [ ![](progress-indicators-images/activity04-vs.png "[小工具] 索引標籤")](progress-indicators-images/activity04-vs.png)
1. 最後，指派**名稱**控制項，讓您可以在 C# 程式碼回應給他們。 例如:  

    [ ![](progress-indicators-images/activity05-vs.png "指派的名稱")](progress-indicators-images/activity05-vs.png)
1. 儲存您的變更。

-----

如需使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Activity-Indicators" />

## <a name="working-with-activity-indicators"></a>使用活動的指標

如同前面所述，當您的應用程式正在執行較長的處理序，但您不知道正確的工作將會需要的時間長度時，應該會顯示活動指示器。

您可以看到活動指示器是否正在執行它的旋轉動畫藉由檢查任何一點`IsAnimating`屬性。 如果`HidesWhenStopped`屬性是`true`，它的動畫停止時，將會自動隱藏活動指示器。

若要啟動動畫，您可以使用下列程式碼： 

```csharp
ActivityIndicator.StartAnimating();
```

下列命令會停止動畫：

```csharp
ActivityIndicator.StopAnimating();
```

<a name="Working-with-Progress-Bars" />

## <a name="working-with-progress-bars"></a>使用進度列

同樣地，進度列應該用於您的應用程式正在執行長時間執行的工作，已知持續時間的任何時間。 

`Progress`屬性用來設定的工作已完成從 0%到 100%(0.0 到 1.0)。 使用`ProgressTintColor`屬性來設定量完成列的色彩和`TrackTintColor`屬性來設定背景色彩 （未完成的數量）。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計與進度指標 Xamarin.tvOS 應用程式內使用。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
