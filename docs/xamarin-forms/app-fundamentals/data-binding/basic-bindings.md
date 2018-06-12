---
title: Xamarin.Forms 基本繫結
description: 本文說明如何使用 Xamarin.Forms 資料繫結，這樣會連結兩個物件之間屬性的一組至少一個通常是使用者介面物件。 這兩個物件稱為 「 目標 」 和 「 來源 」。
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: f932b7dfbcccb8f1c6ccb726f5e48c2df6e93c6c
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241685"
---
# <a name="xamarinforms-basic-bindings"></a>Xamarin.Forms 基本繫結

Xamarin.Forms 資料繫結連結一組兩個物件，其中至少一個通常是使用者介面物件之間的屬性。 這兩個物件稱為*目標*和*來源*:

- *目標*位於物件 （屬性） 上設定資料繫結。
- *來源*是資料繫結所參考的物件 （和屬性）。

這個區別有時可能有點混淆： 在最簡單的情況下，資料會從來源傳送到目標，表示目標屬性的值會設定從來源屬性的值。 不過，在某些情況下，可以或者資料流程從目標到來源，或是兩個方向。 為了避免混淆，請記住，目標永遠是即使它是提供資料，而非接收資料，資料繫結會設定物件。

## <a name="bindings-with-a-binding-context"></a>繫結的繫結內容

資料繫結通常會指定可在 XAML 中，雖然它是相當有用的作法，若要查看在程式碼中的資料繫結項目。 **繫結的基本程式碼**頁面包含的 XAML 檔案`Label`和`Slider`:

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

`Slider`設定 0 到 360 的範圍。 這個程式的目的是要旋轉`Label`操縱`Slider`。

資料繫結，不會設定`ValueChanged`事件`Slider`存取之事件處理常式`Value`屬性`Slider`並將該值設定為`Rotation`屬性`Label`。 資料繫結會自動執行該作業。事件處理常式並在其中的程式碼不再需要。

您可以在任何衍生自的類別的執行個體上設定繫結[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)，其中包括`Element`， `VisualElement`， `View`，和`View`衍生項目。  繫結一定會在目標物件上設定。 繫結參考該來源物件。 若要設定資料繫結，請使用下列兩個目標類別的成員：

- [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)屬性指定的來源物件。
- [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/)方法指定目標屬性和資料來源屬性。

在此範例中，`Label`繫結目標，而`Slider`是繫結來源。 中的變更`Slider`來源影響旋轉的角度`Label`目標。 從來源傳送到目標資料。

