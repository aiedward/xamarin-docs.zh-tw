---
title: Xamarin. 表單多重系結
description: 本文說明如何使用 MultiBinding 類別，將系結物件的集合附加至單一系結目標屬性。
ms.prod: xamarin
ms.assetid: E73AE622-664C-4A90-B5B2-BD47D0E7A1A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2020
ms.openlocfilehash: 0aafe01fcbde6cf1aacf3e2dd47444d4b77021e2
ms.sourcegitcommit: 79ba3deb031c8a60d0841bb3dbeaaf65daf2b224
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85846373"
---
# <a name="xamarinforms-multi-bindings"></a>Xamarin. 表單多重系結

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

多個系結可讓您將物件集合附加 [`Binding`](xref:Xamarin.Forms.Binding) 至單一系結目標屬性。 它們是使用類別所建立 `MultiBinding` ，其會評估其所有 `Binding` 物件，並透過 `IMultiValueConverter` 您的應用程式所提供的實例傳回單一值。 此外，在 `MultiBinding` 任何系結 `Binding` 的資料變更時，重新評估它的所有物件。

`MultiBinding`類別會定義下列屬性：

- `Bindings`，屬於類型 `IList<BindingBase>` ，表示 [`Binding`](xref:Xamarin.Forms.Binding) 實例內物件的集合 `MultiBinding` 。
- `Converter`，屬於類型 `IMultiValueConverter` ，表示要用來將來源值轉換為或從目標值轉換成的轉換器。
- `ConverterParameter`，屬於類型 `object` ，表示要傳遞至的選擇性參數 `Converter` 。

`Bindings`屬性是類別的 content 屬性 `MultiBinding` ，因此不需要從 XAML 明確設定。

此外，類別會 `MultiBinding` 從類別繼承下列屬性 `BindingBase` ：

- `FallbackValue`，屬於類型 `object` ，表示當多個系結無法傳回值時，所要使用的值。
- `Mode`，屬於類型 [`BindingMode`](xref:Xamarin.Forms.BindingMode) ，表示多重系結之資料流程的方向。
- `StringFormat`，屬於類型 `string` ，指定如何格式化多系結結果（如果它顯示為字串）。
- `TargetNullValue`，屬於類型 `object` ，其代表目標之前中所使用的值，而來源的值為 `null` 。

`MultiBinding`必須 `IMultiValueConverter` 根據集合中的系結值，使用來產生系結目標的值 `Bindings` 。 例如， [`Color`](xref:Xamarin.Forms.Color) 可能會從紅色、藍色和綠色值計算，這可能是來自相同或不同系結來源物件的值。 當某個值從目標移到來源時，目標屬性值會轉譯成一組傳回系結的值。

> [!IMPORTANT]
> 集合中的個別系結 `Bindings` 可以有自己的值轉換器。

屬性的值 `Mode` 會決定的功能 `MultiBinding` ，除非個別系結覆寫屬性，否則會用來做為集合中所有系結的系結模式。 例如，如果 `Mode` 物件上的屬性 `MultiBinding` 設定為 [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) ，則會考慮集合中的所有系結， `TwoWay` 除非您在其中一個系結上明確設定不同的 `Mode` 值。

## <a name="define-a-imultivalueconverter"></a>定義 System.windows.data.imultivalueconverter>

`IMultiValueConverter`介面可讓自訂邏輯套用至 `MultiBinding` 。 若要將轉換器與建立關聯 `MultiBinding` ，請建立一個可實作為介面的類別 `IMultiValueConverter` ，然後再執行 `Convert` 和 `ConvertBack` 方法：

```csharp
public class AllTrueMultiConverter : IMultiValueConverter
{
    public object Convert(object[] values, Type targetType, object parameter, CultureInfo culture)
    {
        if (values == null || !targetType.IsAssignableFrom(typeof(bool)))
        {
            return false;
            // Alternatively, return BindableProperty.UnsetValue to use the binding FallbackValue
        }

        foreach (var value in values)
        {
            if (!(value is bool b))
            {
                return false;
                // Alternatively, return BindableProperty.UnsetValue to use the binding FallbackValue
            }
            else if (!b)
            {
                return false;
            }
        }
        return true;
    }

    public object[] ConvertBack(object value, Type[] targetTypes, object parameter, CultureInfo culture)
    {
        if (!(value is bool b) || targetTypes.Any(t => !t.IsAssignableFrom(typeof(bool))))
        {
            // Return null to indicate conversion back is not possible
            return null;
        }

        if (b)
        {
            return targetTypes.Select(t => (object)true).ToArray();
        }
        else
        {
            // Can't convert back from false because of ambiguity
            return null;
        }
    }
}
```

`Convert`方法會將來源值轉換為系結目標的值。 當表單將值從來源系結傳播到系結目標時，會呼叫這個方法。 這個方法接受四個引數：

