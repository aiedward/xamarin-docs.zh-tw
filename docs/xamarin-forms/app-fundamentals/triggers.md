---
title: Xamarin.Forms 觸發程序
description: 本文說明如何使用 Xamarin.Forms 觸發程序，以 XAML 回應使用者介面變更。 觸發程序可讓您用 XAML 以宣告方式表達動作，根據事件或屬性變更改變控制項的外觀。
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2020
ms.openlocfilehash: bf9c06dae0df7da1cc69a85d8436376494039959
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305126"
---
# <a name="xamarinforms-triggers"></a>Xamarin.Forms 觸發程序

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)

觸發程序可讓您用 XAML 以宣告方式表達動作，根據事件或屬性變更改變控制項的外觀。 此外，狀態觸發程式是一組特殊的觸發程式，它會定義何時應套用[`VisualState`](xref:Xamarin.Forms.VisualState) 。

您可以將觸發程序直接指派給控制項，或將它新增至頁面層級或應用程式層級的資源字典，以套用至多個控制項。

## <a name="property-triggers"></a>屬性觸發程式

簡單的觸發程序完全以 XAML 表達，新增 `Trigger` 項目至控制項的觸發程序集合。
此範例示範在 `Entry` 收到焦點時，變更其背景色彩的觸發程序：

```xaml
<Entry Placeholder="enter name">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="IsFocused" Value="True">
            <Setter Property="BackgroundColor" Value="Yellow" />
            <!-- multiple Setters elements are allowed -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

觸發程序宣告的重要部分包括：

- **TargetType** - 觸發程序適用的控制項類型。

- **Property** - 控制項上受監視的屬性。

- **Value** - 針對受監視的屬性發生時，導致啟動觸發程序的值。

- **Setter** - 符合觸發程序條件時可以新增 `Setter` 項目的集合。 您必須指定要設定的 `Property` 和 `Value`。

- **EnterActions 和 ExitActions** (未顯示) - 以程式碼撰寫，且可以和 `Setter` 項目一起使用，或是用來代替它。 其[描述如下](#enteractions-and-exitactions)。

### <a name="applying-a-trigger-using-a-style"></a>使用樣式套用觸發程式

觸發程序也可以新增至控制項的 `Style` 宣告、頁面上或應用程式 `ResourceDictionary`。 這個範例會宣告隱含樣式（也就是未設定 `Key`），這表示它會套用至頁面上的所有 `Entry` 控制項。

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style TargetType="Entry">
                        <Style.Triggers>
                <Trigger TargetType="Entry"
                         Property="IsFocused" Value="True">
                    <Setter Property="BackgroundColor" Value="Yellow" />
                    <!-- multiple Setters elements are allowed -->
                </Trigger>
            </Style.Triggers>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>
```

## <a name="data-triggers"></a>資料觸發程式

資料觸發程序使用資料繫結來監視另一個控制項，導致呼叫 `Setter`。 請設定 `Property` 屬性來監視指定的值，而不是屬性觸發程序中的 `Binding` 屬性。

下列範例使用資料繫結語法 `{Binding Source={x:Reference entry}, Path=Text.Length}`
這是我們指稱另一個控制項屬性的方法。 當 `entry` 的長度為零時，會啟動觸發程序。 在此範例中，觸發程序會在輸入為空時停用按鈕。

```xaml
<!-- the x:Name is referenced below in DataTrigger-->
<!-- tip: make sure to set the Text="" (or some other default) -->
<Entry x:Name="entry"
       Text=""
       Placeholder="required field" />

<Button x:Name="button" Text="Save"
        FontSize="Large"
        HorizontalOptions="Center">
    <Button.Triggers>
        <DataTrigger TargetType="Button"
                     Binding="{Binding Source={x:Reference entry},
                                       Path=Text.Length}"
                     Value="0">
            <Setter Property="IsEnabled" Value="False" />
            <!-- multiple Setters elements are allowed -->
        </DataTrigger>
    </Button.Triggers>
</Button>
```

