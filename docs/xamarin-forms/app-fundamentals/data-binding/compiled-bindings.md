---
title: Xamarin.Forms編譯的系結
description: 本文說明如何使用編譯的系結來改善應用程式中的資料系結效能 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 094691796fed9653f2a2e468ccb1c33d1a408a49
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84571554"
---
# <a name="xamarinforms-compiled-bindings"></a>Xamarin.Forms編譯的系結

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_編譯的系結比傳統系結更快速解析，因此可改善應用程式中的資料系結效能 Xamarin.Forms 。_

資料繫結有兩個主要問題：

1. 無法在編譯時間驗證繫結運算式。 相反地，繫結是在執行階段進行解析。 因此，當應用程式不如預期般運作或出現錯誤訊息時，要到執行階段才會偵測到任何無效的繫結。
1. 不符合成本效益。 繫結使用一般用途物件檢查 (反射) 在執行階段進行解析，而執行此作業的成本會因平台而異。

編譯的系結會 Xamarin.Forms 在編譯時期（而非執行時間）解析系結運算式，以改善應用程式中的資料系結效能 此外，在編譯時間驗證繫結運算式可改善開發人員的疑難排解體驗，因為無效的繫結會回報為建置錯誤。

編譯繫結的使用流程包括：

1. 啟用 XAML 編譯。 如需 XAML 編譯的詳細資訊，請參閱 [XAML 編譯](~/xamarin-forms/xaml/xamlc.md)。
1. 將 `x:DataType` 上的屬性設定 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 為及其子系將系結之物件的型別 `VisualElement` 。

> [!NOTE]
> 建議您在 `x:DataType` 設定時，在視圖階層中的相同層級上設定屬性 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 不過，您可以在視圖階層中的任何位置重新定義這個屬性。

若要使用編譯的系結， `x:DataType` 必須將屬性設定為字串常值，或使用標記延伸的型別 `x:Type` 。 在 XAML 編譯期間，任何無效的繫結運算式都會回報為建置錯誤。 不過，XAML 編譯器只會在第一次遇到無效的繫結運算式時回報建置錯誤。 `VisualElement`不論 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 是在 XAML 或程式碼中設定，都將會編譯在或其子系上定義的任何有效系結運算式。 編譯繫結運算式會產生經過編譯的程式碼，該程式碼會從「來源」** 屬性取得一個值，並在標記中指定的「目標」** 屬性上進行設定。 此外，視繫結運算式而定，產生的程式碼可以在「來源」** 屬性值中觀察變更並重新整理「目標」** 屬性，也可以將變更從「目標」** 推送回到「來源」**。

> [!IMPORTANT]
> 針對定義屬性的任何系結運算式，目前已停用編譯的系結 [`Source`](xref:Xamarin.Forms.Binding.Source) 。 這是因為 `Source` 屬性一律使用 `x:Reference` 標記延伸設定，因此無法在編譯時間進行解析。

## <a name="use-compiled-bindings"></a>使用編譯的繫結

**已編譯的色彩選取器**頁面會示範如何在 Xamarin.Forms views 和 viewmodel 屬性之間使用已編譯的系結：

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

根會具現化，並初始化屬性的屬性專案 [`StackLayout`](xref:Xamarin.Forms.StackLayout) `HslColorViewModel` `Color` 標記中的屬性 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 這個根目錄 `StackLayout` 也會將 `x:DataType` 屬性定義為 viewmodel 類型，表示將會編譯根視圖階層架構中的任何系結運算式 `StackLayout` 。 這可以藉由將任何系結運算式變更為系結至不存在的 viewmodel 屬性來進行驗證，這會導致建立錯誤。 雖然這個範例會將 `x:DataType` 屬性設定為字串常值，但也可以設定為具有 `x:Type` 標記延伸的類型。 如需標記延伸的詳細資訊 `x:Type` ，請參閱[X:Type 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension)。

> [!IMPORTANT]
> 您可以在檢視階層架構中的任何位置重新定義 `x:DataType` 屬性。

[`BoxView`](xref:Xamarin.Forms.BoxView)、 [`Label`](xref:Xamarin.Forms.Label) 元素和 views 會 [`Slider`](xref:Xamarin.Forms.Slider) 從繼承系結內容 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 這些 views 全都是參考 viewmodel 中來源屬性的系結目標。 對於 [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) 屬性和 [`Label.Text`](xref:Xamarin.Forms.Label.Text) 屬性而言，資料系結是 `OneWay` -視圖中的屬性是從 viewmodel 中的屬性設定。 不過，屬性會使用系結 [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) `TwoWay` 。 這可讓您 `Slider` 從 viewmodel 設定每個，也可從每個 viewmodel 進行設定 `Slider` 。

第一次執行應用程式時， [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) [`Slider`](xref:Xamarin.Forms.Slider) 會根據 viewmodel 具現化時設定的初始屬性，從 viewmodel 設定、元素和專案 `Color` 。 如下列螢幕擷取畫面所示：

[![編譯的色彩選取器](compiled-bindings-images/compiledcolorselector-small.png "編譯的色彩選取器")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "編譯的色彩選取器")

操作滑杆時， [`BoxView`](xref:Xamarin.Forms.BoxView) 和專案 [`Label`](xref:Xamarin.Forms.Label) 會隨之更新。

