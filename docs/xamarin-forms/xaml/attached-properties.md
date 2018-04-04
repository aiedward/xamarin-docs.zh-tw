---
title: 附加屬性
description: 附加的屬性是一種特殊的某一個類別中定義，但連接到其他物件，可繫結屬性可辨識在 XAML 中做為屬性包含的類別和屬性名稱以逗號分隔。 本文章提供附加屬性的簡介，並示範如何建立及使用它們。
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/02/2016
ms.openlocfilehash: 5c903a39e5569c7ffedfff8eb8e6b0bd4071be9d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="attached-properties"></a>附加屬性

_附加的屬性是一種特殊的某一個類別中定義，但連接到其他物件，可繫結屬性可辨識在 XAML 中做為屬性包含的類別和屬性名稱以逗號分隔。本文章提供附加屬性的簡介，並示範如何建立及使用它們。_

## <a name="overview"></a>總覽

附加屬性可以讓指派自己的類別未定義的屬性值的物件。 例如，子元素可以使用附加屬性，以通知它們的方式顯示在使用者介面及其父項目。 [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)控制項可讓資料列和資料行的設定，來指定的子系`Grid.Row`和`Grid.Column`附加屬性。 `Grid.Row` 和`Grid.Column`是附加的屬性，因為它們的子系的項目上設定`Grid`，而非在`Grid`本身。

可繫結屬性應該會實作為附加的屬性，在下列案例：

- 當需要將屬性設定機制類別以外定義類別。
- 當此類別代表的服務需要與其他類別整合。

如需可繫結屬性的詳細資訊，請參閱[可繫結屬性](~/xamarin-forms/xaml/bindable-properties.md)。

## <a name="creating-and-consuming-an-attached-property"></a>建立和使用附加的屬性

建立附加的屬性的程序如下所示：

1. 建立[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)與其中一個執行個體[ `CreateAttached` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateAttached/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法多載。
1. 提供`static` `Get` *PropertyName*和`Set` *PropertyName*附加屬性的存取子方法。

### <a name="creating-a-property"></a>建立屬性

建立時使用的附加的屬性上其他類型，其中建立屬性的類別不必是衍生自[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)。 不過，*目標*屬性存取子，應該或衍生自[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)。

您可以建立附加的屬性宣告`public static readonly`型別的屬性[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)。 可繫結的屬性應該設定為傳回的值，其中的[ `BindableProperty.CreateAttached` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableProperty.CreateAttached/p/System.String/System.Type/System.Type/System.Object/Xamarin.Forms.BindingMode/Xamarin.Forms.BindableProperty+ValidateValueDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangedDelegate/Xamarin.Forms.BindableProperty+BindingPropertyChangingDelegate/Xamarin.Forms.BindableProperty+CoerceValueDelegate/Xamarin.Forms.BindableProperty+CreateDefaultValueDelegate/)方法多載。 宣告應該是類別的在主控，但之外的任何成員定義的主體內。

下列程式碼顯示附加屬性的範例：

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

這會建立名為附加的屬性`HasShadow`，型別`bool`。 屬性由所擁有`ShadowEffect`類別，以及具有預設值是`false`。 附加屬性的命名慣例是附加的屬性的識別項必須符合在指定的屬性名稱`CreateAttached`方法，並包含附加至其 「 屬性 」。 因此，在上述範例中，附加的屬性的識別項是`HasShadowProperty`。

如需建立可繫結的屬性，包括您可以在建立期間，指定的參數的詳細資訊，請參閱[建立和使用可繫結屬性](~/xamarin-forms/xaml/bindable-properties.md#consuming-bindable-property)。

### <a name="creating-accessors"></a>建立存取子

靜態`Get` *PropertyName*和`Set` *PropertyName*都不需要附加屬性的存取子方法是，否則將無法使用屬性系統附加屬性。 `Get` *PropertyName*存取子，應該符合下列簽章：

```csharp
public static valueType GetPropertyName(BindableObject target)
```

`Get` *PropertyName*存取子應會傳回包含值，在對應`BindableProperty`附加屬性的欄位。 這可藉由呼叫[ `GetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.GetValue/p/Xamarin.Forms.BindableProperty/)方法中，傳入可繫結屬性的識別項，以取得值，然後再產生值轉換成所需的類型。

`Set` *PropertyName*存取子，應該符合下列簽章：

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

`Set` *PropertyName*存取子都應該設定的對應值`BindableProperty`附加屬性的欄位。 這可藉由呼叫[ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)方法，傳入要設定此值，而要設定的值可繫結屬性的識別項。

兩個存取子，*目標*物件應該或衍生自[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)。

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

一旦建立附加的屬性，它可以取用從 XAML 或程式碼。 在 XAML 中，這是具有前置詞的命名空間宣告與表示 Common Language Runtime (CLR) 命名空間名稱，並選擇性組件名稱的命名空間宣告來達成。 如需詳細資訊，請參閱[XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)。

下列程式碼範例示範包含附加的屬性，在相同的組件參考自訂類型的應用程式程式碼中定義的自訂類型的 XAML 命名空間：

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

當在特定的控制項上時使用的連接的屬性設定為下列 XAML 程式碼範例所示，然後可以使用命名空間宣告：

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

對等的 C# 程式碼會顯示在以下程式碼範例中：

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consuming-an-attached-property-with-a-style"></a>使用附加的屬性的樣式

附加的屬性也可以加入至控制項的樣式。 下列 XAML 程式碼範例所示*明確*使用樣式`HasShadow`附加屬性，可以套用至[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)控制項：

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)可以套用至[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)藉由設定其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性`Style`執行個體使用`StaticResource`標記延伸，如下列程式碼範例所示：

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

如需有關樣式的詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)。

## <a name="advanced-scenarios"></a>進階的案例

在建立附加的屬性時，有幾個可以設定以啟用進階附加的屬性的案例的選擇性參數。 這包括偵測屬性變更、 驗證屬性值和強制型轉屬性值。 如需詳細資訊，請參閱[進階案例](~/xamarin-forms/xaml/bindable-properties.md#advanced)。

## <a name="summary"></a>總結

本文章提供附加屬性的簡介，並示範如何建立及使用它們。 附加的屬性是屬性的一種特殊的可繫結，但附加至其他物件，而且可辨識在 XAML 中的某一個類別中定義為包含的類別和屬性名稱以逗號分隔的屬性。


## <a name="related-links"></a>相關連結

- [可繫結的屬性](~/xamarin-forms/xaml/bindable-properties.md)
- [XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)
- [陰影效果 （範例）](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
- [BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- [BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)
