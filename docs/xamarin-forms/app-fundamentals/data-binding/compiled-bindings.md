---
title: Xamarin.Forms 編譯的繫結
description: 本文說明如何使用編譯的繫結來提升 Xamarin.Forms 應用程式中的資料繫結效能。
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2019
ms.openlocfilehash: 531d9719eb4bf5c23001ebe4260254e13f9989eb
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "72697149"
---
# <a name="xamarinforms-compiled-bindings"></a>Xamarin.Forms 編譯的繫結

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_編譯繫結的解析速度比傳統繫結更快，因此提升 Xamarin.Forms 應用程式中的資料繫結效能。_

資料繫結有兩個主要問題：

1. 無法在編譯時間驗證繫結運算式。 相反地，繫結是在執行階段進行解析。 因此，當應用程式不如預期般運作或出現錯誤訊息時，要到執行階段才會偵測到任何無效的繫結。
1. 不符合成本效益。 繫結使用一般用途物件檢查 (反射) 在執行階段進行解析，而執行此作業的成本會因平台而異。

編譯的繫結是在編譯時間而非執行階段解析繫結運算式，因此提升 Xamarin.Forms 應用程式中的資料繫結效能。 此外，在編譯時間驗證繫結運算式可改善開發人員的疑難排解體驗，因為無效的繫結會回報為建置錯誤。

編譯繫結的使用流程包括：

1. 啟用 XAML 編譯。 如需 XAML 編譯的詳細資訊，請參閱 [XAML 編譯](~/xamarin-forms/xaml/xamlc.md)。
1. 將`x:DataType`[`VisualElement`](xref:Xamarin.Forms.VisualElement)屬性設置為`VisualElement`及其子級將綁定的對象的類型。

> [!NOTE]
> 建議在`x:DataType`檢視層次結構[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)中的屬性與設置 時設置的屬性相同。 但是,可以在檢視層次結構中的任何位置重新定義此屬性。

要使用已編譯的綁定,`x:DataType`必須將屬性設置為字串文本或使用`x:Type`標記延伸名的類型。 在 XAML 編譯期間，任何無效的繫結運算式都會回報為建置錯誤。 不過，XAML 編譯器只會在第一次遇到無效的繫結運算式時回報建置錯誤。 將在或其`VisualElement`子級上定義的任何有效的綁定運算式都將編譯,而不管[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)是在 XAML 或代碼中設置的。 編譯繫結運算式會產生經過編譯的程式碼，該程式碼會從「來源」** 屬性取得一個值，並在標記中指定的「目標」** 屬性上進行設定。 此外，視繫結運算式而定，產生的程式碼可以在「來源」** 屬性值中觀察變更並重新整理「目標」** 屬性，也可以將變更從「目標」** 推送回到「來源」**。

> [!IMPORTANT]
> 當前,對於定義[`Source`](xref:Xamarin.Forms.Binding.Source)該屬性的任何綁定表達式,當前已禁用的綁定。 這是因為 `Source` 屬性一律使用 `x:Reference` 標記延伸設定，因此無法在編譯時間進行解析。

## <a name="use-compiled-bindings"></a>使用編譯的繫結

**已編譯顏色選擇器「** 頁示範」 使用 Xamarin.Forms 檢視與檢視模型屬性之間的編譯繫結:

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

