---
title: Xamarin.Forms單選
description: Xamarin.Forms選項按鈕是一種按鈕類型，可讓使用者從集合中選取一個選項。 每個選項都是以一個選項按鈕表示，而且您只能選取一個群組中的一個選項按鈕。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f7cbd11f98127cb73514112dae785102ff9c51c0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127619"
---
# <a name="xamarinforms-radiobutton"></a>Xamarin.Forms單選

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)

Xamarin.Forms `RadioButton` 是一種按鈕類型，可讓使用者從集合中選取一個選項。 每個選項都是以一個選項按鈕表示，而且您只能選取一個群組中的一個選項按鈕。 `RadioButton`類別繼承自 [`Button`](xref:Xamarin.Forms.Button) 類別。

下列螢幕擷取畫面會在 `RadioButton` iOS 和 Android 上顯示處於已清除和已選取狀態的物件：

![IOS 和 Android 上所選和已清除狀態的選項按鈕螢幕擷取畫面](radiobutton-images/radiobutton-states.png "IOS 和 Android 上的選項按鈕")

> [!IMPORTANT]
> `RadioButton`目前為實驗性，而且只能透過設定旗標來使用 `RadioButton_Experimental` 。 如需詳細資訊，請參閱[實驗性旗標](~/xamarin-forms/internals/experimental-flags.md)。

`RadioButton`控制項會定義下列屬性：

- `IsChecked`，屬於類型 `bool` ，定義是否 `RadioButton` 選取。 這個屬性會使用系結 `TwoWay` ，且預設值為 `false` 。
- `GroupName`，屬於類型 `string` ，定義指定哪些控制項互斥的名稱 `RadioButton` 。 這個屬性的預設值是 `null` 。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

`RadioButton`控制項 `CheckedChanged` 會定義當 `IsChecked` 屬性變更時（透過使用者或程式設計操作）引發的事件。 `CheckedChangedEventArgs`伴隨事件的物件 `CheckedChanged` 具有名為的單一屬性 `Value` ，其類型為 `bool` 。 當引發事件時，屬性的值 `Value` 會設定為屬性的新值 `IsChecked` 。

此外，類別會 `RadioButton` 從類別繼承下列常用的屬性 [`Button`](xref:Xamarin.Forms.Button) ：

- [`Command`](xref:Xamarin.Forms.Button.Command)，屬於類型 `ICommand` ，這是在選取時執行的 `RadioButton` 。
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)，屬於類型 `object` ，這是傳遞至的參數 `Command` 。
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)，屬於類型 [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) ，可決定文字樣式。
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)，屬於類型 `string` ，定義字型系列。
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)，屬於類型 `double` ，定義字型大小。
- [`Text`](xref:Xamarin.Forms.Button.Text)，屬於類型 `string` ，定義要顯示的文字。
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)，屬於類型 [`Color`](xref:Xamarin.Forms.Color) ，定義所顯示文字的色彩。

如需控制項的詳細資訊 [`Button`](xref:Xamarin.Forms.Button) ，請參閱[ Xamarin.Forms 按鈕](~/xamarin-forms/user-interface/button.md)。

## <a name="create-radiobuttons"></a>建立選項按鈕

下列範例顯示如何 `RadioButton` 在 XAML 中具現化物件：

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

在此範例中， `RadioButton` 物件會在相同的父容器內隱含地分組。 此 XAML 會產生下列螢幕擷取畫面中顯示的外觀：

![在 iOS 和 Android 上隱含分組選項按鈕的螢幕擷取畫面](radiobutton-images/radiobuttons.png "在 iOS 和 Android 上以隱含方式分組的選項按鈕")

或者，您也 `RadioButton` 可以在程式碼中建立物件：

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
- 藉由將 `GroupName` 每個選項按鈕上的屬性設定為相同的值。 這就是所謂的明確分組。

下列 XAML 範例示範如何藉 `RadioButton` 由設定屬性來明確地分組物件 `GroupName` ：

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