> [!TIP]
> 在評估 `Path=Text.Length` 時，一律為目標屬性提供預設值 (例如， `Text=""`)，否則它將會是 `null` 且觸發程序將無法如您預期運作。

除了指定 `Setter` 之外，您也可以提供 [`EnterActions` 和 `ExitActions`](#enteractions-and-exitactions)。

## <a name="event-triggers"></a>事件觸發程序

`EventTrigger` 項目只需要 `Event` 屬性，例如下列範例中的 `"Clicked"`。

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

請注意，沒有 `Setter` 項目，但是有 `local:NumericValidationTriggerAction` 所定義類別的參考，它需要在頁面的 XAML 中宣告 `xmlns:local`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

類別本身會實作 `TriggerAction`，這表示每次觸發程序事件發生時，它應該提供所呼叫 `Invoke` 方法的覆寫。

觸發程序動作實作應該：

- 實作泛型 `TriggerAction<T>` 類別，且泛型參數對應觸發程序將套用至的控制項類型。 您可以使用 Superclass，例如 `VisualElement`，來撰寫使用各種控制項的觸發程序動作，或指定如 `Entry` 控制項類型。

- 覆寫 `Invoke` 方法 - 每當符合觸發程序準則時便會呼叫它。

- 選擇性地公開可在宣告觸發程序時，於 XAML 中設定的屬性。 如需相關範例，請參閱隨附範例應用程式中的 `VisualElementPopTriggerAction` 類別。

```csharp
public class NumericValidationTriggerAction : TriggerAction<Entry>
{
    protected override void Invoke (Entry entry)
    {
        double result;
        bool isValid = Double.TryParse (entry.Text, out result);
        entry.TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

您可以從 XAML 取用事件觸發程序：

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

在 `ResourceDictionary` 中共用觸發程序時請小心，將會在控制項之間共用一個執行個體，因此設定一次的任何狀態將會適用於全部。

請注意，事件觸發程序不支援 `EnterActions` 和 `ExitActions`，[如下所述](#enteractions-and-exitactions)。

## <a name="multi-triggers"></a>多個觸發程式

`MultiTrigger` 看起來類似於 `Trigger` 或 `DataTrigger`，但它可以有多個條件。 所有條件必須都為 true 才會觸發 `Setter`。

以下是繫結至兩個不同輸入 (`email` 和 `phone`) 的按鈕觸發程序範例：

```xaml
<MultiTrigger TargetType="Button">
    <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference email},
                                   Path=Text.Length}"
                               Value="0" />
        <BindingCondition Binding="{Binding Source={x:Reference phone},
                                   Path=Text.Length}"
                               Value="0" />
    </MultiTrigger.Conditions>
    <Setter Property="IsEnabled" Value="False" />
    <!-- multiple Setter elements are allowed -->
</MultiTrigger>
```

`Conditions` 集合可能也會包含 `PropertyCondition` 項目，如下所示：

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>建置「需要全部」的多重觸發程序

多重觸發程序只有在所有條件都成立時，只會更新其控制項。 測試「所有的欄位長度為零」(例如必須完成所有輸入的登入頁面) 相當困難，因為您想要條件 "where Text.Length > 0"，但這無法以 XAML 表示。

這可以使用 `IValueConverter` 完成。 以下的轉換器程式碼會將 `Text.Length` 繫結轉換成 `bool`，指出欄位是否為空白：

```csharp
public class MultiTriggerConverter : IValueConverter
{
    public object Convert(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        if ((int)value > 0) // length > 0 ?
            return true;            // some data has been entered
        else
            return false;            // input is empty
    }

    public object ConvertBack(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        throw new NotSupportedException ();
    }
}
```

若要在多重觸發程序中使用這個轉換器，先將其新增至頁面的資源字典 (附上自訂的 `xmlns:local` 命名空間定義)：

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

XAML 如下所示。 請注意第一個多重觸發程序範例的下列差異：

- 按鈕預設已設定 `IsEnabled="false"`。
- 多重觸發程序條件使用轉換器，將 `Text.Length` 值變為 `boolean`。
- 所有條件都為 `true` 時，setter 會讓按鈕的 `IsEnabled` 屬性成為 `true`。

```xaml
<Entry x:Name="user" Text="" Placeholder="user name" />

