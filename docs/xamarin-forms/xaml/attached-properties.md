---
title: 附加屬性
description: 本文提供附加屬性的簡介，並示範如何建立和使用這些屬性。
ms.prod: xamarin
ms.assetid: 6E9DCDC3-A0E4-46A6-BAA9-4FEB6DF8A5A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/02/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 61edbb347b4d3466d1ca756208adb5d173d63b34
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561543"
---
# <a name="attached-properties"></a>附加屬性

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)


附加的屬性可讓物件指派本身的類別未定義之屬性的值。 例如，子項目可以使用附加屬性，通知其父元素如何在使用者介面中呈現它們。 控制項可透過 [`Grid`](xref:Xamarin.Forms.Grid) 設定 `Grid.Row` 和附加屬性，來指定子系的資料列和資料行 `Grid.Column` 。 `Grid.Row` 和 `Grid.Column` 都是附加屬性，因為它們是在子系的專案上設定 `Grid` ，而不是在本身的子系上進行設定 `Grid` 。

在下列案例中，可系結屬性應該實作為附加屬性：

- 當需要為定義類別以外的類別提供屬性設定機制時。
- 當類別代表需要與其他類別輕鬆整合的服務時。

如需可系結屬性的詳細資訊，請參閱可系結 [屬性](~/xamarin-forms/xaml/bindable-properties.md)。

## <a name="create-an-attached-property"></a>建立附加屬性

建立附加屬性的程式如下所示：

1. [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)使用其中一個方法多載來建立實例 [`CreateAttached`](xref:Xamarin.Forms.BindableProperty.CreateAttached*) 。
1. 提供 `static` `Get` *propertyname*和 `Set` *propertyname*方法做為附加屬性的存取子。

### <a name="create-a-property"></a>建立屬性

建立附加屬性以用於其他類型時，建立屬性的類別不需要衍生自 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 。 但是，存取子的 *目標* 屬性應該是或衍生自 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 。

您可以宣告類型的屬性來建立附加屬性 `public static readonly` [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 。 可系結屬性應該設定為其中一個 [ `BindableProperty.CreateAttached` ] (x：的傳回值 Xamarin.Forms 。BindableProperty. CreateAttached (System.string、System.string、system.object、 Xamarin.Forms system.string、。BindingMode， Xamarin.Forms 。BindableProperty. ValidateValueDelegate、 Xamarin.Forms 。BindableProperty. BindingPropertyChangedDelegate、 Xamarin.Forms 。BindableProperty. BindingPropertyChangingDelegate、 Xamarin.Forms 。BindableProperty. CoerceValueDelegate、 Xamarin.Forms 。BindableProperty. CreateDefaultValueDelegate) # A3 方法多載。 宣告應該在擁有類別的主體內，但在任何成員定義之外。

> [!IMPORTANT]
> 附加屬性的命名慣例是附加的屬性識別碼必須符合方法中指定的屬性名稱 `CreateAttached` ，並附加 "property"。

下列程式碼顯示附加屬性的範例：

```csharp
public static readonly BindableProperty HasShadowProperty =
  BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false);
```

這會建立名為的附加屬性 `HasShadowProperty` ，類型為 `bool` 。 屬性是由類別所擁有 `ShadowEffect` ，而且具有的預設值 `false` 。

如需建立可系結屬性的詳細資訊，包括可在建立期間指定的參數，請參閱建立可系結 [屬性](~/xamarin-forms/xaml/bindable-properties.md#consume-a-bindable-property)。

### <a name="create-accessors"></a>建立存取子

需要靜態 `Get` *PropertyName*和 `Set` *PropertyName*方法做為附加屬性的存取子，否則屬性系統將無法使用附加屬性。 `Get` *PropertyName*存取子應符合下列簽章：

```csharp
public static valueType GetPropertyName(BindableObject target)
```

`Get` *PropertyName*存取子應傳回附加屬性之對應欄位中包含的值 `BindableProperty` 。 這可以藉由呼叫 [ `GetValue` ] (x：來達成 Xamarin.Forms 。BindableObject (Xamarin.Forms 。BindableProperty) # A3 方法，傳入可在其上取得值的可系結屬性識別碼，然後將產生的值轉換為所需的類型。

`Set` *PropertyName*存取子應符合下列簽章：

```csharp
public static void SetPropertyName(BindableObject target, valueType value)
```

`Set` *PropertyName*存取子應 `BindableProperty` 針對附加屬性設定對應欄位的值。 這可以藉由呼叫 [ `SetValue` ] (x：來達成 Xamarin.Forms 。BindableObject (Xamarin.Forms 。BindableProperty，System.object) # A3 方法，傳入要設定值的可系結屬性識別碼，以及要設定的值。

針對這兩個存取子， *目標* 物件必須是或衍生自 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 。

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

一旦建立附加的屬性，就可以從 XAML 或程式碼取用它。 在 XAML 中，您可以宣告具有前置詞的命名空間，並以命名空間宣告指出 Common Language Runtime (CLR) 命名空間名稱，以及選擇性的元件名稱來達成這個目的。 如需詳細資訊，請參閱 [XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)。

下列程式碼範例示範包含附加屬性之自訂類型的 XAML 命名空間，該附加屬性是在與參考自訂類型的應用程式程式碼相同的元件中定義：

```xaml
<ContentPage ... xmlns:local="clr-namespace:EffectsDemo" ...>
  ...
</ContentPage>
```

然後，在特定控制項上設定附加屬性時，會使用命名空間宣告，如下列 XAML 程式碼範例所示：

```xaml
<Label Text="Label Shadow Effect" local:ShadowEffect.HasShadow="true" />
```

對等的 C# 程式碼會顯示在以下程式碼範例中：

```csharp
var label = new Label { Text = "Label Shadow Effect" };
ShadowEffect.SetHasShadow (label, true);
```

### <a name="consume-an-attached-property-with-a-style"></a>使用樣式的附加屬性

附加屬性也可以透過樣式加入至控制項。 下列 XAML 程式碼範例顯示使用*explicit* `HasShadow` 附加屬性（可套用至控制項）的明確樣式 [`Label`](xref:Xamarin.Forms.Label) ：

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="true" />
  </Style.Setters>
</Style>
```

[`Style`](xref:Xamarin.Forms.Style)可以套用至，方法是 [`Label`](xref:Xamarin.Forms.Label) [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 使用標記延伸將其屬性設定為 `Style` 實例 `StaticResource` ，如下列程式碼範例所示：

```xaml
<Label Text="Label Shadow Effect" Style="{StaticResource ShadowEffectStyle}" />
```

如需樣式的詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)。

## <a name="advanced-scenarios"></a>進階案例

建立附加的屬性時，有一些選擇性參數可設定為啟用 advanced 附加屬性案例。 這包括偵測屬性變更、驗證屬性值，以及強制屬性值。 如需詳細資訊，請參閱 [Advanced 案例](~/xamarin-forms/xaml/bindable-properties.md#advanced-scenarios)。

## <a name="related-links"></a>相關連結

- [可繫結的屬性](~/xamarin-forms/xaml/bindable-properties.md)
- [XAML 命名空間](~/xamarin-forms/xaml/namespaces.md)
- [Shadow Effect (Samples)](/samples/xamarin/xamarin-forms-samples/effects-shadoweffect) (陰影效果 (範例))
- [BindableProperty API](xref:Xamarin.Forms.BindableProperty)
- [BindableObject API](xref:Xamarin.Forms.BindableObject)