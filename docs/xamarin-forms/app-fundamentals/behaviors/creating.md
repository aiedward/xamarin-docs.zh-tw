---
title: 建立 Xamarin.Forms 行為
description: Xamarin.Forms行為是透過衍生自行為或行為類別來建立 <T> 。 本文示範如何建立和使用 Xamarin.Forms 行為。
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 67db30b5caadce75a41755530db2b245562d0304
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135820"
---
# <a name="create-xamarinforms-behaviors"></a>建立 Xamarin.Forms 行為

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)

_Xamarin：表單行為是由衍生自行為或行為 &lt; T 類別所建立 &gt; 。本文示範如何建立和使用 Xamarin.Forms 行為。_

## <a name="overview"></a>概觀

建立行為的流程如下所示 Xamarin.Forms ：

1. 建立繼承自或類別的類別 [`Behavior`](xref:Xamarin.Forms.Behavior) [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) ，其中 `T` 是應該套用行為的控制項類型。
1. 覆寫 [ `OnAttachedTo` ] （x： Xamarin.Forms 。Behavior'1. OnAttachedTo （ Xamarin.Forms 。BindableObject））方法來執行任何必要的安裝程式。
1. 覆寫 [ `OnDetachingFrom` ] （x： Xamarin.Forms 。Behavior'1. OnDetachingFrom （ Xamarin.Forms 。BindableObject））方法來執行任何必要的清除。
1. 實作行為的核心功能。

這會導致下列程式碼範例所示的結構：

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

[ `OnAttachedTo` ] （X： Xamarin.Forms 。Behavior'1. OnAttachedTo （ Xamarin.Forms 。BindableObject））方法會在行為附加至控制項之後立即引發。 這個方法會接收到其附加至控制項的參考，並可用來註冊事件處理常式，或執行其他支援行為功能的必要設定。 例如，您可以訂閱控制項上的事件。 然後，就可以用事件的事件處理常式來實作行為功能。

[ `OnDetachingFrom` ] （X： Xamarin.Forms 。Behavior'1. OnDetachingFrom （ Xamarin.Forms 。BindableObject））方法會在從控制項中移除行為時引發。 這個方法會接收到其附加至的控制項參考，並用以執行任何必要的清除。 例如，您可以取消訂閱控制項上的事件來防止記憶體流失。

然後，將其附加至適當控制項的集合，即可取用此行為 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 。

## <a name="creating-a-xamarinforms-behavior"></a>建立 Xamarin.Forms 行為

範例應用程式會示範 `NumericValidationBehavior` ，它會將使用者輸入的值反白顯示為 [`Entry`](xref:Xamarin.Forms.Entry) 紅色的控制項（如果不是） `double` 。 此行為會顯示在下列程式碼範例中：

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