<Entry x:Name="pwd" Text="" Placeholder="password" />

<Button x:Name="loginButton" Text="Login"
        FontSize="Large"
        HorizontalOptions="Center"
        IsEnabled="false">
  <Button.Triggers>
    <MultiTrigger TargetType="Button">
      <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference user},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
        <BindingCondition Binding="{Binding Source={x:Reference pwd},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
      </MultiTrigger.Conditions>
      <Setter Property="IsEnabled" Value="True" />
    </MultiTrigger>
  </Button.Triggers>
</Button>
```

這些螢幕擷取畫面顯示上述兩個多重觸發程序範例之間的差異。 在畫面頂端的文字輸入，只需要有一個 `Entry` 即可啟用 [儲存] 按鈕。
在畫面底部，[登入] 按鈕保持非使用中，直到兩個欄位都包含資料為止。

![](triggers-images/multi-requireall.png "MultiTrigger Examples")

## <a name="enteractions-and-exitactions"></a>EnterActions 和 ExitActions

另一種在觸發程序發生時實作變更的方法，是藉由新增 `EnterActions` 和 `ExitActions` 集合，並指定 `TriggerAction<T>` 實作。

[`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) 集合可用來定義符合觸發條件時所叫用 `IList`[`TriggerAction` 物件的 ](xref:Xamarin.Forms.TriggerAction)。 [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) 集合可用來定義觸發條件不再符合之後所叫用 `IList` 物件的 `TriggerAction`。

> [!NOTE]
> [`TriggerAction`](xref:Xamarin.Forms.TriggerAction) 類別會忽略 `EnterActions` 和 `ExitActions` 集合中定義的 [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) 物件。    

您可以「同時」在觸發程序中提供 `EnterActions` 和 `ExitActions`，以及 `Setter`；但請注意，會立即呼叫 `Setter` (其不會等待 `EnterAction` 或 `ExitAction` 完成)。 或者，您可以在程式碼中執行一切，完全不使用 `Setter`。

```xaml
<Entry Placeholder="enter job title">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="Entry.IsFocused" Value="True">
            <Trigger.EnterActions>
                <local:FadeTriggerAction StartsFrom="0"" />
            </Trigger.EnterActions>

            <Trigger.ExitActions>
                <local:FadeTriggerAction StartsFrom="1" />
            </Trigger.ExitActions>
            <!-- You can use both Enter/Exit and Setter together if required -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

如往常，在 XAML 中參考類別時，應該宣告命名空間，例如此處所示的 `xmlns:local`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

`FadeTriggerAction` 程式碼如下所示：

```csharp
public class FadeTriggerAction : TriggerAction<VisualElement>
{
    public int StartsFrom { set; get; }

    protected override void Invoke(VisualElement sender)
    {
        sender.Animate("FadeTriggerAction", new Animation((d) =>
        {
            var val = StartsFrom == 1 ? d : 1 - d;
            // so i was aiming for a different color, but then i liked the pink :)
            sender.BackgroundColor = Color.FromRgb(1, val, 1);
        }),
        length: 1000, // milliseconds
        easing: Easing.Linear);
    }
}
```

## <a name="state-triggers"></a>狀態觸發程式

狀態觸發程式已引進于 Xamarin. 表單4.5 中，而且是一組特殊的觸發程式，定義應該套用[`VisualState`](xref:Xamarin.Forms.VisualState)的條件。 不過，它們目前為實驗性，而且只能透過將下列程式程式碼新增至您的*App.xaml.cs*檔案來使用：

```csharp
Device.SetFlags(new string[]{ "StateTriggers_Experimental" });
```

狀態觸發程式會加入至[`VisualState`](xref:Xamarin.Forms.VisualState)的[`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers)集合。 這個集合可以包含單一狀態觸發程式或多個狀態觸發程式。 當集合中的任何狀態觸發程式為作用中時，將會套用[`VisualState`](xref:Xamarin.Forms.VisualState) 。

