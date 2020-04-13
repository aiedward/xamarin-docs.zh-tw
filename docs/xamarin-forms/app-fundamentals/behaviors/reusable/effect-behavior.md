---
title: 可重複使用的 EffectBehavior
description: 對於將效果新增至控制項，行為是很實用的方法，可從程式碼後置檔案中移除以定案效果方式處理的程式碼。 本文示範如何建立及使用 Xamarin.Forms 行為來將效果新增至控制項。
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: ca03dce3bd39664a07b7bf56d22d7c2e000e931f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771989"
---
# <a name="reusable-effectbehavior"></a>可重複使用的 EffectBehavior

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)

_行為是向控制項添加效果的有用方法,從代碼後面的檔中刪除鍋爐板效果處理代碼。本文演示了創建和使用 Xamarin.Forms 行為,以向控制項添加效果。_

## <a name="overview"></a>概觀

類`EffectBehavior`是可重用的 Xamarin.Forms 自定義[`Effect`](xref:Xamarin.Forms.Effect)行為,當 行為附加到控制項時將實例添加到控制項,並在行為從控制`Effect`項分離時刪除 該實例。

您必須將下列行為屬性 (Property) 設定為使用行為：

- **群組** – 效果類別的 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 屬性 (Attribute) 值。
- **名稱** – 效果類別的 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 屬性 (Attribute) 值。

如需效果的詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

> [!NOTE]
> `EffectBehavior` 是自訂類別，並不屬於 Xamarin.Forms 的一部分；您可以在[效果行為範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)中找到。

## <a name="creating-the-behavior"></a>建立行為

類別`EffectBehavior`派生自[`Behavior<T>`](xref:Xamarin.Forms.Behavior`1)類別,`T`其中是[`View`](xref:Xamarin.Forms.View)。 這表示 `EffectBehavior` 類別可以附加至任何 Xamarin.Forms 控制項。

### <a name="implementing-bindable-properties"></a>實作可繫結屬性

類別`EffectBehavior`定義兩[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)個實體,當行為附加到控制項時,它們用於[`Effect`](xref:Xamarin.Forms.Effect)向控制項添加 。 下列程式碼範例顯示這些屬性：

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

`EffectBehavior`使用 時,應`Group`將 屬性設置[`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute)為效果 的屬性的值。 此外,`Name`屬性應設置為效果[`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute)類 的屬性的值。

### <a name="implementing-the-overrides"></a>實作覆寫

類別`EffectBehavior`重寫[`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))[`Behavior<T>`](xref:Xamarin.Forms.Behavior`1)類別[`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))的和方法,如以下代碼範例所示:

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

該方法[`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))透過調用方法執行設定`AddEffect`, 將附加的控制項作為參數傳入。 該方法[`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))通過調用方法執行清理`RemoveEffect`, 將附加的控制項作為參數傳入。

### <a name="implementing-the-behavior-functionality"></a>實作行為功能

行為的目的是在將行為附加到控制項時會[`Effect`](xref:Xamarin.Forms.Effect)`Group`將`Name`屬性中定義的 將 add 加入到控制項中,並在行為從`Effect`控制檔分離 時移除 。 下列程式碼範例顯示核心行為功能：

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

執行 `AddEffect` 方法以回應要附加至控制項的 `EffectBehavior`，其會接收附加的控制項作為參數。 此方法然後將檢索的效果加入到控制的集合[`Effects`](xref:Xamarin.Forms.Element.Effects)。 執行 `RemoveEffect` 方法以回應要從控制項中斷連結的 `EffectBehavior`，其會接收附加的控制項作為參數。 然後,該方法從控件[`Effects`](xref:Xamarin.Forms.Element.Effects)的集合中刪除效果。

該方法`GetEffect`使用[`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String))方法 檢[`Effect`](xref:Xamarin.Forms.Effect)索 。 效果是透過串連 `Group` 和 `Name` 屬性值來定位。 如果某個平台不提供效果，則 `Effect.Resolve` 方法會傳回非 `null` 值。

## <a name="consuming-the-behavior"></a>使用行為

類別`EffectBehavior`可以附加到控制[`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)檔的集合,例如 XAML 程式碼範例的範例 :

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

行為`Group``Name`的 和 屬性設置為每個特定於平臺的[`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute)專案[`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute)的效果類 和 屬性的值。

在執行時,當行為附加到控制項時[`Label`](xref:Xamarin.Forms.Label),`Xamarin.LabelShadowEffect`將添加到控制項的[`Effects`](xref:Xamarin.Forms.Element.Effects)集合中。 這會導致將陰影新增至 `Label` 控制項所顯示的文字，如下列螢幕擷取畫面所示：

![](effect-behavior-images/screenshots.png "Sample Application with EffectsBehavior")

使用此行為在控制項中新增和移除效果的優點是：可以從程式碼後置檔案中移除以定案效果方式處理的程式碼。

## <a name="summary"></a>總結

本文示範如何使用行將效果新增至控制項。 類`EffectBehavior`是可重用的 Xamarin.Forms 自定義[`Effect`](xref:Xamarin.Forms.Effect)行為,當 行為附加到控制項時將實例添加到控制項,並在行為從控制`Effect`項分離時刪除 該實例。

## <a name="related-links"></a>相關連結

- [影響](~/xamarin-forms/app-fundamentals/effects/index.md)
- [效果行為 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-effectbehavior)
- [行為](xref:Xamarin.Forms.Behavior)
- [行為&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
