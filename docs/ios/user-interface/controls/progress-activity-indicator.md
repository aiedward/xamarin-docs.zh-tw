---
title: 進度和活動的指標
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: 58a492bed81a1d96a482c1396718da1c5e4af589
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="progress-and-activity-indicators"></a>進度和活動的指標

很可能您的應用程式必須執行長時間執行工作，例如載入或處理資料，以及這項延遲可能會造成延遲，更新您的 UI。 在這段期間您應該一律使用進度列指示器 reassure 使用者系統正在忙著處理工作。 這可讓使用者控制的應用程式正在處理其要求，它不是正在等候其輸入，而且可以提供一種詳述完全多久必須等候。

iOS 提供兩種主要的方式，提供您的應用程式在此進度指示： 標記活動 (包括特定_網路_活動指示器) 和進度列。

## <a name="activity-indicator"></a>活動指示器

當您的應用程式正在執行較長的處理序，但您不知道正確的工作將會需要的時間長度時，應該會顯示活動指示器。

Apple 已使用的活動指標的下列建議：

- **您應該盡可能改用進度列**活動指標可讓使用者處理序正在執行大約需要多久，沒有回應，因為如果長度知道 （例如，若要下載的檔案中的多少個位元組），請一律使用進度列。
- **保留指標動畫**位使用者與靜態狀態的活動指示器已停止的應用程式，您都應該有指標時才會顯示。
- **描述工作正在處理**-只顯示活動指示器單獨使用時，也不夠，使用者必須能夠得知它們要等待的程序。 包含有意義標籤 （通常為單一、 完整句子），以清楚地定義工作。

### <a name="implementing-an-activity-indicator"></a>實作活動指示器

透過實作活動指示器[ `UIActivityIndictorView` ](https://developer.xamarin.com/api/type/UIKit.UIActivityIndicatorView/)類別，表示`UIActivity`正在進行中。

### <a name="activity-indicators-and-storyboards"></a>活動的指標和分鏡腳本

如果您使用 iOS 設計工具建立 UI，可配置新增活動指標，從 [工具箱]。 從屬性板，您可以調整下列屬性：

![屬性填補](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>管理活動指標行為

使用`StartAnimating()`和`StopAnimating()`方法啟動或停止活動指示器動畫。

設定`HidesWhenStopped`屬性`true`進行之後消失活動指示器`StopAnimating()`已呼叫。 這會設定為`true`預設。 您可以看到活動指示器是否正在執行它的旋轉動畫藉由檢查任何一點`IsAnimating`屬性。 


### <a name="managing-activity-indicator-appearances"></a>管理活動指示器外觀

`UIActivityIndicatorViewStyle`活動指示器具現化時，可以當做參數傳遞列舉型別。 這個函數可用來將視覺化樣式設定為`Gray`， `White`，或`WhiteLarge`，例如：

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

您可以覆寫所提供的色彩`UIActivityIndicatorViewStyle`藉由設定`Color`屬性。

## <a name="progress-bar"></a>進度列

進度列顯示為線條可填入色彩來表示狀態和長度耗時的工作。 一定會知道工作的長度，或可以計算時，可以用進度列。

Apple 具有進度列所使用的下列建議：

- **正確地報告進度**-應一律進度列的精確表示完成工作所需的時間。 永遠不會無法正確地表示時間讓出現忙碌的應用程式。
- **使用 Well-Defined 工期**-進度列不應該只顯示所花的時間較長的工作，但會讓使用者和工作中有多少已完成的指示，然後估計剩餘時間。

### <a name="implementing-an-progress-bar"></a>實作進度列

進度列由具現化 [`UIProgressView`](https://developer.xamarin.com/api/type/UIKit.UIProgressView/)

### <a name="progress-bars-and-storyboards"></a>進度列和分鏡腳本

您也可以加入至您的 UI 進度列，當使用 iOS 設計工具。 搜尋**進行檢視**中**工具箱**並將它拖曳至您的檢視。

屬性板上，您可以調整下列屬性：

![屬性填補](progress-activity-indicator-images/progress-indicator3.png)


### <a name="managing-progress-bar-behavior"></a>管理進度列行為

進度列的初始設定使用`Progress`屬性：

```csharp
ProgressBar.Progress = 0f;
```

可以藉由調整進度`SetProgress`方法並傳遞宣告是否要變更或不顯示動畫的布林值。

```csharp
ProgressBar.SetProgress(1.0f, true);
```

如需有關使用進度列的詳細資訊，請參閱[報告進度](https://developer.xamarin.com/recipes/cross-platform/networking/download_progress/#Reporting_Progress_in_iOS)配方，而[UICatalog tvOS 範例](https://developer.xamarin.com/samples/monotouch/tvos/UICatalog/)。

### <a name="managing-progress-bar-appearance"></a>管理進度列的外觀

類似於活動指示器，`UIProgressViewStyle`進度列具現化時，可以當做參數傳遞列舉型別。

使用下列屬性可以調整的進度和追蹤映像和濃淡的色彩：

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```



