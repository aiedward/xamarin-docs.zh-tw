---
title: Xamarin.Forms 基本系結
description: 本文說明如何使用資料系 Xamarin.Forms 結，這會連結兩個物件之間的一組屬性，其中至少一個通常是使用者介面物件。 這兩個物件稱為目標和來源。
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: video
ms.openlocfilehash: 107916edee01171b8ff5d4871de3b1243c385dd5
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366421"
---
# <a name="no-locxamarinforms-basic-bindings"></a>Xamarin.Forms 基本系結

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/databindingdemos)

Xamarin.Forms資料系結會連結兩個物件之間的一組屬性，至少其中一個物件通常是使用者介面物件。 這兩個物件稱為「目標」和「來源」：

- 「目標」是資料繫結設定所在的物件 (和屬性)。
- 「來源」是資料繫結參考的物件 (和屬性)。

這項區別有時可能有點令人困惑：在最簡單的情況下，資料會從來源流向目標，這表示目標屬性值會從來源屬性值設定。 不過，在某些情況下，資料可能會從目標流向來源，或是雙向流動。 為了避免混淆，請記住，目標一律是資料繫結設定所在的物件，即使它是提供資料而不是接收資料。

## <a name="bindings-with-a-binding-context"></a>具有繫結內容的繫結

雖然資料繫結通常會完全以 XAML 指定，但在程式碼中看到資料繫結也很有意義。 [基本程式碼繫結] 頁面包含具有 `Label` 和 `Slider` 的 XAML 檔案：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicCodeBindingPage"
             Title="Basic Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="48"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Slider` 設定為 0 到 360 的範圍。 此程式的目的是藉由操作 `Slider` 而旋轉 `Label`。

如果沒有資料繫結，您會將 `Slider` 的 `ValueChanged` 事件設定為事件處理常式，以存取 `Slider` 的 `Value` 屬性，並將該值設定為 `Label` 的 `Rotation` 屬性。 資料繫結會自動執行該作業；已不再需要事件處理常式和其內的程式碼。

您可以在衍生自之任何類別的實例上設定系結 [`BindableObject`](xref:Xamarin.Forms.BindableObject) ，其中包括 `Element` 、 `VisualElement` 、 `View` 和 `View` 衍生。  繫結一律會在目標物件上設定。 繫結會參考來源物件。 若要設定資料繫結，請使用目標類別的下列兩個成員：

