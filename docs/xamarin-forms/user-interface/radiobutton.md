---
title: Xamarin.Forms RadioButton
description: Xamarin.Forms選項按鈕是一種按鈕，可讓使用者從集合中選取一個選項。 每個選項都會以一個選項按鈕表示，而且您只能在群組中選取一個選項按鈕。
ms.prod: xamarin
ms.assetid: E2AA40E0-69A5-41DF-BFC4-C151CA657451
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e57003825e0ea77d54ec528b18328e98f3d1b81e
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939846"
---
# <a name="no-locxamarinforms-radiobutton"></a>Xamarin.Forms RadioButton

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)

Xamarin.Forms [`RadioButton`](xref:Xamarin.Forms.RadioButton) 是一種按鈕，可讓使用者從集合中選取一個選項。 每個選項都會以一個選項按鈕表示，而且您只能在群組中選取一個選項按鈕。 依預設，每一個都會 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 顯示文字：

![預設選項按鈕的螢幕擷取畫面](radiobutton-images/radiobuttons-default.png "預設選項按鈕")

不過，在某些平臺上， [`RadioButton`](xref:Xamarin.Forms.RadioButton) 可能會顯示 [`View`](xref:Xamarin.Forms.View) ，而且在所有平臺上，都 `RadioButton` 可以使用下列程式來重新定義每個平臺的外觀 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) ：

![重新定義選項按鈕的螢幕擷取畫面](radiobutton-images/radiobuttons-controltemplate.png "重新定義選項按鈕")

[`RadioButton`](xref:Xamarin.Forms.RadioButton)控制項會定義下列屬性：

- `Content`，類型為 `object` ，定義 `string` [`View`](xref:Xamarin.Forms.View) 要顯示的或 `RadioButton` 。
- `IsChecked`，類型為 `bool` ，定義是否已 `RadioButton` 核取。 這個屬性會使用系結 `TwoWay` ，而且具有的預設值 `false` 。
- `GroupName`，類型為 `string` ，其定義指定哪些 `RadioButton` 控制項為互斥的名稱。 這個屬性的預設值為 `null` 。
- `Value`，類型為 `object` ，其定義與相關聯的選擇性唯一值 `RadioButton` 。
- `BorderColor`，類型為 [`Color`](xref:Xamarin.Forms.Color) ，定義框線筆觸色彩。
- `BorderWidth`，類型為 `double` ，定義框線的寬度 `RadioButton` 。
- `CharacterSpacing`，類型為 `double` ，定義任何所顯示文字字元之間的間距。
- `CornerRadius`型別為的， `int` 定義的圓角半徑 `RadioButton` 。
- `FontAttributes`， [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) 可決定文字樣式的型別。
- `FontFamily`，類型為 `string` ，定義字型家族。
- `FontSize`，類型為 `double` ，定義字型大小。
- `TextColor`，類型為 [`Color`](xref:Xamarin.Forms.Color) ，定義任何所顯示文字的色彩。
- `TextTransform`，類型為 `TextTransform` ，定義任何所顯示文字的大小寫。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

[`RadioButton`](xref:Xamarin.Forms.RadioButton)控制項也 `CheckedChanged` 會定義屬性變更時所引發的事件 `IsChecked` ，不論是透過使用者或程式設計操作。 `CheckedChangedEventArgs`事件隨附的物件 `CheckedChanged` 具有名為的單一屬性 `Value` ，類型為 `bool` 。 引發事件時，屬性的值 `CheckedChangedEventArgs.Value` 會設定為屬性的新值 `IsChecked` 。

[`RadioButton`](xref:Xamarin.Forms.RadioButton) 群組可以由類別來管理 `RadioButtonGroup` ，這會定義下列附加屬性：

-   `GroupName`，類型為 `string` ，定義中物件的組名 `RadioButton` `Layout<View>` 。
- `SelectedValue`， `object` 代表群組中已檢查物件的值 `RadioButton` `Layout<View>` 。 此附加屬性預設會使用系結 `TwoWay` 。