當使用狀態觸發程式來控制視覺狀態時，Xamarin 會使用下列優先順序規則來判斷哪個觸發程式（和對應的[`VisualState`](xref:Xamarin.Forms.VisualState)）將會作用：

1. 衍生自[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)的任何觸發程式。
1. [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)因為符合[`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth)條件而啟動。
1. [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)因為符合[`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight)條件而啟動。

如果多個觸發程式同時處於作用中狀態（例如，兩個自訂觸發程式），則標記中宣告的第一個觸發程式會優先使用。

> [!NOTE]
> 狀態觸發程式可以在[`Style`](xref:Xamarin.Forms.Style)或直接在專案上設定。

如需視覺狀態的詳細資訊，請參閱 [ [Xamarin] 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

### <a name="state-trigger"></a>狀態觸發程式

衍生自[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)類別的[`StateTrigger`](xref:Xamarin.Forms.StateTrigger)類別具有[`IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive)可系結屬性。 當 `IsActive` 屬性變更值時，`StateTrigger` 會觸發[`VisualState`](xref:Xamarin.Forms.VisualState)變更。

[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)類別，也就是所有狀態觸發程式的基類，都具有[`IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive)屬性和[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件。 每當發生[`VisualState`](xref:Xamarin.Forms.VisualState)變更時，就會引發這個事件。

> [!IMPORTANT]
> [`StateTrigger.IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive)可系結屬性會隱藏繼承的[`StateTriggerBase.IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive)屬性。

下列 XAML 範例顯示包含[`StateTrigger`](xref:Xamarin.Forms.StateTrigger)物件的[`Style`](xref:Xamarin.Forms.Style) ：

```xaml
<Style TargetType="Grid">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Checked">
                    <VisualState.StateTriggers>
                        <StateTrigger IsActive="{Binding IsToggled}"
                                      IsActiveChanged="OnCheckedStateIsActiveChanged" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Black" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Unchecked">
                    <VisualState.StateTriggers>
                        <StateTrigger IsActive="{Binding IsToggled, Converter={StaticResource inverseBooleanConverter}}"
                                      IsActiveChanged="OnUncheckedStateIsActiveChanged" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

在此範例中，隱含[`Style`](xref:Xamarin.Forms.Style)會以[`Grid`](xref:Xamarin.Forms.Grid)物件為目標。 當系結物件的 `IsToggled` 屬性 `true`時，`Grid` 的背景色彩會設定為黑色。 當系結物件的 `IsToggled` 屬性變成 `false`時，就會觸發[`VisualState`](xref:Xamarin.Forms.VisualState)變更，而 `Grid` 的背景色彩會變成白色。

此外，每次發生[`VisualState`](xref:Xamarin.Forms.VisualState)變更時，都會引發 `VisualState` 的[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件。 每個 `VisualState` 都會註冊此事件的事件處理常式：

```csharp
void OnCheckedStateIsActiveChanged(object sender, EventArgs e)
{
    StateTriggerBase stateTrigger = sender as StateTriggerBase;
    Console.WriteLine($"Checked state active: {stateTrigger.IsActive}");
}

void OnUncheckedStateIsActiveChanged(object sender, EventArgs e)
{
    StateTriggerBase stateTrigger = sender as StateTriggerBase;
    Console.WriteLine($"Unchecked state active: {stateTrigger.IsActive}");
}
```

在此範例中，當引發[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件的處理常式時，處理常式會輸出[`VisualState`](xref:Xamarin.Forms.VisualState)是否為使用中。 例如，從 [`Checked`] 視覺狀態變更為 [`Unchecked`] 視覺狀態時，會將下列訊息輸出至主控台視窗：

```
Checked state active: False
Unchecked state active: True
```

> [!NOTE]
> 自訂狀態觸發程式可以藉由從[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)類別衍生來建立。

### <a name="adaptive-trigger"></a>適應性觸發程式

當視窗為指定的高度或寬度時， [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)會觸發[`VisualState`](xref:Xamarin.Forms.VisualState)變更。 此觸發程式有兩個可系結屬性：

- [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight)，屬於 `double`類型，表示應套用[`VisualState`](xref:Xamarin.Forms.VisualState)的最小視窗高度。
- [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight)，屬於 `double`類型，表示應套用[`VisualState`](xref:Xamarin.Forms.VisualState)的最小視窗寬度。

> [!NOTE]
> [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)衍生自[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)類別，因此可將事件處理常式附加至[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件。

下列 XAML 範例顯示包含[`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)物件的[`Style`](xref:Xamarin.Forms.Style) ：

```xaml
<Style TargetType="StackLayout">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Vertical">
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="0" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="Orientation"
                                Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Horizontal">
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="800" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="Orientation"
                                Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

在此範例中，隱含[`Style`](xref:Xamarin.Forms.Style)會以[`StackLayout`](xref:Xamarin.Forms.StackLayout)物件為目標。 當視窗寬度介於0和800裝置獨立單位之間時，套用 `Style` 的 `StackLayout` 物件將會有垂直方向。 當視窗寬度 > = 800 裝置獨立單位時，會觸發[`VisualState`](xref:Xamarin.Forms.VisualState)變更，而 `StackLayout` 方向會變更為水準：

![垂直 StackLayout VisualState](triggers-images/adaptivetrigger-vertical.png "AdaptiveTrigger 範例")
![水準 StackLayout VisualState](triggers-images/adaptivetrigger-horizontal.png "AdaptiveTrigger 範例")

[`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight)和[`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight)屬性可以獨立使用或彼此配合。 下列 XAML 顯示設定這兩個屬性的範例：

```xaml
<AdaptiveTrigger MinWindowWidth="800"
                 MinWindowHeight="1200"/>
```

在此範例中， [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)表示當目前的視窗寬度 > = 800 裝置獨立單位，且目前的視窗高度 > = 1200 裝置獨立單位時，將會套用對應的[`VisualState`](xref:Xamarin.Forms.VisualState) 。

### <a name="compare-state-trigger"></a>比較狀態觸發程式

當屬性等於特定值時， [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger)會觸發[`VisualState`](xref:Xamarin.Forms.VisualState)變更。 此觸發程式有兩個可系結屬性：

- [`Property`](xref:Xamarin.Forms.CompareStateTrigger.Property)，屬於 `object`類型，表示由觸發程式比較的屬性。
- [`Value`](xref:Xamarin.Forms.CompareStateTrigger.Value)，屬於 `object`類型，表示應套用[`VisualState`](xref:Xamarin.Forms.VisualState)的值。

> [!NOTE]
> [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger)衍生自[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)類別，因此可將事件處理常式附加至[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件。

下列 XAML 範例顯示包含[`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger)物件的[`Style`](xref:Xamarin.Forms.Style) ：

