---
title: 中的版面配置選項 Xamarin.Forms
description: 每個 Xamarin.Forms 視圖都有 HorizontalOptions 和 VerticalOptions 屬性，類型為 LayoutOptions。 本文說明每個 LayoutOptions 值對視圖的對齊和展開有何影響。
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dc85f93110d2faec69ae42823a121b6d16fdfe7f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367773"
---
# <a name="layout-options-in-no-locxamarinforms"></a>中的版面配置選項 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)

_每個 Xamarin.Forms 視圖都有 HorizontalOptions 和 VerticalOptions 屬性，類型為 LayoutOptions。本文說明每個 LayoutOptions 值對視圖的對齊和展開有何影響。_

## <a name="overview"></a>概觀

[`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)結構會封裝兩個版面配置喜好設定：

- **對齊** ：視圖的慣用對齊方式，決定其在其父配置內的位置和大小。
- **展開** –僅供使用 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，並指出視圖是否應該使用額外的空間（如果有的話）。

這些配置喜好設定可以套用至 [`View`](xref:Xamarin.Forms.View) 相對於其父系的，方法是將 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 的或 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設定 `View` 為結構中的其中一個公用欄位 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 。 公用欄位如下所示：

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`Start`、、 `Center` `End` 和 `Fill` 欄位是用來定義父配置內的視圖對齊：

- 針對水準對齊，將放置在父配置的 [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) [`View`](xref:Xamarin.Forms.View) 左側，而針對垂直對齊，則會將放置在父配置的 `View` 上方。
- 針對水準和垂直對齊， [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) 水準或垂直對齊 [`View`](xref:Xamarin.Forms.View) 。
- 若為水準對齊，請將放置在父配置的 [`End`](xref:Xamarin.Forms.LayoutOptions.End) [`View`](xref:Xamarin.Forms.View) 右邊，而針對垂直對齊，則會將放置在 `View` 父配置的底部。
- 針對水準對齊， [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) 可確保 [`View`](xref:Xamarin.Forms.View) 填滿父配置的寬度，而針對垂直對齊，則可確保 `View` 填滿父配置的高度。

`StartAndExpand`、、 `CenterAndExpand` `EndAndExpand` 和 `FillAndExpand` 值是用來定義對齊喜好設定，以及如果在父系中有提供時，視圖是否會佔用更多空間 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。

> [!NOTE]
> 檢視的 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性預設值為 [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)。

## <a name="alignment"></a>對應項目

當父配置包含未使用的空間時，對齊方式會控制視圖在其父配置內的位置 (也就是說，父配置大於其所有子系) 的合併大小。

[`StackLayout`](xref:Xamarin.Forms.StackLayout)只會在與 `Start` `Center` `End` `Fill` [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 方向相反方向的子視圖上，使用、、和欄位 `StackLayout` 。 因此，垂直方向內的子視圖 `StackLayout` 可以將其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性設定為 `Start` 、 `Center` 、 `End` 或欄位的其中一個 `Fill` 。 同樣地，水準方向內的子視圖 `StackLayout` 可以將其 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設定為 `Start` 、 `Center` 、 `End` 或欄位 `Fill` 的其中一個。

在與 [`StackLayout`](xref:Xamarin.Forms.StackLayout) `Start` `Center` `End` `Fill` [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 方向相同方向的子視圖上 `StackLayout` ，不會遵循、、和欄位。 因此， `StackLayout` `Start` 如果在 `Center` `End` `Fill` 子視圖的屬性上設定、、或欄位，垂直導向會忽略該 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性。 同樣地， `StackLayout` `Start` `Center` `End` `Fill` 如果是在子視圖的屬性上設定，則會忽略、、或欄位 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 。

> [!NOTE]
> [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) 通常會覆寫使用  [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 和屬性指定 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 的大小要求。

下列 XAML 程式碼範例示範垂直方向 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，每個子系 [`Label`](xref:Xamarin.Forms.Label) 將其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性設定為結構中四個對齊欄位的其中一個 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) ：

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

程式碼會產生下列螢幕擷取畫面所示的版面配置：

[![對齊版面配置選項](layout-options-images/alignment.png)](layout-options-images/alignment-large.png#lightbox "對齊版面配置選項")

## <a name="expansion"></a>擴充

展開可控制某個視圖在中是否會佔用更多的空間（如果有的話） [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 如果 `StackLayout` 包含未使用的空間 (也就是 `StackLayout` 大於其所有子系的合併大小) ，則所有子視圖都會平均共用未使用的空間，而要求展開的所有子視圖都將其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 或 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設定為 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 使用 `AndExpand` 尾碼的欄位。 請注意，當使用中的所有空間時 `StackLayout` ，展開選項沒有任何作用。

[`StackLayout`](xref:Xamarin.Forms.StackLayout) 只會讓子檢視往其方向延展。 因此， `StackLayout` [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) `StartAndExpand` `CenterAndExpand` `EndAndExpand` `FillAndExpand` 如果 `StackLayout` 包含未使用的空間，垂直方向可以展開子視圖，將其屬性設定為其中一個、、或欄位。 同樣地， `StackLayout` [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) `StartAndExpand` `CenterAndExpand` `EndAndExpand` `FillAndExpand` 如果 `StackLayout` 包含未使用的空間，水準方向可以展開子視圖，將其屬性設定為其中一個、、或欄位。

[`StackLayout`](xref:Xamarin.Forms.StackLayout)無法以相對於方向的方向來展開子視圖。 因此，在垂直方向上 `StackLayout` ，將子視圖上的屬性設定為，與 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) 將屬性設為的效果相同 [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) 。

> [!NOTE]
> 請注意，啟用展開並不會變更視圖的大小，除非它使用 [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) 。

下列 XAML 程式碼範例示範垂直方向 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，其中每個子系 [`Label`](xref:Xamarin.Forms.Label) 將其 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設定為結構中四個擴充欄位的其中一個 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) ：

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

程式碼會產生下列螢幕擷取畫面所示的版面配置：

[![展開配置選項](layout-options-images/expansion.png)](layout-options-images/expansion-large.png#lightbox "展開配置選項")

每個都 [`Label`](xref:Xamarin.Forms.Label) 佔用中的相同空間量 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 不過，最後的 `Label` 會將其 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設為 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)，因此只有其大小不同。 此外，每個 `Label` 都是以小紅色分隔 [`BoxView`](xref:Xamarin.Forms.BoxView) ，讓您能夠 `Label` 輕鬆地查看佔用的空間。

## <a name="summary"></a>總結

本文說明每個 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 結構值對視圖的對齊和展開（相對於其父系）有何影響。 `Start`、、 `Center` `End` 和 `Fill` 欄位是用來定義父配置內的視圖對齊，而 `StartAndExpand` 、 `CenterAndExpand` 、 `EndAndExpand` 和 `FillAndExpand` 欄位則用來定義對齊喜好設定，以及判斷視圖是否會在中佔用更多的空間（如果有的話） [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。

## <a name="related-links"></a>相關連結

- [LayoutOptions (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)
- [LayoutOptions](xref:Xamarin.Forms.LayoutOptions)