---
title: '[Xamarin] 核取方塊'
description: '[Xamarin] 核取方塊是一種可核取或空白的按鈕類型。 勾選核取方塊時，會將其視為開啟。 當 checkbox 空白時，會被視為關閉。'
ms.prod: xamarin
ms.assetid: B8B9268B-BCB8-42B9-B08C-C0F22C137238
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: f78ca9d2cf7a9e57b81c5d923c64b36a7982c4b0
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "68739152"
---
# <a name="xamarinforms-checkbox"></a>[Xamarin] 核取方塊

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

[Xamarin `CheckBox` ] 是可以核取或空白的按鈕類型。 勾選核取方塊時，會將其視為開啟。 當 checkbox 空白時，會被視為關閉。

`CheckBox`定義名為`IsChecked`的`CheckBox`屬性，它會指出是否已核取。 `bool` 這個屬性也是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它可以樣式化，而是資料系結的目標。

> [!NOTE]
> 可系結屬性的預設系結模式[`BindingMode.TwoWay`為。](xref:Xamarin.Forms.BindingMode.TwoWay) `IsChecked`

`CheckBox`定義當屬性變更時（不論是透過使用者操作，還是`IsChecked`當應用程式設定屬性時，所引發的事件）。`CheckedChanged` `IsChecked` 伴隨事件的`CheckedChangedEventArgs`物件具有名為的單一屬性, `Value`其類型`bool`為。 `CheckedChanged` 當引發事件時， `Value`屬性的值會設定為`IsChecked`屬性的新值。

## <a name="create-a-checkbox"></a>建立核取方塊

下列範例示範如何具現化`CheckBox`在 XAML 中：

```xaml
<CheckBox />
```

此 XAML 會產生下列螢幕擷取畫面中顯示的外觀：

![IOS 和 Android 上空白核取方塊的螢幕擷取畫面](checkbox-images/checkbox-empty.png "空白核取方塊")

根據預設，是`CheckBox`空的。 使用者操作`IsChecked` 可以檢查`true`，或將屬性設定為： `CheckBox`

```xaml
<CheckBox IsChecked="true" />
```

此 XAML 會產生下列螢幕擷取畫面中顯示的外觀：

![IOS 和 Android 上已核取核取方塊的螢幕擷取畫面](checkbox-images/checkbox-checked.png "核取核取方塊")

或者， `CheckBox`可以在程式碼中建立：

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>回應 CheckBox 變更狀態

當屬性變更時，不論是透過使用者操作，或當應用程式`IsChecked`設定屬性時`CheckedChanged` ，都會引發事件。 `IsChecked` 您可以註冊此事件的事件處理常式，以回應變更：

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

程式碼後置檔案包含的處理常式`CheckedChanged`事件：

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

`sender`引數是`CheckBox`負責此事件。 您可以使用來存取`CheckBox`物件，或若要區別多個`CheckBox`共用相同的物件`CheckedChanged`事件。

或者，也可以在程式碼`CheckedChanged`中註冊事件的事件處理常式：

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>資料系結核取方塊

您可以使用資料系結和觸發程式來回應`CheckBox`已檢查或空白，以消除事件處理常式：`CheckedChanged`

```xaml
<CheckBox x:Name="checkBox" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

在此範例中， [`Label`](xref:Xamarin.Forms.Label)會在資料觸發程式中使用系結運算式來`IsChecked`監視的屬性`CheckBox`。 當這個屬性變成`true`時`FontAttributes` ，會`FontSize` `Label`變更的和屬性。 `FontAttributes`當屬性回到時`FontSize` ，的和屬性`Label`會重設為其初始狀態。 `false` `IsChecked`

在下列[`Label`](xref:Xamarin.Forms.Label)螢幕擷取畫面中， `CheckBox`當是空的時，iOS 螢幕擷取畫面會顯示格式，而 Android 螢幕`Label`快照`CheckBox`會顯示已核取時的格式：

[![IOS 和 Android 上的 [資料系結] 核取方塊的螢幕擷取畫面][資料系結(checkbox-images/checkbox-databinding.png "] 核取方塊")](checkbox-images/checkbox-databinding-large.png#lightbox "[資料系結] 核取方塊")

如需觸發程式的詳細資訊，請參閱[Xamarin. Forms 觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式。

## <a name="disable-a-checkbox"></a>停用核取方塊

有時候，應用程式會進入正在檢查`CheckBox`的狀態不是有效的作業。 在這種情況下`CheckBox` ，可以藉由將其`IsEnabled`屬性設`false`為來停用。

## <a name="checkbox-appearance"></a>核取方塊外觀

除了`CheckBox`繼承`CheckBox` [`Color`](xref:Xamarin.Forms.Color)自[`View`類別的](xref:Xamarin.Forms.View)屬性之外，也會定義將其色彩設為的屬性：`Color`

```xaml
<CheckBox Color="Red" />
```

下列螢幕擷取畫面顯示一系列的已`CheckBox`檢查物件，其中每個物件`Color`的屬性都設定為[`Color`](xref:Xamarin.Forms.Color)不同的：

![IOS 和 Android 上彩色核取方塊的螢幕擷取畫面](checkbox-images/checkbox-colors.png "彩色核取方塊")

## <a name="checkbox-visual-states"></a>核取方塊視覺狀態

`CheckBox`具有，其可在核取`CheckBox`時用來起始視覺效果變更。 [`VisualState`](xref:Xamarin.Forms.VisualState) `IsChecked`

下列 XAML 範例示範如何定義視覺狀態的`IsChecked`狀態：

```xaml
<CheckBox ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Red" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="IsChecked">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Green" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</CheckBox>
```

在此範例中， `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) `CheckBox`會指定當核取時，其`Color`屬性會設定為綠色。 指定當`CheckBox`處於正常狀態時， `Color`其屬性會設定為紅色。 `Normal` `VisualState` 因此，整體效果是當它是`CheckBox`空的時為紅色，而當核取時為綠色。

如需視覺狀態的詳細資訊，請參閱 [ [Xamarin] 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相關連結

- [核取方塊示範（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Xamarin. 表單觸發程式](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin. 表單視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
