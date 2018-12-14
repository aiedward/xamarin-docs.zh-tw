---
title: 可重複使用的 EffectBehavior
description: 對於將效果新增至控制項，行為是很實用的方法，可從程式碼後置檔案中移除以定案效果方式處理的程式碼。 本文示範如何建立及使用 Xamarin.Forms 行為來將效果新增至控制項。
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 2696f0103ce1aa969039c982fb9b82f89b37811e
ms.sourcegitcommit: 06a52ac36031d0d303ac7fc8163a59c178799c80
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2018
ms.locfileid: "50911589"
---
# <a name="reusable-effectbehavior"></a>可重複使用的 EffectBehavior

對於將效果新增至控制項，行為是很實用的方法，可從程式碼後置檔案中移除以定案效果方式處理的程式碼。本文示範如何建立及使用 Xamarin.Forms 行為來將效果新增至控制項。

## <a name="overview"></a>總覽

`EffectBehavior` 類別是可重複使用的 Xamarin.Forms 自訂行為，其可在行為附加至控制項時，將 [`Effect`](xref:Xamarin.Forms.Effect) 執行個體新增至控制項，並在行為與控制項中斷連結時，移除 `Effect` 執行個體。

您必須將下列行為屬性 (Property) 設定為使用行為：

- **群組** – 效果類別的 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 屬性 (Attribute) 值。
- **名稱** – 效果類別的 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 屬性 (Attribute) 值。

如需效果的詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

> [!NOTE]
> `EffectBehavior` 是自訂類別，並不屬於 Xamarin.Forms 的一部分；您可以在[效果行為範例](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)中找到。

## <a name="creating-the-behavior"></a>建立行為

`EffectBehavior` 類別衍生自 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 類別，其中 `T` 是 [`View`](xref:Xamarin.Forms.View)。 這表示 `EffectBehavior` 類別可以附加至任何 Xamarin.Forms 控制項。

### <a name="implementing-bindable-properties"></a>實作可繫結屬性

`EffectBehavior` 類別會定義兩個 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 執行個體，可在行為附加至控制項時，用來將 [`Effect`](xref:Xamarin.Forms.Effect) 新增至控制項。 下列程式碼範例顯示這些屬性：

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

使用 `EffectBehavior` 時，應該將 `Group` 屬性 (Property) 設定為值效果的 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 屬性 (Attribute) 值。 此外，還應該將 `Name` 屬性 (Property) 設定為值效果類別的 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 屬性 (Attribute) 值。

### <a name="implementing-the-overrides"></a>實作覆寫

`EffectBehavior` 類別可覆寫 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 類別的 [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) 和 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法，如下列程式碼範例所示：

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

[`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) 方法會呼叫 `AddEffect` 方法，並將附加的控制項作為參數傳入，以執行設定作業。 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法會呼叫 `RemoveEffect` 方法，並將附加的控制項作為參數傳入，以執行清除作業。

### <a name="implementing-the-behavior-functionality"></a>實作行為功能

行為的目的是要在行為附加至控制項時，將 `Group` 和 `Name` 屬性中定義的 [`Effect`](xref:Xamarin.Forms.Effect) 新增至控制項，並在行為與控制項中斷連結時移除 `Effect`。 下列程式碼範例顯示核心行為功能：

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

執行 `AddEffect` 方法以回應要附加至控制項的 `EffectBehavior`，其會接收附加的控制項作為參數。 此方法接著會將擷取的效果新增至控制項的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合。 執行 `RemoveEffect` 方法以回應要從控制項中斷連結的 `EffectBehavior`，其會接收附加的控制項作為參數。 此方法接著會從控制項的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合中移除效果。

`GetEffect` 方法使用 [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) 方法來擷取此[`Effect`](xref:Xamarin.Forms.Effect)。 效果是透過串連 `Group` 和 `Name` 屬性值來定位。 如果某個平台不提供效果，則 `Effect.Resolve` 方法會傳回非 `null` 值。

## <a name="consuming-the-behavior"></a>使用行為

您可以將 `EffectBehavior` 類別附加至控制項的 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 集合，如下列 XAML 程式碼範例所示：

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

行為的 `Group` 和 `Name` 屬性 (Property) 會設定為每個平台特定專案中效果類別的 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 和 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 屬性 (Attribute) 值。

在執行階段中，當行為附加至 [`Label`](xref:Xamarin.Forms.Label) 控制項時，`Xamarin.LabelShadowEffect` 會新增至控制項的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合。 這會導致將陰影新增至 `Label` 控制項所顯示的文字，如下列螢幕擷取畫面所示：

![](effect-behavior-images/screenshots.png "使用 EffectsBehavior 的範例應用程式")

使用此行為在控制項中新增和移除效果的優點是：可以從程式碼後置檔案中移除以定案效果方式處理的程式碼。

## <a name="summary"></a>總結

本文示範如何使用行將效果新增至控制項。 `EffectBehavior` 類別是可重複使用的 Xamarin.Forms 自訂行為，其可在行為附加至控制項時，將 [`Effect`](xref:Xamarin.Forms.Effect) 執行個體新增至控制項，並在行為與控制項中斷連結時，移除 `Effect` 執行個體。


## <a name="related-links"></a>相關連結

- [Effects](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Effect Behavior (Samples)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/) (效果行為 (範例))
- [Behavior](xref:Xamarin.Forms.Behavior)
- [Behavior<T>](xref:Xamarin.Forms.Behavior`1)
