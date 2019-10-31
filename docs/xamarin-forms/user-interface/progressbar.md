---
title: Xamarin. Forms ProgressBar
description: ProgressBar 是控制項，以視覺化方式將進度表示為根據 float 屬性填滿的水準橫條。
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: 40f3c9a5af29d1782249775b9f3166698eb6221a
ms.sourcegitcommit: 7acff5c5a03a0351962c05beebfc347503d83fe6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2019
ms.locfileid: "73201940"
---
# <a name="xamarinforms-progressbar"></a>Xamarin. Forms ProgressBar
[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

[Xamarin] [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)控制項會以視覺方式表示水準橫條的進度，填滿由 `float` 值表示的百分比。 `ProgressBar` 類別繼承自[`View`](xref:Xamarin.Forms.View)。

下列螢幕擷取畫面顯示 iOS 和 Android 上的 `ProgressBar`：

![IOS 和 Android 上的 ProgressBar 螢幕擷取畫面](progressbar-images/progressbars-default.png "IOS 和 Android 上的 ProgressBar")

`ProgressBar` 控制項會定義兩個屬性：

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress)是 `float` 值，表示目前的進度，其值介於0到1之間。 小於0的 `Progress` 值會壓制為0，大於1的值將會壓制為1。
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor)是影響內部長條色彩的 `Color`，代表目前的進度。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示可以將 `ProgressBar` 樣式化，並將其作為資料系結的目標。

`ProgressBar` 控制項也會定義 `ProgressTo` 方法，以將其目前值中的橫條繪製為指定的值。 如需詳細資訊，請參閱[建立 ProgressBar 的動畫](#animate-a-progressbar)。

> [!NOTE]
> `ProgressBar` 不接受使用者操作，因此在使用 Tab 鍵選取控制項時，會略過該動作。

## <a name="create-a-progressbar"></a>建立 ProgressBar

`ProgressBar` 可以在 XAML 中具現化。 其 `Progress` 屬性會決定內部彩色橫條的填滿百分比。 預設 `Progress` 屬性值為0。 下列範例顯示如何使用選擇性的 `Progress` 屬性集，在 XAML 中具現化 `ProgressBar`：

```xaml
<ProgressBar Progress="0.5" />
```

您也可以在程式碼中建立 `ProgressBar`：

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> 請勿使用不受限制的水準配置選項，例如 `Center`、`Start`或 `End` 與 `ProgressBar`。 在 UWP 上，`ProgressBar` 會折迭為零寬度的橫條。 保留預設值 `HorizontalOptions` `Fill`，並在將 `ProgressBar` 放在 `Grid` 版面配置時，不要使用 `Auto` 的寬度。

## <a name="progressbar-appearance-properties"></a>ProgressBar 外觀屬性

當 `Progress` 屬性大於零時，`ProgressColor` 屬性會定義內部橫條圖色彩。 下列範例示範如何使用 `ProgressColor` 屬性集，在 XAML 中具現化 `ProgressBar`：

```xaml
<ProgressBar ProgressColor="Orange" />
```

在程式碼中建立 `ProgressBar` 時，也可以設定 `ProgressColor` 屬性：

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

下列螢幕擷取畫面顯示在 iOS 和 Android 上，`ProgressColor` 屬性設定為 `Color.Orange` 的 `ProgressBar`：

![IOS 和 Android 上的樣式 ProgressBar 螢幕擷取畫面](progressbar-images/progressbars-styled.png "IOS 和 Android 上的樣式 ProgressBar")

## <a name="animate-a-progressbar"></a>建立 ProgressBar 的動畫

`ProgressTo` 方法會在一段時間後，將 `ProgressBar` 從其目前的 `Progress` 值動畫處理為提供的值。 方法會接受 `float` 的進度值、`uint` 持續時間（以毫秒為單位）、`Easing` 列舉值，並傳回 `Task<bool>`。 下列程式碼示範如何建立 `ProgressBar`的動畫：

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

如需 `Easing` 列舉的詳細資訊，請參閱[在 Xamarin 中簡化](~/xamarin-forms/user-interface/animation/easing.md)函式。

## <a name="related-links"></a>相關連結

* [ProgressBar 示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)