如需附加屬性的詳細資訊 `GroupName` ，請參閱 [群組選項按鈕](#group-radiobuttons)。 如需附加屬性的詳細資訊 `SelectedValue` ，請參閱 [回應選項按鈕狀態變更](#respond-to-radiobutton-state-changes)。

## <a name="create-radiobuttons"></a>建立選項按鈕

的外觀 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 是由指派給屬性的資料類型所定義 `RadioButton.Content` ：

- 當 `RadioButton.Content` 指派給屬性時 `string` ，它會顯示在每個平臺上，以水準對齊的方式顯示在選項按鈕圓形旁。
- 當 `RadioButton.Content` 指派給時 [`View`](xref:Xamarin.Forms.View) ，它會顯示在支援的平臺上 (IOS、UWP) ，而不支援的平臺則會回復為 `View` (Android) 之物件的字串表示。 在這兩種情況下，內容會以水準方式顯示在選項按鈕圓形的旁邊。
- 當套用 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 至時 [`RadioButton`](xref:Xamarin.Forms.RadioButton) ， [`View`](xref:Xamarin.Forms.View) 可以指派給 `RadioButton.Content` 所有平臺上的屬性。 如需詳細資訊，請參閱 [重新定義選項按鈕外觀](#redefine-radiobutton-appearance)。

### <a name="display-string-based-content"></a>顯示以字串為基礎的內容

[`RadioButton`](xref:Xamarin.Forms.RadioButton)當 `Content` 指派給屬性時，會顯示文字 `string` ：

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton Content="Cat" />
    <RadioButton Content="Dog" />
    <RadioButton Content="Elephant" />
    <RadioButton Content="Monkey"
                 IsChecked="true" />
</StackLayout>
```

在此範例中， [`RadioButton`](xref:Xamarin.Forms.RadioButton) 物件會隱含地分組在相同的父容器內。 此 XAML 會產生下列螢幕擷取畫面中顯示的外觀：

![以文字為基礎之選項按鈕的螢幕擷取畫面](radiobutton-images/radiobuttons-text.png "以文字為基礎的選項按鈕")

### <a name="display-arbitrary-content"></a>顯示任意內容

在 iOS 和 UWP 上， [`RadioButton`](xref:Xamarin.Forms.RadioButton) 當指派給屬性時，可以顯示任意內容 `Content` [`View`](xref:Xamarin.Forms.View) ：

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton>
        <RadioButton.Content>
            <Image Source="cat.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton>
        <RadioButton.Content>
            <Image Source="dog.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton>
        <RadioButton.Content>
            <Image Source="elephant.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton>
        <RadioButton.Content>
            <Image Source="monkey.png" />
        </RadioButton.Content>
    </RadioButton>
</StackLayout>
```

在此範例中， [`RadioButton`](xref:Xamarin.Forms.RadioButton) 物件會隱含地分組在相同的父容器內。 此 XAML 會產生下列螢幕擷取畫面中顯示的外觀：

![以視圖為基礎之選項按鈕的螢幕擷取畫面](radiobutton-images/radiobuttons-view.png "以視圖為基礎的選項按鈕")

在 Android 上， [`RadioButton`](xref:Xamarin.Forms.RadioButton) 物件將會顯示 [`View`](xref:Xamarin.Forms.View) 已設定為內容之物件的字串標記法：

![Android 上以 view 為基礎之選項按鈕的螢幕擷取畫面](radiobutton-images/radiobutton-view-android.png "Android 上以視圖為基礎的選項按鈕")

> [!NOTE]
> 當套用 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 至時 [`RadioButton`](xref:Xamarin.Forms.RadioButton) ， [`View`](xref:Xamarin.Forms.View) 可以指派給 `RadioButton.Content` 所有平臺上的屬性。 如需詳細資訊，請參閱 [重新定義選項按鈕外觀](#redefine-radiobutton-appearance)。

## <a name="associate-values-with-radiobuttons"></a>將值與選項按鈕產生關聯

每個 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 物件都有一個型別為 `Value` 的屬性， `object` 它會定義要與選項按鈕相關聯的選擇性唯一值。 這可讓的值與 `RadioButton` 其內容不同，並且在物件顯示物件時特別有用 `RadioButton` [`View`](xref:Xamarin.Forms.View) 。

下列 XAML 顯示如何設定 `Content` `Value` 每個物件的和屬性 [`RadioButton`](xref:Xamarin.Forms.RadioButton) ：

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton Value="Cat">
        <RadioButton.Content>
            <Image Source="cat.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton Value="Dog">
        <RadioButton.Content>
            <Image Source="dog.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton Value="Elephant">
        <RadioButton.Content>
            <Image Source="elephant.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton Value="Monkey">
        <RadioButton.Content>
            <Image Source="monkey.png" />
        </RadioButton.Content>
    </RadioButton>
</StackLayout>
```

在此範例中，每個 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 都有 [`Image`](xref:Xamarin.Forms.Image) 做為其內容，同時也會定義以字串為基礎的值。 這可讓您輕鬆地識別已核取之選項按鈕的值。

## <a name="group-radiobuttons"></a>群組選項按鈕

選項按鈕可在群組中運作，而且有三種方法可以分組選項按鈕：

- 將它們放在相同的父容器內。 這就是所謂的 *隱含* 群組。
- 在 `GroupName` 群組中的每個選項按鈕上，將屬性設定為相同的值。 這就是所謂的 *明確* 群組。
- `RadioButtonGroup.GroupName`在父容器上設定附加屬性，進而設定 `GroupName` [`RadioButton`](xref:Xamarin.Forms.RadioButton) 容器中任何物件的屬性。 這也稱為 *明確* 群組。

> [!IMPORTANT]
> [`RadioButton`](xref:Xamarin.Forms.RadioButton) 物件不必屬於要分組的相同父代。 如果它們共用組名，它們就是互斥的。

### <a name="explicit-grouping-with-the-groupname-property"></a>具有 [群組] 屬性的明確群組

下列 XAML 範例顯示如何藉 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 由設定屬性來明確分組物件 `GroupName` ：

```xaml
<Label Text="What's your favorite color?" />
<RadioButton Text="Red"
             GroupName="colors" />
<RadioButton Text="Green"
             GroupName="colors" />
<RadioButton Text="Blue"
             GroupName="colors" />
<RadioButton Text="Other"
             GroupName="colors" />
```

在此範例中，每個 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 都是互斥的，因為它會共用相同的 `GroupName` 值。

### <a name="explicit-grouping-with-the-radiobuttongroupgroupname-attached-property"></a>具有 RadioButtonGroup 附加屬性的明確群組

`RadioButtonGroup`類別 `GroupName` `string` 會定義可以在物件上設定之型別的附加屬性 `Layout<View>` 。 這可將任何版面配置轉換成選項按鈕群組：

```xaml
<StackLayout RadioButtonGroup.GroupName="colors">
    <Label Text="What's your favorite color?" />
    <RadioButton Content="Red" />
    <RadioButton Content="Green" />
    <RadioButton Content="Blue" />
    <RadioButton Content="Other" />
</StackLayout>
```

在此範例中， [`RadioButton`](xref:Xamarin.Forms.RadioButton) 中的每個 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 都會將其 `GroupName` 屬性設定為 `fruits` ，而且將會相互排斥。

> [!NOTE]
> 當 `Layout<View` 設定附加屬性的> 物件 `RadioButtonGroup.GroupName` 包含 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 設定其屬性的時 `GroupName` ，屬性的值 `RadioButton.GroupName` 將會優先。

## <a name="respond-to-radiobutton-state-changes"></a>回應選項按鈕狀態變更

選項按鈕有兩種狀態：已勾選或未勾選。 選取選項按鈕時，其 `IsChecked` 屬性為 `true` 。 未選取選項按鈕時，其 `IsChecked` 屬性為 `false` 。 您可以藉由在相同群組中的另一個選項按鈕來清除選項按鈕，但無法藉由再次加以清除。 不過，您可以將選項按鈕的 `IsChecked` 屬性設定為 `false`，以程式設計方式清除該選項按鈕。

### <a name="respond-to-an-event-firing"></a>回應事件引發

當 `IsChecked` 屬性變更時，無論是透過使用者或程式設計操作，都會 `CheckedChanged` 引發事件。 您可以註冊這個事件的事件處理常式，以回應變更：

```xaml
<RadioButton Text="Red"
             GroupName="colors"
             CheckedChanged="OnColorsRadioButtonCheckedChanged" />
```

程式碼後端包含事件的處理常式 `CheckedChanged` ：

```csharp
void OnColorsRadioButtonCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation
}
```

`sender`引數是 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 此事件的負責任。 您可以使用這個來存取 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 物件，或區別多個 `RadioButton` 共用相同 `CheckedChanged` 事件處理常式的物件。

### <a name="respond-to-a-property-change"></a>回應屬性變更

`RadioButtonGroup`類別 `SelectedValue` `object` 會定義可以在物件上設定之型別的附加屬性 `Layout<View>` 。 此附加屬性代表在配置上定義的群組內所檢查的值 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 。

當 `IsChecked` 屬性變更時，無論是透過使用者或程式設計操作， `RadioButtonGroup.SelectedValue` 附加屬性也會變更。 因此， `RadioButtonGroup.SelectedValue` 附加屬性可以是資料系結至儲存使用者選取專案的屬性：

```xaml
<StackLayout RadioButtonGroup.GroupName="{Binding GroupName}"
             RadioButtonGroup.SelectedValue="{Binding Selection}">
    <Label Text="What's your favorite animal?" />
    <RadioButton Content="Cat"
                 Value="Cat" />
    <RadioButton Content="Dog"
                 Value="Dog" />
    <RadioButton Content="Elephant"
                 Value="Elephant" />
    <RadioButton Content="Monkey"
                 Value="Monkey"/>
    <Label x:Name="animalLabel">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="You have chosen:" />
                <Span Text="{Binding Selection}" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
</StackLayout>
```

在此範例中， `RadioButtonGroup.GroupName` 附加屬性的值是由系結 `GroupName` 內容上的屬性所設定。 同樣地， `RadioButtonGroup.SelectedValue` 附加屬性的值是由系結 `Selection` 內容上的屬性所設定。 此外， `Selection` 屬性也會更新為 `Value` 已核取的屬性 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 。

## <a name="radiobutton-visual-states"></a>選項按鈕視覺狀態

[`RadioButton`](xref:Xamarin.Forms.RadioButton) 物件具有 `Checked` 和 `Unchecked` 視覺狀態，可用來在 `RadioButton` 核取或取消核取時起始視覺效果變更。

下列 XAML 範例顯示如何定義和狀態的視覺狀態 `Checked` `Unchecked` ：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="RadioButton">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CheckedStates">
                        <VisualState x:Name="Checked">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Green" />
                                <Setter Property="Opacity"
                                        Value="1" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="Unchecked">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Red" />
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout>
        <Label Text="What's your favorite mode of transport?" />
        <RadioButton Text="Car" />
        <RadioButton Text="Bike" />
        <RadioButton Text="Train" />
        <RadioButton Text="Walking" />
    </StackLayout>
</ContentPage>
```

在此範例中，隱含的 [`Style`](xref:Xamarin.Forms.Style) 目標 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 物件。 `Checked` [`VisualState`](xref:Xamarin.Forms.VisualState) 指定當核取時 `RadioButton` ，其 `TextColor` 屬性會設定為 `Opacity` 值為1的綠色。 `Unchecked` `VisualState` 指定當處於未核取 `RadioButton` 狀態時，其 `TextColor` 屬性會設定為紅色，其 `Opacity` 值為0.5。 因此，整體效果是當 `RadioButton` 取消核取時，它會是紅色且部分透明，而不會在其已核取時保持透明：

![選項按鈕視覺狀態的螢幕擷取畫面](radiobutton-images/radiobuttons-visualstates.png "選項按鈕視覺狀態")

如需視覺狀態的詳細資訊，請參閱[ Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="redefine-radiobutton-appearance"></a>重新定義選項按鈕外觀

根據預設， [`RadioButton`](xref:Xamarin.Forms.RadioButton) 物件會使用平臺轉譯器，在支援的平臺上使用原生控制項。 不過， `RadioButton` 您可以使用來重新定義視覺結構 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) ，讓 `RadioButton` 物件在所有平臺上都有相同的外觀。 這是可能的，因為 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 類別繼承自 [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) 類別。

下列 XAML 顯示 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 可以用來重新定義物件視覺結構的 [`RadioButton`](xref:Xamarin.Forms.RadioButton) ：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="RadioButtonTemplate">
            <Frame BorderColor="#F3F2F1"
                   BackgroundColor="#F3F2F1"
                   HasShadow="False"
                   HeightRequest="100"
                   WidthRequest="100"
                   HorizontalOptions="Start"
                   VerticalOptions="Start"
                   Padding="0">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CheckedStates">
                            <VisualState x:Name="Checked">
                                <VisualState.Setters>
                                    <Setter Property="BorderColor"
                                            Value="#FF3300" />
                                    <Setter TargetName="check"
                                            Property="Opacity"
                                            Value="1" />
                                </VisualState.Setters>
                            </VisualState>
                            <VisualState x:Name="Unchecked">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor"
                                            Value="#F3F2F1" />
                                    <Setter Property="BorderColor"
                                            Value="#F3F2F1" />
                                    <Setter TargetName="check"
                                            Property="Opacity"
                                            Value="0" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </VisualStateManager.VisualStateGroups>
                <Grid Margin="4"
                      WidthRequest="100">
                    <Grid WidthRequest="18"
                          HeightRequest="18"
                          HorizontalOptions="End"
                          VerticalOptions="Start">
                        <Ellipse Stroke="Blue"
                                 Fill="White"
                                 WidthRequest="16"
                                 HeightRequest="16"
                                 HorizontalOptions="Center"
                                 VerticalOptions="Center" />
                        <Ellipse x:Name="check"
                                 Fill="Blue"
                                 WidthRequest="8"
                                 HeightRequest="8"
                                 HorizontalOptions="Center"
                                 VerticalOptions="Center" />
                    </Grid>
                    <ContentPresenter />
                </Grid>
            </Frame>
        </ControlTemplate>

        <Style TargetType="RadioButton">
            <Setter Property="ControlTemplate"
                    Value="{StaticResource RadioButtonTemplate}" />
        </Style>
    </ContentPage.Resources>
    <!-- Page content -->
</ContentPage>
```

在此範例中，的根項目 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 是 [`Frame`](xref:Xamarin.Forms.Frame) 定義 `Checked` 和視覺狀態的物件 `Unchecked` 。 `Frame`物件會使用 [`Grid`](xref:Xamarin.Forms.Grid) 、和物件的組合， [`Ellipse`](xref:Xamarin.Forms.Shapes.Ellipse) [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) 來定義的視覺化結構 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 。 此範例也包含 *隱含* 樣式，此樣式會將指派給 `RadioButtonTemplate` `ControlTemplate` `RadioButton` 頁面上任何物件的屬性。

> [!NOTE]
> [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)物件會標示視覺結構中顯示內容的位置 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 。

下列 XAML 顯示透過 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 隱含樣式使用的物件 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) ： 

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <StackLayout RadioButtonGroup.GroupName="animals"
                 Orientation="Horizontal">
        <RadioButton Value="Cat">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="cat.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Cat"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
        <RadioButton Value="Dog">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="dog.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Dog"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
        <RadioButton Value="Elephant">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="elephant.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Elephant"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
        <RadioButton Value="Monkey">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="monkey.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Monkey"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
    </StackLayout>
</StackLayout>
```

在此範例中，為每個定義的視覺化結構 [`RadioButton`](xref:Xamarin.Forms.RadioButton) 都會取代為中定義的視覺結構 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) ，因此在執行時間，中的物件 `ControlTemplate` 會成為每個的視覺化樹狀結構的一部分 `RadioButton` 。 此外，每個的內容 `RadioButton` 都會替代為 [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) 控制項範本中定義的。 這會產生下列 `RadioButton` 外觀：

![樣板化選項按鈕的螢幕擷取畫面](radiobutton-images/radiobuttons-templated.png "樣板化選項按鈕")

如需控制項範本的詳細資訊，請參閱[ Xamarin.Forms 控制項範本](~/xamarin-forms/app-fundamentals/templates/control-template.md)。

## <a name="disable-a-radiobutton"></a>停用選項按鈕

有時候，應用程式會進入檢查不是有效作業的狀態 `RadioButton` 。 在這種情況下，您 `RadioButton` 可以藉由將其 `IsEnabled` 屬性設定為來停用 `false` 。

## <a name="related-links"></a>相關連結

- [選項按鈕示範 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)
- [Xamarin.Forms 按鈕](~/xamarin-forms/user-interface/button.md)
- [Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
