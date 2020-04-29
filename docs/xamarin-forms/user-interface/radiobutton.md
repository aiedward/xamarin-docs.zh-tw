---
title: '[Xamarin] 選項按鈕'
description: '[Xamarin] 選項按鈕是一種按鈕類型，可讓使用者從集合中選取一個選項。 每個選項都是以一個選項按鈕表示，而且您只能選取一個群組中的一個選項按鈕。'
ms.prod: xamarin
ms.assetid: E2AA40E0-69A5-41DF-BFC4-C151CA657451
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/13/2020
ms.openlocfilehash: 128ab4f6f00adaf86afc08eba37bcb81d3a04a90
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532811"
---
# <a name="xamarinforms-radiobutton"></a>[Xamarin] 選項按鈕

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)

[Xamarin] `RadioButton`是一種按鈕類型，可讓使用者從集合中選取一個選項。 每個選項都是以一個選項按鈕表示，而且您只能選取一個群組中的一個選項按鈕。 `RadioButton`類別繼承自[`Button`](xref:Xamarin.Forms.Button)類別。

下列螢幕擷取畫面會`RadioButton`在 IOS 和 Android 上顯示處於已清除和已選取狀態的物件：

![IOS 和 Android 上所選和已清除狀態的選項按鈕螢幕擷取畫面](radiobutton-images/radiobutton-states.png "IOS 和 Android 上的選項按鈕")

> [!IMPORTANT]
> `RadioButton`目前為實驗性，而且只能透過設定`RadioButton_Experimental`旗標來使用。 如需詳細資訊，請參閱[實驗性旗標](~/xamarin-forms/internals/experimental-flags.md)。

`RadioButton`控制項會定義下列屬性：

- `IsChecked`，屬於類型`bool`，定義是否`RadioButton`選取。 這個屬性會使用`TwoWay`系結，且預設值為`false`。
- `GroupName`，屬於類型`string`，定義指定哪些`RadioButton`控制項互斥的名稱。 這個屬性的預設值是`null`。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以是資料系結的目標，以及樣式化的。

`RadioButton`控制項會定義當`CheckedChanged` `IsChecked`屬性變更時（透過使用者或程式設計操作）引發的事件。 伴隨`CheckedChangedEventArgs` `CheckedChanged`事件的物件具有名為的單一屬性， `Value`其類型`bool`為。 當引發事件時， `Value`屬性的值會設定為`IsChecked`屬性的新值。

此外， `RadioButton`類別會從[`Button`](xref:Xamarin.Forms.Button)類別繼承下列常用的屬性：

- [`Command`](xref:Xamarin.Forms.Button.Command)，屬於類型`ICommand`，這是在選取時`RadioButton`執行的。
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)，屬於類型`object`，這是傳遞至的`Command`參數。
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)，屬於類型[`FontAttributes`](xref:Xamarin.Forms.FontAttributes)，可決定文字樣式。
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)，屬於類型`string`，定義字型系列。
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)，屬於類型`double`，定義字型大小。
- [`Text`](xref:Xamarin.Forms.Button.Text)，屬於類型`string`，定義要顯示的文字。
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)，屬於類型[`Color`](xref:Xamarin.Forms.Color)，定義所顯示文字的色彩。

如需控制項的詳細[`Button`](xref:Xamarin.Forms.Button)資訊，請參閱 [ [Xamarin] 按鈕](~/xamarin-forms/user-interface/button.md)。

## <a name="create-radiobuttons"></a>建立選項按鈕

下列範例顯示如何在 XAML 中`RadioButton`具現化物件：

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton Text="Cat" />
    <RadioButton Text="Dog" />
    <RadioButton Text="Elephant" />
    <RadioButton Text="Monkey"
                 IsChecked="true" />
</StackLayout>
```

在此範例中`RadioButton` ，物件會在相同的父容器內隱含地分組。 此 XAML 會產生下列螢幕擷取畫面中顯示的外觀：

![在 iOS 和 Android 上隱含分組選項按鈕的螢幕擷取畫面](radiobutton-images/radiobuttons.png "在 iOS 和 Android 上以隱含方式分組的選項按鈕")

或者， `RadioButton`您也可以在程式碼中建立物件：

```csharp
StackLayout stackLayout = new StackLayout
{
    Children =
    {
        new Label { Text = "What's your favorite animal?" },
        new RadioButton { Text = "Cat" },
        new RadioButton { Text = "Dog" },
        new RadioButton { Text = "Elephant" },
        new RadioButton { Text = "Monkey", IsChecked = true }
    }
};
```

## <a name="group-radiobuttons"></a>群組選項按鈕

選項按鈕會在群組中工作，而且有兩種方法可以分組選項按鈕：

- 將它們放在相同的父容器中。 這就是所謂的隱含群組。
- 藉由將`GroupName`每個選項按鈕上的屬性設定為相同的值。 這就是所謂的明確分組。

下列 XAML 範例示範如何藉由`RadioButton`設定`GroupName`屬性來明確地分組物件：

```xaml
<Label Text="What's your favorite color?" />
<RadioButton Text="Red"
             TextColor="Red"
             GroupName="colors" />
