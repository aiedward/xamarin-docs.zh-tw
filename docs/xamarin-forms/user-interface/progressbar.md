---
title: Xamarin.Forms ProgressBar
description: Xamarin.FormsProgressBar 是一個控制項，以視覺方式將進度表示為根據 float 屬性填滿的水準橫條。
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 111b3ad13902b272256464f6f70f0db3dfd015b6
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559450"
---
# <a name="no-locxamarinforms-progressbar"></a>Xamarin.Forms ProgressBar
[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

Xamarin.Forms [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) 控制項以視覺化方式將進度表示為水準橫條，填滿至以值表示的百分比 `float` 。 `ProgressBar`類別繼承自 [`View`](xref:Xamarin.Forms.View) 。

下列螢幕擷取畫面顯示 `ProgressBar` 在 iOS 和 Android 上的：

![ProgressBar 在 iOS 和 Android 上的螢幕擷取畫面](progressbar-images/progressbars-default.png "IOS 和 Android 上的 ProgressBar")

`ProgressBar`控制項會定義兩個屬性：

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) 這是一個 `float` 值，表示目前的進度，以0到1的值表示。 `Progress` 小於0的值會壓制為0，大於1的值將會壓制為1。
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor) 是 `Color` ，它會影響代表目前進度的內部橫條色彩。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示 `ProgressBar` 可以設定樣式，也可以是資料系結的目標。

此 `ProgressBar` 控制項也會定義一種 `ProgressTo` 方法，以從其目前值到指定值的動畫。 如需詳細資訊，請參閱 [建立 ProgressBar 的動畫](#animate-a-progressbar)。

> [!NOTE]
> `ProgressBar`使用 Tab 鍵選取控制項時，不接受使用者操作，因此會略過使用者操作。

## <a name="create-a-progressbar"></a>建立 ProgressBar

`ProgressBar`可以在 XAML 中具現化。 其 `Progress` 屬性會決定內部、彩色橫條的填滿百分比。 預設 `Progress` 屬性值為0。 下列範例示範如何 `ProgressBar` 在 XAML 中具現化具有選擇性 `Progress` 屬性集的 a：

```xaml
<ProgressBar Progress="0.5" />
```

您 `ProgressBar` 也可以在程式碼中建立：

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> 請勿使用不受限制的水準配置選項 `Center` ，例如、 `Start` 或 `End` `ProgressBar` 。 在 UWP 上，會折迭 `ProgressBar` 為零寬度的橫條。 將預設 `HorizontalOptions` 值保留為 `Fill` ，而且不使用在配置時使用的寬度 `Auto` `ProgressBar` `Grid` 。

## <a name="progressbar-appearance-properties"></a>ProgressBar 外觀屬性

`ProgressColor`當屬性大於零時，屬性會定義內部橫條色彩 `Progress` 。 下列範例顯示如何 `ProgressBar` 在 XAML 中具現化具有 `ProgressColor` 屬性集的 a：

```xaml
<ProgressBar ProgressColor="Orange" />
```

`ProgressColor`在程式碼中建立時，也可以設定此屬性 `ProgressBar` ：

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

下列螢幕擷取畫面顯示在 `ProgressBar` `ProgressColor` `Color.Orange` iOS 和 Android 上，將屬性設定為的：

![IOS 和 Android 上樣式 ProgressBar 的螢幕擷取畫面](progressbar-images/progressbars-styled.png "IOS 和 Android 上的樣式 ProgressBar")

## <a name="animate-a-progressbar"></a>建立 ProgressBar 的動畫

`ProgressTo`方法會 `ProgressBar` 從其目前的 `Progress` 值到一段時間內提供的值進行動畫處理。 方法會接受 `float` 進度值、 `uint` 以毫秒為單位的持續時間、 `Easing` 列舉值，然後傳回 `Task<bool>` 。 下列程式碼示範如何建立動畫 `ProgressBar` ：

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

如需列舉的詳細資訊 `Easing` ，請參閱[中 Xamarin.Forms ](~/xamarin-forms/user-interface/animation/easing.md)的「簡化函式」。

## <a name="related-links"></a>相關連結

* [ProgressBar 示範](/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)