```xaml
<Style TargetType="Grid">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Checked">
                    <VisualState.StateTriggers>
                        <CompareStateTrigger Property="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                                             Value="True" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Black" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Unchecked">
                    <VisualState.StateTriggers>
                        <CompareStateTrigger Property="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                                             Value="False" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
...
<Grid>
    <Frame BackgroundColor="White"
           CornerRadius="12"
           Margin="24"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <StackLayout Orientation="Horizontal">
            <CheckBox x:Name="checkBox"
                      VerticalOptions="Center" />
            <Label Text="Check the CheckBox to modify the Grid background color."
                   VerticalOptions="Center" />
        </StackLayout>
    </Frame>
</Grid>
```

在此範例中，隱含[`Style`](xref:Xamarin.Forms.Style)會以[`Grid`](xref:Xamarin.Forms.Grid)物件為目標。 `false`[`CheckBox`](xref:Xamarin.Forms.CheckBox)的 [ [`IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) ] 屬性時，`Grid` 的背景色彩會設定為白色。 當 [`CheckBox.IsChecked`] 屬性變成 [`true`] 時，就會觸發[`VisualState`](xref:Xamarin.Forms.VisualState)變更，而 `Grid` 的背景色彩會變成黑色：

[![Ios 和 android 上觸發之視覺狀態變更的螢幕擷取畫面](triggers-images/comparestatetrigger-unchecked.png "CompareStateTrigger 範例")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "CompareStateTrigger 範例")
[ ![ios 和 android 上觸發的視覺狀態變更的螢幕擷取畫面](triggers-images/comparestatetrigger-checked.png "CompareStateTrigger 範例")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "CompareStateTrigger 範例")

### <a name="device-state-trigger"></a>裝置狀態觸發程式

[`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger)會根據應用程式執行所在的裝置平臺，觸發[`VisualState`](xref:Xamarin.Forms.VisualState)變更。 此觸發程式有一個可系結屬性：

- [`Device`](xref:Xamarin.Forms.DeviceStateTrigger.Device)，屬於 `string`類型，表示應該在其上套用[`VisualState`](xref:Xamarin.Forms.VisualState)的裝置平臺。

> [!NOTE]
> [`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger)衍生自[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)類別，因此可將事件處理常式附加至[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件。

下列 XAML 範例顯示包含 `DeviceStateTrigger` 物件的[`Style`](xref:Xamarin.Forms.Style) ：

```xaml
<Style x:Key="DeviceStateTriggerPageStyle"
       TargetType="ContentPage">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="iOS">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="iOS" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Silver" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Android">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="Android" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="#2196F3" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="UWP">
                    <VisualState.StateTriggers>
                        <DeviceStateTrigger Device="UWP" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Aquamarine" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

在此範例中，明確[`Style`](xref:Xamarin.Forms.Style)會以[`ContentPage`](xref:Xamarin.Forms.ContentPage)物件為目標。 `ContentPage` 使用樣式的物件，將其背景色彩設為 iOS 上的銀級，在 Android 上為淺藍色，並在 UWP 上青綠色。 下列螢幕擷取畫面顯示 iOS 和 Android 上產生的頁面：

[![在 iOS 和 Android 上觸發之視覺狀態變更的螢幕擷取畫面](triggers-images/devicestatetrigger.png "DeviceStateTrigger 範例")](triggers-images/devicestatetrigger-large.png#lightbox "DeviceStateTrigger 範例")

### <a name="orientation-state-trigger"></a>方向狀態觸發程式

當裝置的方向變更時， [`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger)會觸發[`VisualState`](xref:Xamarin.Forms.VisualState)變更。 此觸發程式有一個可系結屬性：

- [`Orientation`](xref:Xamarin.Forms.OrientationStateTrigger.Orientation)，屬於[`DeviceOrientation`](xref:Xamarin.Forms.Internals.DeviceOrientation)類型，表示應套用[`VisualState`](xref:Xamarin.Forms.VisualState)的方向。

> [!NOTE]
> [`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger)衍生自[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)類別，因此可將事件處理常式附加至[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件。

下列 XAML 範例顯示包含 `OrientationStateTrigger` 物件的[`Style`](xref:Xamarin.Forms.Style) ：

```xaml
<Style x:Key="OrientationStateTriggerPageStyle"
       TargetType="ContentPage">
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup>
                <VisualState x:Name="Portrait">
                    <VisualState.StateTriggers>
                        <OrientationStateTrigger Orientation="Portrait" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="Silver" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState x:Name="Landscape">
                    <VisualState.StateTriggers>
                        <OrientationStateTrigger Orientation="Landscape" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor"
                                Value="White" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

在此範例中，明確[`Style`](xref:Xamarin.Forms.Style)會以[`ContentPage`](xref:Xamarin.Forms.ContentPage)物件為目標。 `ContentPage` 使用樣式的物件會在方向為直向時，將其背景色彩設為銀，並在方向為橫向時，將其背景色彩設定為白色。

## <a name="related-links"></a>相關連結

- [觸發程序範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)
- [Xamarin. 表單視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
- [Xamarin. 表單觸發程式 API](xref:Xamarin.Forms.TriggerAction`1)
