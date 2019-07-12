---
title: 在 Xamarin.Forms 中的活動指標
description: ActivityIndicator 控制會向使用者指出，應用程式參與的冗長的活動，而不需要提供進度的任何資訊指出。 這篇文章說明如何使用 ActivityIndicator XAML 和程式碼中。
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: abd8150e3aa4ec347c8d956004993340630208bf
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67837058"
---
# <a name="xamarinforms-activityindicator"></a>Xamarin.Forms ActivityIndicator
[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ActivityIndicatorDemos)

Xamarin.Forms[ `ActivityIndicator` ](xref:Xamarin.Forms.ActivityIndicator)是控制項，顯示的動畫，以顯示應用程式參與的冗長的活動。 不同於[ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar)，則`ActivityIndicator`不提供進度的任何指示。 `ActivityIndicator`繼承自[ `View` ](xref:Xamarin.Forms.View)。

下列螢幕擷取畫面顯示`ActivityIndicator`iOS 和 Android 上的控制項：

![螢幕擷取畫面的 iOS 和 Android 上 ActivityIndicator](activityindicator-images/activityindicators-default.png "螢幕擷取畫面的 iOS 和 Android 上 ActivityIndicator")

`ActivityIndicator`控制項定義下列屬性：

* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) 已`bool`值，指出是否`ActivityIndicator`應該是可見和製作動畫，或隱藏。 當這個值是`false``ActivityIndicator`將不會顯示。
* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color) 已`Color`定義的顯示色彩值`ActivityIndicator`。

這些屬性都會受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示`ActivityIndicator`可自訂樣式和資料繫結的目標。

## <a name="create-an-activityindicator"></a>建立 ActivityIndicator

`ActivityIndicator`可以在 XAML 中具現化。 其`IsRunning`屬性可以設定來判斷控制項是否顯示和製作動畫。 如果`IsRunning`屬性未設定，則預設為`false`而`ActivityIndicator`將不會顯示。 下列範例示範如何具現化`ActivityIndicator`搭配可省略的 XAML 中`IsRunning`屬性集：

```xaml
<ActivityIndicator IsRunning="true" />
```

`ActivityIndicator`也可以建立程式碼中：

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>ActivityIndicator 外觀屬性

`Color`屬性可以設定為定義`ActivityIndicator`色彩。 下列範例示範如何具現化`ActivityIndicator`中使用的 XAML`Color`屬性集：

```xaml
<ActivityIndicator Color="Orange" />
```

`Color`屬性也可以設定建立時`ActivityIndicator`在程式碼中：

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

下列螢幕擷取畫面示`ActivityIndicator`具有`Color`屬性設定為`Color.Orange`iOS 和 Android 上：

![在 iOS 上的已設定樣式的 ActivityIndicator 和 Android 的螢幕擷取畫面](activityindicator-images/activityindicators-styled.png "樣式的 ActivityIndicator ios 和 Android 的螢幕擷取畫面")

## <a name="related-links"></a>相關連結

* [ActivityIndicator 示範](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ActivityIndicatorDemos)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)
