---
title: Xamarin. Forms ProgressBar
description: ProgressBar 是控制項, 以視覺化方式將進度表示為根據 float 屬性填滿的水準橫條。
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: a4cfc6c54eb2864707f328106761af029e0734cf
ms.sourcegitcommit: 9178e2e689f027212ea3e623b556b312985d79fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69658085"
---
# <a name="xamarinforms-progressbar"></a>Xamarin. Forms ProgressBar
[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

[Xamarin [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) ] 控制項會以水準橫條表示進度, 填滿以`float`值表示的百分比。 類別繼承自[`View`。](xref:Xamarin.Forms.View) `ProgressBar`

下列螢幕擷取畫面顯示 iOS `ProgressBar`和 Android 上的:

![IOS 和 Android 上的 ProgressBar 螢幕擷取畫面](progressbar-images/progressbars-default.png "IOS 和 Android 上的 ProgressBar")

`ProgressBar`控制項會定義兩個屬性:

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress)這是`float`一個值, 表示目前的進度, 做為0到1的值。 `Progress`小於0的值將會壓制為 0, 大於1的值將會壓制為1。
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor)`Color`是, 它會影響代表目前進度的內部橫條色彩。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援, 這表示`ProgressBar`可以設定樣式, 並作為資料系結的目標。

控制項也會`ProgressTo`定義方法, 以將其目前值中的橫條繪製為指定的值。 `ProgressBar` 如需詳細資訊, 請參閱[建立 ProgressBar 的動畫](#animate-a-progressbar)。

> [!NOTE]
> `ProgressBar`不接受使用者操作, 因此在使用 Tab 鍵選取控制項時, 會略過該動作。

## <a name="create-a-progressbar"></a>建立 ProgressBar

`ProgressBar`可以在 XAML 中具現化。 其`Progress`屬性會決定內部彩色橫條的填滿百分比。 預設`Progress`屬性值為0。 下列範例顯示如何`ProgressBar`在 XAML 中使用選擇性`Progress`屬性集來具現化:

```xaml
<ProgressBar Progress="0.5" />
```

也`ProgressBar`可以在程式碼中建立:

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> 請勿`Center`使用無限制的`Start` `End` 水準配置選項,`ProgressBar`例如、或。 在 UWP 上, `ProgressBar`會折迭為零寬度的橫條。 `ProgressBar`在配置中`HorizontalOptions` `Auto` `Fill` 放置時,請保留的預設值,並不要使用的寬度。`Grid`

## <a name="progressbar-appearance-properties"></a>ProgressBar 外觀屬性

當屬性大於零時, `ProgressColor`屬性會定義內部橫條色彩。 `Progress` 下列範例顯示如何`ProgressBar`在 XAML 中`ProgressColor`使用屬性集來具現化:

```xaml
<ProgressBar OnColor="Orange" />
```

`ProgressBar`在程式碼中建立時, `ProgressColor`也可以設定屬性:

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

下列螢幕擷取畫面顯示`ProgressBar` `ProgressColor`在 iOS 和 Android 上, `Color.Orange`將屬性設定為:

![IOS 和 Android 上的樣式 ProgressBar 螢幕擷取畫面](progressbar-images/progressbars-styled.png "IOS 和 Android 上的樣式 ProgressBar")

## <a name="animate-a-progressbar"></a>建立 ProgressBar 的動畫

方法會在一`ProgressBar`段時間後`Progress` , 從其目前的值將動畫複製到提供的值。 `ProgressTo` 方法會接受一個`float`進度值`uint` (以毫秒為單位), `Easing`並傳回一個`Task<bool>`列舉值。 下列程式碼示範如何建立`ProgressBar`動畫:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

如需列舉的`Easing`詳細資訊, 請參閱[在 Xamarin 中簡化函數](~/xamarin-forms/user-interface/animation/easing.md)。

## <a name="related-links"></a>相關連結

* [ProgressBar 示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)
