---
title: 附加的行為
description: 附加的行為會與一或多個附加屬性的靜態類別。 這篇文章會示範如何建立和使用附加的行為。
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 2c9bd9ad4e7572b9eae6f0073da8a2c8f1e7c9fc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995342"
---
# <a name="attached-behaviors"></a>附加的行為

_附加的行為會與一或多個附加屬性的靜態類別。這篇文章會示範如何建立和使用附加的行為。_

## <a name="overview"></a>總覽

附加的屬性是屬性的特殊類型的可繫結。 它們會定義一個類別中，但附加至其他物件，而且是可辨識在 XAML 中做為包含類別，以及以句號分隔屬性名稱的屬性。

附加的屬性可以定義`propertyChanged`屬性的值變更，例如當屬性設定控制項上時，會執行的委派。 當`propertyChanged`委派執行時，它已通過的參照它正在附加的控制項，以及包含屬性的舊和新值的參數。 這個委派可用於加入屬性，會藉由操作，如下所示傳遞中，參考附加至控制項的新功能：

1. `propertyChanged`委派會轉換為接收的控制參考[ `BindableObject`](xref:Xamarin.Forms.BindableObject)為控制項型別，則行為是設計用來加強。
1. `propertyChanged`委派修改控制項屬性的控制項或註冊事件處理常式的控制項，來實作的核心行為功能所公開的事件的呼叫方法。

附加的行為的問題是，以定義它們`static`類別，與`static`屬性和方法。 這可讓您難以建立附加有狀態的行為。 此外，Xamarin.Forms 表現方式已成為慣用方法行為建構取代附加的行為。 如需有關 Xamarin.Forms 表現方式的詳細資訊，請參閱 < [Xamarin.Forms 表現方式](~/xamarin-forms/app-fundamentals/behaviors/creating.md)並[可重複使用行為](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md)。

## <a name="creating-an-attached-behavior"></a>建立連接的行為

範例應用程式示範`NumericValidationBehavior`，當中會重點說明到使用者所輸入的值[ `Entry` ](xref:Xamarin.Forms.Entry)控制呈現紅色，如果不是`double`。 行為是以下列程式碼範例所示：

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

`NumericValidationBehavior`類別包含名為附加的屬性`AttachBehavior`使用`static`getter 和 setter，控制的新增或移除要它將會附加的控制項的行為。 此連接屬性暫存器`OnAttachBehaviorChanged`屬性的值變更時，會執行的方法。 這個方法會登錄或取消註冊事件處理常式[ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged)的值為依據的事件`AttachBehavior`附加屬性。 核心功能的行為由`OnEntryTextChanged`方法，它會剖析輸入的值[ `Entry` ](xref:Xamarin.Forms.Entry)的使用者，並將設定由`TextColor`屬性設為紅色，如果值不是`double`。

## <a name="consuming-an-attached-behavior"></a>使用附加的行為

`NumericValidationBehavior`類別可供新增`AttachBehavior`; 附加屬性[ `Entry` ](xref:Xamarin.Forms.Entry)控制項，如下列 XAML 程式碼範例所示：

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

對等[ `Entry` ](xref:Xamarin.Forms.Entry)在 C# 會顯示在下列程式碼範例：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

在執行階段，行為會根據行為實作回應與控制項互動。 下列螢幕擷取畫面示範回應無效的輸入連接的行為：

[![](attached-images/screenshots-sml.png "範例應用程式與附加行為")](attached-images/screenshots.png#lightbox "範例具有附加行為的應用程式")

> [!NOTE]
> 附加的行為會寫入特定控制項類型 （或可以套用於許多控制項的超級類別），而且只應新增至相容的控制項。 嘗試附加至不相容的控制項的行為會導致不明的行為和行為的實作而定。

### <a name="removing-an-attached-behavior-from-a-control"></a>從控制項移除附加的行為

`NumericValidationBehavior`類別可以從控制項移除，藉由設定`AttachBehavior`; 附加屬性`false`，如下列 XAML 程式碼範例所示：

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

對等[ `Entry` ](xref:Xamarin.Forms.Entry)在 C# 會顯示在下列程式碼範例：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

在執行階段`OnAttachBehaviorChanged`方法將會執行時的值`AttachBehavior`附加的屬性設定為`false`。 `OnAttachBehaviorChanged`方法會再取消註冊的事件處理常式[ `TextChanged` ](xref:Xamarin.Forms.Entry.TextChanged)事件，確保使用者與控制項互動，不執行行為。

## <a name="summary"></a>總結

這篇文章會示範如何建立和使用附加的行為。 附加的行為是一種`static`具有一或多個附加屬性的類別。


## <a name="related-links"></a>相關連結

- [附加的行為 （範例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/attachednumericvalidationbehavior/)
