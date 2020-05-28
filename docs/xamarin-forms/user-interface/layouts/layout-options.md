---
title: 中的版面配置選項Xamarin.Forms
description: 每個 Xamarin.Forms view 都有 LayoutOptions 類型的 HorizontalOptions 和 VerticalOptions 屬性。 本文說明每個 LayoutOptions 值對視圖對齊和展開的效果。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 17f4e76f9bef71352cabddfba9397e95bcdd24d3
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138018"
---
# <a name="layout-options-in-xamarinforms"></a>中的版面配置選項Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)

_每個 Xamarin.Forms view 都有 LayoutOptions 類型的 HorizontalOptions 和 VerticalOptions 屬性。本文說明每個 LayoutOptions 值對視圖對齊和展開的效果。_

## <a name="overview"></a>概觀

[`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)結構會封裝兩個版面配置喜好設定：

- **對齊**–視圖的慣用對齊方式，決定其在其父配置內的位置和大小。
- **擴充**–僅供使用 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，並指出此視圖是否應該使用額外的空間（如果有的話）。

這些版面配置喜好設定可以套用至 [`View`](xref:Xamarin.Forms.View) 相對於其父系的，方法是將的 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 或 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設 `View` 為結構中的其中一個公用欄位 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 。 公用欄位如下所示：

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`Start`、 `Center` 、 `End` 和 `Fill` 欄位是用來定義視圖在父配置中的對齊方式：

- 針對水準對齊， [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) 會將 [`View`](xref:Xamarin.Forms.View) 放在父配置的左邊，而對於垂直對齊，則會將放在父配置的 `View` 頂端。
- 針對水準和垂直對齊， [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) 水準或垂直中央 [`View`](xref:Xamarin.Forms.View) 。
- 針對水準對齊， [`End`](xref:Xamarin.Forms.LayoutOptions.End) 會將放在父配置的 [`View`](xref:Xamarin.Forms.View) 右手邊，而對於垂直對齊，則會將放在 `View` 父配置的底部。
- 針對水準對齊， [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) 可確保 [`View`](xref:Xamarin.Forms.View) 填滿父配置的寬度，並在垂直對齊時確保 `View` 填滿父配置的高度。

`StartAndExpand`、 `CenterAndExpand` 、 `EndAndExpand` 和 `FillAndExpand` 值是用來定義對齊喜好設定，以及如果父系內有提供更多的空間（如果有的話） [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。

> [!NOTE]
> 檢視的 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性預設值為 [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)。

<a name="alignment" />

## <a name="alignment"></a>對齊

對齊控制當父配置包含未使用的空間（亦即，父配置大於其所有子系的合併大小）時，該視圖在其父系版面配置內的位置。

[`StackLayout`](xref:Xamarin.Forms.StackLayout)只 `Start` `Center` 會遵循 `End` `Fill` [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 子視圖上相對於方向 `StackLayout` 方向的、、和欄位。 因此，垂直方向內的子視圖 `StackLayout` 可以將其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性設為其中一個 `Start` 、 `Center` 、 `End` 或 `Fill` 欄位。 同樣地，水準方向內的子視圖 `StackLayout` 可以將其 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設為其中一個 `Start` 、 `Center` 、 `End` 或 `Fill` 欄位。

不 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 遵守 `Start` 子視圖的、 `Center` 、 `End` 和欄位，其 `Fill` [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 方向與 `StackLayout` 方向相同。 因此，垂直方向 `StackLayout` `Start` 會忽略、 `Center` 、 `End` 或欄位， `Fill` 如果它們是在子視圖的屬性上設定的話 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。 同樣地，如果將 `StackLayout` `Start` 、 `Center` 、 `End` 或 `Fill` 欄位設定在子視圖的屬性上，則會將其忽略 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 。

> [!NOTE]
> [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)通常會覆寫使用和屬性指定的大小要求 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 。

下列 XAML 程式碼範例示範垂直方向 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，其中每個子系會 [`Label`](xref:Xamarin.Forms.Label) 將其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性設定為結構中四個對齊欄位的其中一個 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) ：

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

對等的 c # 程式碼如下所示：

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
    new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
    new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
    new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
  }
};
```

程式碼會產生下列螢幕擷取畫面中所示的版面配置：

[![](layout-options-images/alignment.png "Alignment Layout Options")](layout-options-images/alignment-large.png#lightbox "Alignment Layout Options")

<a name="expansion" />

## <a name="expansion"></a>擴充

展開可控制在中，視圖是否會佔用更多的空間（如果有的話） [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 如果 `StackLayout` 包含未使用的空間（亦即，大於 `StackLayout` 其所有子系的合併大小），則所有要求展開的子視圖都會以同樣的方式來共用未使用的空間，方法是將其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 或屬性設定 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 為 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 使用尾碼的欄位 `AndExpand` 。 請注意，當使用中的所有空間時 `StackLayout` ，展開選項不會有任何作用。

[`StackLayout`](xref:Xamarin.Forms.StackLayout) 只會讓子檢視往其方向延展。 因此， `StackLayout` [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) `StartAndExpand` `CenterAndExpand` `EndAndExpand` `FillAndExpand` 如果 `StackLayout` 包含未使用的空間，垂直方向可以展開將其屬性設定為其中一個、、或欄位的子視圖。 同樣地， `StackLayout` [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) `StartAndExpand` `CenterAndExpand` `EndAndExpand` `FillAndExpand` 如果 `StackLayout` 包含未使用的空間，水準導向也可以展開將其屬性設定為其中一個、、或欄位的子視圖。

A [`StackLayout`](xref:Xamarin.Forms.StackLayout) 無法以相對於其方向的方向展開子視圖。 因此，在垂直方向上 `StackLayout` ，將 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 子視圖上的屬性設定為 [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) 具有與將屬性設定為相同的效果 [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) 。

> [!NOTE]
> 請注意，啟用展開並不會變更視圖的大小，除非它使用 [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) 。

下列 XAML 程式碼範例會示範垂直方向 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，其中每個子系會 [`Label`](xref:Xamarin.Forms.Label) 將其 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設為結構中四個擴充欄位的其中一個 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) ：

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Start" BackgroundColor="Gray" VerticalOptions="StartAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Center" BackgroundColor="Gray" VerticalOptions="CenterAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="End" BackgroundColor="Gray" VerticalOptions="EndAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Fill" BackgroundColor="Gray" VerticalOptions="FillAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
</StackLayout>
```

對等的 c # 程式碼如下所示：

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
  }
};
```

程式碼會產生下列螢幕擷取畫面中所示的版面配置：

[![](layout-options-images/expansion.png "Expansion Layout Options")](layout-options-images/expansion-large.png#lightbox "Expansion Layout Options")

每個都 [`Label`](xref:Xamarin.Forms.Label) 佔用中相同的空間量 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 不過，最後的 `Label` 會將其 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設為 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)，因此只有其大小不同。 此外，每個 `Label` 都以小型紅色分隔 [`BoxView`](xref:Xamarin.Forms.BoxView) ，可讓您 `Label` 輕鬆地查看所佔用的空間。

## <a name="summary"></a>摘要

本文說明每個 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 結構值對相對於其父項的對齊和擴充的效果。 `Start`、 `Center` 、 `End` 和 `Fill` 欄位是用來定義父配置內的視圖對齊，而 `StartAndExpand` 、 `CenterAndExpand` 、 `EndAndExpand` 和 `FillAndExpand` 欄位則是用來定義對齊喜好設定，以及判斷視圖是否會在中佔用更多的空間（如果有的話） [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。

## <a name="related-links"></a>相關連結

- [LayoutOptions （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)
- [LayoutOptions](xref:Xamarin.Forms.LayoutOptions)