- `values`，屬於類型 `object[]` ，是中的來源系結所產生的值陣列 `MultiBinding` 。
- `targetType`型別為的，是系結 `Type` 目標屬性的型別。
- `parameter`，屬於類型 `object` ，這是要使用的轉換子參數。
- `culture`，屬於類型 `CultureInfo` ，這是要在轉換子中使用的文化特性。

`Convert`方法 `object` 會傳回代表已轉換值的。 這個方法應該會傳回：

- `BindableProperty.UnsetValue`表示轉換器未產生值，而且系結將會使用 `FallbackValue` 。
- `Binding.DoNothing`指示 Xamarin 不執行任何動作。 例如，指示 Xamarin 不會將值傳送至系結目標，或不會使用 `FallbackValue` 。
- `null`表示轉換器無法執行轉換，而且系結將會使用 `TargetNullValue` 。

> [!IMPORTANT]
> `MultiBinding` `BindableProperty.UnsetValue` 從方法接收的 `Convert` 必須定義其 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 屬性。 同樣地， `MultiBinding` 從方法接收的也 `null` `Convert` 必須定義其 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) propety。

方法會將系結 `ConvertBack` 目標轉換成來源系結值。 這個方法接受四個引數：

- `value`，屬於類型 `object` ，是系結目標所產生的值。
- `targetTypes`，屬於類型 `Type[]` ，是要轉換成的類型陣列。 陣列長度表示方法要傳回之建議值的數目和類型。
- `parameter`，屬於類型 `object` ，這是要使用的轉換子參數。
- `culture`，屬於類型 `CultureInfo` ，這是要在轉換子中使用的文化特性。

`ConvertBack`方法 `object[]` 會傳回已從目標值轉換回來源值之類型的值陣列。 這個方法應該會傳回：

- `BindableProperty.UnsetValue`在位置， `i` 指出轉換器無法在索引上提供來源系結的值 `i` ，而且不會在其上設定任何值。
- `Binding.DoNothing`在位置 `i` ，表示不會在索引的來源系結上設定任何值 `i` 。
- `null`表示轉換器無法執行轉換，或是不支援以這個方向進行轉換。

## <a name="consume-a-imultivalueconverter"></a>使用 System.windows.data.imultivalueconverter>

`IMultiValueConverter`會藉由在資源字典中將其具現化來使用，然後使用 `StaticResource` 標記延伸來參考它來設定 `MultiBinding.Converter` 屬性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.MultiBindingConverterPage"
             Title="MultiBinding Converter demo">

    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />
        <local:InverterConverter x:Key="InverterConverter" />
    </ContentPage.Resources>

    <CheckBox>
        <CheckBox.IsChecked>
            <MultiBinding Converter="{StaticResource AllTrueConverter}">
                <Binding Path="Employee.IsOver16" />
                <Binding Path="Employee.HasPassedTest" />
                <Binding Path="Employee.IsSuspended"
                         Converter="{StaticResource InverterConverter}" />
            </MultiBinding>
        </CheckBox.IsChecked>
    </CheckBox>
</ContentPage>    
```

在此範例中， `MultiBinding` 物件會使用 `AllTrueMultiConverter` 實例來將 [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) 屬性設定為 `true` ，前提是這三個 [`Binding`](xref:Xamarin.Forms.Binding) 物件會評估為 `true` 。 否則， `CheckBox.IsChecked` 屬性會設定為 `false` 。

根據預設，屬性會使用系結 [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) 。 因此， `ConvertBack` `AllTrueMultiConverter` 當使用者取消核取時，會執行實例的方法 [`CheckBox`](xref:Xamarin.Forms.CheckBox) ，將來源系結值設定為屬性的值 `CheckBox.IsChecked` 。

## <a name="format-strings"></a>格式字串

`MultiBinding`可以使用屬性，將顯示為字串的任何多系結結果格式化 `StringFormat` 。 這個屬性可以設定為標準的 .NET 格式字串，並加上預留位置，以指定如何格式化多重系結結果：

```xaml
<Label>
    <Label.Text>
        <MultiBinding StringFormat="{}{0} {1} {2}">
            <Binding Path="Employee1.Forename" />
            <Binding Path="Employee1.MiddleName" />
            <Binding Path="Employee1.Surname" />
        </MultiBinding>
    </Label.Text>
