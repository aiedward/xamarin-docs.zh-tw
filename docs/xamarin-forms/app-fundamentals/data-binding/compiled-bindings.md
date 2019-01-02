---
title: Xamarin.Forms 編譯的繫結
description: 本文說明如何使用編譯的繫結來提升 Xamarin.Forms 應用程式中的資料繫結效能。
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2018
ms.openlocfilehash: a5273897539cdce4aeb0abde28a0912e8327284a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052176"
---
# <a name="xamarinforms-compiled-bindings"></a>Xamarin.Forms 編譯的繫結

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)

_編譯繫結的解析速度比傳統繫結更快，因此提升 Xamarin.Forms 應用程式中的資料繫結效能。_

資料繫結有兩個主要問題：

1. 無法在編譯時間驗證繫結運算式。 相反地，繫結是在執行階段進行解析。 因此，當應用程式不如預期般運作或出現錯誤訊息時，要到執行階段才會偵測到任何無效的繫結。
1. 不符合成本效益。 繫結使用一般用途物件檢查 (反射) 在執行階段進行解析，而執行此作業的成本會因平台而異。

編譯的繫結是在編譯時間而非執行階段解析繫結運算式，因此提升 Xamarin.Forms 應用程式中的資料繫結效能。 此外，在編譯時間驗證繫結運算式可改善開發人員的疑難排解體驗，因為無效的繫結會回報為建置錯誤。

編譯繫結的使用流程包括：

1. 啟用 XAML 編譯。 如需 XAML 編譯的詳細資訊，請參閱 [XAML 編譯](~/xamarin-forms/xaml/xamlc.md)。
1. 將 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 上的 `x:DataType` 屬性設定為 `VisualElement` 及其子系將要繫結的物件類型。 請注意，您可以在檢視階層架構中的任何位置重新定義此屬性。

> [!NOTE]
> 建議在檢視階層架構中與設定 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 相同的層級設定 `x:DataType` 屬性。

在 XAML 編譯期間，任何無效的繫結運算式都會回報為建置錯誤。 不過，XAML 編譯器只會在第一次遇到無效的繫結運算式時回報建置錯誤。 定義於 `VisualElement` 或其子系的任何有效繫結運算式都會經過編譯，無論 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 是在 XAML 或程式碼中設定。 編譯繫結運算式會產生經過編譯的程式碼，該程式碼會從「來源」屬性取得一個值，並在標記中指定的「目標」屬性上進行設定。 此外，視繫結運算式而定，產生的程式碼可以在「來源」屬性值中觀察變更並重新整理「目標」屬性，也可以將變更從「目標」推送回到「來源」。

> [!IMPORTANT]
> 定義 [`Source`](xref:Xamarin.Forms.Binding.Source) 屬性的任何繫結運算式目前已停用編譯的繫結。 這是因為 `Source` 屬性一律使用 `x:Reference` 標記延伸設定，因此無法在編譯時間進行解析。

## <a name="using-compiled-bindings"></a>使用編譯的繫結

[Compiled Color Selector] \(編譯的色彩選取器\) 頁面示範如何在 Xamarin.Forms 檢視與 ViewModel 屬性之間使用編譯的繫結：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorSelectorPage"
             Title="Compiled Color Selector">
    ...
    <StackLayout x:DataType="local:HslColorViewModel">
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>
        <BoxView Color="{Binding Color}"
                 ... />
        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />
            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>    
</ContentPage>
```

根 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 會在 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 屬性的屬性項目標記中具現化 `HslColorViewModel` 並初始化 `Color` 屬性。 這個根 `StackLayout` 也會將 `x:DataType` 屬性定義為 ViewModel 類型，表示根 `StackLayout` 檢視階層架構中的任何繫結運算式都會經過編譯。 若要進行驗證，請將任何繫結運算式變更為繫結至不存在的 ViewModel 屬性，這會導致建置錯誤。

> [!IMPORTANT]
> 您可以在檢視階層架構中的任何位置重新定義 `x:DataType` 屬性。

[`BoxView`](xref:Xamarin.Forms.BoxView)、[`Label`](xref:Xamarin.Forms.Label) 項目和 [`Slider`](xref:Xamarin.Forms.Slider) 檢視會繼承 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的繫結內容。 這些檢視都是參考 ViewModel 中來源屬性的繫結目標。 針對 [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) 屬性和 [`Label.Text`](xref:Xamarin.Forms.Label.Text) 屬性，資料繫結為 `OneWay` (檢視中的屬性會從 ViewModel 屬性來設定)。 不過，[`Slider.Value`](xref:Xamarin.Forms.Slider.Value) 屬性使用 `TwoWay` 繫結。 您可以從 ViewModel 設定每個 `Slider`，也可以從每個 `Slider` 設定 ViewModel。

第一次執行應用程式時，[`BoxView`](xref:Xamarin.Forms.BoxView)、[`Label`](xref:Xamarin.Forms.Label) 項目和 [`Slider`](xref:Xamarin.Forms.Slider) 項目都會從 ViewModel 來設定，並以 ViewModel 具現化時設定的初始 `Color` 屬性為依據。 如下列螢幕擷取畫面所示：

[![編譯的色彩選取器](compiled-bindings-images/compiledcolorselector-small.png "編譯的色彩選取器")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "編譯的色彩選取器")

當您操作滑桿時，[`BoxView`](xref:Xamarin.Forms.BoxView) 和 [`Label`](xref:Xamarin.Forms.Label) 項目會隨之更新。

如需此色彩選取器的詳細資訊，請參閱 [ViewModel 和屬性變更通知](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications)。

## <a name="using-compiled-bindings-in-a-datatemplate"></a>在 DataTemplate 中使用編譯的繫結

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 中的繫結是在所要樣板化的物件內容中進行解譯。 因此，在 `DataTemplate` 中使用編譯的繫結時，`DataTemplate` 必須使用 `x:DataType` 屬性來宣告其資料物件類型。

[Compiled Color List] \(編譯的色彩清單\) 頁面示範如何在 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 中使用編譯的繫結：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorListPage"
             Title="Compiled Color List">
    <Grid>
        ...
        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  ... >
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="local:NamedColor">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     ... />
                            <Label Text="{Binding FriendlyName}"
                                   ... />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
        <!-- The BoxView doesn't use compiled bindings -->
        <BoxView Color="{Binding Source={x:Reference colorListView}, Path=SelectedItem.Color}"
                 ... />
    </Grid>
</ContentPage>
```

