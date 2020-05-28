---
title: 中的活動指標Xamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a83885175a44f2174db343abf4591f8777041d39
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136509"
---
# <a name="xamarinforms-activityindicator"></a>Xamarin.FormsActivityIndicator
[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

Xamarin.Forms [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) 控制項會顯示一個動畫，顯示應用程式正在進行長時間的活動。 與不同的 [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) 是， `ActivityIndicator` 不會提供進度的指示。 `ActivityIndicator`繼承自 [`View`](xref:Xamarin.Forms.View) 。

下列螢幕擷取畫面顯示 `ActivityIndicator` iOS 和 Android 上的控制項：

![IOS 和 Android 上的 ActivityIndicator 螢幕擷取畫面](activityindicator-images/activityindicators-default.png "IOS 和 Android 上的 ActivityIndicator 螢幕擷取畫面")

`ActivityIndicator`控制項會定義下列屬性：

* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color)這是 `Color` 定義顯示色彩的值 `ActivityIndicator` 。
* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)這是一個 `bool` 值，表示是否 `ActivityIndicator` 應該顯示、建立動畫或隱藏。 當值為時，則 `false` `ActivityIndicator` 不會顯示。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示 `ActivityIndicator` 可以設定樣式，並作為資料系結的目標。

## <a name="create-an-activityindicator"></a>建立 ActivityIndicator

`ActivityIndicator`類別可以在 XAML 中具現化。 其 `IsRunning` 屬性會決定控制項是否為可見和動畫。 `IsRunning`屬性預設為 `false` 。 下列範例示範如何 `ActivityIndicator` 使用選擇性的屬性集，在 XAML 中具現化 `IsRunning` ：

```xaml
<ActivityIndicator IsRunning="true" />
```

`ActivityIndicator`也可以在程式碼中建立：

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>ActivityIndicator 外觀屬性

`Color`屬性會定義 `ActivityIndicator` 色彩。 下列範例顯示如何 `ActivityIndicator` 在 XAML 中使用屬性集來具現化 `Color` ：

```xaml
<ActivityIndicator Color="Orange" />
```

`Color`在程式碼中建立時，也可以設定屬性 `ActivityIndicator` ：

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

下列螢幕擷取畫面顯示在 `ActivityIndicator` `Color` `Color.Orange` iOS 和 Android 上，將屬性設定為：

![IOS 和 Android 上的樣式 ActivityIndicator 螢幕擷取畫面](activityindicator-images/activityindicators-styled.png "IOS 和 Android 上的樣式 ActivityIndicator 螢幕擷取畫面")

## <a name="related-links"></a>相關連結

* [ActivityIndicator 示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [進度列](~/xamarin-forms/user-interface/progressbar.md)
