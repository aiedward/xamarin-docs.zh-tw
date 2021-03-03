---
title: Xamarin.Forms 步 進
description: Xamarin.Forms分檔器可讓使用者從值範圍中選取數值。 它包含兩個標示為減號和加號的按鈕。 操作兩個按鈕會以累加方式變更選取的值。
ms.prod: xamarin
ms.assetid: 62571B3E-D84B-4F52-9FC7-C105D6733B16
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f1a84dad3048597cada0ec3fcbc5ef4035830abb
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369788"
---
# <a name="xamarinforms-stepper"></a>Xamarin.Forms 步 進

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)

_使用分檔器從值範圍中選取數值。_

Xamarin.Forms [`Stepper`](xref:Xamarin.Forms.Stepper) 包含兩個標示為減號和加號的按鈕。 這些按鈕可由使用者操作，以累加方式 `double` 從某個值範圍選取一個值。

會 [`Stepper`](xref:Xamarin.Forms.Stepper) 定義類型的四個屬性 `double` ：

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) 這是要變更所選值的數量，預設值為1。
- [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 這是範圍的最小值，預設值是0。
- [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 這是範圍的最大值，預設值是100。
- [`Value`](xref:Xamarin.Forms.Stepper.Value) 這是分檔器的值，其範圍可以介於和之間， `Minimum` `Maximum` 且預設值為0。

所有這些屬性都受物件支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 。 [`Value`](xref:Xamarin.Forms.Stepper.Value)屬性具有的預設系結模式 [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) ，這表示它適合作為應用程式中的系結來源，此應用程式會使用[模型視圖 ViewModel (MVVM) ](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構。

> [!WARNING]
> 在內部， [`Stepper`](xref:Xamarin.Forms.Stepper) 可確保 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 小於 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 。 如果 `Minimum` 設定或的 `Maximum` 設定 `Minimum` 不小於，則會 `Maximum` 引發例外狀況。 如需有關設定和屬性的詳細資訊 `Minimum` `Maximum` ，請參閱 [預防措施](#precautions) 一節。

[`Stepper`](xref:Xamarin.Forms.Stepper) [`Value`](xref:Xamarin.Forms.Stepper.Value) 會強制屬性，使其介於 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 和 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) （含）之間。 如果 `Minimum` 屬性設定為大於屬性的值 `Value` ，則會 `Stepper` 將 `Value` 屬性設定為 `Minimum` 。 同樣地，如果 `Maximum` 設定為小於的值 `Value` ，則會 `Stepper` 將 `Value` 屬性設定為 `Maximum` 。

[`Stepper`](xref:Xamarin.Forms.Stepper) 定義在 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 變更時所引發的事件 [`Value`](xref:Xamarin.Forms.Stepper.Value) ，不論是透過使用者操作 `Stepper` 或當應用程式直接設定屬性時 `Value` 。 `ValueChanged`當 `Value` 屬性如前一段落所述強制轉型時，也會引發事件。

[`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs)事件隨附的物件 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 有兩個屬性，兩者都是類型 `double` ： [`OldValue`](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) 和 [`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue) 。 引發事件時，的值 `NewValue` 會與 [`Value`](xref:Xamarin.Forms.Stepper.Value) 物件的屬性相同 [`Stepper`](xref:Xamarin.Forms.Stepper) 。

## <a name="basic-stepper-code-and-markup"></a>基本的分檔器程式碼和標記

[**StepperDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)範例包含三個功能相同，但以不同方式執行的頁面。 第一頁只使用 c # 程式碼，第二個頁面會在程式碼中使用 XAML 搭配事件處理常式，而第三個頁面則可以在 XAML 檔案中使用資料系結來避免事件處理常式。

### <a name="creating-a-stepper-in-code"></a>在程式碼中建立分檔器

[**StepperDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)範例中的 **基本 [分檔器代碼**] 頁面會顯示如何 [`Stepper`](xref:Xamarin.Forms.Stepper) 在程式碼中建立和兩個 [`Label`](xref:Xamarin.Forms.Label) 物件：

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

[`Stepper`](xref:Xamarin.Forms.Stepper)會初始化為具有360的 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 屬性和 [`Increment`](xref:Xamarin.Forms.Stepper.Increment) 屬性30。 根據屬性的值，操作會將 `Stepper` 選取的值以累加方式從變更 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 為 `Maximum` `Increment` 。 的 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 處理常式會 `Stepper` 使用 [`Value`](xref:Xamarin.Forms.Stepper.Value) 物件的屬性 `stepper` 來設定 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 第一個的屬性 [`Label`](xref:Xamarin.Forms.Label)  ，並使用 `string.Format` 方法與 `NewValue` 事件引數的屬性來設定 [`Text`](xref:Xamarin.Forms.Label.Text) 第二個 `Label` 的屬性。 這兩種取得目前值的方法 `Stepper` 都是可互換的。

下列螢幕擷取畫面顯示 **基本的分檔器代碼** 頁：

[![基本的分檔器程式碼](stepper-images/basic-stepper-code.png "基本的分檔器程式碼")](stepper-images/basic-stepper-code-large.png#lightbox)

第二個會在 [`Label`](xref:Xamarin.Forms.Label)  操作之前顯示「 (未初始化的) 」文字 [`Stepper`](xref:Xamarin.Forms.Stepper) ，這會導致引發第一個 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 事件。

### <a name="creating-a-stepper-in-xaml"></a>在 XAML 中建立分檔器

**基本的分檔器 XAML** 頁面功能與基本的 **分檔** 器程式碼相同，但實際上是在 XAML 中執行：

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

程式碼後端檔案包含事件的處理常式 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) ：

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

事件處理常式也可以 [`Stepper`](xref:Xamarin.Forms.Stepper) 透過引數取得正在引發事件的 `sender` 。 [`Value`](xref:Xamarin.Forms.Stepper.Value)屬性包含目前的值：

```csharp
double value = ((Stepper)sender).Value;
```

如果 [`Stepper`](xref:Xamarin.Forms.Stepper) 物件是在具有屬性的 XAML 檔案中提供名稱 `x:Name` (例如 "分檔器" ) ，則事件處理常式可以直接參考該物件：

```csharp
double value = stepper.Value;
```

### <a name="data-binding-the-stepper"></a>資料系結至分檔器

[**基本的分檔** 器系結] 頁面會顯示如何使用資料系結來撰寫幾乎相等的應用程式，以消除 [`Value`](xref:Xamarin.Forms.Stepper.Value) 事件處理常式： [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)

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

[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)第一個屬性（property [`Label`](xref:Xamarin.Forms.Label) ）系結至的 [`Value`](xref:Xamarin.Forms.Stepper.Value) 屬性（property），其為 [`Stepper`](xref:Xamarin.Forms.Stepper) [`Text`](xref:Xamarin.Forms.Label.Text) 第二個 `Label` 具有規格的屬性（property） `StringFormat` 。 基本的 [ **分檔** 器系結] 頁面的運作方式與前兩個頁面稍有不同：當頁面第一次出現時，第二個頁面會 `Label` 顯示具有值的文字字串。 這是使用資料系結的優點。 若要顯示沒有資料系結的文字，您必須明確地初始化的 `Text` 屬性， `Label` 或從類別的函式 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 呼叫事件處理常式，以模擬事件的引發。

## <a name="precautions"></a>預防 措施

屬性的值 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 必須一律小於屬性的值 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 。 下列程式碼片段會導致 [`Stepper`](xref:Xamarin.Forms.Stepper) 引發例外狀況：

```csharp
// Throws an exception!
Stepper stepper = new Stepper
{
    Minimum = 180,
    Maximum = 360
};
```

C # 編譯器會產生依序設定這兩個屬性的程式碼，而當 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 屬性設定為180時，它會大於預設 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 值100。 在此情況下，您可以先設定屬性，以避免發生例外狀況 `Maximum` ：

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

設定 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 為360不是問題，因為它大於預設 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 值0。 當 `Minimum` 設定時，值小於 `Maximum` 360 的值。

XAML 中存在相同的問題。 以確保永遠大於下列順序的順序設定屬性 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) `Minimum` ：

```xaml
<Stepper Maximum="360"
         Minimum="180" ... />
```

您可以將 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 和 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 值設定為負數，但只有順序中的 `Minimum` 永遠小於 `Maximum` ：

```xaml
<Stepper Minimum="-360"
         Maximum="-180" ... />
```

[`Value`](xref:Xamarin.Forms.Stepper.Value)屬性一律大於或等於 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 值，且小於或等於 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 。 如果 `Value` 設定為範圍以外的值，則會將值強制轉型為範圍內的，但不會引發任何例外狀況。 例如，此程式碼 *不* 會引發例外狀況：

```csharp
Stepper stepper = new Stepper
{
    Value = 180
};
```

相反地， [`Value`](xref:Xamarin.Forms.Stepper.Value) 屬性會強制轉型 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 為100的值。

以下是上面所示的程式碼片段：

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

當 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 設定為180時， [`Value`](xref:Xamarin.Forms.Stepper.Value) 也會設定為180。

如果 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 事件處理常式已在 [`Value`](xref:Xamarin.Forms.Stepper.Value) 屬性強制轉型為預設值0以外的值時附加，則 `ValueChanged` 會引發事件。 以下是 XAML 的程式碼片段：

```xaml
<Stepper ValueChanged="OnStepperValueChanged"
         Maximum="360"
         Minimum="180" />
```

當 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 設為180時， [`Value`](xref:Xamarin.Forms.Stepper.Value) 也會設定為180，並 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 引發事件。 這可能會在已建立頁面的其餘部分之前發生，而且處理常式可能會嘗試參考頁面上尚未建立的其他元素。 您可能會想要在 `ValueChanged` 處理常式中加入一些程式碼，以檢查 `null` 頁面上其他元素的值。 或者，您可以在 `ValueChanged` 值初始化之後設定事件處理常式 [`Stepper`](xref:Xamarin.Forms.Stepper) 。

## <a name="related-links"></a>相關連結

- [分檔器示範範例](/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)
- [分檔器 API](xref:Xamarin.Forms.Stepper)