`NumericValidationBehavior`衍生自 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 類別，其中 `T` 是 [`Entry`](xref:Xamarin.Forms.Entry) 。 [ `OnAttachedTo` ] （X： Xamarin.Forms 。行為 `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method registers an event handler for the [` TextChanged `](xref:Xamarin.Forms.InputView.TextChanged) event, with the [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom （ Xamarin.Forms 。BindableObject））方法會取消註冊 `TextChanged` 事件，以防止記憶體流失。 行為的核心功能是由方法所提供 `OnEntryTextChanged` ，它會剖析使用者輸入的值 `Entry` ，並將 [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) 屬性設定為紅色（如果值不是） `double` 。

> [!NOTE]
> Xamarin.Forms不會設定 `BindingContext` 行為的，因為行為可以透過樣式共用和套用到多個控制項。

## <a name="consuming-a-xamarinforms-behavior"></a>使用 Xamarin.Forms 行為

每個 Xamarin.Forms 控制項都有一個 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 集合，可以加入一個或多個行為，如下列 XAML 程式碼範例所示：

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

[`Entry`](xref:Xamarin.Forms.Entry)下列程式碼範例顯示 c # 中的對等用法：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

在執行階段，行為會根據行為實作回應與控制項的互動。 下列螢幕擷取畫面示範行為如何回應無效的輸入：

[![](creating-images/screenshots-sml.png "Sample Application with Xamarin.Forms Behavior")](creating-images/screenshots.png#lightbox "Sample Application with Xamarin.Forms Behavior")

> [!NOTE]
> 行為是針對特定的控制項類型 (或可套用至許多控制項的 Superclass) 所撰寫，且應該只新增至相容的控制項。 嘗試將行為附加至不相容的控制項會導致擲回例外狀況。

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>使用 Xamarin.Forms 樣式的行為

行為也可以明確或隱含樣式來使用。 不過，不可能建立設定控制項屬性的樣式， [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 因為屬性是唯讀的。 解決方案是將附加屬性新增至會控制新增和移除行為的行為類別。 此程序如下：

1. 將附加屬性新增至行為類別，其用於控制所要附加之控制項的行為新增與移除。 請確保此附加屬性會註冊 `propertyChanged` 委派，當屬性值變更時，就會執行此委派。
1. 建立附加屬性的 `static` getter 和 setter。
1. 實作 `propertyChanged` 委派中的邏輯，以新增和移除行為。

下列程式碼範例示範的附加屬性，會控制 `NumericValidationBehavior` 的新增和移除：

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

`NumericValidationBehavior` 類別包含名為 `AttachBehavior` 並具有 `static` getter 和 setter 的附加屬性，其控制所要附加之控制項的行為新增與移除。 此附加屬性會註冊 `OnAttachBehaviorChanged` 方法；當屬性的值變更時，就會執行此方法。 此方法會根據 `AttachBehavior` 附加屬性的值，新增或移除控制項的行為。

下列程式碼範例示範使用 `AttachBehavior` 附加屬性之 `NumericValidationBehavior` 的「明確」** 樣式，且可套用至 [`Entry`](xref:Xamarin.Forms.Entry) 控制項：

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

[`Style`](xref:Xamarin.Forms.Style)可以套用至 [`Entry`](xref:Xamarin.Forms.Entry) 控制項，方法是 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 使用標記延伸將其屬性設為 `Style` 實例 `StaticResource` ，如下列程式碼範例所示：

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

如需樣式的詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)。

> [!NOTE]
> 如果您確實建立了其狀態不應該在 `ResourceDictionary` 的 `Style` 控制項之間共用的行為，則您可以將可繫結的屬性新增至使用 XAML 所設定或查詢的行為。

### <a name="removing-a-behavior-from-a-control"></a>移除控制項的行為

[ `OnDetachingFrom` ] （X： Xamarin.Forms 。行為 `1.OnDetachingFrom(Xamarin.Forms.BindableObject)) method is fired when a behavior is removed from a control, and is used to perform any required cleanup such as unsubscribing from an event to prevent a memory leak. However, behaviors are not implicitly removed from controls unless the control's [` 行為 `](xref:Xamarin.Forms.VisualElement.Behaviors) collection is modified by a ` 移除 ` or ` 清除 ` method. The following code example demonstrates removing a specific behavior from a control's ` 行為的集合：

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

或者， [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 可以清除控制項的集合，如下列程式碼範例所示：

```csharp
entry.Behaviors.Clear();
```

另請注意，當頁面從導覽堆疊快顯時，控制項的行為不會以隱含方式移除。 而是必須在頁面超出範圍之前先明確移除它們。

## <a name="summary"></a>摘要

本文示範了如何建立和使用 Xamarin.Forms 行為。 Xamarin.Forms行為是藉由衍生自 [`Behavior`](xref:Xamarin.Forms.Behavior) 或類別來建立 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 。

## <a name="related-links"></a>相關連結

- [Xamarin.Forms行為（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)
- [Xamarin.Forms使用樣式套用的行為（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehaviorstyle)
- [行為](xref:Xamarin.Forms.Behavior)
- [行為\<T>](xref:Xamarin.Forms.Behavior`1)