根[`StackLayout`](xref:Xamarin.Forms.StackLayout)實體化`HslColorViewModel`屬性的屬性元素標`Color`[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)記 中的屬性並初始化。 此根`StackLayout``x:DataType`還將該屬性定義為視圖模型類型,指示將編譯`StackLayout`根 視圖層次結構中的任何綁定表達式。 可以通過更改任何綁定表達式綁定到不存在的視圖模型屬性來驗證這一點,這將導致生成錯誤。 雖然此示例將`x:DataType`屬性設置為字串文本,但它也可以設置為具有`x:Type`標記擴展名的類型。 關於標記延伸的詳細資訊,`x:Type`請參考[x:鍵入標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#type)。

> [!IMPORTANT]
> 您可以在檢視階層架構中的任何位置重新定義 `x:DataType` 屬性。

的元素[`BoxView`](xref:Xamarin.Forms.BoxView)[`Label`](xref:Xamarin.Forms.Label)與[`Slider`](xref:Xamarin.Forms.Slider)檢視從 繼承上下文[`StackLayout`](xref:Xamarin.Forms.StackLayout)。 這些檢視都是引用檢視模型中源屬性的綁定目標。 對於[`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color)屬性和屬性[`Label.Text`](xref:Xamarin.Forms.Label.Text), 資料`OneWay`綁定是 - 視圖中的屬性是從檢視模型中的屬性設置的。 但是,該[`Slider.Value`](xref:Xamarin.Forms.Slider.Value)屬性使用`TwoWay`綁定。 這允許從檢視`Slider`模型設置每個檢視模型,也允許從每個`Slider`設置視圖模型。

[`BoxView`](xref:Xamarin.Forms.BoxView)首次執行應用程式時,將根據實例化檢視[`Label`](xref:Xamarin.Forms.Label)模型時的初始[`Slider`](xref:Xamarin.Forms.Slider)`Color`屬性集從視圖模型設置 , 元素和元素都設置。 如下列螢幕擷取畫面所示：

[![已編譯顏色選擇器](compiled-bindings-images/compiledcolorselector-small.png "已編譯顏色選擇器")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "已編譯顏色選擇器")

當滑動時,[`BoxView`](xref:Xamarin.Forms.BoxView)和[`Label`](xref:Xamarin.Forms.Label)元素將相應地更新。

如需此色彩選取器的詳細資訊，請參閱 [ViewModel 和屬性變更通知](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications)。

## <a name="use-compiled-bindings-in-a-datatemplate"></a>在資料樣本中使用已編譯的結合

中的綁定[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)在要範本化的物件的上下文中解釋。 因此，在 `DataTemplate` 中使用編譯的繫結時，`DataTemplate` 必須使用 `x:DataType` 屬性來宣告其資料物件類型。

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

屬性[`ListView.ItemsSource`](xref:Xamarin.Forms.ListView)設置為靜態`NamedColor.All`屬性。 類`NamedColor`使用 .NET 反射[`Color`](xref:Xamarin.Forms.Color)來枚舉 結構中的所有靜態公共欄位,並將它們的名稱存儲在`All`可從靜態 屬性訪問的集合中。 因此，`ListView` 會填入所有的 `NamedColor` 執行個體。 針對 `ListView` 中的每個項目，項目的繫結內容會設定為 `NamedColor` 物件。 中的[`BoxView`](xref:Xamarin.Forms.BoxView)[`Label`](xref:Xamarin.Forms.Label)和[`ViewCell`](xref:Xamarin.Forms.ViewCell)元素繫結`NamedColor`成屬性。

請注意,將[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)`x:DataType`屬性定義`NamedColor`為 類型,指示將編譯視圖層次結構`DataTemplate`中的任何 綁定表達式。 若要進行驗證，請將任何繫結運算式變更為繫結至不存在的 `NamedColor` 屬性，這會導致建置錯誤。  雖然此示例將`x:DataType`屬性設置為字串文本,但它也可以設置為具有`x:Type`標記擴展名的類型。 關於標記延伸的詳細資訊,`x:Type`請參考[x:鍵入標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#type)。

首次執行應用程式時,[`ListView`](xref:Xamarin.Forms.ListView)將填`NamedColor`充 實體。 選擇的項目`ListView`時,該[`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color)屬性設定為`ListView`選取的項目的顏色:

[![已編譯顏色清單](compiled-bindings-images/compiledcolorlist-small.png "已編譯顏色清單*")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "已編譯顏色清單")

選擇其他[`ListView`](xref:Xamarin.Forms.BoxView)項目更新的顏色[`BoxView`](xref:Xamarin.Forms.BoxView)。

## <a name="combine-compiled-bindings-with-classic-bindings"></a>將編譯的綁定與經典綁定相結合

只有在已定義 `x:DataType` 屬性的檢視階層架構中才能編譯繫結運算式。 相反地，未定義 `x:DataType` 屬性之階層架構中的任何檢視則會使用傳統繫結。 因此，您可以將編譯的繫結與傳統繫結合併成一頁。 例如,在上一節中,使用編譯綁定中的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)視圖,[`BoxView`](xref:Xamarin.Forms.BoxView)而 設置為 中所選顏色[`ListView`](xref:Xamarin.Forms.ListView)的視圖 則不。

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

根[`StackLayout`](xref:Xamarin.Forms.StackLayout)`x:DataType`將 屬性`HslColorViewModel`集為 類型,指示將`StackLayout`編譯根 視圖層次結構中的任何綁定運算式。 不過，內部 `StackLayout` 會使用 `x:Null` 標記延伸將 `x:DataType` 屬性重新定義為 `null`。 因此，內部 `StackLayout` 中的繫結運算式會使用傳統繫結。 只有[`BoxView`](xref:Xamarin.Forms.BoxView)中的,`StackLayout`在根檢視層次結構中使用已編譯的綁定。

如需 `x:Null` 標記延伸的詳細資訊，請參閱 [x:Null 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#null)。

## <a name="performance"></a>效能

編譯的繫結可提升資料繫結效能，但效能優點各異。 單元測試顯示：

- 使用屬性變更通知之編譯繫結 (例如 `OneWay`、`OneWayToSource` 或 `TwoWay` 繫結) 的解析速度，大致上比傳統繫結快 8 倍。
- 不使用屬性變更通知之編譯繫結 (例如 `OneTime` 繫結) 的解析速度，大致上比傳統繫結快 20 倍。
- 在使用[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)屬性更改通知(`OneWay`即`OneWayToSource`,`TwoWay`或綁定) 的已編譯綁定上`BindingContext`設置 大約比 在經典綁定上設置 快 5 倍。
- 在不使用[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)屬性更改通知(`OneTime`即綁定)的已編譯綁`BindingContext`定 上設置 比在經典綁定上設置 快大約 7 倍。

這些效能差異在行動裝置上可能會更大，視所使用的平台、所使用的作業系統版本，以及執行應用程式的裝置而定。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))
