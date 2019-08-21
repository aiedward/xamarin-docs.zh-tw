---
title: Xamarin. Forms 參數
description: '[Xamarin] 參數是一種按鈕類型, 可由使用者操作以在 [開啟] 和 [關閉] 狀態之間切換。 本文說明如何使用 Switch 類別來顯示切換的 UI 元素。'
ms.prod: xamarin
ms.assetId: B2F9CC65-481B-4323-8E77-C6BE29C90DE9
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/03/2019
ms.openlocfilehash: 825561c6106ba2ab8e5886df64c3ff850750587b
ms.sourcegitcommit: 9178e2e689f027212ea3e623b556b312985d79fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69658038"
---
# <a name="xamarinforms-switch"></a>Xamarin. Forms 參數

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)

[Xamarin [`Switch`](xref:Xamarin.Forms.Switch) ] 控制項是一個水準切換按鈕, 可由使用者操作以切換 [開啟] 和 [關閉] 狀態 (以`boolean`值表示)。 類別繼承自[`View`。](xref:Xamarin.Forms.View) `Switch`

下列螢幕擷取畫面會在`Switch` iOS 和 Android 上的 [**開啟**] 和 [**關閉**] 切換狀態中顯示控制項:

![IOS 和 Android 上開啟和關閉狀態的切換畫面快照](switch-images/switch-states-default.png "IOS 和 Android 上的交換器")

`Switch`控制項會定義兩個屬性:

* [`OnColor`](xref:Xamarin.Forms.Switch.OnColor)是, 它會影響在`Switch`切換或狀態下呈現的方式。 `Color`
* [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)這是指出`Switch`是否**開啟**的值。`boolean`

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援, 這`Switch`表示可以設定樣式, 並作為資料系結的目標。

控制項會定義當`IsToggled`屬性變更時 (不論是透過`IsToggled`使用者操作, 還是當應用程式設定屬性時, 所引發的事件)。`Toggled` `Switch` 伴隨事件的`ToggledEventArgs`物件具有名為的單一屬性, `Value`其類型`bool`為。 `Toggled` 當引發事件時, `Value`屬性的值會反映`IsToggled`屬性的新值。

## <a name="create-a-switch"></a>建立交換器

`Switch`可以在 XAML 中具現化。 其`IsToggled`屬性可以設定為`Switch`切換。 根據預設, `IsToggled`屬性為`false`。 下列範例顯示如何`Switch`在 XAML 中使用選擇性`IsToggled`屬性集來具現化:

```xaml
<Switch IsToggled="true"/>
```

也`Switch`可以在程式碼中建立:

```csharp
Switch switchControl = new Switch { IsToggled = true };
```

### <a name="switch-style-properties"></a>轉場樣式屬性

屬性可以設定為在切換為其`Switch` [開啟] 狀態時定義色彩。 `OnColor` 下列範例顯示如何`Switch`在 XAML 中`OnColor`使用屬性集來具現化:

```xaml
<Switch OnColor="Orange" />
```

`Switch`在程式碼中建立時, `OnColor`也可以設定屬性:

```csharp
Switch switchControl = new Switch { OnColor = Color.Orange };
```

下列螢幕擷取畫面顯示其`Switch` [**開啟**] 和 [**關閉**] 切換狀態, `OnColor`並在 iOS `Color.Orange`和 Android 上將屬性設為:

![IOS 和 Android 上開啟和關閉狀態的切換畫面快照](switch-images/switch-states-oncolor.png "IOS 和 Android 上的交換器")

## <a name="respond-to-a-switch-state-change"></a>回應交換器狀態變更

當屬性變更時, 不論是透過使用者操作, 或當應用程式`IsToggled`設定屬性時`Toggled` , 都會引發事件。 `IsToggled` 您可以註冊此事件的事件處理常式, 以回應變更:

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

事件`sender`處理常式中的引數`Switch`是負責引發此事件。 您可以使用`sender`屬性來`Switch`存取物件, 或區別共用同一個`Toggled`事件處理`Switch`程式的多個物件。

`Toggled`事件處理常式也可以在程式碼中指派:

```csharp
Switch switchControl = new Switch {...};
switchControl.Toggled += (sender, e) =>
{
    // Perform an action after examining e.Value
}
```

## <a name="data-bind-a-switch"></a>資料系結參數

您可以使用資料系結和觸發程式來回應變更的`Switch`切換狀態, 以消除事件處理常式。`Toggled`

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

在此範例中, [`Label`](xref:Xamarin.Forms.Label)會使用`DataTrigger`中的系結運算式來監視`IsToggled`名為`styleSwitch`之`Switch`的屬性。 當這個屬性變成`true`時`FontAttributes` , 的`FontSize`和屬性`Label`就會變更。 `FontAttributes`當屬性回到時`FontSize` , 的和屬性`Label`會重設為其初始狀態。 `false` `IsToggled`

如需觸發程式的詳細資訊, 請參閱[Xamarin。](~/xamarin-forms/app-fundamentals/triggers.md)

## <a name="disable-a-switch"></a>停用交換器

應用程式可能會進入正在切換的`Switch`狀態, 而這不是有效的作業。 在這種情況下`Switch` , 可以藉由將其`IsEnabled`屬性設`false`為來停用。 這會讓使用者無法操作`Switch`。

## <a name="related-links"></a>相關連結

* [切換示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-switchdemos/)
* [Xamarin. 表單觸發程式](~/xamarin-forms/app-fundamentals/triggers.md)
