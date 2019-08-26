---
title: 建立 Xamarin.Forms 行為
description: Xamarin.Forms 行為是由衍生自 Behavior 或 Behavior<T> 的類別所建立。 本文示範如何建立與使用 Xamarin.Forms 行為。
ms.prod: xamarin
ms.assetid: 300C16FE-A7E0-445B-9099-8E93ABB6F73D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 37403b9e33ccd54baac4fd36afbe50504e0380f0
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528962"
---
# <a name="create-xamarinforms-behaviors"></a>建立 Xamarin.Forms 行為

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)

_Xamarin.Forms 行為由衍生自 Behavior 或 Behavior&lt;T&gt; 的類別建立。本文示範如何建立與使用 Xamarin.Forms 行為。_

## <a name="overview"></a>總覽

建立 Xamarin.Forms 行為的程序如下：

1. 建立繼承自 [`Behavior`](xref:Xamarin.Forms.Behavior) 或是 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 類別的類別，其中 `T` 是應該套用行為的控制項類型。
1. 覆寫 [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) 方法，以執行任何必要的設定。
1. 覆寫 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法，以執行任何必要的清除。
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

當行為附加至控制項後，立即引發 [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) 方法。 這個方法會接收到其附加至控制項的參考，並可用來註冊事件處理常式，或執行其他支援行為功能的必要設定。 例如，您可以訂閱控制項上的事件。 然後，就可以用事件的事件處理常式來實作行為功能。

從控制項移除行為時會引發 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法。 這個方法會接收到其附加至的控制項參考，並用以執行任何必要的清除。 例如，您可以取消訂閱控制項上的事件來防止記憶體流失。

然後就可以將行為附加至適當控制項的 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 集合來使用行為。

## <a name="creating-a-xamarinforms-behavior"></a>建立 Xamarin.Forms 行為

此範例應用程式會示範 `NumericValidationBehavior`，其以紅色醒目提示使用者輸入 [`Entry`](xref:Xamarin.Forms.Entry) 控制項的值，如果值不是 `double`。 此行為會顯示在下列程式碼範例中：

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

`NumericValidationBehavior` 衍生自 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 類別，其中 `T` 是 [`Entry`](xref:Xamarin.Forms.Entry)。 [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) 方法會註冊 [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) 事件的事件處理常式，以 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法取消註冊 `TextChanged` 事件來防止記憶體流失。 行為的核心功能是由 `OnEntryTextChanged` 方法提供，它會剖析使用者輸入 `Entry` 的值，並將 [`TextColor`](xref:Xamarin.Forms.Entry.TextColor) 屬性設為紅色，如果值不是 `double`。

> [!NOTE]
> Xamarin.Forms 不會設定行為的 `BindingContext`，因為行為可以透過樣式共用並套用到多個控制項。

## <a name="consuming-a-xamarinforms-behavior"></a>使用 Xamarin.Forms 行為

每個 Xamarin.Forms 控制項皆有 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 集合，可接受新增一或多個行為，如下列 XAML 程式碼範例所示：

```xaml
<Entry Placeholder="Enter a System.Double">
    <Entry.Behaviors>
        <local:NumericValidationBehavior />
    </Entry.Behaviors>
</Entry>
```

下列程式碼範例顯示 C# 中的對等 [`Entry`](xref:Xamarin.Forms.Entry)：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
entry.Behaviors.Add (new NumericValidationBehavior ());
```

在執行階段，行為會根據行為實作回應與控制項的互動。 下列螢幕擷取畫面示範行為如何回應無效的輸入：

[![](creating-images/screenshots-sml.png "使用 Xamarin.Forms 行為的範例應用程式")](creating-images/screenshots.png#lightbox "使用 Xamarin.Forms 行為的範例應用程式")

> [!NOTE]
> 行為是針對特定的控制項類型 (或可套用至許多控制項的 Superclass) 所撰寫，且應該只新增至相容的控制項。 嘗試將行為附加至不相容的控制項會導致擲回例外狀況。

### <a name="consuming-a-xamarinforms-behavior-with-a-style"></a>利用樣式使用 Xamarin.Forms 行為

行為也可以明確或隱含樣式來使用。 但不能建立可設定控制項之 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 屬性的樣式，因為屬性為唯讀。 解決方案是將附加屬性新增至會控制新增和移除行為的行為類別。 程序如下：

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

下列程式碼範例示範使用 `AttachBehavior` 附加屬性之 `NumericValidationBehavior` 的「明確」  樣式，且可套用至 [`Entry`](xref:Xamarin.Forms.Entry) 控制項：

```xaml
<Style x:Key="NumericValidationStyle" TargetType="Entry">
    <Style.Setters>
        <Setter Property="local:NumericValidationBehavior.AttachBehavior" Value="true" />
    </Style.Setters>
</Style>
```

使用 `StaticResource` 標記延伸將其 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 屬性設為 `Style` 執行個體，[`Style`](xref:Xamarin.Forms.Style) 即可套用至 [`Entry`](xref:Xamarin.Forms.Entry) 控制項，如下列程式碼範例所示：

```xaml
<Entry Placeholder="Enter a System.Double" Style="{StaticResource NumericValidationStyle}">
```

如需樣式的詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)。

> [!NOTE]
> 如果您確實建立了其狀態不應該在 `ResourceDictionary` 的 `Style` 控制項之間共用的行為，則您可以將可繫結的屬性新增至使用 XAML 所設定或查詢的行為。

### <a name="removing-a-behavior-from-a-control"></a>移除控制項的行為

移除控制項的行為時，會引發 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法，並使用此方法執行任何必要的清除，例如取消訂閱事件，防止記憶體流失。 不過，除非控制項的 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 集合經 `Remove` 或 `Clear` 方法修改，否則不會以隱含方式移除控制項的行為。 下列程式碼範例示範移除控制項 `Behaviors` 集合中的特定行為：

```csharp
var toRemove = entry.Behaviors.FirstOrDefault (b => b is NumericValidationBehavior);
if (toRemove != null) {
    entry.Behaviors.Remove (toRemove);
}
```

或者，您也可以清除控制項的 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 集合，如下列程式碼範例所示：

```csharp
entry.Behaviors.Clear();
```

另請注意，當頁面從導覽堆疊快顯時，控制項的行為不會以隱含方式移除。 而是必須在頁面超出範圍之前先明確移除它們。

## <a name="summary"></a>總結

本文已示範如何建立和使用 Xamarin.Forms 行為。 Xamarin.Forms 行為是由衍生自 [`Behavior`](xref:Xamarin.Forms.Behavior) 或 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 類別所建立。


## <a name="related-links"></a>相關連結

- [Xamarin.Forms 行為 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehavior)
- [使用樣式套用的 Xamarin.Forms 行為 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-numericvalidationbehaviorstyle)
- [Behavior](xref:Xamarin.Forms.Behavior)
- [Behavior\<T>](xref:Xamarin.Forms.Behavior`1)
