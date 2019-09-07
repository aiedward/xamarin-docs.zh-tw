---
title: Xamarin 中的進度和活動指示器
description: 本檔討論如何在 Xamarin 中使用進度和活動指示器。 其中說明如何以程式設計方式和分鏡腳本來使用它們。
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/11/2017
ms.openlocfilehash: a2197a1ff9c37546fd97eb5a2459764ec05d4412
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768920"
---
# <a name="progress-and-activity-indicators-in-xamarinios"></a>Xamarin 中的進度和活動指示器

您的應用程式很可能必須執行長時間執行的工作，例如載入或處理資料，而且這項延遲可能會導致更新 UI 的延遲。 在這段期間，您應該一律使用進度列指示器，以 reassure 系統正在執行工作的使用者。 這可讓使用者控制項應用程式正在處理其要求，而不會等候其輸入，而且可以提供一種方式來詳細說明他們必須等待的時間。

iOS 提供兩種主要方式，可在您的應用程式中提供此進度指示：活動指示器（包括特定的_網路_活動指標）和進度列。

## <a name="activity-indicator"></a>活動指標

當您的應用程式正在執行較長的進程，但您不知道工作將需要的確切時間長度時，應該會顯示活動指示器。

Apple 對於使用活動指示器有下列建議：

- **可能的話，請改為使用進度**列，因為活動指示器會提供使用者對執行的程式所需時間的任何意見反應，如果長度知道，請一律使用進度列（例如，要在檔案中下載多少個位元組）。
- **將指標保持動畫**-使用者會將固定的活動指標與停止的應用程式產生關聯，如此一來，在顯示指示器時，您應該一律有動畫。
- **描述所處理的工作**-只顯示活動指示器本身不夠，使用者必須知道他們正在等候的進程。 包含清楚定義工作的有意義標籤（通常是單一、完整的句子）。

### <a name="implementing-an-activity-indicator"></a>執行活動指標

活動指標會透過[`UIActivityIndictorView`](xref:UIKit.UIActivityIndicatorView)類別來執行，以指出`UIActivity`正在進行中。

### <a name="activity-indicators-and-storyboards"></a>活動指示器和分鏡腳本

如果您使用 iOS 設計工具來建立 UI，則可以從 [工具箱] 將活動指示器新增至您的版面配置。 下列屬性可以從 Properties Pad 進行調整：

![Properties Pad](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>管理活動指標行為

`StartAnimating()`使用和`StopAnimating()`方法來啟動和停止活動指示器動畫。

將屬性設定為`true` ，讓活動指示器在呼叫之後`StopAnimating()`消失。 `HidesWhenStopped` 根據預設，這`true`會設定為。 您可以透過檢查`IsAnimating`屬性，查看活動指標是否正在執行其旋轉動畫。 

### <a name="managing-activity-indicator-appearances"></a>管理活動指示器外觀

具`UIActivityIndicatorViewStyle`現化活動指標時，可以將列舉當做參數傳遞。 您可以使用這個來將視覺化樣式設定為`Gray`、 `White`或`WhiteLarge`，例如：

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

您可以藉`UIActivityIndicatorViewStyle`由`Color`設定屬性來覆寫所提供的色彩。

## <a name="progress-bar"></a>進度列

進度列會顯示為填滿色彩的線條，以指出耗時工作的狀態和長度。 當工作的長度知道或可以計算時，應該一律使用進度列。

Apple 具有下列使用進度列的建議：

- **正確地報告進度**列：進度列應一律是完成工作所需時間的精確表示。 絕對不要將讓應用程式顯示為忙碌的時間。
- 用於**定義良好的持續**時間-進度列不應該只會顯示有較長的工作正在進行中，而是提供使用者和指示完成的工作和剩餘時間的估計。

### <a name="implementing-an-progress-bar"></a>執行進度列

進度列是藉由具現化來建立的[`UIProgressView`](xref:UIKit.UIProgressView)

### <a name="progress-bars-and-storyboards"></a>進度列和分鏡腳本

您也可以在使用 iOS 設計工具時，將進度列新增至 UI。 在 [**工具箱**] 中搜尋**進度視圖**，並將它拖曳至您的 view。

您可以在 properties pad 上調整下列屬性：

![Properties Pad](progress-activity-indicator-images/progress-indicator3.png)

### <a name="managing-progress-bar-behavior"></a>管理進度列行為

您可以使用`Progress`屬性，一開始就能設定橫條的進度：

```csharp
ProgressBar.Progress = 0f;
```

如果您想要動畫變更，可以`SetProgress`使用方法來調整進度，並傳遞布林值宣告。

```csharp
ProgressBar.SetProgress(1.0f, true);
```

如需有關使用進度列的詳細資訊，請參閱[報告進度](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/networking/download_progress)配方和[UICatalog tvOS 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-uicatalog)。

### <a name="managing-progress-bar-appearance"></a>管理進度列的外觀

類似于活動指標，在`UIProgressViewStyle`具現化進度列時，可以將列舉當做參數來傳遞。

您可以使用下列屬性來調整進度和追蹤影像和色調色彩：

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```
