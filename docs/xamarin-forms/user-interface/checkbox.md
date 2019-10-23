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
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "68739152"
---
# <a name="xamarinforms-checkbox"></a>[Xamarin] 核取方塊

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

[Xamarin] `CheckBox` 是可核取或空白的按鈕類型。 勾選核取方塊時，會將其視為開啟。 當 checkbox 空白時，會被視為關閉。

`CheckBox` 定義名為 `IsChecked` 的 `bool` 屬性，指出是否已核取 `CheckBox`。 這個屬性也是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它可以樣式化，而是資料系結的目標。

> [!NOTE]
> @No__t_0 可系結屬性的預設系結模式為[`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)。

`CheckBox` 定義當 `IsChecked` 屬性變更時（不論是透過使用者操作，還是當應用程式設定 `IsChecked` 屬性時，所引發的 `CheckedChanged` 事件）。 隨附 `CheckedChanged` 事件的 `CheckedChangedEventArgs` 物件具有名為 `Value` 的單一屬性，其類型為 `bool`。 當引發事件時，`Value` 屬性的值會設定為 `IsChecked` 屬性的新值。

## <a name="create-a-checkbox"></a>建立核取方塊

下列範例顯示如何在 XAML 中具現化 `CheckBox`：

```xaml
<CheckBox />
```

此 XAML 會產生下列螢幕擷取畫面中顯示的外觀：

![IOS 和 Android 上空白核取方塊的螢幕擷取畫面](checkbox-images/checkbox-empty.png "空白核取方塊")

根據預設，`CheckBox` 是空的。 @No__t_0 可以透過使用者操作來檢查，或將 `IsChecked` 屬性設定為 `true`：

```xaml
<CheckBox IsChecked="true" />
```

此 XAML 會產生下列螢幕擷取畫面中顯示的外觀：

![IOS 和 Android 上已核取核取方塊的螢幕擷取畫面](checkbox-images/checkbox-checked.png "核取核取方塊")

或者，您也可以在程式碼中建立 `CheckBox`：

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>回應 CheckBox 變更狀態

當 `IsChecked` 屬性變更時（不論是透過使用者操作，或當應用程式設定 `IsChecked` 屬性時），都會引發 `CheckedChanged` 事件。 您可以註冊此事件的事件處理常式，以回應變更：

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

程式碼後置檔案包含 `CheckedChanged` 事件的處理常式：

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

@No__t_0 引數是負責此事件的 `CheckBox`。 您可以使用這個來存取 `CheckBox` 物件，或區別共用相同 `CheckedChanged` 事件的多個 `CheckBox` 物件。

或者，您可以在程式碼中註冊 `CheckedChanged` 事件的事件處理常式：

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>資料系結核取方塊

您可以使用資料系結和觸發程式來回應所檢查或空白的 `CheckBox`，以消除 `CheckedChanged` 事件處理常式：

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

在此範例中， [`Label`](xref:Xamarin.Forms.Label)在資料觸發程式中使用系結運算式來監視 `CheckBox` 的 `IsChecked` 屬性。 當這個屬性 `true` 時，`Label` 的 `FontAttributes` 和 `FontSize` 屬性會變更。 當 `IsChecked` 屬性回到 `false` 時，`Label` 的 `FontAttributes` 和 `FontSize` 屬性會重設為其初始狀態。

在下列螢幕擷取畫面中，當 `CheckBox` 為空白時，iOS 螢幕擷取畫面會顯示[`Label`](xref:Xamarin.Forms.Label)格式，而 Android 螢幕擷取畫面會顯示 `CheckBox` 核取時的 `Label` 格式：

[![IOS 和 Android 上的 [資料系結] 核取方塊的螢幕擷取畫面](checkbox-images/checkbox-databinding.png "[資料系結] 核取方塊")](checkbox-images/checkbox-databinding-large.png#lightbox "[資料系結] 核取方塊")

如需觸發程式的詳細資訊，請參閱[Xamarin. Forms 觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式。

## <a name="disable-a-checkbox"></a>停用核取方塊

有時候應用程式會進入狀態，其中所要檢查的 `CheckBox` 不是有效的作業。 在這種情況下，`CheckBox` 可以藉由將其 `IsEnabled` 屬性設定為 `false` 來停用。

## <a name="checkbox-appearance"></a>核取方塊外觀

除了 `CheckBox` 繼承自[`View`](xref:Xamarin.Forms.View)類別的屬性之外，`CheckBox` 也會定義 `Color` 屬性，將其色彩設為[`Color`](xref:Xamarin.Forms.Color)：

```xaml
<CheckBox Color="Red" />
```

下列螢幕擷取畫面顯示一系列已檢查的 `CheckBox` 物件，其中每個物件的 `Color` 屬性都設定為不同的[`Color`](xref:Xamarin.Forms.Color)：

![IOS 和 Android 上彩色核取方塊的螢幕擷取畫面](checkbox-images/checkbox-colors.png "彩色核取方塊")

## <a name="checkbox-visual-states"></a>核取方塊視覺狀態

`CheckBox` 具有 `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) ，可用來在選取時對 `CheckBox` 起始視覺效果變更。

下列 XAML 範例顯示如何定義 `IsChecked` 狀態的視覺狀態：

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

在此範例中，`IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState)指定選取 `CheckBox` 時，其 `Color` 屬性會設定為綠色。 @No__t_0 `VisualState` 指定當 `CheckBox` 處於正常狀態時，其 `Color` 屬性會設定為紅色。 因此，整體效果是當它是空的時，`CheckBox` 為紅色，而當核取時則為綠色。

如需視覺狀態的詳細資訊，請參閱 [ [Xamarin] 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相關連結

- [核取方塊示範（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Xamarin. 表單觸發程式](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin. 表單視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
