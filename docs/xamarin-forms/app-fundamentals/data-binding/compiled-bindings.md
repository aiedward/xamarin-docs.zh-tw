---
title: Xamarin.Forms 編譯繫結
description: 這篇文章說明如何使用已編譯的繫結來改善在 Xamarin.Forms 應用程式中的資料繫結效能。
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2018
ms.openlocfilehash: 0b350082c834076a1d69427644259087d64bf26a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111525"
---
# <a name="xamarinforms-compiled-bindings"></a>Xamarin.Forms 編譯繫結

_已編譯的繫結會比傳統的繫結，因此可改善在 Xamarin.Forms 應用程式中的資料繫結效能更快速地解決。_

資料繫結有兩個主要的問題：

1. 沒有任何編譯時期驗證繫結運算式。 相反地，繫結是在執行階段解析。 因此，任何無效的繫結偵測到執行階段時，應用程式不行為如預期般運作，或是出現錯誤訊息。
1. 它們不符合成本效益。 使用一般用途的物件檢查 (reflection)，在執行階段解決繫結，而且執行此動作的額外負荷而異的平台之間。

已編譯的繫結解析在編譯時期，而不是執行階段的繫結運算式，藉以改善在 Xamarin.Forms 應用程式中的資料繫結效能。 此外，繫結運算式的編譯時期驗證可讓更好的開發人員，疑難排解體驗，因為無效的繫結會報告為建置錯誤。

使用已編譯的繫結的程序是：

1. 啟用 XAML 編譯。 如需 XAML 編譯的詳細資訊，請參閱[XAML 編譯](~/xamarin-forms/xaml/xamlc.md)。
1. 設定`x:DataType`屬性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)物件的型別，`VisualElement`和其子系會繫結至。 請注意，可以檢視階層中的任何位置重新定義這個屬性。

> [!NOTE]
> 若要設定建議`x:DataType`做為檢視階層中相同層級的屬性[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)設定。

在 XAML 編譯時期，為建置錯誤，將會報告任何無效的繫結運算式。 不過，XAML 編譯器只會報告遇到的第一個無效的繫結運算式的建置錯誤。 未定義任何有效的繫結運算式`VisualElement`或其子系會編譯，而不論是否[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) XAML 或程式碼中設定。 編譯的繫結運算式會產生編譯的程式碼會取得從屬性的值上*來源*，並將它的屬性上設定上*目標*在標記中指定。 此外，根據繫結運算式，產生的程式碼可能會注意到的值中的變更*來源*屬性並重新整理*目標*屬性，可能會將變更推送從*目標*回到*來源*。

> [!IMPORTANT]
> 已編譯的繫結目前已停用任何定義的繫結運算式[ `Source` ](xref:Xamarin.Forms.Binding.Source)屬性。 這是因為`Source`屬性永遠會設定使用`x:Reference`標記延伸，在編譯時期無法解析。

## <a name="using-compiled-bindings"></a>使用已編譯的繫結

**編譯色彩選取器**頁面示範如何使用 Xamarin.Forms 檢視和 ViewModel 屬性之間的編譯繫結：

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

根目錄[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)具現化`HslColorViewModel`並初始化`Color`內的屬性項目標記屬性[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)屬性。 這個根`StackLayout`也會定義`x:DataType`屬性做為 ViewModel 類型，表示根目錄中的任何繫結運算式`StackLayout`將編譯的檢視階層。 這可以經過變更的任何繫結運算式繫結至不存在的 ViewModel 屬性，這會導致建置錯誤。

> [!IMPORTANT]
> `x:DataType`屬性可在任何時間點檢視階層中重新定義。

[ `BoxView` ](xref:Xamarin.Forms.BoxView)， [ `Label` ](xref:Xamarin.Forms.Label)項目，以及[ `Slider` ](xref:Xamarin.Forms.Slider)檢視繼承繫結內容從[ `StackLayout`](xref:Xamarin.Forms.StackLayout). 這些檢視會參考在 ViewModel 的來源屬性的所有繫結目標。 針對[ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color)屬性，和[ `Label.Text` ](xref:Xamarin.Forms.Label.Text)屬性中的資料繫結是`OneWay`– 檢視中的屬性會設定從 ViewModel 的屬性。 不過， [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)屬性使用`TwoWay`繫結。 這可讓每個`Slider`從 ViewModel，以及從每個設定 ViewModel 設定`Slider`。

第一次執行應用程式時， [ `BoxView` ](xref:Xamarin.Forms.BoxView)， [ `Label` ](xref:Xamarin.Forms.Label)項目，以及[ `Slider` ](xref:Xamarin.Forms.Slider)項目都會從根據 ViewModel 設定初始`Color`ViewModel 具現化時設定的屬性。 這是由下列的螢幕擷取畫面所示：

[![編譯色彩選取器](compiled-bindings-images/compiledcolorselector-small.png "編譯色彩選取器")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "編譯色彩選取器")

