---
title： " Xamarin.Forms 分檔器" 描述： " Xamarin.Forms 分檔器允許使用者從值範圍選取數值。 其中包含以減號和加號標記的兩個按鈕。 操作這兩個按鈕會以累加方式變更選取的值。」
assetid： 62571B3E-D84B-4F52-9FC7-C105D6733B16 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：10/17/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-stepper"></a>Xamarin.Forms步進

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)

_使用分檔器，從值範圍選取數值。_

Xamarin.Forms [`Stepper`](xref:Xamarin.Forms.Stepper) 包含以減號和加號標記的兩個按鈕。 使用者可以操作這些按鈕，以累加方式 `double` 從某個範圍的值中選取值。

會 [`Stepper`](xref:Xamarin.Forms.Stepper) 定義類型的四個屬性 `double` ：

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment)這是要變更所選值的數量，預設值是1。
- [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum)這是範圍的最小值，預設值是0。
- [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum)這是範圍的上限，預設值是100。
- [`Value`](xref:Xamarin.Forms.Stepper.Value)是分檔器的值，其範圍可以介於 `Minimum` 和之間， `Maximum` 且預設值為0。

所有這些屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 。 [`Value`](xref:Xamarin.Forms.Stepper.Value)屬性具有的預設系結模式 [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) ，這表示它適合做為使用[ViewModel （MVVM）](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構之應用程式中的系結來源。

> [!WARNING]
> [`Stepper`](xref:Xamarin.Forms.Stepper)就內部而言， [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 會確保小於 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 。 如果 `Minimum` 或 `Maximum` 已設定 `Minimum` 為不小於 `Maximum` ，則會引發例外狀況。 如需設定和屬性的詳細資訊 `Minimum` `Maximum` ，請參閱[預防措施](#precautions)一節。

[`Stepper`](xref:Xamarin.Forms.Stepper)會強制轉型 [`Value`](xref:Xamarin.Forms.Stepper.Value) 屬性，使其介於 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 和 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) （含）之間。 如果 `Minimum` 屬性設定為大於屬性的值 `Value` ，則會 `Stepper` 將 `Value` 屬性設定為 `Minimum` 。 同樣地，如果 `Maximum` 設定為小於的值 `Value` ，則會 `Stepper` 將 `Value` 屬性設定為 `Maximum` 。

[`Stepper`](xref:Xamarin.Forms.Stepper)定義 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 當 [`Value`](xref:Xamarin.Forms.Stepper.Value) 變更時（不論是透過使用者操作， `Stepper` 或當應用程式直接設定屬性時）所引發的事件 `Value` 。 `ValueChanged`當屬性已強制轉型時，也會引發事件， `Value` 如前一段所述。

[`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs)事件隨附的物件 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 有兩個屬性，兩種類型都是 `double` ： [`OldValue`](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) 和 [`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue) 。 引發事件時，的值 `NewValue` 會與 [`Value`](xref:Xamarin.Forms.Stepper.Value) 物件的屬性相同 [`Stepper`](xref:Xamarin.Forms.Stepper) 。

## <a name="basic-stepper-code-and-markup"></a>基本的分檔器程式碼和標記

[**StepperDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)範例包含三個功能相同的頁面，但以不同的方式執行。 第一個頁面只會使用 c # 程式碼，第二個會使用 XAML 搭配程式碼中的事件處理常式，而第三個則能夠使用 XAML 檔案中的資料系結來避免事件處理常式。

### <a name="creating-a-stepper-in-code"></a>在程式碼中建立分檔器

[**StepperDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)範例中的**基本分檔器代碼**頁示範如何 [`Stepper`](xref:Xamarin.Forms.Stepper) 在程式碼中建立和兩個 [`Label`](xref:Xamarin.Forms.Label) 物件：

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

[`Stepper`](xref:Xamarin.Forms.Stepper)已初始化，其屬性為 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 360，而 [`Increment`](xref:Xamarin.Forms.Stepper.Increment) 屬性為30。 視屬性的值而定，操作會 `Stepper` 以累加方式將選取的值變更 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 為 `Maximum` `Increment` 。 的 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 處理常式會 `Stepper` 使用 [`Value`](xref:Xamarin.Forms.Stepper.Value) 物件的屬性 `stepper` 來設定 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 第一個的屬性 [`Label`](xref:Xamarin.Forms.Label) ，並使用 `string.Format` 方法與 `NewValue` 事件引數的屬性來設定 [`Text`](xref:Xamarin.Forms.Label.Text) 第二個的屬性 `Label` 。 這兩種取得目前值的方法是可 `Stepper` 互換的。

下列螢幕擷取畫面顯示**基本的分檔器代碼**頁：

[![基本的分檔器程式碼](stepper-images/basic-stepper-code.png "基本的分檔器程式碼")](stepper-images/basic-stepper-code-large.png#lightbox)

第二個 [`Label`](xref:Xamarin.Forms.Label) 會顯示 "（未初始化）" 文字 [`Stepper`](xref:Xamarin.Forms.Stepper) ，直到操作為止，這會導致引發第一個 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 事件。

### <a name="creating-a-stepper-in-xaml"></a>在 XAML 中建立分檔器

**基本的分檔器 XAML**頁面在功能上與**基本的分檔**器程式碼相同，但大部分都是在 XAML 中執行：

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

程式碼後置檔案包含事件的處理常式 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) ：

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

事件處理常式也可以 [`Stepper`](xref:Xamarin.Forms.Stepper) 透過引數取得引發事件的 `sender` 。 [`Value`](xref:Xamarin.Forms.Stepper.Value)屬性包含目前的值：

```csharp
double value = ((Stepper)sender).Value;
```

如果 [`Stepper`](xref:Xamarin.Forms.Stepper) 物件的名稱在 XAML 檔案中具有 `x:Name` 屬性（例如，"分檔"），則事件處理常式可以直接參考該物件：

```csharp
double value = stepper.Value;
```

### <a name="data-binding-the-stepper"></a>資料系結至分檔器

基本的 [**分檔**器系結] 頁面會顯示如何撰寫幾乎相等的應用程式，藉由使用資料系結來消除 [`Value`](xref:Xamarin.Forms.Stepper.Value) 事件處理常式： [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)

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

[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)第一個的屬性會系結 [`Label`](xref:Xamarin.Forms.Label) 至的 [`Value`](xref:Xamarin.Forms.Stepper.Value) 屬性 [`Stepper`](xref:Xamarin.Forms.Stepper) ，如同 [`Text`](xref:Xamarin.Forms.Label.Text) 第二個 `Label` 具有規格的屬性 `StringFormat` 。 基本的 [**分檔**器系結] 頁面與前兩個頁面的運作方式稍有不同：第一次出現時，第二頁會 `Label` 以值顯示文字字串。 這是使用資料系結的優點。 若要顯示沒有資料系結的文字，您必須明確地初始化的 `Text` 屬性， `Label` 或從類別的函式 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 呼叫事件處理常式，以模擬事件的引發。

## <a name="precautions"></a>措施

屬性的值 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 必須一律小於屬性的值 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 。 下列程式碼片段會導致 [`Stepper`](xref:Xamarin.Forms.Stepper) 引發例外狀況：

```csharp
// Throws an exception!
Stepper stepper = new Stepper
{
    Minimum = 180,
    Maximum = 360
};
```

C # 編譯器會產生依序設定這兩個屬性的程式碼，當 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 屬性設定為180時，它會大於預設 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 值100。 在此情況下，您可以藉由先設定屬性來避免例外狀況 `Maximum` ：

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

將設定 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 為360不會有問題，因為它大於預設 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 值0。 當 `Minimum` 設定時，此值會小於360的 `Maximum` 值。

XAML 中存在相同的問題。 以確保一律大於的順序，設定屬性 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) `Minimum` ：

```xaml
<Stepper Maximum="360"
         Minimum="180" ... />
```

您可以將 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 和 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 值設定為負數，但只能在一律小於的順序中 `Minimum` `Maximum` ：

```xaml
<Stepper Minimum="-360"
         Maximum="-180" ... />
```

[`Value`](xref:Xamarin.Forms.Stepper.Value)屬性一律大於或等於 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 值，且小於或等於 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 。 如果 `Value` 設定為超出該範圍的值，則會將值強制轉型為位於範圍內，但不會引發任何例外狀況。 例如，此程式碼*不*會引發例外狀況：

```csharp
Stepper stepper = new Stepper
{
    Value = 180
};
```

相反地， [`Value`](xref:Xamarin.Forms.Stepper.Value) 屬性會強制轉型 [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) 為100的值。

以下是如上所示的程式碼片段：

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

當 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 設定為180時， [`Value`](xref:Xamarin.Forms.Stepper.Value) 也會設定為180。

如果在 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 屬性被強制轉型為預設值為0的時候附加了事件處理常式 [`Value`](xref:Xamarin.Forms.Stepper.Value) ，則 `ValueChanged` 會引發事件。 以下是 XAML 的程式碼片段：

```xaml
<Stepper ValueChanged="OnStepperValueChanged"
         Maximum="360"
         Minimum="180" />
```

當 [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) 設定為180時， [`Value`](xref:Xamarin.Forms.Stepper.Value) 也會設定為180，並 [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) 引發事件。 這可能會在已建立其餘頁面之前發生，而且處理常式可能會嘗試參考尚未建立之頁面上的其他元素。 您可能會想要將一些程式碼加入至 `ValueChanged` 處理常式，以檢查 `null` 頁面上其他元素的值。 或者，您可以在 `ValueChanged` 值初始化之後，設定事件處理常式 [`Stepper`](xref:Xamarin.Forms.Stepper) 。

## <a name="related-links"></a>相關連結

- [分檔器示範範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)
- [分檔器 API](xref:Xamarin.Forms.Stepper)
