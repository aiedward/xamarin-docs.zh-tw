---
title: 進度和活動在 Xamarin.iOS 中的指標
description: 本文討論如何在 Xamarin.iOS 中使用的進度和活動的指標。 它說明如何使用它們，以程式設計方式及使用分鏡腳本。
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2017
ms.openlocfilehash: cb56af300444020a543c16afb0dfb48015fc2153
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102590"
---
# <a name="progress-and-activity-indicators-in-xamarinios"></a>進度和活動在 Xamarin.iOS 中的指標

很可能您的應用程式必須執行長時間執行工作，例如載入或處理資料，和此延遲可能會延遲導致更新您的 UI。 在這段期間您應該一律使用進度列指示器來 reassure 系統正在執行工作的使用者。 這可讓使用者控制的應用程式正在處理其要求，它不是正在等候其輸入，而且可以提供一種詳述他們不必等候的時間長度完全。

iOS 提供兩種主要的方式，來提供此應用程式中的進度指示： 活動的指標 (包括特定_網路_活動指示器) 和進度列。

## <a name="activity-indicator"></a>活動指示器

當您的應用程式正在執行較長的處理序，但您不知道確切的需要工作的時間長度時，應該會顯示活動的指標。

Apple 會有下列建議使用活動的指標：

- **可能的話，請改用進度列**-因為活動指示器可讓使用者處理序正在執行需要多久，無回應一律使用進度列，如果長度知道 （例如，若要下載檔案中的多少個位元組）。
- **讓指標動畫**-使用者與 「 定態的活動指標已停止的應用程式，您應一律具有動畫效果時所顯示的指示器。
- **描述工作正在處理**-只顯示本身的活動指標是不夠的使用者必須告知它們要等待的程序。 包含有意義的標籤 （通常是一個單一、 完整句子），以清楚定義的工作。

### <a name="implementing-an-activity-indicator"></a>實作的活動指標

透過實作活動指示器[ `UIActivityIndictorView` ](https://developer.xamarin.com/api/type/UIKit.UIActivityIndicatorView/)類別，表示`UIActivity`正在進行。

### <a name="activity-indicators-and-storyboards"></a>活動的指標和分鏡腳本

如果您使用 iOS 設計工具建立 UI，可以為您配置加入活動指示器，從 [工具箱]。 從 [屬性] 面板，可以調整下列屬性：

![Properties Pad](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>管理活動指標行為

使用`StartAnimating()`和`StopAnimating()`方法啟動或停止活動指示器動畫。

設定`HidesWhenStopped`屬性，以`true`讓後會消失的活動指標`StopAnimating()`已呼叫。 此值設為`true`預設。 在任何時間點，您可以看到活動的指標是否正在檢查來執行它旋轉的動畫`IsAnimating`屬性。 


### <a name="managing-activity-indicator-appearances"></a>管理活動指示器外觀

`UIActivityIndicatorViewStyle`具現化的活動指標時，可以做為參數傳遞列舉型別。 您可以將視覺化樣式設定為使用這`Gray`， `White`，或`WhiteLarge`，例如：

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

您可以覆寫所提供的色彩`UIActivityIndicatorViewStyle`藉由設定`Color`屬性。

## <a name="progress-bar"></a>進度列

進度列顯示為線條，以填滿色彩，以指出狀態和長度耗時的工作。 工作的長度就是，或您可以計算時，應該一律使用進度列。

Apple 有進度列所使用的下列建議：

- **準確地報告進度**-進度列應該一律是完成工作所需的時間的精確表示。 永遠不會無法正確地表示若要讓應用程式出現忙碌的時間。
- **用於 Well-Defined 持續時間**-進度列不應該只顯示漫長的工作正在放置，但讓使用者和指出多少工作已完成，然後估計剩餘時間。

### <a name="implementing-an-progress-bar"></a>實作進度列

進度列由具現化 [`UIProgressView`](https://developer.xamarin.com/api/type/UIKit.UIProgressView/)

### <a name="progress-bars-and-storyboards"></a>進度列和分鏡腳本

您也可以新增至您的 UI 進度列，當使用 iOS 設計工具。 搜尋**進度檢視**中**工具箱**並將它拖曳至您的檢視。

在 [屬性] 面板上，可以調整下列屬性：

![Properties Pad](progress-activity-indicator-images/progress-indicator3.png)


### <a name="managing-progress-bar-behavior"></a>管理進度列行為

進度列的初始設定使用`Progress`屬性：

```csharp
ProgressBar.Progress = 0f;
```

使用可調整進度`SetProgress`方法並傳遞布林值，如果您想要或不以動畫顯示變更的宣告。

```csharp
ProgressBar.SetProgress(1.0f, true);
```

如需有關使用進度列的詳細資訊，請參閱[報告的進度](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/networking/download_progress)配方，而[UICatalog tvOS 範例](https://developer.xamarin.com/samples/monotouch/tvos/UICatalog/)。

### <a name="managing-progress-bar-appearance"></a>管理進度列的外觀

類似於活動指示器，`UIProgressViewStyle`具現化進度列時，可以做為參數傳遞列舉型別。

可以調整的進度和追蹤映像和濃淡的色彩，使用下列屬性：

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```



