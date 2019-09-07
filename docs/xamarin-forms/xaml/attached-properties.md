---
title: 附加屬性
description: 本文章提供附加屬性的簡介，並示範如何建立和使用它們。
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
ms.openlocfilehash: b5d1ddc4cf3a6817851d22aba920abb29d9f746f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767641"
---
# <a name="attached-properties"></a>附加屬性

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)

_附加的屬性是特殊類型的可繫結的屬性，定義一個類別中，但連接至其他物件，並可辨識在 XAML 做為屬性中包含的類別，以句號分隔屬性名稱。本文章提供附加屬性的簡介，並示範如何建立和使用它們。_

## <a name="overview"></a>總覽

附加屬性可以讓物件以指派值給它自己的類別未定義的屬性。 例如，子元素可以使用附加屬性，以通知它們的方式呈現使用者介面及其父項目。 [ `Grid` ](xref:Xamarin.Forms.Grid)控制項可讓資料列和資料行，以藉由設定子系`Grid.Row`和`Grid.Column`附加屬性。 `Grid.Row` 並`Grid.Column`會附加的屬性，因為它們會在子系的項目上設定`Grid`，而非在`Grid`本身。

可繫結屬性應該實作為附加屬性，在下列情況：

- 定義類別時就需要將屬性設定機制，可以使用類別以外。
- 當此類別代表的服務需要與其他類別輕鬆地整合。

如需有關可繫結屬性的詳細資訊，請參閱[可繫結屬性](~/xamarin-forms/xaml/bindable-properties.md)。

## <a name="creating-and-consuming-an-attached-property"></a>建立及使用附加的屬性

建立附加的屬性的程序如下所示：

1. 建立[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)執行個體，其中[ `CreateAttached` ](xref:Xamarin.Forms.BindableProperty.CreateAttached*)方法多載。
1. 提供`static` `Get` *PropertyName*並`Set` *PropertyName*附加屬性存取子方法。

### <a name="creating-a-property"></a>建立屬性

在建立附加的屬性使用其他型別上時，系統會建立屬性的類別並沒有衍生自[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)。 不過，*目標*存取子的屬性應該的或衍生自[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)。

附加的屬性可由宣告`public static readonly`型別的屬性[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)。 可繫結的屬性應該設定為傳回的值，其中[ `BindableProperty.CreateAttached` ](xref:Xamarin.Forms.BindableProperty.CreateAttached(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate))方法多載。 宣告應該是類別的在主體內主控，但任何成員定義之外。

下列程式碼顯示附加屬性的範例：

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

這會建立名為附加的屬性`HasShadow`，類型的`bool`。 屬性由所擁有`ShadowEffect`類別，並具有預設值是`false`。 附加屬性的命名慣例是附加的屬性的識別項必須符合在指定的屬性名稱`CreateAttached`方法，並有"Property"附加到它。 因此，在上述範例中，附加的屬性的識別項是`HasShadowProperty`。

如需有關建立可繫結的屬性，包括參數可指定在建立期間，請參閱[建立及使用可繫結屬性](~/xamarin-forms/xaml/bindable-properties.md#consuming-bindable-property)。

### <a name="creating-accessors"></a>建立存取子

靜態`Get` *PropertyName*並`Set` *PropertyName*方法所需當做附加屬性的存取子，否則屬性系統將無法使用附加屬性。 `Get` *PropertyName*存取子應符合下列簽章：

```csharp
public static valueType GetPropertyName(BindableObject target)
```

`Get` *PropertyName*存取子應會傳回包含值，在對應`BindableProperty`附加屬性的欄位。 這可藉由呼叫[ `GetValue` ](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty))方法中，傳入可繫結的屬性識別項，以取得值，然後再轉型為所需的型別產生的值。

`Set` *PropertyName*存取子應符合下列簽章：

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

`Set` *PropertyName*存取子都應該設定相對應的值`BindableProperty`附加屬性的欄位。 這可藉由呼叫[ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object))方法並傳入要設定值，並要設定的值可繫結的屬性識別項。

這兩個存取子，如*目標*物件應該的或衍生自[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)。

下列程式碼範例顯示存取子`HasShadow`附加屬性：

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

### <a name="consuming-an-attached-property"></a>使用附加的屬性

一旦建立附加的屬性，便可以從 XAML 或程式碼。 在 XAML，做法是以指出 Common Language Runtime (CLR) 命名空間名稱，並選擇性組件名稱的命名空間宣告中宣告具有前置詞的命名空間。 如需詳細資訊，請參閱 < [XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)。

下列程式碼範例示範包含附加的屬性在相同的組件所參考的自訂類型的應用程式程式碼內定義的自訂類型的 XAML 命名空間：

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

當在特定的控制項上設定附加的屬性，為下列 XAML 程式碼範例所示，然後使用命名空間宣告：

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

對等的 C# 程式碼會顯示在以下程式碼範例中：

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consuming-an-attached-property-with-a-style"></a>使用附加的屬性的樣式

附加的屬性也可以加入至控制項中，但會由樣式。 下列 XAML 程式碼範例所示*明確*使用的樣式`HasShadow`附加屬性，可以套用至[ `Label` ](xref:Xamarin.Forms.Label)控制項：

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

[ `Style` ](xref:Xamarin.Forms.Style)可以套用至[ `Label` ](xref:Xamarin.Forms.Label)藉由設定其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)屬性`Style`執行個體使用`StaticResource`標記延伸，如下列程式碼範例所示：

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

如需有關樣式的詳細資訊，請參閱 <<c0> [ 樣式](~/xamarin-forms/user-interface/styles/index.md)。

## <a name="advanced-scenarios"></a>進階的案例

當建立附加的屬性，有幾個可以設定以啟用進階附加的屬性案例的選擇性參數。 這包括偵測屬性變更、 驗證屬性值，以及將強制轉型屬性值。 如需詳細資訊，請參閱 <<c0> [ 進階案例](~/xamarin-forms/xaml/bindable-properties.md#advanced)。

## <a name="summary"></a>總結

本文章提供附加屬性的簡介，並示範如何建立和使用它們。 附加的屬性是屬性的特殊類型的可繫結，但連結至其他物件，而且可辨識在 XAML 中的一個類別中定義為包含類別，以及以句號分隔屬性名稱的屬性。

## <a name="related-links"></a>相關連結

- [可繫結的屬性](~/xamarin-forms/xaml/bindable-properties.md)
- [XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)
- [延伸陰影效果 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)
- [BindableProperty](xref:Xamarin.Forms.BindableProperty)
- [BindableObject](xref:Xamarin.Forms.BindableObject)
