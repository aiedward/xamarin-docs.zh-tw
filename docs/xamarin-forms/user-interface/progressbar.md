---
title: Xamarin.Forms ProgressBar
description: Xamarin.Forms ProgressBar 是控制項，來以視覺方式表示進度的水平列填滿浮點數屬性為基礎。
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: 2e2917077575ebc78dbdda8ae55aa230a39a7ba1
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67837008"
---
# <a name="xamarinforms-progressbar"></a>Xamarin.Forms ProgressBar
[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ProgressBarDemos)

Xamarin.Forms [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar)是以視覺方式填滿百分比所表示的水平列表示進度控制項`float`值。 `ProgressBar`類別繼承自[ `View` ](xref:Xamarin.Forms.View)。

下列螢幕擷取畫面顯示`ProgressBar`iOS 和 Android 上：

![螢幕擷取畫面的 iOS 和 Android 上 ProgressBar](progressbar-images/progressbars-default.png "iOS 和 Android 上的進度列")

`ProgressBar`控制項會定義兩個屬性：

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) 是`float`呈現目前進度的值從 0 到 1 的值。 `Progress` 值小於 0 會限制為 0，大於 1 會限制為 1 的值。
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor) 是`Color`影響內部長條表示目前進度的色彩。

這些屬性都會受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示`ProgressBar`可自訂樣式和資料繫結的目標。

`ProgressBar`控制項也會定義`ProgressTo`以動畫顯示的列從其目前的值為指定值的方法。 如需詳細資訊，請參閱 < [ProgressBar 以動畫顯示](#animate-a-progressbar)。

> [!NOTE]
> `ProgressBar`不接受使用者操作，因此會略過使用 Tab 鍵，若要選取控制項時。

## <a name="create-a-progressbar"></a>建立進度列

A`ProgressBar`可以在 XAML 中具現化。 其`Progress`屬性可以設定來判斷內部、 彩色列的填滿百分比。 如果`Progress`屬性未設定，其預設值為 0。 下列範例示範如何具現化`ProgressBar`搭配可省略的 XAML 中`Progress`屬性集：

```xaml
<ProgressBar Progress="0.5" />
```

A`ProgressBar`也可以建立程式碼中：

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> 這類不使用不受限制的水平版面配置選項`Center`， `Start`，或`End`使用`ProgressBar`。 在 UWP 上`ProgressBar`會摺疊為零寬度的列。 保留預設值`HorizontalOptions`的值`Fill`而未使用的寬度`Auto`當放置`ProgressBar`在`Grid`版面配置。

## <a name="progressbar-appearance-properties"></a>ProgressBar 的外觀屬性

`ProgressColor`屬性可以設定為定義內部列色彩的時機`Progress`屬性大於零。 下列範例示範如何具現化`ProgressBar`中使用的 XAML`ProgressColor`屬性集：

```xaml
<ProgressBar OnColor="Orange" />
```

`ProgressColor`屬性也可以設定建立時`ProgressBar`在程式碼中：

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

下列螢幕擷取畫面示`ProgressBar`具有`ProgressColor`屬性設定為`Color.Orange`iOS 和 Android 上：

![已設定樣式的進度列，在 iOS 和 Android 的螢幕擷取畫面](progressbar-images/progressbars-styled.png "iOS 和 Android 上的 樣式的進度列")

## <a name="animate-a-progressbar"></a>以動畫顯示進度列

`ProgressTo`方法以動畫展示`ProgressBar`從其目前`Progress`值經過一段時間所提供的值。 方法會接受`float`進度值`uint`持續時間，以毫秒為單位，`Easing`列舉值，並傳回`Task<bool>`。 下列程式碼示範如何以動畫顯示`ProgressBar`:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

如需詳細資訊`Easing`列舉型別，請參閱 < [Easing 函式，在 Xamarin.Forms 中](~/xamarin-forms/user-interface/animation/easing.md)。

## <a name="related-links"></a>相關連結

* [ProgressBar 示範](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ProgressBarDemos)
