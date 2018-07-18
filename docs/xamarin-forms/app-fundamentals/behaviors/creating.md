---
title: Xamarin.Forms 表現方式
description: Xamarin.Forms 表現方式藉由衍生自行為或行為<T>類別。 這篇文章會示範如何建立和使用 Xamarin.Forms 表現方式。
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 7e057567ec0bb72e9bcc016d4a9fef3af78a3ea1
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998891"
---
# <a name="xamarinforms-behaviors"></a>Xamarin.Forms 表現方式

_Xamarin.Forms 表現方式藉由衍生自行為或行為<T>類別。這篇文章會示範如何建立和使用 Xamarin.Forms 表現方式。_

## <a name="overview"></a>總覽

建立 Xamarin.Forms 行為的程序如下所示：

1. 建立繼承自類別[ `Behavior` ](xref:Xamarin.Forms.Behavior)或是[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)類別，其中`T`是行為應該套用至控制項的型別。
1. 覆寫[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))方法，以執行任何必要的設定。
1. 覆寫[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))執行任何必要的清除作業的方法。
1. 實作核心功能的行為。

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

[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))方法行為附加至控制項之後，立即引發。 這個方法會接收的控制項的方法，它可以連接，並可用來註冊事件處理常式，或執行其他安裝程式以支援行為功能所需的參考。 例如，您無法訂閱上控制項的事件。 行為的功能，則會實作事件的事件處理常式中。

[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))行為從控制項移除時引發方法。 這個方法會接收的控制項的方法，它可以連接，並使用執行任何必要的清除作業的參考。 例如，您可以取消訂閱事件上的控制項，以防止記憶體流失。

行為可以再由附加至[ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors)適當的控制項集合。

## <a name="creating-a-xamarinforms-behavior"></a>建立 Xamarin.Forms 行為

範例應用程式示範`NumericValidationBehavior`，當中會重點說明到使用者所輸入的值[ `Entry` ](xref:Xamarin.Forms.Entry)控制呈現紅色，如果不是`double`。 行為是以下列程式碼範例所示：

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

`NumericValidationBehavior`衍生自[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)類別，其中`T`是[ `Entry` ](xref:Xamarin.Forms.Entry)。 [ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))方法會註冊事件處理常式[ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged)事件，以[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法取消註冊`TextChanged`事件，以避免記憶體流失。 核心功能的行為由`OnEntryTextChanged`方法，它會剖析成使用者所輸入的值`Entry`，並設定[ `TextColor` ](xref:Xamarin.Forms.Entry.TextColor)屬性設為紅色，如果值不是`double`。

> [!NOTE]
> Xamarin.Forms 不會設定`BindingContext`的行為，因為行為可共用及套用到多個控制項所有樣式。

## <a name="consuming-a-xamarinforms-behavior"></a>使用 Xamarin.Forms 行為

每個 Xamarin.Forms 控制項具有[ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors) ，一或多個行為可以會加入的集合，如下列 XAML 程式碼範例所示：

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

對等[ `Entry` ](xref:Xamarin.Forms.Entry)在 C# 會顯示在下列程式碼範例：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

在執行階段行為會根據行為實作回應與控制項互動。 下列螢幕擷取畫面示範回應無效輸入的行為：

[![](creating-images/screenshots-sml.png "範例應用程式使用 Xamarin.Forms 行為")](creating-images/screenshots.png#lightbox "Xamarin.Forms 行為的應用程式範例")

> [!NOTE]
> 行為會寫入特定控制項類型 （或可以套用於許多控制項的超級類別），而且只應新增至相容的控制項。 嘗試附加至不相容的控制項的行為，會導致擲回例外狀況。

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>使用 Xamarin.Forms 行為的樣式

行為也可供明確或隱含的樣式。 不過，建立可設定樣式[ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors)因為屬性是唯讀，不可能的控制項屬性。 解決方法是將控制項加入和移除行為的行為類別中的附加的屬性。 此程序如下所示：

1. 加入行為類別會用來控制的新增或移除要附加的控制項行為將會執行的行為中的附加的屬性。 請確定該附加的屬性註冊`propertyChanged`屬性的值變更時，會執行的委派。
1. 建立`static`getter 和 setter，附加屬性。
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

`NumericValidationBehavior`類別包含名為附加的屬性`AttachBehavior`使用`static`getter 和 setter，控制的新增或移除要它將會附加的控制項的行為。 此連接屬性暫存器`OnAttachBehaviorChanged`屬性的值變更時，會執行的方法。 此方法加入或移除的值為基礎的控制項的行為`AttachBehavior`附加屬性。

下列程式碼範例所示*明確*樣式`NumericValidationBehavior`使用`AttachBehavior`連接屬性，且可套用至[ `Entry` ](xref:Xamarin.Forms.Entry)控制項：

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

[ `Style` ](xref:Xamarin.Forms.Style)可以套用至[ `Entry` ](xref:Xamarin.Forms.Entry)控制項，藉由設定其[ `Style` ](xref:Xamarin.Forms.VisualElement.Style)屬性`Style`執行個體`StaticResource`標記延伸，如下列程式碼範例所示：

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

如需有關樣式的詳細資訊，請參閱 <<c0> [ 樣式](~/xamarin-forms/user-interface/styles/index.md)。

> [!NOTE]
> 雖然您可以增加可繫結的屬性，以設定或查詢中 XAML，如果您建立行為的行為有狀態它們不應該共用中的控制項之間`Style`在`ResourceDictionary`。

### <a name="removing-a-behavior-from-a-control"></a>從控制項移除的行為

[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法會引發時的行為會從一個控制項，移除與用來執行任何必要的清除作業，例如取消訂閱事件，以防止記憶體流失。 不過，行為不會以隱含方式移除控制項從除非控制項的[ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors)藉由修改集合`Remove`或`Clear`方法。 下列程式碼範例示範如何從控制項移除特定的行為`Behaviors`集合：

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

或者，控制項的[ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors)可以清除集合，如下列程式碼範例所示：

```csharp
entry.Behaviors.Clear();
```

此外，請注意，行為時不會以隱含方式移除控制項的頁面會從導覽堆疊快顯。 相反地，它們必須明確移除之前超出範圍的頁面。

## <a name="summary"></a>總結

這篇文章會示範如何建立和使用 Xamarin.Forms 表現方式。 Xamarin.Forms 表現方式藉由衍生自[ `Behavior` ](xref:Xamarin.Forms.Behavior)或是[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)類別。


## <a name="related-links"></a>相關連結

- [Xamarin.Forms 行為 （範例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehavior/)
- [Xamarin.Forms 行為套用的樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/numericvalidationbehaviorstyle/)
- [行為](xref:Xamarin.Forms.Behavior)
- [行為<T>](xref:Xamarin.Forms.Behavior`1)