`SetBinding`方法所定義`BindableObject`具有類型的引數[ `BindingBase` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingBase/)從中[ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/)類別衍生，但有其他`SetBinding`方法定義由[ `BindableObjectExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObjectExtensions/)類別。 中的程式碼後置檔案**繫結的基本程式碼**範例會使用更簡單[ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObjectExtensions.SetBinding/p/Xamarin.Forms.BindableObject/Xamarin.Forms.BindableProperty/System.String/)自此類別的擴充方法。

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

`Label`物件則繫結目標，因此，和呼叫的方法上設定這個屬性的物件。 `BindingContext`屬性會指出繫結來源，也就是`Slider`。

`SetBinding`繫結目標上呼叫方法，但指定的目標屬性和 [來源] 屬性。 目標屬性會指定為`BindableProperty`物件： `Label.RotationProperty`。 [來源] 屬性指定為字串，並指出`Value`屬性`Slider`。

`SetBinding`方法會顯示其中一個最重要的資料繫結規則：

*目標屬性必須在備份可繫結的屬性。*

這個規則表示目標物件必須衍生自類別的執行個體`BindableObject`。 請參閱[**可繫結屬性**](~/xamarin-forms/xaml/bindable-properties.md)概觀文件之可繫結的物件和可繫結的屬性。

沒有這類規則的來源屬性，指定為字串。 就內部而言，使用反映來存取實際的屬性。 在這個特殊案例，不過，`Value`屬性也支援可繫結的屬性。

程式碼可以簡化稍微：`RotationProperty`可繫結屬性由定義`VisualElement`，而且繼承`Label`和`ContentPage`，因此類別名稱不，您需要在`SetBinding`呼叫：

```csharp
label.SetBinding(RotationProperty, "Value");
```

不過，包含的類別名稱是好提醒您的目標物件。

以操作`Slider`、`Label`據以旋轉：

[![Basice 繫結的程式碼](basic-bindings-images/basiccodebinding-small.png "基本程式碼繫結")](basic-bindings-images/basiccodebinding-large.png#lightbox "基本程式碼繫結")

**基本 Xaml 繫結**頁面等同於**基本的程式碼繫結**不同之處在於它會在 XAML 中定義的整個資料繫結：

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

如同程式碼中，資料繫結設定的目標物件，也就是上`Label`。 牽涉到兩個 XAML 標記延伸。 這些是立即可辨認的大括號分隔符號：

- `x:Reference`標記延伸，才能參考來源物件，也就是`Slider`名為`slider`。
- `Binding`標記延伸連結`Rotation`屬性`Label`至`Value`屬性`Slider`。

請參閱文章[XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)的 XAML 標記延伸的詳細資訊。 `x:Reference`標記延伸支援[ `ReferenceExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/)類別;`Binding`支援[ `BindingExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/)類別。 為 XML 命名空間前置詞指出，`x:Reference`是 XAML 2009 規格的一部分時`Binding`屬於 Xamarin.Forms。 請注意，沒有引號會出現在大括號。

很容易會忘記`x:Reference`設定時，標記延伸`BindingContext`。 通常會不小心將屬性直接設定為類似的繫結來源的名稱：

```xaml
BindingContext="slider"
```

但並不正確。 該標記設定`BindingContext`屬性`string`字元拼字"slider"的物件 ！

請注意，[來源] 屬性指定[ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Path/)屬性`BindingExtension`，它會對應與[ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/)屬性[ `Binding`](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/)類別。

上所顯示的標記**繫結的基本 XAML**可簡化頁面： XAML 標記延伸，例如`x:Reference`和`Binding`可以有*內容屬性*屬性定義，以針對 XAML標記延伸表示的屬性名稱不需要出現。 `Name`屬性是內容屬性`x:Reference`，而`Path`屬性是內容屬性`Binding`，這表示它們可以消除從運算式：

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>沒有繫結內容的繫結

`BindingContext`屬性是資料繫結的重要元件，但是它不一定。 中可以改為指定的來源物件`SetBinding`呼叫或`Binding`標記延伸。

這示範於**替代方案的程式碼繫結**範例。 XAML 檔案是類似於**繫結的基本程式碼**範例不同處在於`Slider`定義控制`Scale`屬性`Label`。 基於這個原因，`Slider`設定某一範圍的&ndash;2 到 2:

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

程式碼後置檔案中設定的繫結[ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/)方法所定義`BindableObject`。 引數是[建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Binding.Binding/p/System.String/Xamarin.Forms.BindingMode/Xamarin.Forms.IValueConverter/System.Object/System.String/System.Object/)如[ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/)類別：

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

`Binding`建構函式有 6 參數，所以`source`參數指定的具名引數。 引數是`slider`物件。

執行此程式可能會有點令人意外：

[![替代程式碼繫結](basic-bindings-images/alternativecodebinding-small.png "替代程式碼繫結")](basic-bindings-images/alternativecodebinding-large.png#lightbox "替代程式碼繫結")

左側的 [iOS] 畫面會顯示在第一次出現時，螢幕的外觀。 其中是`Label`嗎？

問題在於`Slider`初始的值為 0。 這會導致`Scale`屬性`Label`也設定為 0，覆寫其預設值為 1。 這會導致`Label`正在一開始不可見。 如 Android 和通用 Windows 平台 (UWP) 的螢幕擷取畫面所示，您可以使用操作`Slider`進行`Label`出現一次，但其初始消失令人不安。

您會發現在[下一篇文章](binding-mode.md)如何避免這個問題發生初始化`Slider`的預設值從`Scale`屬性。

**替代 XAML 繫結**頁面會顯示在 XAML 中相等的繫結：

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

現在`Binding`標記延伸可以兩個屬性設定，`Source`和`Path`、 以逗號分隔。 如果您想要的話，它們可以出現在同一行：

```xaml
Scale="{Binding Source={x:Reference slider}, Path=Value}" />
```

`Source`屬性設定內嵌`x:Reference`標記延伸模組，否則會具有相同的語法，做為設定`BindingContext`。 請注意，沒有引號會出現在大括號，而且，必須以逗號分隔的兩個屬性。

內容屬性`Binding`標記延伸是`Path`，但`Path=`如果它是在運算式中的第一個屬性，可以只排除標記延伸的一部分。 若要消除`Path=`組件，您必須交換兩個屬性：

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

雖然 XAML 標記延伸通常以大括號分隔的它們可以也表示為物件項目：

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

現在`Source`和`Path`屬性是一般的 XAML 屬性： 值引號內的顯示和屬性不以逗號分隔。 `x:Reference`標記延伸也會成為物件項目：

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

此語法不常見，但有時候很有必要時涉及複雜的物件。

到目前為止所顯示的範例設定`BindingContext`屬性和`Source`屬性`Binding`至`x:Reference`來參考另一個檢視頁面上的標記延伸。 這兩個屬性都屬於型別`Object`，並可以將任何物件，其中包含適用於繫結來源的屬性。

文件中繼續，您會發現，您可以設定`BindingContext`或`Source`屬性`x:Static`參考的靜態屬性或欄位值的標記延伸或`StaticResource`來參考物件儲存在標記延伸資源字典，或直接至物件，這是通常 （但並非一定） ViewModel 的執行個體。

`BindingContext`屬性也設定為`Binding`物件以便`Source`和`Path`屬性`Binding`定義繫結內容。

## <a name="binding-context-inheritance"></a>繫結內容繼承

在本文中，您所見，您可以指定來源物件使用`BindingContext`屬性或`Source`屬性`Binding`物件。 如果同時設定`Source`屬性`Binding`優先於`BindingContext`。

`BindingContext`屬性就相當重要的特性：

*設定`BindingContext`透過視覺化樹狀結構會繼承屬性。*

如您所見，這可以簡化繫結運算式，以及在某些情況下，非常方便&mdash;特別是在模型-檢視-ViewModel (MVVM) 案例&mdash;很重要。

**繫結內容繼承**範例是繼承的繫結內容的簡單示範：

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

`BindingContext`屬性`StackLayout`設`slider`物件。 這個繫結內容會繼承由`Label`和`BoxView`，這兩個的具有其`Rotation`屬性設定為`Value`屬性`Slider`:

[![繫結內容繼承](basic-bindings-images/bindingcontextinheritance-small.png "繫結內容繼承")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "繫結內容繼承")

在[下一篇文章](binding-mode.md)，您會看到如何*繫結模式*可以變更目標和來源的物件之間的資料流程。


## <a name="related-links"></a>相關連結

- [資料繫結示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [資料繫結 Xamarin.Forms 書籍章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
