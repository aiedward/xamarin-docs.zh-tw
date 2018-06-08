---
title: Xamarin.Forms 行為
description: Xamarin.Forms 行為由衍生自行為或行為<T>類別。 本文將示範如何建立和使用 Xamarin.Forms 行為。
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 3a86e7713620eff90db995941eb35df7bc393a76
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848287"
---
# <a name="xamarinforms-behaviors"></a>Xamarin.Forms 行為

_Xamarin.Forms 行為由衍生自行為或行為<T>類別。本文將示範如何建立和使用 Xamarin.Forms 行為。_

## <a name="overview"></a>總覽

建立 Xamarin.Forms 行為的程序如下所示：

1. 建立繼承自一個類別[ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)或[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)類別，其中`T`行為應該套用至控制項的類型。
1. 覆寫[ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/)方法，以執行任何必要的安裝程式。
1. 覆寫[ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法，以執行任何必要的清除。
1. 實作的核心功能的行為。

這會導致下列的程式碼範例所示的結構：

```csharp
public class CustomBehavior : Behavior<View>
{
    protected override void OnAttachedTo (View bindable)
    {
        base.OnAttachedTo (bindable);
        // Perform setup
    }

    protected override void OnDetachingFrom (View bindable)
    {
        base.OnDetachingFrom (bindable);
        // Perform clean up
    }

    // Behavior implementation
}
```

[ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/)行為已附加至控制項之後，立即引發方法。 這個方法會接收的控制項的連接，並可用來註冊事件處理常式，或執行其他安裝程式所需以支援行為功能的參考。 例如，您無法訂閱至控制項上的事件。 行為的功能，則會實作事件的事件處理常式。

[ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)時從控制項移除時的行為，方法會引發。 這個方法會接收的附加時，它用來執行任何必要的清除控制項的參考。 例如，您無法取消訂閱事件上的控制項，以防止記憶體流失。

然後可以使用行為附加到[ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/)適當的控制項集合。

## <a name="creating-a-xamarinforms-behavior"></a>建立 Xamarin.Forms 行為

範例應用程式示範`NumericValidationBehavior`，這為使用者所輸入的值會反白顯示[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)如果不是以紅色控制`double`。 行為是以下列程式碼範例所示：

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    protected override void OnAttachedTo(Entry entry)
    {
        entry.TextChanged += OnEntryTextChanged;
        base.OnAttachedTo(entry);
    }

    protected override void OnDetachingFrom(Entry entry)
    {
        entry.TextChanged -= OnEntryTextChanged;
        base.OnDetachingFrom(entry);
    }

    void OnEntryTextChanged(object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

`NumericValidationBehavior`衍生自[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)類別，其中`T`是[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)。 [ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/)方法註冊的事件處理常式[ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/)事件，與[ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法取消註冊`TextChanged`事件以避免記憶體流失。 所提供的核心功能的行為`OnEntryTextChanged`方法，它會剖析成使用者所輸入的值`Entry`，並設定[ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/)屬性為紅色，如果此值不`double`。

> [!NOTE]
> Xamarin.Forms 不會設定`BindingContext`的行為，因為可以共用行為，而且套用至多個控制項所有樣式。

## <a name="consuming-a-xamarinforms-behavior"></a>使用 Xamarin.Forms 行為

Xamarin.Forms 中的每個控制項具有[ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/)集合的一個或多個可以將行為新增，如下列 XAML 程式碼範例所示：

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

對等項目[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)在 C# 會顯示在下列程式碼範例：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

在執行階段行為會根據行為實作回應與控制項互動。 下列螢幕擷取畫面會示範回應輸入無效的行為：

[![](creating-images/screenshots-sml.png "範例應用程式與 Xamarin.Forms 行為")](creating-images/screenshots.png#lightbox "範例應用程式與 Xamarin.Forms 行為")

> [!NOTE]
> 行為會寫入特定控制項類型 （或超級類別，以套用至許多控制項），而且只應加入至相容的控制項。 嘗試附加至不相容的控制項的行為會導致擲回例外狀況。

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>使用 Xamarin.Forms 行為的樣式

行為也可供明確或隱含的樣式。 不過，建立設定的樣式[ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/)控制項的屬性不可能因為屬性是唯讀狀態。 解決方案是將控制項加入和移除行為的行為類別中加入附加的屬性。 處理程序如下所示：

1. 可用於控制的新增或移除要附加的控制項行為將之行為的行為類別中加入附加的屬性。 請確認附加的屬性會註冊`propertyChanged`屬性的值變更時，會執行的委派。
1. 建立`static`getter 和 setter 為附加屬性。
1. 實作中的邏輯`propertyChanged`新增和移除行為的委派。

下列程式碼範例會顯示該附加的屬性會控制新增和移除`NumericValidationBehavior`:

```csharp
public class NumericValidationBehavior : Behavior<Entry>
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached ("AttachBehavior", typeof(bool), typeof(NumericValidationBehavior), false, propertyChanged: OnAttachBehaviorChanged);

    public static bool GetAttachBehavior (BindableObject view)
    {
        return (bool)view.GetValue (AttachBehaviorProperty);
    }

    public static void SetAttachBehavior (BindableObject view, bool value)
    {
        view.SetValue (AttachBehaviorProperty, value);
    }

    static void OnAttachBehaviorChanged (BindableObject view, object oldValue, object newValue)
    {
        var entry = view as Entry;
        if (entry == null) {
            return;
        }

        bool attachBehavior = (bool)newValue;
        if (attachBehavior) {
            entry.Behaviors.Add (new NumericValidationBehavior ());
        } else {
            var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
            if (toRemove != null) {
                entry.Behaviors.Remove (toRemove);
            }
        }
    }
    ...
}
```

`NumericValidationBehavior`類別包含名為附加的屬性`AttachBehavior`與`static`getter 和 setter，控制的新增或移除控制項，它將會附加該行為。 這個檔案附加屬性的暫存器`OnAttachBehaviorChanged`屬性的值變更時，會執行的方法。 這個方法中加入或移除控制項，並根據值的行為`AttachBehavior`附加屬性。

下列程式碼範例示範*明確*樣式的`NumericValidationBehavior`使用`AttachBehavior`連接屬性，且可套用至[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項：

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)可以套用至[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項設定其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性`Style`執行個體使用`StaticResource`標記延伸，如下列程式碼範例所示：

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

如需有關樣式的詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)。

> [!NOTE]
> 雖然您可以新增至行為設定，或如果您建立行為，在 XAML 中，查詢可繫結內容具有狀態應該不能共用它們中的控制項之間`Style`中`ResourceDictionary`。

### <a name="removing-a-behavior-from-a-control"></a>從控制項移除的行為

[ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法引發時的行為會從控制項，移除，且用來執行任何必要的清除作業，例如取消訂閱事件，以防止記憶體流失。 不過，行為不會以隱含方式移除控制項從除非控制項的[ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/)集合來修改`Remove`或`Clear`方法。 下列程式碼範例示範如何從控制項移除的特定行為`Behaviors`集合：

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

或者，控制項的[ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/)可以清除集合，如下列程式碼範例所示：

```csharp
entry.Behaviors.Clear();
```

此外，請注意，行為不會以隱含方式移除控制項時自巡覽堆疊取出頁面。 相反地，它們必須明確地移除，才能超出範圍的頁面。

## <a name="summary"></a>總結

本文示範如何建立和使用 Xamarin.Forms 行為。 由衍生自建立 Xamarin.Forms 行為[ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)或[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)類別。


## <a name="related-links"></a>相關連結

- [Xamarin.Forms 行為 （範例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehavior/)
- [Xamarin.Forms 行為套用的樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehaviorstyle/)
- [行為](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [行為<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
