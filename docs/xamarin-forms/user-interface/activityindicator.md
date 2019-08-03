---
title: Xamarin 中的活動指標
description: ActivityIndicator 控制項會向使用者表示應用程式正在進行長時間的活動, 而不會提供進度的指示。 本文說明如何在 XAML 和程式碼中使用 ActivityIndicator。
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: e13a46e1022f4e33ace6f9f19bb5cea5d1ac784b
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739159"
---
# <a name="xamarinforms-activityindicator"></a>Xamarin. Forms ActivityIndicator
[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

[Xamarin[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) ] 是顯示動畫的控制項, 可顯示應用程式正在進行長時間的活動。 與不同的是`ActivityIndicator` [,不會提供進度的指示。`ProgressBar`](xref:Xamarin.Forms.ProgressBar) `ActivityIndicator`繼承[自`View`](xref:Xamarin.Forms.View)。

下列螢幕擷取畫面顯示 iOS `ActivityIndicator`和 Android 上的控制項:

![IOS 和 Android 上的 ActivityIndicator 螢幕擷取畫面](activityindicator-images/activityindicators-default.png "IOS 和 Android 上的 ActivityIndicator 螢幕擷取畫面")

`ActivityIndicator`控制項會定義下列屬性:

* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)這是`bool`一個值, 表示`ActivityIndicator`是否應該顯示、建立動畫或隱藏。 當值為`false` `ActivityIndicator`時, 將不會顯示。
* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color)這是定義顯示色彩`ActivityIndicator`的值。`Color`

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援, 這表示`ActivityIndicator`可以設定樣式, 並作為資料系結的目標。

## <a name="create-an-activityindicator"></a>建立 ActivityIndicator

`ActivityIndicator`可以在 XAML 中具現化。 您`IsRunning`可以設定它的屬性, 以判斷控制項是否為可見並建立動畫。 如果未設定`false` `ActivityIndicator`屬性, 則會預設為, 且不會顯示。 `IsRunning` 下列範例示範如何使用選擇性`ActivityIndicator` `IsRunning`的屬性集, 在 XAML 中具現化:

```xaml
<ActivityIndicator IsRunning="true" />
```

也`ActivityIndicator`可以在程式碼中建立:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>ActivityIndicator 外觀屬性

您可以設定`ActivityIndicator`屬性來定義色彩。 `Color` 下列範例顯示如何`ActivityIndicator`在 XAML 中`Color`使用屬性集來具現化:

```xaml
<ActivityIndicator Color="Orange" />
```

`ActivityIndicator`在程式碼中建立時, `Color`也可以設定屬性:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

下列螢幕擷取畫面顯示`ActivityIndicator` `Color`在 iOS 和 Android 上, `Color.Orange`屬性設為的:

![IOS 和 Android 上的樣式 ActivityIndicator 螢幕擷取畫面](activityindicator-images/activityindicators-styled.png "IOS 和 Android 上的樣式 ActivityIndicator 螢幕擷取畫面")

## <a name="related-links"></a>相關連結

* [ActivityIndicator 示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)
