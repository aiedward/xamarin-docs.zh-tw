---
title: Xamarin.Forms 開關
description: Xamarin.Forms參數是一種按鈕類型，可由使用者操作以在 [開啟] 和 [關閉] 狀態之間切換。 本文說明如何使用 Switch 類別來顯示切換 UI 元素。
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 94f77fd70fee595efd341ff7372828b12661442d
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561725"
---
# <a name="no-locxamarinforms-switch"></a>Xamarin.Forms 開關

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

Xamarin.Forms [`Switch`](xref:Xamarin.Forms.Switch) 控制項是水準切換按鈕，可由使用者操作，以便在開啟和關閉狀態（以值表示）之間切換 `boolean` 。 `Switch`類別繼承自 [`View`](xref:Xamarin.Forms.View) 。

下列螢幕擷取畫面顯示 `Switch` 在 iOS 和 Android **上** ，其開啟和 **關閉** 切換狀態的控制項：

![螢幕擷取畫面： iOS 和 Android 上的開啟和關閉狀態開關](switch-images/switch-states-default.png "IOS 和 Android 上的交換器")

`Switch`控制項會定義下列屬性：

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)這是 `boolean` 指出是否開啟的值 `Switch` 。 **on**
- [`OnColor`](xref:Xamarin.Forms.Switch.OnColor)是 `Color` ，它會影響在 `Switch` 切換或狀態下呈現的方式**on**。
- `ThumbColor` 是 `Color` 切換捲動方塊的。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示 `Switch` 可以設定樣式，也可以是資料系結的目標。

`Switch`控制項 `Toggled` 會定義屬性變更時所引發的事件 `IsToggled` ，不論是透過使用者操作或應用程式設定 `IsToggled` 屬性。 `ToggledEventArgs`事件隨附的物件 `Toggled` 具有名為的單一屬性 `Value` ，類型為 `bool` 。 引發事件時，屬性的值會 `Value` 反映屬性的新值 `IsToggled` 。

## <a name="create-a-switch"></a>建立交換器

`Switch`可以在 XAML 中具現化。 其 `IsToggled` 屬性可以設定為切換 `Switch` 。 根據預設， `IsToggled` 屬性為 `false` 。 下列範例示範如何 `Switch` 在 XAML 中具現化具有選擇性 `IsToggled` 屬性集的 a：

```xaml
<Switch IsToggled="true"/>
```

您 `Switch` 也可以在程式碼中建立：

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

## <a name="switch-appearance"></a>切換外觀

除了 [`Switch`](xref:Xamarin.Forms.Switch) 繼承自類別的屬性之外 [`View`](xref:Xamarin.Forms.View) ，也會 `Switch` 定義 `OnColor` 和 `ThumbColor` 屬性。 您 `OnColor` 可以設定屬性來定義切換 `Switch` 為其 **開啟** 狀態時的色彩，而且 `ThumbColor` 可以設定屬性來定義 `Color` 切換捲動方塊的。 下列範例示範如何 `Switch` 在 XAML 中具現化具有這些屬性集的：

```xaml
<Switch OnColor="Orange"
        ThumbColor="Green" />
```

在程式碼中建立時，也可以設定這些屬性 `Switch` ：

```csharp
Switch switch = new Switch { OnColor = Color.Orange, ThumbColor = Color.Green };
```

下列螢幕擷取畫面顯示在 `Switch` 其 **開啟** 和 **關閉** 切換狀態中的， `OnColor` 並設定和 `ThumbColor` 屬性：

![螢幕擷取畫面： iOS 和 Android 上的開啟和關閉狀態開關](switch-images/switch-states-colors.png "IOS 和 Android 上的交換器")

## <a name="respond-to-a-switch-state-change"></a>回應切換狀態變更

當 `IsToggled` 屬性變更時，無論是透過使用者操作，或是當應用程式設定 `IsToggled` 屬性時，都會 `Toggled` 引發事件。 您可以註冊這個事件的事件處理常式，以回應變更：

```xaml
<Switch Toggled="OnToggled" />
```

程式碼後端檔案包含事件的處理常式 `Toggled` ：

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

`sender`事件處理常式中的引數是 `Switch` 負責引發這個事件的。 您可以使用 `sender` 屬性來存取 `Switch` 物件，或區別多個 `Switch` 共用相同 `Toggled` 事件處理常式的物件。

您 `Toggled` 也可以在程式碼中指派事件處理常式：

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>資料系結參數

您 `Toggled` 可以使用資料系結和觸發程式來回應變更的切換狀態，以消除事件處理常式 `Switch` 。

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

在此範例中，會 [`Label`](xref:Xamarin.Forms.Label) 在中使用系結運算式 `DataTrigger` 來監視 `IsToggled` 名為之的屬性 `Switch` `styleSwitch` 。 當這個屬性變成時 `true` ，的 `FontAttributes` 和 `FontSize` 屬性 `Label` 會變更。 當 `IsToggled` 屬性回到時 `false` ，的 `FontAttributes` 和 `FontSize` 屬性 `Label` 會重設為其初始狀態。

如需觸發程式的詳細資訊，請參閱[ Xamarin.Forms 觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式。

## <a name="switch-visual-states"></a>切換視覺狀態

[`Switch`](xref:Xamarin.Forms.Switch) 具有 `On` 和 `Off` 視覺狀態，可在屬性變更時用來起始視覺效果變更 [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 。

下列 XAML 範例顯示如何定義和狀態的視覺狀態 `On` `Off` ：

```xaml
<Switch IsToggled="True">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="On">
                <VisualState.Setters>
                    <Setter Property="ThumbColor"
                            Value="MediumSpringGreen" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="Off">
                <VisualState.Setters>
                    <Setter Property="ThumbColor"
                            Value="Red" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Switch>
```

在此範例中， `On` [`VisualState`](xref:Xamarin.Forms.VisualState) 會指定當 [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 屬性為時 `true` ， `ThumbColor` 屬性會設定為中度綠色。 `Off` `VisualState` 指定當 `IsToggled` 屬性為時 `false` ， `ThumbColor` 屬性會設定為紅色。 因此，整體效果是當處於 `Switch` off 位置時，其 thumb 為紅色，而當位於的位置為時，其 thumb 會是中等草綠色 `Switch` ：

![螢幕擷取畫面： iOS 和 Android](switch-images/on-visualstate.png "在 VisualState 上切換") 
 上的 VisualState 開關![在 iOS 和 Android 上切換關閉 VisualState 的螢幕擷取畫面](switch-images/off-visualstate.png "切換關閉 VisualState")

如需視覺狀態的詳細資訊，請參閱[ Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="disable-a-switch"></a>停用交換器

應用程式可能會進入要切換的狀態不是有效的作業 `Switch` 。 在這種情況下，您 `Switch` 可以藉由將其 `IsEnabled` 屬性設定為來停用 `false` 。 這會讓使用者無法操作 `Switch` 。

## <a name="related-links"></a>相關連結

- [切換示範](/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
- [Xamarin.Forms 觸發器](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)