<RadioButton Text="Green"
             TextColor="Green"
             GroupName="colors" />
<RadioButton Text="Blue"
             TextColor="Blue"
             GroupName="colors" />
<RadioButton Text="Other"
             GroupName="colors" />
```

在此範例中， `RadioButton`每個都是互斥的，因為`GroupName`它會共用相同的值。 此 XAML 會產生下列螢幕擷取畫面中顯示的外觀：

![在 iOS 和 Android 上明確分組的選項按鈕的螢幕擷取畫面](radiobutton-images/grouped-radiobuttons.png "在 iOS 和 Android 上明確分組的選項按鈕")

## <a name="respond-to-a-radiobutton-state-change"></a>回應選項按鈕狀態變更

選項按鈕有兩個狀態：已選取 或 已清除。 選取選項按鈕時，其`IsChecked`屬性為。 `true` 清除選項按鈕時，其`IsChecked`屬性為。 `false` 按一下同一個群組中的另一個選項按鈕，即可清除選項按鈕，但無法藉由再按一次來清除。 不過，您可以將選項按鈕的屬性設為`IsChecked` `false`，以程式設計方式清除它。

當`IsChecked`屬性變更時（不論是透過使用者或程式設計操作`CheckedChanged` ），都會引發事件。 您可以註冊此事件的事件處理常式，以回應變更：

```xaml
<RadioButton Text="Red"
             TextColor="Red"
             GroupName="colors"
             CheckedChanged="OnColorsRadioButtonCheckedChanged" />
```

程式碼後置包含`CheckedChanged`事件的處理常式：

```csharp
void OnColorsRadioButtonCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation
}
```

`sender`引數是負責`RadioButton`這個事件的。 您可以使用這個來存取`RadioButton`物件，或區別共用同一個`RadioButton` `CheckedChanged`事件處理常式的多個物件。

或者，也可以在程式碼`CheckedChanged`中註冊事件的事件處理常式：

```csharp
RadioButton radioButton = new RadioButton { ... };
radioButton.CheckedChanged += (sender, e) =>
{
    // Perform required operation
};
```

> [!NOTE]
> 回應`RadioButton`狀態變更的替代方法是定義`ICommand` ，並將它指派給`RadioButton.Command`屬性。 如需詳細資訊，請參閱[Button：使用命令介面](~/xamarin-forms/user-interface/button.md#using-the-command-interface)。

## <a name="radiobutton-visual-states"></a>選項按鈕視覺狀態

`RadioButton``IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState)具有，可以在選取時`RadioButton`用來起始視覺效果變更。

下列 XAML 範例顯示如何定義`IsChecked`狀態的視覺狀態：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="RadioButton">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Red" />
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="IsChecked">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Green" />
                                <Setter Property="Opacity"
                                        Value="1" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout>
        <Label Text="What's your favorite mode of transport?" />
        <RadioButton Text="Car"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Bike"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Train"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Walking"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
    </StackLayout>
</ContentPage>
```

在此範例中，隱含[`Style`](xref:Xamarin.Forms.Style)目標`RadioButton`物件。 `IsChecked` `RadioButton` `TextColor`指定當選取時，其屬性會設定為綠色，其`Opacity`值為1。 [`VisualState`](xref:Xamarin.Forms.VisualState) `Normal` `RadioButton` `TextColor`指定當處於已清除狀態時，其屬性會設定為紅色，其`Opacity`值為0.5。 `VisualState` 因此，整體的效果是當被清除`RadioButton`時，它會以紅色和部分透明的方式呈現，且在選取時不會有透明度：

![在 iOS 和 Android 上，由視覺狀態設定的選項按鈕外觀的螢幕擷取畫面](radiobutton-images/ischecked-visualstate.png "IOS 和 Android 上的選項按鈕視覺狀態")

如需視覺狀態的詳細資訊，請參閱 [ [Xamarin] 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="disable-a-radiobutton"></a>停用選項按鈕

有時候，應用程式會進入正在檢查`RadioButton`的狀態不是有效的作業。 在這種情況下`RadioButton` ，可以藉由將其`IsEnabled`屬性設`false`為來停用。

## <a name="related-links"></a>相關連結

- [選項按鈕示範（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)
- [[Xamarin] 按鈕](~/xamarin-forms/user-interface/button.md)
- [Xamarin. 表單視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
