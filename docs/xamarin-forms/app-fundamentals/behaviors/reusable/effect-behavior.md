---
title: 可重複使用 EffectBehavior
description: 這些行為屬於效果加入至控制項，移除鍋爐調色盤效果處理從程式碼後置檔案的程式碼很實用的方法。 本文示範如何使用 Xamarin.Forms 行為新增至控制項的效果。
ms.prod: xamarin
ms.assetid: A909B24D-960A-4023-AFF6-4B9256C55ADD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: a1612d1e87f0e05c859babd93fd03ac9a5736b47
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30785051"
---
# <a name="reusable-effectbehavior"></a>可重複使用 EffectBehavior

_這些行為屬於效果加入至控制項，移除鍋爐調色盤效果處理從程式碼後置檔案的程式碼很實用的方法。本文示範如何使用 Xamarin.Forms 行為新增至控制項的效果。_

## <a name="overview"></a>總覽

`EffectBehavior`類別是可重複使用 Xamarin.Forms 自訂行為加入[ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)控制項時，行為會附加到控制項，並移除執行個體`Effect`時的行為是執行個體中斷連接控制項。

下列行為屬性必須設定為使用行為：

- **群組**– 值[ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/)效果類別的屬性。
- **名稱**– 值[ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/)效果類別的屬性。

如需效果的詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

## <a name="creating-the-behavior"></a>建立行為

`EffectBehavior`類別衍生自[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)類別，其中`T`是[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)。 這表示`EffectBehavior`類別可以附加至任何 Xamarin.Forms 控制項。

### <a name="implementing-bindable-properties"></a>實作可繫結屬性

`EffectBehavior`類別會定義兩個[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)執行個體，用來新增[ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)控制項時的行為已附加至控制項。 這些屬性是以下列程式碼範例所示：

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

當`EffectBehavior`取用`Group`屬性應該設定的值[ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/)屬性的效果。 此外，`Name`屬性應該設定的值[ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/)效果類別的屬性。

### <a name="implementing-the-overrides"></a>實作覆寫

`EffectBehavior`類別覆寫[ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/)和[ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)類別，如下列程式碼所示範例：

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

[ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/)方法會藉由呼叫來執行安裝程式`AddEffect`方法，在附加的控制項，做為參數傳遞。 [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法呼叫會執行清除`RemoveEffect`方法，在附加的控制項，做為參數傳遞。

### <a name="implementing-the-behavior-functionality"></a>實作的行為的功能

行為的用途是要新增[ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)中定義`Group`和`Name`內容控制項時，行為會附加到控制項，並移除`Effect`時的行為中斷連接控制項。 核心行為的功能是以下列程式碼範例所示：

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

`AddEffect`方法執行以回應`EffectBehavior`附加至控制項，以及它收到連接的控制項做為參數。 方法接著會將擷取的效果至控制項的[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合。 `RemoveEffect`方法執行以回應`EffectBehavior`正在中斷連結從控制項，以及它收到連接的控制項做為參數。 方法接著會移除效果控制項的[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合。

`GetEffect`方法會使用[ `Effect.Resolve` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.Resolve/p/System.String/)方法來擷取[ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)。 效果所在的串連透過`Group`和`Name`屬性值。 如果平台不提供效果`Effect.Resolve`方法會傳回非`null`值。

## <a name="consuming-the-behavior"></a>使用行為

`EffectBehavior`類別可以附加至[ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/)集合的控制項，如下列 XAML 程式碼範例所示：

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

`Group`和`Name`行為的屬性設定為值的[ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/)和[ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/)效果類別中每個平台專屬的屬性專案。

在執行階段時的行為已附加至[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)控制項，`Xamarin.LabelShadowEffect`會加入至控制項的[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合。 這會導致新增至所顯示的文字陰影`Label`控制，如下列螢幕擷取畫面所示：

![](effect-behavior-images/screenshots.png "EffectsBehavior 範例應用程式")

使用此行為來加入和移除控制項的效果的優點是鍋爐調色盤效果處理程式碼可以從程式碼後置檔案中移除。

## <a name="summary"></a>總結

本文示範使用行為新增至控制項的效果。 `EffectBehavior`類別是可重複使用 Xamarin.Forms 自訂行為加入[ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)控制項時，行為會附加到控制項，並移除執行個體`Effect`時的行為是執行個體中斷連接控制項。


## <a name="related-links"></a>相關連結

- [Effects](~/xamarin-forms/app-fundamentals/effects/index.md)
- [影響行為 （範例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/effectbehavior/)
- [Behavior](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [行為<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
