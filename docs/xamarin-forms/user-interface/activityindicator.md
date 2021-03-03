---
title: 中的活動指標 Xamarin.Forms
description: ActivityIndicator 控制項向使用者表示應用程式正在參與冗長的活動，而不會提供任何進度指示。 本文說明如何在 XAML 和程式碼中使用 ActivityIndicator。
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 739fc90eda513e4627f5804283bdcab9c0cbacb4
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373116"
---
# <a name="xamarinforms-activityindicator"></a>Xamarin.Forms ActivityIndicator
[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

Xamarin.Forms [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) 控制項會顯示一個動畫，以顯示應用程式正在參與冗長的活動。 與不同的是 [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) ， `ActivityIndicator` 不會提供進度的指示。 `ActivityIndicator`繼承自 [`View`](xref:Xamarin.Forms.View) 。

下列螢幕擷取畫面顯示 `ActivityIndicator` iOS 和 Android 上的控制項：

![ActivityIndicator 在 iOS 和 Android 上的螢幕擷取畫面](activityindicator-images/activityindicators-default.png "ActivityIndicator 在 iOS 和 Android 上的螢幕擷取畫面")

`ActivityIndicator`控制項會定義下列屬性：

* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color) 這是一個 `Color` 值，它會定義的顯示色彩 `ActivityIndicator` 。
* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) 這是一個 `bool` 值，指出是否 `ActivityIndicator` 應該顯示、建立動畫或隱藏。 當值為時， `false` 就 `ActivityIndicator` 不會顯示。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示 `ActivityIndicator` 可以設定樣式，也可以是資料系結的目標。

## <a name="create-an-activityindicator"></a>建立 ActivityIndicator

`ActivityIndicator`類別可以在 XAML 中具現化。 其 `IsRunning` 屬性會決定控制項是否可見並以動畫顯示。 `IsRunning`屬性預設為 `false` 。 下列範例顯示如何 `ActivityIndicator` 在 XAML 中具現化具有選擇性 `IsRunning` 屬性集的：

```xaml
<ActivityIndicator IsRunning="true" />
```

您 `ActivityIndicator` 也可以在程式碼中建立：

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>ActivityIndicator 外觀屬性

`Color`屬性會定義 `ActivityIndicator` 色彩。 下列範例顯示如何 `ActivityIndicator` 在 XAML 中具現化具有 `Color` 屬性集的：

```xaml
<ActivityIndicator Color="Orange" />
```

`Color`在程式碼中建立時，也可以設定此屬性 `ActivityIndicator` ：

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

下列螢幕擷取畫面顯示在 `ActivityIndicator` `Color` `Color.Orange` iOS 和 Android 上，將屬性設定為的：

![IOS 和 Android 上樣式 ActivityIndicator 的螢幕擷取畫面](activityindicator-images/activityindicators-styled.png "IOS 和 Android 上樣式 ActivityIndicator 的螢幕擷取畫面")

## <a name="related-links"></a>相關連結

* [ActivityIndicator 示範](/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [進度列](~/xamarin-forms/user-interface/progressbar.md)