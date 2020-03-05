---
title: Xamarin. Forms 參數
description: '[Xamarin] 參數是一種按鈕類型，可由使用者操作以在 [開啟] 和 [關閉] 狀態之間切換。 本文說明如何使用 Switch 類別來顯示切換的 UI 元素。'
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/18/2019
ms.openlocfilehash: 88655aabdbd32db63aaf3330a18b0ad8105ea26c
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "78291892"
---
# <a name="xamarinforms-switch"></a>Xamarin. Forms 參數

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

[Xamarin] [`Switch`](xref:Xamarin.Forms.Switch)控制項是水準切換按鈕，可由使用者操作以切換 [開啟] 和 [關閉] 狀態（以 `boolean` 值表示）。 `Switch` 類別繼承自[`View`](xref:Xamarin.Forms.View)。

下列螢幕擷取畫面顯示 iOS 和 Android 上的 [**開啟**] 和 [**關閉**] 切換狀態中的 `Switch` 控制項：

![IOS 和 Android 上開啟和關閉狀態的切換畫面快照](switch-images/switch-states-default.png "IOS 和 Android 上的交換器")

`Switch` 控制項會定義下列屬性：

* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)是指出 `Switch` 是否**開啟**的 `boolean` 值。
* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor)是一種 `Color`，會影響在**切換或狀態**下呈現 `Switch` 的方式。
* `ThumbColor` 是切換捲動方塊的 `Color`。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示可以將 `Switch` 樣式化，並成為資料系結的目標。

`Switch` 控制項會定義當 `IsToggled` 屬性變更時（不論是透過使用者操作，還是當應用程式設定 `IsToggled` 屬性時，所引發的 `Toggled` 事件）。 隨附 `Toggled` 事件的 `ToggledEventArgs` 物件具有名為 `Value`的單一屬性，其類型為 `bool`。 當引發事件時，`Value` 屬性的值會反映 `IsToggled` 屬性的新值。

## <a name="create-a-switch"></a>建立交換器

`Switch` 可以在 XAML 中具現化。 其 `IsToggled` 屬性可以設定為切換 `Switch`。 根據預設，`IsToggled` 屬性會 `false`。 下列範例顯示如何使用選擇性的 `IsToggled` 屬性集，在 XAML 中具現化 `Switch`：

```xaml
<Switch IsToggled="true"/>
```

您也可以在程式碼中建立 `Switch`：

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>切換外觀

除了[`Switch`](xref:Xamarin.Forms.Switch)繼承自[`View`](xref:Xamarin.Forms.View)類別的屬性之外，`Switch` 也會定義 `OnColor` 和 `ThumbColor` 屬性。 `OnColor` 屬性可以設定為在切換至其 [**開啟**] 狀態時定義 `Switch` 色彩，而且可以設定 [`ThumbColor`] 屬性來定義切換捲動方塊的 `Color`。 下列範例示範如何使用下列屬性設定，在 XAML 中具現化 `Switch`：

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

在程式碼中建立 `Switch` 時，也可以設定屬性：

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

下列螢幕擷取畫面顯示其 [**開啟**] 和 [**關閉**] 切換狀態中的 `Switch`，並已設定 `OnColor` 和 `ThumbColor` 屬性：

![IOS 和 Android 上開啟和關閉狀態的切換畫面快照](switch-images/switch-states-colors.png "IOS 和 Android 上的交換器")

## <a name="respond-to-a-switch-state-change"></a>回應交換器狀態變更

當 `IsToggled` 屬性變更時（不論是透過使用者操作，或當應用程式設定 `IsToggled` 屬性時），都會引發 `Toggled` 事件。 您可以註冊此事件的事件處理常式，以回應變更：

```xaml
<Switch Toggled="OnToggled" />
```

程式碼後置檔案包含 `Toggled` 事件的處理常式：

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

事件處理常式中的 `sender` 引數是負責引發此事件的 `Switch`。 您可以使用 `sender` 屬性來存取 `Switch` 物件，或區分多個共用相同 `Toggled` 事件處理常式的 `Switch` 物件。

您也可以在程式碼中指派 `Toggled` 事件處理常式：

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>資料系結參數

您可以使用資料系結和觸發程式來回應 `Switch` 變更切換狀態，以消除 `Toggled` 事件處理常式。

```xaml
<Switch x:Name="styleSwitch" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference styleSwitch}, Path=IsToggled}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

在此範例中， [`Label`](xref:Xamarin.Forms.Label)會使用 `DataTrigger` 中的系結運算式來監視名為 `styleSwitch`之 `Switch` 的 `IsToggled` 屬性。 當這個屬性變成 `true`時，`Label` 的 `FontAttributes` 和 `FontSize` 屬性會變更。 當 `IsToggled` 屬性回到 `false`時，`Label` 的 `FontAttributes` 和 `FontSize` 屬性會重設為其初始狀態。

如需觸發程式的詳細資訊，請參閱[Xamarin。](~/xamarin-forms/app-fundamentals/triggers.md)

## <a name="disable-a-switch"></a>停用交換器

應用程式可能會進入狀態，其中切換的 `Switch` 不是有效的作業。 在這種情況下，`Switch` 可以藉由將其 `IsEnabled` 屬性設定為 `false`來停用。 這會讓使用者無法操作 `Switch`。

## <a name="related-links"></a>相關連結

* [切換示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
* [Xamarin. 表單觸發程式](~/xamarin-forms/app-fundamentals/triggers.md)
