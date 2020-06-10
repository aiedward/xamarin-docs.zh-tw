---
title： "附加屬性" 描述： "本文提供附加屬性的簡介，並示範如何建立和使用它們。"
assetid： 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：06/02/2016 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="attached-properties"></a>附加屬性

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)


附加屬性可讓物件指派其本身的類別未定義之屬性的值。 例如，子項目可以使用附加屬性來通知其父元素如何呈現在使用者介面中。 控制項可讓您藉 [`Grid`](xref:Xamarin.Forms.Grid) 由設定 `Grid.Row` 和附加屬性來指定子系的資料列和資料行 `Grid.Column` 。 `Grid.Row`和 `Grid.Column` 是附加的屬性，因為它們是設定在屬於子系的專案上 `Grid` ，而不是在本身的元素上 `Grid` 。

在下列案例中，可系結屬性應該實作為附加屬性：

- 當定義類別以外的類別必須有可用的屬性設定機制時。
- 當類別代表需要與其他類別輕鬆整合的服務時。

如需可系結屬性的詳細資訊，請參閱可系結[屬性](~/xamarin-forms/xaml/bindable-properties.md)。

## <a name="create-an-attached-property"></a>建立附加屬性

建立附加屬性的流程如下所示：

1. [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)使用其中一個方法多載來建立實例 [`CreateAttached`](xref:Xamarin.Forms.BindableProperty.CreateAttached*) 。
1. 提供 `static` `Get` *PropertyName*和 `Set` *propertyname*方法做為附加屬性的存取子。

### <a name="create-a-property"></a>建立屬性

建立附加屬性以用於其他類型時，建立屬性的類別不需要衍生自 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 。 不過，存取子的*目標*屬性應為，或衍生自 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 。

您可以藉由宣告 `public static readonly` 類型的屬性來建立附加屬性 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 。 可系結屬性應設定為其中一個 [ `BindableProperty.CreateAttached` ] （x：）的傳回值 Xamarin.Forms 。BindableProperty. CreateAttached （System.string，System.object，type，system.object，， Xamarin.Forms 。BindingMode， Xamarin.Forms 。BindableProperty. ValidateValueDelegate、 Xamarin.Forms 。BindableProperty. BindingPropertyChangedDelegate、 Xamarin.Forms 。BindableProperty. BindingPropertyChangingDelegate、 Xamarin.Forms 。BindableProperty. CoerceValueDelegate、 Xamarin.Forms 。BindableProperty. CreateDefaultValueDelegate））方法多載。 宣告應位於擁有類別的主體內，但不應位於任何成員定義的外部。

下列程式碼顯示附加屬性的範例：

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

這會建立名為的附加屬性 `HasShadow` ，其類型為 `bool` 。 屬性是由類別所擁有 `ShadowEffect` ，且預設值為 `false` 。 附加屬性的命名慣例是附加屬性識別碼必須符合方法中所指定的屬性名稱 `CreateAttached` ，並附加 "property"。 因此，在上述範例中，附加屬性識別碼為 `HasShadowProperty` 。

如需建立可系結屬性的詳細資訊，包括在建立期間可以指定的參數，請參閱建立可系結[屬性](~/xamarin-forms/xaml/bindable-properties.md#consume-a-bindable-property)。

### <a name="create-accessors"></a>建立存取子

需要靜態 `Get` *PropertyName*和 `Set` *PropertyName*方法做為附加屬性的存取子，否則屬性系統將無法使用附加屬性。 `Get` *PropertyName*存取子應符合下列簽章：

```csharp
public static valueType GetPropertyName(BindableObject target)
```

`Get` *PropertyName*存取子應該會傳回包含在附加屬性之對應欄位中的值 `BindableProperty` 。 呼叫 [ `GetValue` ] （x：）即可達成此目的 Xamarin.Forms 。BindableObject. GetValue （ Xamarin.Forms 。BindableProperty））方法，傳入要取得值的可系結屬性識別碼，然後將產生的值轉換成所需的類型。

`Set` *PropertyName*存取子應符合下列簽章：

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

`Set` *PropertyName*存取子應該設定附加屬性之對應欄位的值 `BindableProperty` 。 呼叫 [ `SetValue` ] （x：）即可達成此目的 Xamarin.Forms 。BindableObject. SetValue （ Xamarin.Forms 。BindableProperty，System.object）方法，傳入要設定值的可系結屬性識別碼，以及要設定的值。

對於這兩個存取子，*目標*物件應為，或衍生自 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 。

下列程式碼範例顯示附加屬性的存取子 `HasShadow` ：

```csharp
public static bool GetHasShadow (BindableObject view)
{
  return (bool)view.GetValue (HasShadowProperty);
}

public static void SetHasShadow (BindableObject view, bool value)
{
  view.SetValue (HasShadowProperty, value);
}
```

### <a name="consume-an-attached-property"></a>使用附加屬性

一旦建立附加屬性之後，就可以從 XAML 或程式碼使用它。 在 XAML 中，這是藉由宣告具有前置詞的命名空間來達成，其中命名空間宣告表示 Common Language Runtime （CLR）命名空間名稱，以及選擇性的元件名稱。 如需詳細資訊，請參閱[XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)。

下列程式碼範例會示範自訂類型的 XAML 命名空間，其中包含附加屬性，該附加屬性是在與參考自訂類型的應用程式程式碼相同的元件中定義的：

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

然後在特定控制項上設定附加屬性時，會使用命名空間宣告，如下列 XAML 程式碼範例所示：

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

對等的 C# 程式碼會顯示在以下程式碼範例中：

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consume-an-attached-property-with-a-style"></a>使用樣式的附加屬性

附加屬性也可以透過樣式加入至控制項。 下列 XAML 程式碼範例示範使用附加屬性的*明確*樣式 `HasShadow` ，可套用至 [`Label`](xref:Xamarin.Forms.Label) 控制項：

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

[`Style`](xref:Xamarin.Forms.Style)可以套用至 [`Label`](xref:Xamarin.Forms.Label) ，方法是 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 使用標記延伸將其屬性設為 `Style` 實例 `StaticResource` ，如下列程式碼範例所示：

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

如需樣式的詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)。

## <a name="advanced-scenarios"></a>進階案例

建立附加屬性時，可以設定一些選擇性參數，以啟用「高階附加屬性」案例。 這包括偵測屬性變更、驗證屬性值，以及強制屬性值。 如需詳細資訊，請參閱[Advanced 案例](~/xamarin-forms/xaml/bindable-properties.md#advanced-scenarios)。

## <a name="related-links"></a>相關連結

- [可繫結的屬性](~/xamarin-forms/xaml/bindable-properties.md)
- [XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)
- [Shadow Effect (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect) (陰影效果 (範例))
- [BindableProperty API](xref:Xamarin.Forms.BindableProperty)
- [BindableObject API](xref:Xamarin.Forms.BindableObject)