- [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)屬性會指定來源物件。
- [ `SetBinding` ] (x： Xamarin.Forms 。BindableObject. SetBinding (Xamarin.Forms 。BindableProperty， Xamarin.Forms 。System.windows.data.bindingbase.delay) # A3 方法會指定目標屬性和來源屬性。

在此範例中，`Label` 是繫結目標，而 `Slider` 是繫結來源。 `Slider` 來源中的變更會影響 `Label` 目標的旋轉。 資料會從來源流向目標。

`SetBinding`由定義的方法 `BindableObject` 具有 [`BindingBase`](xref:Xamarin.Forms.BindingBase) 類別所衍生類型的引數 [`Binding`](xref:Xamarin.Forms.Binding) ，但 `SetBinding` 類別會定義其他方法 [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions) 。 **基本程式碼繫結** 範例中的程式碼後置檔案，使用來自此類別的較簡單 [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) 延伸模組方法。

```csharp
public partial class BasicCodeBindingPage : ContentPage
{
    public BasicCodeBindingPage()
    {
        InitializeComponent();

        label.BindingContext = slider;
        label.SetBinding(Label.RotationProperty, "Value");
    }
}
```

`Label` 物件是繫結目標，因此它是這個屬性設定所在的物件，也是對其呼叫方法的物件。 `BindingContext` 屬性指出繫結來源，也就是 `Slider`。

`SetBinding` 方法在繫結目標上呼叫，但它同時指定目標屬性和來源屬性。 目標屬性指定為 `BindableProperty` 物件：`Label.RotationProperty`。 來源屬性指定為字串，並指出 `Slider` 的 `Value` 屬性。

`SetBinding` 方法顯示其中一個最重要的資料繫結規則：

目標屬性都必須受到可繫結屬性的支援。

此規則暗示，目標物件必須是衍生自 `BindableObject` 的類別執行個體。 請參閱 [**可繫結屬性**](~/xamarin-forms/xaml/bindable-properties.md)一文，以了解可繫結物件和可繫結屬性的概觀。

指定為字串的來源屬性沒有這類規則。 就內部而言，會使用反映來存取實際的屬性。 不過在這個特定案例中，`Value` 屬性也受到可繫結屬性支援。

程式碼可稍微簡化：`RotationProperty` 可繫結屬性由 `VisualElement` 定義，且由 `Label` 和 `ContentPage` 繼承，因此在 `SetBinding` 呼叫中不需要類別名稱：

```csharp
label.SetBinding(RotationProperty, "Value");
```

不過，包含類別名稱對於目標物件是很好的提醒。

在您操作 `Slider` 時，`Label` 會跟著旋轉：

[![基本程式碼系結](basic-bindings-images/basiccodebinding-small.png "基本程式碼系結")](basic-bindings-images/basiccodebinding-large.png#lightbox "基本程式碼系結")

[基本 XAML 繫結] 頁面與 [基本程式碼繫結] 相同，不同之處在於它會在 XAML 中定義整個資料繫結：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicXamlBindingPage"
             Title="Basic XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

如同程式碼，資料繫結是在目標物件上設定，也就是 `Label`。 牽涉到兩個 XAML 標記延伸。 這些可藉由大括弧分隔符號立即辨識出來：

- 需要 `x:Reference` 標記延伸才能參考來源物件，也就是名為 `slider`的 `Slider`。
- `Binding` 標記延伸會將 `Label` 的 `Rotation` 屬性連結至 `Slider` 的 `Value` 屬性。

如需 XAML 標記延伸的詳細資訊，請參閱 [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。 `x:Reference`類別支援標記延伸 [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) ; `Binding` [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) 類別支援。 當 XML 命名空間前置詞指出時， `x:Reference` 是 XAML 2009 規格的一部分，而 `Binding` 是的一部分 Xamarin.Forms 。 請注意，在大括弧內沒有出現引號。

在設定 `BindingContext` 時，很容易就會忘記 `x:Reference` 標記延伸。 通常會錯誤地將屬性直接設定為如下的繫結來源名稱：

```xaml
BindingContext="slider"
```

但那是不對的。 該標記會將 `BindingContext` 屬性設定為 `string` 物件，其字元拼起來是 "slider"！

請注意，source 屬性是使用的屬性來指定的 [`Path`](xref:Xamarin.Forms.Xaml.BindingExtension.Path) `BindingExtension` ，它會對應至 [`Path`](xref:Xamarin.Forms.Binding.Path) 類別的屬性 [`Binding`](xref:Xamarin.Forms.Binding) 。

[基本 XAML 繫結] 頁面上所顯示的標記可以簡化：XAML 標記延伸，例如 `x:Reference` 和 `Binding` 可以定義「內容屬性」屬性，這對於 XAML 標記延伸表示屬性名稱不需要出現。 `Name` 屬性是 `x:Reference` 的內容屬性，而 `Path` 屬性是 `Binding` 的內容屬性，這表示它們可以從運算式排除：

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>沒有繫結內容的繫結

`BindingContext` 屬性是資料繫結的重要元件，但是它不一定必要。 來源物件可以改為指定在 `SetBinding` 呼叫或 `Binding` 標記延伸中。

這示範於 **替代程式碼繫結** 範例中。 XAML 檔案類似於 **繫結基本程式碼** 範例，不同之處在於定義了 `Slider` 來控制 `Label` 的 `Scale` 屬性。 因此，`Slider` 設定為 &ndash;2 到 2 的範圍：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeCodeBindingPage"
             Title="Alternative Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

程式碼後端檔案會使用 [ `SetBinding` ] (x：來設定系結 Xamarin.Forms 。BindableObject. SetBinding (Xamarin.Forms 。BindableProperty， Xamarin.Forms 。System.windows.data.bindingbase.delay) # A3 方法定義 `BindableObject` 。 引數是 [函式] (x： Xamarin.Forms 。Binding .% 23ctor (System.string， Xamarin.Forms 。BindingMode， Xamarin.Forms 。類別的 IValueConverter、System.object、System.string、System.object) # A3 [`Binding`](xref:Xamarin.Forms.Binding) ：

```csharp
public partial class AlternativeCodeBindingPage : ContentPage
{
    public AlternativeCodeBindingPage()
    {
        InitializeComponent();

        label.SetBinding(Label.ScaleProperty, new Binding("Value", source: slider));
    }
}
```

`Binding` 建構函式有 6 個參數，因此會使用具名引數來指定 `source` 參數。 引數是 `slider` 物件。

執行此程式可能會有點令人驚訝：

[![替代程式碼系結](basic-bindings-images/alternativecodebinding-small.png "替代程式碼系結")](basic-bindings-images/alternativecodebinding-large.png#lightbox "替代程式碼系結")

左側的 iOS 畫面會顯示頁面第一次出現時的螢幕外觀。 `Label` 在哪裡？

問題在於 `Slider` 初始值為 0。 這會導致 `Label` 的 `Scale` 屬性也設定為 0，覆寫其預設值 1。 這導致一開始看不到 `Label`。 如 Android 螢幕擷取畫面所示，您可以操作 `Slider` 來讓 `Label` 再次出現，但其初始消失是令人不安。

您將在[下一篇文章](binding-mode.md)中發現如何藉由從 `Scale` 屬性的預設值初始化 `Slider` 來避免這個問題。

> [!NOTE]
> [`VisualElement`](xref:Xamarin.Forms.VisualElement)類別也會定義 [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) 和 [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) 屬性，以 `VisualElement` 水準和垂直方向來調整不同的大小。

[替代 XAML 繫結] 頁面會顯示完全使用 XAML 的相等繫結：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeXamlBindingPage"
             Title="Alternative XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="{Binding Source={x:Reference slider},
                               Path=Value}" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

現在 `Binding` 標記延伸已設定兩個屬性，分別是 `Source` 和 `Path`，並以逗號分隔。 如果您偏好，它們可以出現在同一行：

```xaml
Scale="{Binding Source={x:Reference slider}, Path=Value}" />
```

`Source` 屬性設定為內嵌的 `x:Reference` 標記延伸，這在其他方面具有與設定 `BindingContext` 相同的語法。 請注意，大括弧中未出現引號，而且必須以逗號分隔兩個屬性。

`Binding` 標記延伸的內容屬性是 `Path`，但只有在它是運算式中的第一個屬性時，才能去除標記延伸的 `Path=` 部分。 若要去除 `Path=` 部分，您需要交換兩個屬性：

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

雖然 XAML 標記延伸通常以大括弧分隔，它們也可以用物件項目來表示：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Source="{x:Reference slider}"
                 Path="Value" />
    </Label.Scale>
</Label>
```

現在 `Source` 和 `Path` 屬性是一般的 XAML 屬性：值出現在引號內，且屬性不以逗號分隔。 `x:Reference` 標記延伸也可以成為物件項目：

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Path="Value">
            <Binding.Source>
                <x:Reference Name="slider" />
            </Binding.Source>
        </Binding>
    </Label.Scale>
</Label>
```

此語法不常見，但有時候涉及複雜物件時會需要。

到目前為止所顯示的範例，會將 `BindingContext` 屬性和 `Binding` 的 `Source` 屬性設定為 `x:Reference` 標記延伸，以參考頁面上的另一個檢視。 這兩個屬性都屬於類型 `Object`，並可以設定為任何物件，物件內包含適合繫結來源的屬性。

在接下來的文章中，您將發現可以將 `BindingContext` 或 `Source` 屬性設定為 `x:Static` 標記延伸，以便參考靜態屬性或欄位的值；或是設定為 `StaticResource` 標記延伸，以便參考儲存在資源字典中的物件，或是直接設定為物件，這個物件通常 (但不一定) 是 ViewModel 的執行個體。

`BindingContext` 屬性也可以設定為 `Binding` 物件，以便 `Binding` 的 `Source` 和 `Path` 屬性能定義繫結內容。

## <a name="binding-context-inheritance"></a>繫結內容繼承

在本文中，您已了解，您可以使用 `BindingContext` 屬性或 `Binding` 物件的 `Source` 屬性來指定來源物件。 如果同時設定，`Binding` 的 `Source` 屬性會優先於 `BindingContext`。

`BindingContext` 屬性有個極重要的特性：

`BindingContext` 屬性的設定會透過視覺化樹狀結構繼承。

如您所見，這對於簡化繫結運算式可能非常好用，且在某些情況下 &mdash; 特別是在 Model View ViewModel (MVVM) 案例 &mdash; 很重要。

**繫結內容繼承** 範例是繫結內容繼承的簡單示範：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BindingContextInheritancePage"
             Title="BindingContext Inheritance">
    <StackLayout Padding="10">

        <StackLayout VerticalOptions="FillAndExpand"
                     BindingContext="{x:Reference slider}">

            <Label Text="TEXT"
                   FontSize="80"
                   HorizontalOptions="Center"
                   VerticalOptions="EndAndExpand"
                   Rotation="{Binding Value}" />

            <BoxView Color="#800000FF"
                     WidthRequest="180"
                     HeightRequest="40"
                     HorizontalOptions="Center"
                     VerticalOptions="StartAndExpand"
                     Rotation="{Binding Value}" />
        </StackLayout>

        <Slider x:Name="slider"
                Maximum="360" />

    </StackLayout>
</ContentPage>
```

`StackLayout` 的 `BindingContext` 屬性設定為 `slider` 物件。 這個繫結內容同時由 `Label` 和 `BoxView` 繼承，這兩者的 `Rotation` 屬性都設定為 `Slider` 的 `Value` 屬性：

[![繫結內容繼承](basic-bindings-images/bindingcontextinheritance-small.png "繫結內容繼承")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "繫結內容繼承")

在[下一篇文章](binding-mode.md)中，您會看到「繫結模式」如何變更目標和來源物件之間的資料流程。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))
- [書籍中的資料系結章節 Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Data-Binding/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]