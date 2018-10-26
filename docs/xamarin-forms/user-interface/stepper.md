---
title: Xamarin.Forms 步進
description: Xamarin.Forms 步進可讓使用者從某個範圍的值中選取一個數字值。 它包含兩個按鈕加上減號和加號。 操作的兩個按鈕會變更選取的值，以累加方式所示。
ms.prod: xamarin
ms.assetid: 62571B3E-D84B-4F52-9FC7-C105D6733B16
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/17/2018
ms.openlocfilehash: 53485d0d948f31b69f74b6933d05c14f69fa64c0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111473"
---
# <a name="xamarinforms-stepper"></a>Xamarin.Forms 步進

_使用步進從某個範圍的值中選取一個數字值。_

Xamarin.Forms [ `Stepper` ](xref:Xamarin.Forms.Stepper)組成兩個按鈕加上減號和加號。 這些按鈕可由使用者以累加方式選取操作`double`中值的範圍值。

[ `Stepper` ](xref:Xamarin.Forms.Stepper)定義四個屬性的型別`double`:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) 是要變更選取的值，預設值為 1 的數量。
- [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 最小的範圍，預設值為 0。
- [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 是預設值為 100 的範圍中，最大值。
- [`Value`](xref:Xamarin.Forms.Stepper.Value) 是步進的值，範圍必須介於`Minimum`和`Maximum`且具有預設值為 0。

所有這些屬性會受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件。 [ `Value` ](xref:Xamarin.Forms.Stepper.Value)屬性具有預設繫結模式[ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)，這表示它很適合做為使用應用程式中的繫結來源[Model View ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構。

> [!WARNING]
> 就內部而言， [ `Stepper` ](xref:Xamarin.Forms.Stepper)確保[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)是小於[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)。 如果`Minimum`或是`Maximum`曾經設定以便`Minimum`是不小於`Maximum`，會引發例外狀況。 如需設定的詳細資訊`Minimum`並`Maximum`屬性，請參閱[預防措施](#precautions)一節。

[ `Stepper` ](xref:Xamarin.Forms.Stepper)強制轉型[ `Value` ](xref:Xamarin.Forms.Stepper.Value)屬性，使其之間[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)並[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)(含） 之間。 如果`Minimum`屬性設定為值大於`Value`屬性，`Stepper`設定`Value`屬性設`Minimum`。 同樣地，如果`Maximum`的值設定小於`Value`，然後`Stepper`設定`Value`屬性設`Maximum`。

[`Stepper`](xref:Xamarin.Forms.Stepper) 定義[ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)時引發的事件[ `Value` ](xref:Xamarin.Forms.Stepper.Value)變更，不論是透過使用者操作`Stepper`或應用程式的設定時`Value`直接屬性。 A`ValueChanged`引發事件時也當`Value`前段中所述，會強制轉型屬性。

[ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs)隨附的物件[ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)事件有兩個屬性，這兩個型別`double`: [ `OldValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue)與[`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue). 在引發事件時，windows 7`NewValue`等同[ `Value` ](xref:Xamarin.Forms.Stepper.Value)屬性[ `Stepper` ](xref:Xamarin.Forms.Stepper)物件。

## <a name="basic-stepper-code-and-markup"></a>基本的步進程式碼和標記

[ **StepperDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos)範例包含三個頁面的功能相同，但會以不同的方式實作。 第一頁只會使用C#程式碼中，第二個會使用 XAML 與程式碼和第三個事件處理常式是能夠使用 XAML 檔案中的資料繫結，以避免事件處理常式。

### <a name="creating-a-stepper-in-code"></a>建立程式碼中的步進

**步進的基本程式碼**頁面[ **StepperDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos)範例示範如何建立[ `Stepper` ](xref:Xamarin.Forms.Stepper)並將兩個[`Label` ](xref:Xamarin.Forms.Label)程式碼中的物件：

```csharp
public class BasicStepperCodePage : ContentPage
{
    public BasicStepperCodePage()
    {
        Label rotationLabel = new Label
        {
            Text = "ROTATING TEXT",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Label displayLabel = new Label
        {
            Text = "(uninitialized)",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Stepper stepper = new Stepper
        {
            Maximum = 360,
            Increment = 30,
            HorizontalOptions = LayoutOptions.Center
        };
        stepper.ValueChanged += (sender, e) =>
        {
            rotationLabel.Rotation = stepper.Value;
            displayLabel.Text = string.Format("The Stepper value is {0}", e.NewValue);
        };

        Title = "Basic Stepper Code";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = { rotationLabel, stepper, displayLabel }
        };
    }
}
```

[ `Stepper` ](xref:Xamarin.Forms.Stepper)初始化有[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) 360、 屬性和[ `Increment` ](xref:Xamarin.Forms.Stepper.Increment)屬性為 30。 操作`Stepper`變更選取的值，以累加方式之間[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)來`Maximum`的值為基礎`Increment`屬性。 [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)處理常式`Stepper`會使用[ `Value` ](xref:Xamarin.Forms.Stepper.Value)屬性`stepper`物件來設定[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)屬性的第一個[ `Label` ](xref:Xamarin.Forms.Label)並用`string.Format`方法`NewValue`若要設定之事件引數的屬性[ `Text` ](xref:Xamarin.Forms.Label.Text)屬性第二個`Label`。 這兩種方法來取得目前的值`Stepper`是可互換的。

下列螢幕擷取畫面所示**步進的基本程式碼**頁面：

[![基本的步進程式碼](stepper-images/basic-stepper-code.png "基本步進程式碼")](stepper-images/basic-stepper-code-large.png#lightbox)

第二個[ `Label` ](xref:Xamarin.Forms.Label)會顯示"（未初始化） 」 的文字，直到[ `Stepper` ](xref:Xamarin.Forms.Stepper)操作，因而導致第一個[ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)事件若要引發。

### <a name="creating-a-stepper-in-xaml"></a>在 XAML 中建立步進

**基本步進 XAML**頁功能上是相同**步進的基本程式碼**但是實作大部分是在 XAML 中：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperXAMLPage"
             Title="Basic Stepper XAML">
    <StackLayout Margin="20">
        <Label x:Name="_rotatingLabel"
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center"
                 ValueChanged="OnStepperValueChanged" />
        <Label x:Name="_displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />        
    </StackLayout>
</ContentPage>
```

程式碼後置檔案包含的處理常式[ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)事件：

```csharp
public partial class BasicStepperXAMLPage : ContentPage
{
    public BasicStepperXAMLPage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs e)
    {
        double value = e.NewValue;
        _rotatingLabel.Rotation = value;
        _displayLabel.Text = string.Format("The Stepper value is {0}", value);
    }
}
```

您也可取得事件處理常式[ `Stepper` ](xref:Xamarin.Forms.Stepper)所引發的事件，透過`sender`引數。 [ `Value` ](xref:Xamarin.Forms.Stepper.Value)屬性包含目前的值：

```csharp
double value = ((Stepper)sender).Value;
```

如果[ `Stepper` ](xref:Xamarin.Forms.Stepper)物件所指定的 XAML 檔案中的名稱`x:Name`屬性 （例如，"步進 」），則事件處理常式無法參考該物件直接：

```csharp
double value = stepper.Value;
```

### <a name="data-binding-the-stepper"></a>資料繫結步進

**基本步進繫結**頁面會顯示如何撰寫幾乎相等的應用程式，可減少[ `Value` ](xref:Xamarin.Forms.Stepper.Value)事件處理常式，使用[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperBindingsPage"
             Title="Basic Stepper Bindings">
    <StackLayout Margin="20">
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference _stepper}, Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper x:Name="_stepper"
                 Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center" />
        <Label Text="{Binding Source={x:Reference _stepper}, Path=Value, StringFormat='The Stepper value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)的第一個屬性[ `Label` ](xref:Xamarin.Forms.Label)繫結至[ `Value` ](xref:Xamarin.Forms.Stepper.Value)屬性[ `Stepper` ](xref:Xamarin.Forms.Stepper)，因為正[ `Text` ](xref:Xamarin.Forms.Label.Text)第二個屬性`Label`與`StringFormat`規格。 **基本步進繫結**頁面上函式稍有不同的兩個先前的頁面： 第一次出現的頁面中，第二個`Label`顯示文字字串，其值。 這是使用資料繫結的優點。 若要顯示沒有資料繫結的文字，您必須特別初始化`Text`的屬性`Label`，或模擬的引發[ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)藉由從類別建構函式呼叫的事件處理常式的事件.

## <a name="precautions"></a>預防措施

值[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)屬性一律必須的值小於[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)屬性。 下列程式碼片段會導致[ `Stepper` ](xref:Xamarin.Forms.Stepper)引發例外狀況：

```csharp
// Throws an exception!
Stepper stepper = new Stepper
{
    Minimum = 180,
    Maximum = 360
};
```

C#編譯器會產生在順序中，設定這兩個屬性的程式碼及何時[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)屬性設定為 180，大於預設值[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) 100 的值。 您可以在此情況下避免例外狀況，藉由設定`Maximum`屬性第一次：

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

設定[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)到 360 並不成問題，因為它大於預設值[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)值為 0。 當`Minimum`設定，這個值是小於`Maximum`360 的值。

在 XAML 中，有相同的問題。 設定屬性的順序可確保[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)一律會大於`Minimum`:

```xaml
<Stepper Maximum="360"
         Minimum="180" ... />
```

您可以設定[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)並[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)負數的數字，但只能在訂單中的值其中`Minimum`是一定小於`Maximum`:

```xaml
<Stepper Minimum="-360"
         Maximum="-180" ... />
```

[ `Value` ](xref:Xamarin.Forms.Stepper.Value)屬性一定是大於或等於[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)值且小於或等於[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum)。 如果`Value`設定的值超出該範圍中，值將會強制轉型為落在範圍內，但會引發任何例外狀況。 比方說，這段程式碼將會*不*引發例外狀況：

```csharp
Stepper stepper = new Stepper
{
    Value = 180
};
```

相反地， [ `Value` ](xref:Xamarin.Forms.Stepper.Value)屬性會強制轉型[ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) 100 的值。

以下是如上所示的程式碼片段：

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

當[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)設定為 180，則[ `Value` ](xref:Xamarin.Forms.Stepper.Value)也會設定為 180。

如果[ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)已附加事件處理常式時， [ `Value` ](xref:Xamarin.Forms.Stepper.Value)屬性會強制轉型為 0，其預設值以外的項目則`ValueChanged`引發事件。 以下是 XAML 程式碼片段：

```xaml
<Stepper ValueChanged="OnStepperValueChanged"
         Maximum="360"
         Minimum="180" />
```

當[ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum)設為 180， [ `Value` ](xref:Xamarin.Forms.Stepper.Value)也會設定為 180，而[ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged)引發事件。 這可能是頁面的其餘部分都建構完畢，並處理常式可能會嘗試參考頁面上尚未建立的其他項目之前。 您可能想要新增一些程式碼`ValueChanged`處理常式會檢查`null`頁面上的其他元素的值。 或者，您可以設定`ValueChanged`之後的事件處理常式[ `Stepper` ](xref:Xamarin.Forms.Stepper)已初始化值。

## <a name="related-links"></a>相關連結

- [步進示範範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos)
- [步進 API](xref:Xamarin.Forms.Stepper)
