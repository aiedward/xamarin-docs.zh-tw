---
title: Xamarin.Forms 核取方塊
description: Xamarin.Forms 的核取方塊是按鈕的一種是按鈕的要檢查或空。 若勾選核取方塊，其認為上。 當核取方塊是空的時它已被視為關閉。
ms.prod: xamarin
ms.assetid: B8B9268B-BCB8-42B9-B08C-C0F22C137238
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: 42631b1b67dc1d342e9f8666916604e68ee158d8
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67517917"
---
# <a name="xamarinforms-checkbox"></a>Xamarin.Forms 核取方塊

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CheckBoxDemos)

Xamarin.Forms`CheckBox`一種按鈕，可以為已核取或空白。 若勾選核取方塊，其認為上。 當核取方塊是空的時它已被視為關閉。

`CheckBox` 定義`bool`名為屬性`IsChecked`，表示是否`CheckBox`已核取。 此屬性也由[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示它可以自訂樣式，並成為資料繫結的目標。

> [!NOTE]
> `IsChecked`可繫結的屬性具有預設繫結模式[ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)。

`CheckBox` 定義`CheckedChanged`具有時引發的事件`IsChecked`屬性變更，透過使用者操作，或當應用程式設定`IsChecked`屬性。 `CheckedChangedEventArgs`隨附的物件`CheckedChanged`事件會有一個名為的單一屬性`Value`，型別的`bool`。 當事件引發時，windows 7`Value`屬性設定為新值`IsChecked`屬性。

## <a name="create-a-checkbox"></a>建立一個核取方塊

下列範例示範如何具現化`CheckBox`在 XAML 中：

```xaml
<CheckBox />
```

此 XAML 會導致下列的螢幕擷取畫面所示的外觀：

![空白的核取方塊，在 iOS 和 Android 上的螢幕擷取畫面](checkbox-images/checkbox-empty.png "空的核取方塊")

根據預設，`CheckBox`是空的。 `CheckBox`可以透過使用者操作，或藉由設定`IsChecked`屬性設`true`:

```xaml
<CheckBox IsChecked="true" />
```

此 XAML 會導致下列的螢幕擷取畫面所示的外觀：

![已核取核取方塊，在 iOS 和 Android 上的螢幕擷取畫面](checkbox-images/checkbox-checked.png "簽核取方塊")

或者，`CheckBox`可由程式碼：

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>回應變更狀態的核取方塊

當`IsChecked`屬性變更，透過使用者操作，或當應用程式設定`IsChecked`屬性，`CheckedChanged`引發事件。 此事件的事件處理常式可登錄以便回應變更：

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

或者，事件處理常式`CheckedChanged`可以在程式碼中註冊事件：

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>將資料繫結的核取方塊

`CheckedChanged`事件處理常式會消除，可以使用資料繫結和觸發程序來回應`CheckBox`核取或空白：

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

在此範例中， [ `Label` ](xref:Xamarin.Forms.Label)資料觸發程序中使用的繫結運算式，來監視`IsChecked`屬性`CheckBox`。 當這個屬性會變成`true`，則`FontAttributes`並`FontSize`的屬性`Label`變更。 時`IsChecked`屬性會傳回到`false`，則`FontAttributes`並`FontSize`的屬性`Label`會重設為其初始狀態。

在下列螢幕擷取畫面，顯示 iOS 螢幕擷取畫面[ `Label` ](xref:Xamarin.Forms.Label)格式化時`CheckBox`是空的而 Android 的螢幕擷取畫面顯示`Label`格式化時`CheckBox`已核取：

[![螢幕擷取畫面的資料繫結 iOS 和 Android 上的核取方塊，](checkbox-images/checkbox-databinding.png "資料繫結的核取方塊")](checkbox-images/checkbox-databinding-large.png#lightbox "資料繫結的核取方塊")

如需有關觸發程序的詳細資訊，請參閱[Xamarin.Forms 觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)。

## <a name="disable-a-checkbox"></a>停用核取方塊

有時候應用程式便會進入狀態其中`CheckBox`正在檢查不是有效的作業。 在此情況下，`CheckBox`您可以藉由設定停用其`IsEnabled`屬性設`false`。

## <a name="checkbox-appearance"></a>核取方塊的外觀

除了屬性之外，`CheckBox`繼承自[ `View` ](xref:Xamarin.Forms.View)類別`CheckBox`也會定義`Color`屬性，將其色彩會設定為[ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<CheckBox Color="Red" />
```

下列螢幕擷取畫面顯示一系列的核取`CheckBox`物件，其中每個物件都有其`Color`屬性設定為不同[ `Color` ](xref:Xamarin.Forms.Color):

![彩色核取方塊，在 iOS 和 Android 上的螢幕擷取畫面](checkbox-images/checkbox-colors.png "彩色的核取方塊")

## <a name="checkbox-visual-states"></a>核取方塊的視覺狀態

`CheckBox` 已`IsChecked` [ `VisualState` ](xref:Xamarin.Forms.VisualState)可用來起始 visual 變更`CheckBox`變成勾選時。

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

在此範例中， `IsChecked` [ `VisualState` ](xref:Xamarin.Forms.VisualState)指定`CheckBox`會檢查其`Color`屬性會設定為綠色。 `Normal` `VisualState`指定`CheckBox`處於正常狀態，其`Color`屬性會設定為紅色。 因此，整體影響在於`CheckBox`是紅色，當它是空的而綠色勾選時。

如需有關視覺狀態的詳細資訊，請參閱[Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相關連結

- [核取方塊示範 （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CheckBoxDemos)
- [Xamarin.Forms 的觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