</Label>
```

在此範例中， `StringFormat` 屬性會將三個系結的值結合成所顯示的單一字串 [`Label`](xref:Xamarin.Forms.Label) 。

> [!IMPORTANT]
> 複合字串格式的參數數目不能超過中的子 `Binding` 物件數目 `MultiBinding` 。

設定 `Converter` 和屬性時 `StringFormat` ，會先將轉換器套用至資料值，然後套用 `StringFormat` 。

如需有關在 Xamarin 中設定字串格式的詳細資訊，請參閱[xamarin. 表單字串格式](string-formatting.md)。

## <a name="provide-fallback-values"></a>提供 fallback 值

當系結程式失敗時，藉由定義要使用的回溯值，可以讓資料系結更穩固。 您可以選擇性地 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 在物件上定義和屬性來完成這 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 項作業 `MultiBinding` 。

`MultiBinding` [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 當實例的方法傳回時，將會使用它 `Convert` `IMultiValueConverter` `BindableProperty.UnsetValue` ，這表示轉換器未產生值。 `MultiBinding` [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 當實例的方法傳回時，將會使用它 `Convert` `IMultiValueConverter` `null` ，這表示轉換器無法執行轉換。

如需系結回退的詳細資訊，請參閱[Xamarin。表單](binding-fallbacks.md)系結回退。

## <a name="nest-multibinding-objects"></a>MultiBinding 物件的嵌套

`MultiBinding`物件可以進行嵌套，以便評估多個 `MultiBinding` 物件，以透過實例傳回值 `IMultiValueConverter` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.NestedMultiBindingPage"
             Title="Nested MultiBinding demo">

    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />
        <local:AnyTrueMultiConverter x:Key="AnyTrueConverter" />
        <local:InverterConverter x:Key="InverterConverter" />
    </ContentPage.Resources>

    <CheckBox>
        <CheckBox.IsChecked>
            <MultiBinding Converter="{StaticResource AnyTrueConverter}">
                <MultiBinding Converter="{StaticResource AllTrueConverter}">
                    <Binding Path="Employee.IsOver16" />
                    <Binding Path="Employee.HasPassedTest" />
                    <Binding Path="Employee.IsSuspended" Converter="{StaticResource InverterConverter}" />                        
                </MultiBinding>
                <Binding Path="Employee.IsMonarch" />
            </MultiBinding>
        </CheckBox.IsChecked>
    </CheckBox>
</ContentPage>
```

在此範例中， `MultiBinding` `AnyTrueMultiConverter` [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) `true` 如果 [`Binding`](xref:Xamarin.Forms.Binding) 內建物件中的所有物件都 `MultiBinding` 評估為 `true` ，或假設 `Binding` 外部物件中的物件評估 `MultiBinding` 為， `true` 則物件會使用其實例將屬性設定為。 否則， `CheckBox.IsChecked` 屬性會設定為 `false` 。

## <a name="use-a-relativesource-binding-in-a-multibinding"></a>在 MultiBinding 中使用 RelativeSource 系結

`MultiBinding`物件支援相對系結，讓您能夠設定相對於系結目標位置的系結來源：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:DataBindingDemos">
    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />

        <ControlTemplate x:Key="CardViewExpanderControlTemplate">
            <Expander BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                      IsExpanded="{Binding IsExpanded, Source={RelativeSource TemplatedParent}}"
                      BackgroundColor="{Binding CardColor}">
                <Expander.IsVisible>
                    <MultiBinding Converter="{StaticResource AllTrueConverter}">
                        <Binding Path="IsExpanded" />
                        <Binding Path="IsEnabled" />
                    </MultiBinding>
                </Expander.IsVisible>
                <Expander.Header>
                    <Grid>
                        <!-- XAML that defines Expander header goes here -->
                    </Grid>
                </Expander.Header>
                <Grid>
                    <!-- XAML that defines Expander content goes here -->
                </Grid>
            </Expander>
        </ControlTemplate>
    </ContentPage.Resources>

    <StackLayout>
        <controls:CardViewExpander BorderColor="DarkGray"
                                   CardTitle="John Doe"
                                   CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                                   IconBackgroundColor="SlateGray"
                                   IconImageSource="user.png"
                                   ControlTemplate="{StaticResource CardViewExpanderControlTemplate}"
                                   IsEnabled="True"
                                   IsExpanded="True" />
    </StackLayout>
</ContentPage>
```

在此範例中， `TemplatedParent` 會使用相對系結模式，將控制項範本內的系結至套用範本的執行時間物件實例。 `Expander`是的根項目， [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 其已 `BindingContext` 設定為套用範本的執行時間物件實例。 因此，及其 `Expander` 子系會針對物件的屬性，解析其系結運算式和 [`Binding`](xref:Xamarin.Forms.Binding) 物件 `CardViewExpander` 。 `MultiBinding` `AllTrueMultiConverter` 會使用實例來將屬性設定 `Expander.IsVisible` 為， `true` 前提是兩個 [`Binding`](xref:Xamarin.Forms.Binding) 物件會評估為 `true` 。 否則， `Expander.IsVisible` 屬性會設定為 `false` 。

如需相對繫結的詳細資訊，請參閱 [Xamarin.Forms 相對繫結](relative-bindings.md)。 如需控制項範本的詳細資訊，請參閱[Xamarin 控制項範本](~/xamarin-forms/app-fundamentals/templates/control-template.md)。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))
- [Xamarin.Forms 字串格式化](string-formatting.md)
- [Xamarin.Forms 繫結後援](binding-fallbacks.md)
- [Xamarin.Forms 相對繫結](relative-bindings.md)
- [Xamarin. Forms 控制項範本](~/xamarin-forms/app-fundamentals/templates/control-template.md)