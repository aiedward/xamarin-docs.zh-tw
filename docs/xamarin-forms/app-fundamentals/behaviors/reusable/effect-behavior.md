---
title: 可重複使用 EffectBehavior
description: 行為是實用的方法加入至控制項，移除必不可少效果處理從程式碼後置檔案的程式碼的影響。 這篇文章示範如何使用 Xamarin.Forms 行為新增至控制項的效果。
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 1ce7eda6f556041cbffc3793b00e8e2cba44d3d0
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995777"
---
# <a name="reusable-effectbehavior"></a>可重複使用 EffectBehavior

_行為是實用的方法加入至控制項，移除必不可少效果處理從程式碼後置檔案的程式碼的影響。這篇文章示範如何使用 Xamarin.Forms 行為新增至控制項的效果。_

## <a name="overview"></a>總覽

`EffectBehavior`類別是可重複使用 Xamarin.Forms 自訂行為，將它新增[ `Effect` ](xref:Xamarin.Forms.Effect)執行個體，以控制當行為附加至控制項，並移除`Effect`執行個體時的行為控制項與中斷連結。

下列的行為屬性必須設定為使用行為：

- **群組**– windows 7 [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute)效果類別的屬性。
- **名稱**– windows 7 [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute)效果類別的屬性。

如需效果的詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

## <a name="creating-the-behavior"></a>建立行為

`EffectBehavior`類別衍生自[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)類別，其中`T`是[ `View` ](xref:Xamarin.Forms.View)。 這表示`EffectBehavior`類別可以附加至任何 Xamarin.Forms 控制項。

### <a name="implementing-bindable-properties"></a>實作可繫結屬性

`EffectBehavior`類別會定義兩個[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)執行個體，用來加入[ `Effect` ](xref:Xamarin.Forms.Effect)至當行為附加至控制項的控制項。 這些屬性是以下列程式碼範例所示：

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

時`EffectBehavior`會取用`Group`屬性應設為值[ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute)屬性的效果。 颾魤 ㄛ`Name`屬性應設為值[ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute)效果類別的屬性。

### <a name="implementing-the-overrides"></a>實作覆寫

`EffectBehavior`類別會覆寫[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))並[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)類別，如下列程式碼所示範例：

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

[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))方法會藉由呼叫來執行安裝程式`AddEffect`方法，在附加的控制項，做為參數傳遞。 [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法會執行清除，藉由呼叫`RemoveEffect`方法，在附加的控制項，做為參數傳遞。

### <a name="implementing-the-behavior-functionality"></a>實作行為的功能

行為的用途是要新增[ `Effect` ](xref:Xamarin.Forms.Effect)中定義`Group`並`Name`屬性，用以控制當行為附加至控制項，並移除`Effect`時的行為控制項與中斷連結。 核心行為功能是以下列程式碼範例所示：

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

`AddEffect`方法執行以回應`EffectBehavior`附加至控制項，以及它收到連接的控制項做為參數。 方法接著會將擷取的效果至控制項的[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。 `RemoveEffect`方法執行以回應`EffectBehavior`要卸離控制項，以及它從接收附加的控制項，做為參數。 方法接著會移除效果從控制項的[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。

`GetEffect`方法會使用[ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String))方法來擷取[ `Effect` ](xref:Xamarin.Forms.Effect)。 效果是透過串連所在`Group`和`Name`屬性值。 如果一個平台不提供效果`Effect.Resolve`方法會傳回非`null`值。

## <a name="consuming-the-behavior"></a>使用行為

`EffectBehavior`類別可以附加至[ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors)集合的控制項，如下列 XAML 程式碼範例所示：

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

`Group`並`Name`行為屬性會設為值的[ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute)並[ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute)效果類別中每個平台專屬的屬性專案。

在執行階段，當行為附加至[ `Label` ](xref:Xamarin.Forms.Label)控制`Xamarin.LabelShadowEffect`會加入至控制項的[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。 這會導致要新增至所顯示的文字陰影`Label`控制項，如下列螢幕擷取畫面所示：

![](effect-behavior-images/screenshots.png "範例應用程式及 EffectsBehavior")

使用這種行為來新增和移除控制項的效果的優點是必不可少效果處理程式碼可以從程式碼後置檔案中移除。

## <a name="summary"></a>總結

這篇文章會示範使用行為新增至控制項的效果。 `EffectBehavior`類別是可重複使用 Xamarin.Forms 自訂行為，將它新增[ `Effect` ](xref:Xamarin.Forms.Effect)執行個體，以控制當行為附加至控制項，並移除`Effect`執行個體時的行為控制項與中斷連結。


## <a name="related-links"></a>相關連結

- [Effects](~/xamarin-forms/app-fundamentals/effects/index.md)
- [影響行為 （範例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)
- [行為](xref:Xamarin.Forms.Behavior)
- [行為<T>](xref:Xamarin.Forms.Behavior`1)
