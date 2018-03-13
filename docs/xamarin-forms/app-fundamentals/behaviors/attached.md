---
title: "附加的行為"
description: "附加這些行為屬於靜態類別具有一或多個附加的屬性。 本文將示範如何建立和使用附加的行為。"
ms.topic: article
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 84e60e8ce698e3d87db3e1bdc61613325ad831c8
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="attached-behaviors"></a>附加的行為

_附加這些行為屬於靜態類別具有一或多個附加的屬性。本文將示範如何建立和使用附加的行為。_

## <a name="overview"></a>總覽

附加的屬性是一種特殊的可繫結屬性。 它們會定義一個類別中附加其他物件，但它們是可辨識在 XAML 中包含的類別和屬性名稱以逗號分隔的屬性。

附加的屬性可以定義`propertyChanged`屬性的值變更，例如當屬性設定在控制項上時，會執行的委派。 當`propertyChanged`委派執行、 已通過的參照控制項在其它正在附加，以及包含屬性的舊的和新值的參數。 這個委派可用於管理傳入，，如下所示的參考附加屬性的控制項加入新的功能：

1. `propertyChanged`委派轉換收到的控制參考[ `BindableObject`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)至控制項的類型的行為是設計來增強。
1. `propertyChanged`委派修改內容控制項的控制項或註冊事件處理常式的控制項，來實作的核心行為的功能所公開的事件的呼叫方法。

附加行為的問題是其定義位於`static`類別，與`static`屬性和方法。 這樣會讓您建立具有狀態附加的行為困難。 此外，Xamarin.Forms 行為有做為慣用的方法行為建構取代附加的行為。 如需 Xamarin.Forms 行為的詳細資訊，請參閱[Xamarin.Forms 行為](~/xamarin-forms/app-fundamentals/behaviors/creating.md)和[可重複使用行為](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md)。

## <a name="creating-an-attached-behavior"></a>建立連接的行為

範例應用程式示範`NumericValidationBehavior`，這為使用者所輸入的值會反白顯示[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)如果不是以紅色控制`double`。 行為是以下列程式碼範例所示：

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

`NumericValidationBehavior`類別包含名為附加的屬性`AttachBehavior`與`static`getter 和 setter，控制的新增或移除控制項，它將會附加該行為。 這個檔案附加屬性的暫存器`OnAttachBehaviorChanged`屬性的值變更時，會執行的方法。 這個方法會註冊或取消註冊的事件處理常式[ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/)事件，根據值`AttachBehavior`附加屬性。 所提供的核心功能的行為`OnEntryTextChanged`方法，將輸入值剖析[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)的使用者，並將設定由`TextColor`屬性為紅色，如果此值不`double`。

## <a name="consuming-an-attached-behavior"></a>使用附加的行為

`NumericValidationBehavior`類別可供加入`AttachBehavior`附加屬性[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制，如下列 XAML 程式碼範例所示：

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

對等項目[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)在 C# 會顯示在下列程式碼範例：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

在執行階段，行為會根據行為實作回應與控制項互動。 下列螢幕擷取畫面會示範回應輸入無效的連接的行為：

[![](attached-images/screenshots-sml.png "範例應用程式與附加行為")](attached-images/screenshots.png#lightbox "範例具有附加行為的應用程式")

> [!NOTE]
> 附加的行為會寫入特定控制項類型 （或超級類別，以套用至許多控制項），而且只應加入至相容的控制項。 嘗試附加行為至不相容的控制項將會產生未知的行為以及行為實作而定。

### <a name="removing-an-attached-behavior-from-a-control"></a>從控制項移除附加的行為

`NumericValidationBehavior`可以從控制項移除類別，藉由設定`AttachBehavior`附加屬性`false`下列 XAML 程式碼範例所示：

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

對等項目[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)在 C# 會顯示在下列程式碼範例：

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

在執行階段，`OnAttachBehaviorChanged`方法將會執行時的值`AttachBehavior`附加的屬性設定為`false`。 `OnAttachBehaviorChanged`方法接著會取消註冊事件處理常式[ `TextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/)事件，確保當使用者與控制項互動時，不執行行為。

## <a name="summary"></a>總結

本文示範如何建立和使用附加的行為。 附加這些行為屬於`static`具有一或多個附加屬性的類別。


## <a name="related-links"></a>相關連結

- [附加的行為 （範例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/attachednumericvalidationbehavior/)
