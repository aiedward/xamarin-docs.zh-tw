---
title: Xamarin.FormsProgressBar
description: Xamarin.FormsProgressBar 是控制項，以視覺化方式將進度表示為根據 float 屬性填滿的水準橫條。
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b4ac6231c0483c0c44755c2ac9539f237dd64251
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136275"
---
# <a name="xamarinforms-progressbar"></a>Xamarin.FormsProgressBar
[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

Xamarin.Forms [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) 控制項會以水準橫條表示進度，填滿值所表示的百分比 `float` 。 `ProgressBar`類別繼承自 [`View`](xref:Xamarin.Forms.View) 。

下列螢幕擷取畫面顯示 `ProgressBar` iOS 和 Android 上的：

![IOS 和 Android 上的 ProgressBar 螢幕擷取畫面](progressbar-images/progressbars-default.png "IOS 和 Android 上的 ProgressBar")

`ProgressBar`控制項會定義兩個屬性：

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress)這是一個 `float` 值，表示目前的進度，做為0到1的值。 `Progress`小於0的值將會壓制為0，大於1的值將會壓制為1。
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor)是 `Color` ，它會影響代表目前進度的內部橫條色彩。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示 `ProgressBar` 可以設定樣式，並作為資料系結的目標。

`ProgressBar`控制項也 `ProgressTo` 會定義方法，以將其目前值中的橫條繪製為指定的值。 如需詳細資訊，請參閱[建立 ProgressBar 的動畫](#animate-a-progressbar)。

> [!NOTE]
> `ProgressBar`不接受使用者操作，因此在使用 Tab 鍵選取控制項時，會略過該動作。

## <a name="create-a-progressbar"></a>建立 ProgressBar

`ProgressBar`可以在 XAML 中具現化。 其 `Progress` 屬性會決定內部彩色橫條的填滿百分比。 預設 `Progress` 屬性值為0。 下列範例顯示如何 `ProgressBar` 在 XAML 中使用選擇性屬性集來具現化 `Progress` ：

```xaml
<ProgressBar Progress="0.5" />
```

`ProgressBar`也可以在程式碼中建立：

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> 請勿使用無限制的水準配置選項 `Center` ，例如、 `Start` 或 `End` `ProgressBar` 。 在 UWP 上，會折迭 `ProgressBar` 為零寬度的橫條。 `HorizontalOptions`在配置中放置時，請保留的預設值 `Fill` ，並不要使用的寬度 `Auto` `ProgressBar` `Grid` 。

## <a name="progressbar-appearance-properties"></a>ProgressBar 外觀屬性

`ProgressColor`當屬性大於零時，屬性會定義內部橫條色彩 `Progress` 。 下列範例顯示如何 `ProgressBar` 在 XAML 中使用屬性集來具現化 `ProgressColor` ：

```xaml
<ProgressBar ProgressColor="Orange" />
```

`ProgressColor`在程式碼中建立時，也可以設定屬性 `ProgressBar` ：

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

下列螢幕擷取畫面顯示在 `ProgressBar` `ProgressColor` `Color.Orange` iOS 和 Android 上，將屬性設定為：

![IOS 和 Android 上的樣式 ProgressBar 螢幕擷取畫面](progressbar-images/progressbars-styled.png "IOS 和 Android 上的樣式 ProgressBar")

## <a name="animate-a-progressbar"></a>建立 ProgressBar 的動畫

方法會在一段時間後， `ProgressTo` `ProgressBar` 從其目前的值將動畫複製 `Progress` 到提供的值。 方法會接受一個 `float` 進度值（ `uint` 以毫秒為單位）， `Easing` 並傳回一個列舉值 `Task<bool>` 。 下列程式碼示範如何建立動畫 `ProgressBar` ：

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

如需列舉的詳細資訊 `Easing` ，請參閱[中 Xamarin.Forms 的緩時函數](~/xamarin-forms/user-interface/animation/easing.md)。

## <a name="related-links"></a>相關連結

* [ProgressBar 示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)