在此範例中，每個 `RadioButton` 都是互斥的，因為它會共用相同的 `GroupName` 值。 此 XAML 會產生下列螢幕擷取畫面中顯示的外觀：

![在 iOS 和 Android 上明確分組的選項按鈕的螢幕擷取畫面](radiobutton-images/grouped-radiobuttons.png "在 iOS 和 Android 上明確分組的選項按鈕")

## <a name="respond-to-a-radiobutton-state-change"></a>回應選項按鈕狀態變更

選項按鈕有兩個狀態：已選取 或 已清除。 選取選項按鈕時，其 `IsChecked` 屬性為 `true` 。 清除選項按鈕時，其 `IsChecked` 屬性為 `false` 。 按一下同一個群組中的另一個選項按鈕，即可清除選項按鈕，但無法藉由再按一次來清除。 不過，您可以將選項按鈕的屬性設為，以程式設計方式清除它 `IsChecked` `false` 。

當 `IsChecked` 屬性變更時（不論是透過使用者或程式設計操作），都會 `CheckedChanged` 引發事件。 您可以註冊此事件的事件處理常式，以回應變更：

```xaml
<RadioButton Text="Red"
             TextColor="Red"
             GroupName="colors"
             CheckedChanged="OnColorsRadioButtonCheckedChanged" />
```

程式碼後置包含事件的處理常式 `CheckedChanged` ：

```csharp
void OnColorsRadioButtonCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation
}
```

`sender`引數是 `RadioButton` 負責這個事件的。 您可以使用這個來存取 `RadioButton` 物件，或區別 `RadioButton` 共用同一個事件處理常式的多個物件 `CheckedChanged` 。

或者，也 `CheckedChanged` 可以在程式碼中註冊事件的事件處理常式：

```csharp
RadioButton radioButton = new RadioButton { ... };
radioButton.CheckedChanged += (sender, e) =>
{
    // Perform required operation
};
```

> [!NOTE]
> 回應狀態變更的替代方法 `RadioButton` 是定義 `ICommand` ，並將它指派給 `RadioButton.Command` 屬性。 如需詳細資訊，請參閱[Button：使用命令介面](~/xamarin-forms/user-interface/button.md#using-the-command-interface)。

## <a name="radiobutton-visual-states"></a>選項按鈕視覺狀態

`RadioButton`具有 `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) ，可以在選取時用來起始視覺效果變更 `RadioButton` 。

下列 XAML 範例顯示如何定義狀態的視覺狀態 `IsChecked` ：

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

在此範例中，隱含 [`Style`](xref:Xamarin.Forms.Style) 目標 `RadioButton` 物件。 `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) 指定當 `RadioButton` 選取時，其 `TextColor` 屬性會設定為綠色，其 `Opacity` 值為1。 `Normal` `VisualState` 指定當 `RadioButton` 處於已清除狀態時，其 `TextColor` 屬性會設定為紅色，其 `Opacity` 值為0.5。 因此，整體的效果是當 `RadioButton` 被清除時，它會以紅色和部分透明的方式呈現，且在選取時不會有透明度：

![在 iOS 和 Android 上，由視覺狀態設定的選項按鈕外觀的螢幕擷取畫面](radiobutton-images/ischecked-visualstate.png "IOS 和 Android 上的選項按鈕視覺狀態")

如需視覺狀態的詳細資訊，請參閱[ Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="disable-a-radiobutton"></a>停用選項按鈕

有時候，應用程式會進入正在檢查的狀態不是有效的作業 `RadioButton` 。 在這種情況下， `RadioButton` 可以藉由將其 `IsEnabled` 屬性設為來停用 `false` 。

## <a name="related-links"></a>相關連結

- [選項按鈕示範（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)
- [Xamarin.FormsButton](~/xamarin-forms/user-interface/button.md)
- [Xamarin.Forms視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
