---
title: Xamarin 中的進度和活動指示器
description: 本檔討論如何在 Xamarin 中使用進度和活動指示器。 它描述如何以程式設計方式和使用分鏡腳本來使用它們。
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2017
ms.openlocfilehash: 0bfb4168e3d990ae1afb3ee2022553053c383083
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697588"
---
# <a name="progress-and-activity-indicators-in-xamarinios"></a>Xamarin 中的進度和活動指示器

您的應用程式很可能必須執行長時間執行的工作，例如載入或處理資料，而且此延遲可能會導致更新 UI 的延遲。 在這段期間，您應該一律使用進度指標來讓系統正在執行工作的使用者。 這可讓使用者控制應用程式正在處理其要求，但不會等待其輸入，並且可提供一種方式來詳細描述他們必須等待的時間長度。

iOS 提供兩種主要方式，在您的應用程式中提供此進度指示：活動指標 (包括特定的 _網路_ 活動指標) 和進度列。

## <a name="activity-indicator"></a>活動指示器

當您的應用程式執行較長的程式，但您不知道工作將需要的確切時間長度時，應該會顯示活動指示器。

Apple 針對使用活動指標有下列建議：

- **可能的話，請改為使用進度** 列，因為活動指標讓使用者不會看到執行中程式所需的時間，請一律使用進度列，如果長度是已知的，則一律使用進度列 (例如，檔案) 中要下載的位元組數目。
- **將指標保持在動畫** -使用者會將靜止的活動指標與停止的應用程式產生關聯，如此一來，當它顯示時，您應該一律將指標動畫。
- **描述正在處理的工作** -只顯示活動指示器本身並不夠，使用者需要知道他們正在等待的進程。 包含有意義的標籤 (通常是明確定義工作的單一完整句子) 。

### <a name="implementing-an-activity-indicator"></a>執行活動指標

活動指標是透過 [`UIActivityIndictorView`](xref:UIKit.UIActivityIndicatorView) 類別來執行，以表示正在進行 `UIActivity` 。

### <a name="activity-indicators-and-storyboards"></a>活動指標和分鏡腳本

如果您使用 iOS 設計工具建立 UI，則可以從 [工具箱] 將活動指標加入至您的版面配置。 您可以從 Properties Pad 調整下列屬性：

![螢幕擷取畫面：顯示您可以在其中修改樣式、色彩和行為屬性的 Properties Pad。](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>管理活動指標行為

使用 `StartAnimating()` 和 `StopAnimating()` 方法來啟動及停止活動指標動畫。

將 `HidesWhenStopped` 屬性設為， `true` 可讓活動指標在呼叫之後消失 `StopAnimating()` 。 根據預設，這會設為 `true` 。 在任何時間點，您都可以藉由檢查屬性來查看活動指標是否正在執行其旋轉動畫 `IsAnimating` 。 

### <a name="managing-activity-indicator-appearances"></a>管理活動指標外觀

當具現 `UIActivityIndicatorViewStyle` 化活動指標時，可以將列舉傳遞為參數。 您可以使用這種方式，將視覺效果樣式設定為 `Gray` 、 `White` 或 `WhiteLarge` ，例如：

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

您可以藉由設定屬性來覆寫提供的色彩 `UIActivityIndicatorViewStyle` `Color` 。

## <a name="progress-bar"></a>進度列

進度列會顯示為填滿色彩的線條，以指出耗時工作的狀態和長度。 當工作的長度為已知或可計算時，應一律使用進度列。

Apple 針對使用進度列有下列建議：

- **正確地報告進度** ：進度列應一律精確表示完成工作所需的時間。 絕對不要讓應用程式看似忙碌的時間。
- **使用於 Well-Defined 持續** 時間-進度列應該不只會顯示漫長的工作正在進行中，而是讓使用者和指出工作的完成程度，以及估計剩餘的時間。

### <a name="implementing-an-progress-bar"></a>執行進度列

藉由具現化來建立進度列 [`UIProgressView`](xref:UIKit.UIProgressView)

### <a name="progress-bars-and-storyboards"></a>進度列和分鏡腳本

使用 iOS 設計工具時，您也可以將進度列新增至 UI。 在 [**工具箱**] 中搜尋 **進度視圖**，然後將它拖曳至您的視圖。

下列屬性可以在 properties pad 上調整：

![螢幕擷取畫面顯示 Properties Pad，您可以在其中修改樣式、進度、進度色調、追蹤色調、進度影像，以及追蹤影像屬性。](progress-activity-indicator-images/progress-indicator3.png)

### <a name="managing-progress-bar-behavior"></a>管理進度列行為

您可以使用屬性一開始就設定橫條的進度 `Progress` ：

```csharp
ProgressBar.Progress = 0f;
```

`SetProgress`如果您想要動畫的變更，可以使用方法來調整進度，並傳遞布林值。

```csharp
ProgressBar.SetProgress(1.0f, true);
```

如需使用進度列的詳細資訊，請參閱 [報告進度](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/networking/download_progress) 配方和 [UICatalog tvOS 範例](/samples/xamarin/ios-samples/tvos-uicatalog)。

### <a name="managing-progress-bar-appearance"></a>管理進度列外觀

與活動指標類似，在具現 `UIProgressViewStyle` 化進度列時，可以將列舉傳遞為參數。

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