---
title: 附加的行為
description: 附加的行為是具有一或多個附加屬性的靜態類別。 本文示範如何建立和使用附加的行為。
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 2c9bd9ad4e7572b9eae6f0073da8a2c8f1e7c9fc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995342"
---
# <a name="attached-behaviors"></a>附加的行為

_附加的行為是具有一或多個附加屬性的靜態類別。本文示範如何建立和使用附加的行為。_

## <a name="overview"></a>總覽

附加屬性是可繫結屬性的特殊類型。 它們會定義於一個類別但附加至其他物件，而且在 XAML 中會識別為包含類別和屬性名稱並以句號分隔的屬性。

附加屬性可定義 `propertyChanged` 委派。當屬性的值變更時 (例如當設定控制項的屬性時)，就會執行此委派。 當 `propertyChanged` 委派執行時，會將其附加至的控制項參考，以及包含屬性新舊值的參數傳遞給該委派。 您可以使用此委派，透過操作傳入的參考來將新功能新增至屬性所附加的控制項，如下所示：

1. `propertyChanged` 委派會將以 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 形式接收的控制項參考，轉換為其行為旨在增強的控制項類型。
1. `propertyChanged` 委派會修改控制項的屬性、呼叫控制項的方法，或註冊控制項所公開事件的事件處理常式，以便實作核心行為功能。

附加行為的問題在於，它們是在 `static` 類別中定義，並具有 `static` 屬性和方法。 這點會讓您難以建立具有狀態的附加行為。 此外，Xamarin.Forms 行為已取代附加的行為，成為行為建構的慣用方法。 如需 Xamarin.Forms 行為的詳細資訊，請參閱 [Xamarin.Forms 行為](~/xamarin-forms/app-fundamentals/behaviors/creating.md)和[可重複使用的行為](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md)。

## <a name="creating-an-attached-behavior"></a>建立附加的行為

此範例應用程式示範 `NumericValidationBehavior`，其中以紅色醒目提示使用者輸入 [`Entry`](xref:Xamarin.Forms.Entry) 控制項的值 (如果不是 `double`)。 此行為會顯示在下列程式碼範例中：

```csharp
public static class NumericValidationBehavior
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached (
            "AttachBehavior",
            typeof(bool),
            typeof(NumericValidationBehavior),
            false,
            propertyChanged:OnAttachBehaviorChanged);

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
            entry.TextChanged += OnEntryTextChanged;
        } else {
            entry.TextChanged -= OnEntryTextChanged;
        }
    }

    static void OnEntryTextChanged (object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

`NumericValidationBehavior` 類別包含名為 `AttachBehavior` 並具有 `static` getter 和 setter 的附加屬性，其控制所要附加之控制項的行為新增與移除。 此附加屬性會註冊 `OnAttachBehaviorChanged` 方法；當屬性的值變更時，就會執行此方法。 此方法會根據 `AttachBehavior` 附加屬性的值，註冊或取消註冊 [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) 事件的事件處理常式。 行為的核心功能是由 `OnEntryTextChanged` 方法所提供，它會剖析使用者輸入 [`Entry`](xref:Xamarin.Forms.Entry) 的值，並將 `TextColor` 屬性設定為紅色 (如果值不是 `double`)。

## <a name="consuming-an-attached-behavior"></a>使用附加的行為

您可以將 `AttachBehavior` 附加屬性新增至 [`Entry`](xref:Xamarin.Forms.Entry) 控制項來使用 `NumericValidationBehavior` 類別，如下列 XAML 程式碼範例所示：

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

下列程式碼範例顯示 C# 中的對等 [`Entry`](xref:Xamarin.Forms.Entry)：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

在執行階段，行為會根據行為實作來回應與控制項的互動。 下列螢幕擷取畫面示範附加的行為如何回應無效的輸入：

[![](attached-images/screenshots-sml.png "具有附加行為的範例應用程式")](attached-images/screenshots.png#lightbox "具有附加行為的範例應用程式")

> [!NOTE]
> 附加的行為是針對特定控制項類型 (或可套用至許多控制項的子類別) 所撰寫，而且只應新增至相容的控制項。 嘗試將行為附加至不相容的控制項會導致行為不明，並會取決於行為實作。

### <a name="removing-an-attached-behavior-from-a-control"></a>從控制項中移除附加的行為

您可以將 `AttachBehavior` 附加屬性設定為 `false`，從控制項中移除 `NumericValidationBehavior` 類別，如下列 XAML 程式碼範例所示：

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

下列程式碼範例顯示 C# 中的對等 [`Entry`](xref:Xamarin.Forms.Entry)：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

在執行階段，當 `AttachBehavior` 附加屬性的值設定為 `false` 時，將會執行 `OnAttachBehaviorChanged` 方法。 `OnAttachBehaviorChanged` 方法會接著取消註冊 [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) 事件的事件處理常式，以確保當使用者與控制項互動時不會執行此行為。

## <a name="summary"></a>總結

本文示範了如何建立和使用附加的行為。 附加行為是具有一或多個附加屬性的 `static` 類別。


## <a name="related-links"></a>相關連結

- [Attached Behaviors (Samples)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/attachednumericvalidationbehavior/) (附加的行為 (範例))
