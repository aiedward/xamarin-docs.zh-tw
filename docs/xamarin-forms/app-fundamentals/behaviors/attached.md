---
title: 附加的行為
description: 附加的行為是具有一或多個附加屬性的靜態類別。 本文示範如何建立和使用附加的行為。
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 7d16eef4fe5422fb5cf3c039c66d1b0f113727fd
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "77131124"
---
# <a name="attached-behaviors"></a>附加的行為

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-attachednumericvalidationbehavior)

_附加行為是具有一個或多個附加屬性的靜態類。本文演示如何創建和使用附加行為。_

## <a name="overview"></a>概觀

附加屬性是可繫結屬性的特殊類型。 它們會定義於一個類別但附加至其他物件，而且在 XAML 中會識別為包含類別和屬性名稱並以句號分隔的屬性。

附加屬性可定義 `propertyChanged` 委派。當屬性的值變更時 (例如當設定控制項的屬性時)，就會執行此委派。 當 `propertyChanged` 委派執行時，會將其附加至的控制項參考，以及包含屬性新舊值的參數傳遞給該委派。 您可以使用此委派，透過操作傳入的參考來將新功能新增至屬性所附加的控制項，如下所示：

1. 委託`propertyChanged`將作為 接收[`BindableObject`](xref:Xamarin.Forms.BindableObject)的控制者引用轉換為該行為旨在增強的控制項類型。
1. `propertyChanged` 委派會修改控制項的屬性、呼叫控制項的方法，或註冊控制項所公開事件的事件處理常式，以便實作核心行為功能。

附加行為的問題在於，它們是在 `static` 類別中定義，並具有 `static` 屬性和方法。 這點會讓您難以建立具有狀態的附加行為。 此外，Xamarin.Forms 行為已取代附加的行為，成為行為建構的慣用方法。 如需 Xamarin.Forms 行為的詳細資訊，請參閱 [Xamarin.Forms 行為](~/xamarin-forms/app-fundamentals/behaviors/creating.md)和[可重複使用的行為](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md)。

## <a name="creating-an-attached-behavior"></a>建立附加的行為

範例應用程式示範了`NumericValidationBehavior`, 它突出顯示使用者輸入[`Entry`](xref:Xamarin.Forms.Entry)到 紅色控制項中的值(如果它不是`double`)。 此行為會顯示在下列程式碼範例中：

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

`NumericValidationBehavior` 類別包含名為 `AttachBehavior` 並具有 `static` getter 和 setter 的附加屬性，其控制所要附加之控制項的行為新增與移除。 此附加屬性會註冊 `OnAttachBehaviorChanged` 方法；當屬性的值變更時，就會執行此方法。 此方法根據[`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)`AttachBehavior`附加屬性的值註冊或取消註冊事件的事件處理程式。 行為的核心`OnEntryTextChanged`功能由 方法提供,該方法解析使用者輸入[`Entry`](xref:Xamarin.Forms.Entry)的值 ,如果值不是`TextColor`,則將`double`屬性設置為紅色。

## <a name="consuming-an-attached-behavior"></a>使用附加的行為

`NumericValidationBehavior`可以通過`AttachBehavior`將 附加屬性[`Entry`](xref:Xamarin.Forms.Entry)添加到 控制項來使用類,如下 XAML 代碼範例所示:

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

C#[`Entry`](xref:Xamarin.Forms.Entry)中的等效項目顯示在以下代碼範例中:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

在執行階段，行為會根據行為實作來回應與控制項的互動。 下列螢幕擷取畫面示範附加的行為如何回應無效的輸入：

[![](attached-images/screenshots-sml.png "Sample Application with Attached Behavior")](attached-images/screenshots.png#lightbox "Sample Application with Attached Behavior")

> [!NOTE]
> 附加的行為是針對特定控制項類型 (或可套用至許多控制項的子類別) 所撰寫，而且只應新增至相容的控制項。 嘗試將行為附加至不相容的控制項會導致行為不明，並會取決於行為實作。

### <a name="removing-an-attached-behavior-from-a-control"></a>從控制項中移除附加的行為

您可以將 `AttachBehavior` 附加屬性設定為 `false`，從控制項中移除 `NumericValidationBehavior` 類別，如下列 XAML 程式碼範例所示：

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

C#[`Entry`](xref:Xamarin.Forms.Entry)中的等效項目顯示在以下代碼範例中:

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

在執行階段，當 `AttachBehavior` 附加屬性的值設定為 `false` 時，將會執行 `OnAttachBehaviorChanged` 方法。 然後`OnAttachBehaviorChanged`,該方法將取消[`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)註冊 事件的事件處理程式,以確保在使用者與控件交互時不會執行該行為。

## <a name="summary"></a>總結

本文示範了如何建立和使用附加的行為。 附加的行為是具有一或多個附加屬性的 `static` 類別。

## <a name="related-links"></a>相關連結

- [Attached Behaviors (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-attachednumericvalidationbehavior) (附加的行為 (範例))