[`ListView.ItemsSource`](xref:Xamarin.Forms.ListView) 屬性設定為靜態 `NamedColor.All` 屬性。 `NamedColor` 類別使用 .NET 反射來列舉 [`Color`](xref:Xamarin.Forms.Color) 結構中所有的靜態公用欄位，並使用其名稱將它們儲存在可供靜態 `All` 屬性存取的集合中。 因此，`ListView` 會填入所有的 `NamedColor` 執行個體。 針對 `ListView` 中的每個項目，項目的繫結內容會設定為 `NamedColor` 物件。 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 中的 [`BoxView`](xref:Xamarin.Forms.BoxView) 和 [`Label`](xref:Xamarin.Forms.Label) 項目會繫結至 `NamedColor` 屬性。

請注意，[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 會將 `x:DataType` 屬性定義為 `NamedColor` 類型，表示 `DataTemplate` 檢視階層架構中的任何繫結運算式都會經過編譯。 若要進行驗證，請將任何繫結運算式變更為繫結至不存在的 `NamedColor` 屬性，這會導致建置錯誤。

第一次執行應用程式時，[`ListView`](xref:Xamarin.Forms.ListView) 會填入 `NamedColor` 執行個體。 當選取 `ListView` 中的項目時，[`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) 屬性會設定為 `ListView` 中所選項目的色彩：

[![編譯的色彩清單](compiled-bindings-images/compiledcolorlist-small.png "編譯的色彩清單]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Compiled Color List")

選取 [`ListView`](xref:Xamarin.Forms.BoxView) 中其他項目會更新 [`BoxView`](xref:Xamarin.Forms.BoxView) 的色彩。

## <a name="combining-compiled-bindings-with-classic-bindings"></a>合併編譯的繫結與傳統繫結

只有在已定義 `x:DataType` 屬性的檢視階層架構中才能編譯繫結運算式。 相反地，未定義 `x:DataType` 屬性之階層架構中的任何檢視則會使用傳統繫結。 因此，您可以將編譯的繫結與傳統繫結合併成一頁。 例如，在上一節中，[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 中的檢視使用所編譯繫結，而設定為 [`ListView`](xref:Xamarin.Forms.ListView) 中所選色彩的 [`BoxView`](xref:Xamarin.Forms.BoxView) 則否。

因此，您可以謹慎建構 `x:DataType` 屬性，來產生同時使用編譯繫結和傳統繫結的頁面。 或者，您可以在檢視階層架構中的任何位置，使用 `x:Null` 標記延伸將 `x:DataType` 屬性重新定義為 `null`。 這樣做表示檢視階層架構中的任何繫結運算式都會使用傳統繫結。 [Mixed Bindings] \(混合繫結\) 頁面會示範此方法：

```xaml
<StackLayout x:DataType="local:HslColorViewModel">
    <StackLayout.BindingContext>
        <local:HslColorViewModel Color="Sienna" />
    </StackLayout.BindingContext>
    <BoxView Color="{Binding Color}"
             VerticalOptions="FillAndExpand" />
    <StackLayout x:DataType="{x:Null}"
                 Margin="10, 0">
        <Label Text="{Binding Name}" />
        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</StackLayout>   
```

根 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 會將 `x:DataType` 屬性設定為 `HslColorViewModel` 類型，表示根 `StackLayout` 檢視階層架構中的任何繫結運算式都會經過編譯。 不過，內部 `StackLayout` 會使用 `x:Null` 標記延伸將 `x:DataType` 屬性重新定義為 `null`。 因此，內部 `StackLayout` 中的繫結運算式會使用傳統繫結。 只有根 `StackLayout` 檢視階層架構中的 [`BoxView`](xref:Xamarin.Forms.BoxView) 會使用所編譯繫結。

如需 `x:Null` 標記延伸的詳細資訊，請參閱 [x:Null 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#null)。

## <a name="performance"></a>效能

編譯的繫結可提升資料繫結效能，但效能優點各異。 單元測試顯示：

- 使用屬性變更通知之編譯繫結 (例如 `OneWay`、`OneWayToSource` 或 `TwoWay` 繫結) 的解析速度，大致上比傳統繫結快 8 倍。
- 不使用屬性變更通知之編譯繫結 (例如 `OneTime` 繫結) 的解析速度，大致上比傳統繫結快 20 倍。
- 在使用屬性變更通知的編譯繫結 (例如 `OneWay`、`OneWayToSource` 或 `TwoWay` 繫結) 上設定 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)，大致上比在傳統繫結上設定 `BindingContext` 快 5 倍。
- 在不使用屬性變更通知的編譯繫結 (例如 `OneTime` 繫結) 上設定 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)，大致上比在傳統繫結上設定 `BindingContext` 快 7 倍。

這些效能差異在行動裝置上可能會更大，視所使用的平台、所使用的作業系統版本，以及執行應用程式的裝置而定。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) (資料繫結示範 (範例))
