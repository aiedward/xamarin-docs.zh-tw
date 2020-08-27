---
title: 可重複使用的 EffectBehavior
description: 對於將效果新增至控制項，行為是很實用的方法，可從程式碼後置檔案中移除以定案效果方式處理的程式碼。 本文將示範如何建立和使用 Xamarin.Forms 行為，以將效果新增至控制項。
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7ecc094abf816f11dcb16d9eba7d715c8c4e4ab1
ms.sourcegitcommit: a003b036f6fb83818e2ecc9c72a641e3aeb373bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88964812"
---
# <a name="reusable-effectbehavior"></a>可重複使用的 EffectBehavior

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)

_行為是將效果新增至控制項的實用方法，可從程式碼後端檔案中移除定案板效果處理常式代碼。本文將示範如何建立和使用 Xamarin.Forms 行為，以將效果新增至控制項。_

## <a name="overview"></a>概觀

`EffectBehavior`類別是可重複使用的 Xamarin.Forms 自訂行為，可在 [`Effect`](xref:Xamarin.Forms.Effect) 行為附加至控制項時，將實例新增至控制項，並在 `Effect` 行為與控制項中斷連結時移除實例。

您必須將下列行為屬性 (Property) 設定為使用行為：

- **群組** – 效果類別的 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 屬性 (Attribute) 值。
- **名稱** – 效果類別的 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 屬性 (Attribute) 值。

如需效果的詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

> [!NOTE]
> `EffectBehavior`是自訂類別，可以在[效果行為範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)中找到，而且不屬於的一部分 Xamarin.Forms 。

## <a name="creating-the-behavior"></a>建立行為

`EffectBehavior`類別衍生自 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 類別，其中 `T` 是 [`View`](xref:Xamarin.Forms.View) 。 這表示 `EffectBehavior` 類別可以附加至任何 Xamarin.Forms 控制項。

### <a name="implementing-bindable-properties"></a>實作可繫結屬性

`EffectBehavior`類別會定義兩個 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 實例， [`Effect`](xref:Xamarin.Forms.Effect) 當行為附加至控制項時，這些實例會用來將加入至控制項。 下列程式碼範例顯示這些屬性：

```csharp
public class EffectBehavior : Behavior<View>
{
  public static readonly BindableProperty GroupProperty =
    BindableProperty.Create ("Group", typeof(string), typeof(EffectBehavior), null);
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(EffectBehavior), null);

  public string Group {
    get { return (string)GetValue (GroupProperty); }
    set { SetValue (GroupProperty, value); }
  }

  public string Name {
    get { return(string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }
  ...
}
```

當 `EffectBehavior` 使用時，應該將 `Group` 屬性設定為 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 效果的屬性值。 此外， `Name` 屬性應該設定為 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 效果類別的屬性值。

### <a name="implementing-the-overrides"></a>實作覆寫

`EffectBehavior`類別會覆寫 [ `OnAttachedTo` ] (x： Xamarin.Forms 。行為 `1.OnAttachedTo(Xamarin.Forms.BindableObject)) and [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom (Xamarin.Forms 。BindableObject 類別) # A3 方法 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) ，如下列程式碼範例所示：

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  protected override void OnAttachedTo (BindableObject bindable)
  {
    base.OnAttachedTo (bindable);
    AddEffect (bindable as View);
  }

  protected override void OnDetachingFrom (BindableObject bindable)
  {
    RemoveEffect (bindable as View);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

[ `OnAttachedTo` ] (x： Xamarin.Forms 。行為 `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method performs setup by calling the ` AddEffect ` method, passing in the attached control as a parameter. The [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom (Xamarin.Forms 。BindableObject) # A3 方法會藉由呼叫 `RemoveEffect` 方法（以參數形式傳入附加的控制項）來執行清除。

### <a name="implementing-the-behavior-functionality"></a>實作行為功能

行為的目的是在 [`Effect`](xref:Xamarin.Forms.Effect) `Group` `Name` 行為附加至控制項時，將和屬性中定義的新增至控制項，並在行為與控制項中斷連結 `Effect` 時移除。 下列程式碼範例顯示核心行為功能：

```csharp
public class EffectBehavior : Behavior<View>
{
  ...
  void AddEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Add (GetEffect ());
    }
  }

  void RemoveEffect (View view)
  {
    var effect = GetEffect ();
    if (effect != null) {
      view.Effects.Remove (GetEffect ());
    }
  }

  Effect GetEffect ()
  {
    if (!string.IsNullOrWhiteSpace (Group) && !string.IsNullOrWhiteSpace (Name)) {
      return Effect.Resolve (string.Format ("{0}.{1}", Group, Name));
    }
    return null;
  }
}
```

執行 `AddEffect` 方法以回應要附加至控制項的 `EffectBehavior`，其會接收附加的控制項作為參數。 方法接著會將抓取的效果新增至控制項的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合。 執行 `RemoveEffect` 方法以回應要從控制項中斷連結的 `EffectBehavior`，其會接收附加的控制項作為參數。 然後，方法會從控制項的集合中移除效果 [`Effects`](xref:Xamarin.Forms.Element.Effects) 。

`GetEffect`方法會使用 [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) 方法來取得 [`Effect`](xref:Xamarin.Forms.Effect) 。 效果是透過串連 `Group` 和 `Name` 屬性值來定位。 如果某個平台不提供效果，則 `Effect.Resolve` 方法會傳回非 `null` 值。

## <a name="consuming-the-behavior"></a>使用行為

`EffectBehavior`類別可以附加至 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 控制項的集合，如下列 XAML 程式碼範例所示：

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Behaviors>
    <local:EffectBehavior Group="Xamarin" Name="LabelShadowEffect" />
  </Label.Behaviors>
</Label>
```

對等的 C# 程式碼會顯示在以下程式碼範例中：

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};
label.Behaviors.Add (new EffectBehavior {
  Group = "Xamarin",
  Name = "LabelShadowEffect"
});
```

`Group`行為的和 `Name` 屬性會設定為 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 每個平臺特定專案中效果類別的和屬性值。

在執行時間，當行為附加至控制項時 [`Label`](xref:Xamarin.Forms.Label) ，將會 `Xamarin.LabelShadowEffect` 加入控制項的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合中。 這會導致將陰影新增至 `Label` 控制項所顯示的文字，如下列螢幕擷取畫面所示：

![使用 EffectsBehavior 的範例應用程式](effect-behavior-images/screenshots.png)

使用此行為在控制項中新增和移除效果的優點是：可以從程式碼後置檔案中移除以定案效果方式處理的程式碼。

## <a name="summary"></a>總結

本文示範如何使用行將效果新增至控制項。 `EffectBehavior`類別是可重複使用的 Xamarin.Forms 自訂行為，可在 [`Effect`](xref:Xamarin.Forms.Effect) 行為附加至控制項時，將實例新增至控制項，並在 `Effect` 行為與控制項中斷連結時移除實例。

## <a name="related-links"></a>相關連結

- [效果](~/xamarin-forms/app-fundamentals/effects/index.md)
- [效果行為 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)
- [行為](xref:Xamarin.Forms.Behavior)
- [行為 &lt; T&gt;](xref:Xamarin.Forms.Behavior`1)
