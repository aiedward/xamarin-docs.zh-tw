---
title: Xamarin.Forms Switch
description: Xamarin.Forms 交換器是按鈕的一種可由使用者切換開啟和關閉狀態操作。 這篇文章說明如何使用參數類別顯示切換的 UI 項目。
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/03/2019
ms.openlocfilehash: 22a17f9a916d94a3a0f44a451512de43c943e95a
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675034"
---
# <a name="xamarinforms-switch"></a>Xamarin.Forms Switch

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SwitchDemos)

Xamarin.Forms [ `Switch` ](xref:Xamarin.Forms.Switch)是一個可以管理之間進行切換開啟和關閉狀態，這由使用者的水平的切換按鈕`boolean`值。 `Switch`類別繼承自[ `View` ](xref:Xamarin.Forms.View)。

下列螢幕擷取畫面顯示`Switch`控制在其**上**並**關閉**切換 iOS 和 Android 上的狀態：

![螢幕擷取畫面的參數中開啟和關閉狀態，在 iOS 和 Android 上](switch-images/switch-states-default.png "iOS 和 Android 上的參數")

`Switch`控制項會定義兩個屬性：

* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor) 已`Color`影響如何`Switch`會呈現已切換，或**上**，狀態。
* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 已`boolean`值，指出是否`Switch`是**上**。

這些屬性都會受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示`Switch`可自訂樣式和資料繫結的目標。

`Switch`控制項定義`Toggled`時引發的事件`IsToggled`屬性變更，透過使用者操作，或當應用程式設定`IsToggled`屬性。 `ToggledEventArgs`隨附的物件`Toggled`事件會有一個名為的單一屬性`Value`，型別的`bool`。 當事件引發時，windows 7`Value`屬性會反映新的值`IsToggled`屬性。

## <a name="create-a-switch"></a>建立交換器

A`Switch`可以在 XAML 中具現化。 其`IsToggled`屬性可以設定為切換`Switch`。 根據預設，`IsToggled`屬性是`false`。 下列範例示範如何具現化`Switch`搭配可省略的 XAML 中`IsToggled`屬性集：

```xaml
<Switch IsToggled="true"/>
```

A`Switch`也可以建立程式碼中：

```csharp
Switch switch = new Switch { IsToggled = true };
```

### <a name="switch-style-properties"></a>交換器樣式內容

`OnColor`屬性可以設定來定義`Switch`色彩時切換到其**上**狀態。 下列範例示範如何具現化`Switch`中使用的 XAML`OnColor`屬性集：

```xaml
<Switch OnColor="Orange" />
```

`OnColor`屬性也可以設定建立時`Switch`在程式碼中：

```csharp
Switch switch = new Switch { OnColor = Color.Orange };
```

下列螢幕擷取畫面顯示`Switch`在其**上**並**關閉**切換狀態，與`OnColor`屬性設定為`Color.Orange`iOS 和 Android 上：

![螢幕擷取畫面的參數中開啟和關閉狀態，在 iOS 和 Android 上](switch-images/switch-states-oncolor.png "iOS 和 Android 上的參數")

## <a name="respond-to-a-switch-state-change"></a>回應參數狀態變更

當`IsToggled`屬性變更，透過使用者操作，或當應用程式設定`IsToggled`屬性，`Toggled`引發事件。 此事件的事件處理常式可登錄以便回應變更：

```xaml
<Switch Toggled="OnToggled" />
```

程式碼後置檔案包含的處理常式`Toggled`事件：

```csharp
void OnToggled(object sender, ToggledEventArgs e)
{
    // Perform an action after examining e.Value
}
```

`sender`事件處理常式的引數是`Switch`負責引發此事件。 您可以使用`sender`屬性來存取`Switch`物件，或若要區別多個`Switch`共用相同的物件`Toggled`事件處理常式。

`Toggled`事件處理常式也可以在程式碼中指派：

```csharp
Switch switch = new Switch {...};
switch.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>將資料繫結的參數

`Toggled`事件處理常式會消除，可以使用資料繫結和觸發程序來回應`Switch`變更切換狀態。

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

在此範例中， [ `Label` ](xref:Xamarin.Forms.Label)會使用中的繫結運算式`DataTrigger`監視`IsToggled`屬性`Switch`名為`styleSwitch`。 當這個屬性會變成`true`，則`FontAttributes`並`FontSize`的屬性`Label`變更。 時`IsToggled`屬性會傳回到`false`，則`FontAttributes`並`FontSize`的屬性`Label`會重設為其初始狀態。

如需觸發程序的詳細資訊，請參閱[Xamarin.Forms 觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)。

## <a name="disable-a-switch"></a>停用參數

應用程式可能會進入狀態其中`Switch`要切換並不是有效的作業。 在此情況下，`Switch`您可以藉由設定停用其`IsEnabled`屬性設`false`。 這會防止使用者操作`Switch`。

## <a name="related-links"></a>相關連結

* [切換示範](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/SwitchDemos)
* [Xamarin.Forms 的觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)