如需此色彩選取器的詳細資訊，請參閱 [ViewModel 和屬性變更通知](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications)。

## <a name="use-compiled-bindings-in-a-datatemplate"></a>在 DataTemplate 中使用編譯的系結

中的系結 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 會在要樣板化的物件內容中解讀。 因此，在 `DataTemplate` 中使用編譯的繫結時，`DataTemplate` 必須使用 `x:DataType` 屬性來宣告其資料物件類型。

[Compiled Color List] \(編譯的色彩清單\)**** 頁面示範如何在 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 中使用編譯的繫結：

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

[`ListView.ItemsSource`](xref:Xamarin.Forms.ListView)屬性會設定為靜態 `NamedColor.All` 屬性。 `NamedColor`類別會使用 .net 反映來列舉結構中所有的靜態公用欄位 [`Color`](xref:Xamarin.Forms.Color) ，並將其名稱儲存在可從靜態屬性存取的集合中 `All` 。 因此，`ListView` 會填入所有的 `NamedColor` 執行個體。 針對 `ListView` 中的每個項目，項目的繫結內容會設定為 `NamedColor` 物件。 [`BoxView`](xref:Xamarin.Forms.BoxView)中的和 [`Label`](xref:Xamarin.Forms.Label) 元素會系結 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 至 `NamedColor` 屬性。

請注意，會將 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) `x:DataType` 屬性定義為 `NamedColor` 類型，表示將會編譯 view 階層架構中的任何系結運算式 `DataTemplate` 。 若要進行驗證，請將任何繫結運算式變更為繫結至不存在的 `NamedColor` 屬性，這會導致建置錯誤。  雖然這個範例會將 `x:DataType` 屬性設定為字串常值，但也可以設定為具有 `x:Type` 標記延伸的類型。 如需標記延伸的詳細資訊 `x:Type` ，請參閱[X:Type 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension)。

第一次執行應用程式時， [`ListView`](xref:Xamarin.Forms.ListView) 會填入 `NamedColor` 實例。 當選取中的專案時 `ListView` ， [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) 屬性會設定為中所選項目的色彩 `ListView` ：

[![已編譯的色彩清單](compiled-bindings-images/compiledcolorlist-small.png "已編譯的色彩清單]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "已編譯的色彩清單")

選取中的其他專案會 [`ListView`](xref:Xamarin.Forms.BoxView) 更新的色彩 [`BoxView`](xref:Xamarin.Forms.BoxView) 。

## <a name="combine-compiled-bindings-with-classic-bindings"></a>結合編譯的系結與傳統系結

只有在已定義 `x:DataType` 屬性的檢視階層架構中才能編譯繫結運算式。 相反地，未定義 `x:DataType` 屬性之階層架構中的任何檢視則會使用傳統繫結。 因此，您可以將編譯的繫結與傳統繫結合併成一頁。 例如，在上一節中，中的 views [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 會使用已編譯的系結，而 [`BoxView`](xref:Xamarin.Forms.BoxView) 設定為中所選取之色彩的則不 [`ListView`](xref:Xamarin.Forms.ListView) 會。

因此，您可以謹慎建構 `x:DataType` 屬性，來產生同時使用編譯繫結和傳統繫結的頁面。 或者，您可以在檢視階層架構中的任何位置，使用 `x:Null` 標記延伸將 `x:DataType` 屬性重新定義為 `null`。 這樣做表示檢視階層架構中的任何繫結運算式都會使用傳統繫結。 [Mixed Bindings] \(混合繫結\)** 頁面會示範此方法：

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

根會將 [`StackLayout`](xref:Xamarin.Forms.StackLayout) `x:DataType` 屬性設定為 `HslColorViewModel` 類型，表示將會編譯根視圖階層架構中的任何系結運算式 `StackLayout` 。 不過，內部 `StackLayout` 會使用 `x:Null` 標記延伸將 `x:DataType` 屬性重新定義為 `null`。 因此，內部 `StackLayout` 中的繫結運算式會使用傳統繫結。 只有在 [`BoxView`](xref:Xamarin.Forms.BoxView) 根視圖階層中的才會 `StackLayout` 使用編譯的系結。

如需 `x:Null` 標記延伸的詳細資訊，請參閱 [x:Null 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#xnull-markup-extension)。

## <a name="performance"></a>效能

編譯的繫結可提升資料繫結效能，但效能優點各異。 單元測試顯示：

- 使用屬性變更通知之編譯繫結 (例如 `OneWay`、`OneWayToSource` 或 `TwoWay` 繫結) 的解析速度，大致上比傳統繫結快 8 倍。
- 不使用屬性變更通知之編譯繫結 (例如 `OneTime` 繫結) 的解析速度，大致上比傳統繫結快 20 倍。
- [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)在使用屬性變更通知的編譯系結（也就是、或系結）上設定， `OneWay` `OneWayToSource` `TwoWay` 大約比在傳統系結上設定來得快5倍 `BindingContext` 。
- [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)在未使用屬性變更通知的編譯系結（也就是系結）上設定， `OneTime` 大約比在傳統系結上設定來得快7倍 `BindingContext` 。

這些效能差異在行動裝置上可能會更大，視所使用的平台、所使用的作業系統版本，以及執行應用程式的裝置而定。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))