滑桿進行操作，因為[ `BoxView` ](xref:Xamarin.Forms.BoxView)並[ `Label` ](xref:Xamarin.Forms.Label)元素會隨之更新。

如需有關這個色彩選取器的詳細資訊，請參閱 < [Viewmodel 和屬性變更通知](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications)。

## <a name="using-compiled-bindings-in-a-datatemplate"></a>使用 DataTemplate 中已編譯的繫結

中的繫結[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)正在樣板化的物件內容中解譯。 因此，當使用編譯中的繫結`DataTemplate`，則`DataTemplate`必須宣告其資料物件使用的型別`x:DataType`屬性。

**編譯的色彩清單**頁面會示範使用中的已編譯繫結[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

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

[ `ListView.ItemsSource` ](xref:Xamarin.Forms.ListView)屬性設定為靜態`NamedColor.All`屬性。 `NamedColor`類別會使用.NET 反映來列舉中的所有靜態公用欄位[ `Color` ](xref:Xamarin.Forms.Color)結構，並將其儲存以名稱存取靜態集合中`All`屬性。 因此，`ListView`填滿所有`NamedColor`執行個體。 中的每個項目的`ListView`，項目的繫結內容設定為`NamedColor`物件。 [ `BoxView` ](xref:Xamarin.Forms.BoxView)並[ `Label` ](xref:Xamarin.Forms.Label)中的項目[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)繫結至`NamedColor`屬性。

請注意， [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)定義`x:DataType`屬性設為`NamedColor`型別，指出的任何繫結中的運算式`DataTemplate`將編譯的檢視階層。 這可由繫結至不存在的繫結運算式的任何變更驗證`NamedColor`屬性，這會導致建置錯誤。

當應用程式第一次執行時， [ `ListView` ](xref:Xamarin.Forms.ListView)填入`NamedColor`執行個體。 中的項目`ListView`已選取[ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color)屬性設定為選取的項目中的色彩`ListView`:

[![編譯色彩清單](compiled-bindings-images/compiledcolorlist-small.png "編譯色彩清單]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Compiled Color List")

選取其他項目[ `ListView` ](xref:Xamarin.Forms.BoxView)更新的色彩[ `BoxView` ](xref:Xamarin.Forms.BoxView)。

## <a name="combining-compiled-bindings-with-classic-bindings"></a>結合編譯使用傳統的繫結的繫結

繫結運算式只會編譯檢視階層，`x:DataType`在定義屬性。 相反地，任何檢視階層中的所在`x:DataType`未定義屬性將會使用傳統的繫結。 因此，它是可以結合已編譯的繫結和傳統的繫結在頁面上。 例如，在上一個區段內的檢視[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)使用已編譯的繫結，而[ `BoxView` ](xref:Xamarin.Forms.BoxView)中選取的色彩設定[ `ListView`](xref:Xamarin.Forms.ListView)則否。

謹慎建構的`x:DataType`屬性可能因此會導致使用已編譯和傳統的繫結的頁面。 或者，`x:DataType`屬性可重新定義的檢視階層，以隨時`null`使用`x:Null`標記延伸。 執行此動作會指示檢視階層內的任何繫結運算式，將會使用傳統的繫結。 *混合的繫結*頁面會示範這種方法：

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

根目錄[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)設定`x:DataType`屬性設為`HslColorViewModel`型別，指出的任何繫結根目錄中的運算式`StackLayout`將編譯的檢視階層。 不過，內部`StackLayout`會重新定義`x:DataType`屬性設定為`null`使用`x:Null`標記運算式。 因此，繫結運算式內內部`StackLayout`使用傳統的繫結。 只有[ `BoxView` ](xref:Xamarin.Forms.BoxView)，在根`StackLayout`檢視階層，編譯會使用繫結。

如需詳細資訊`x:Null`標記運算式，請參閱 < [X:null 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#null)。

## <a name="performance"></a>效能

已編譯的繫結來改善資料繫結效能優點不同的效能。 單元測試顯示︰

- 編譯的繫結，會使用屬性變更通知 (亦即`OneWay`， `OneWayToSource`，或`TwoWay`繫結) 會解析大約 8 倍的更快速，比傳統的繫結。
- 編譯的繫結，不會使用屬性變更通知 (也就是`OneTime`繫結) 會解析大約 20 倍的更快速，比傳統的繫結。
- 設定[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的已編譯的繫結，會使用屬性變更通知 (亦即`OneWay`， `OneWayToSource`，或`TwoWay`繫結) 大約 5 倍快於設定`BindingContext`傳統的繫結上。
- 設定[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的已編譯的繫結，不會使用屬性變更通知 (亦即`OneTime`繫結) 是大約 7 次不用設定`BindingContext`傳統的繫結上。

這些效能差異可以放大行動裝置，取決於所使用的平台上所使用的作業系統和應用程式執行所在裝置的版本。

## <a name="related-links"></a>相關連結

- [資料繫結示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
