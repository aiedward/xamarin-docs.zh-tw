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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305126"
---
# <a name="xamarinforms-triggers"></a>Xamarin.Forms 觸發程序

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)

觸發程序可讓您用 XAML 以宣告方式表達動作，根據事件或屬性變更改變控制項的外觀。 此外,狀態觸發器(是一組專門的觸發器)定義何時應用[`VisualState`](xref:Xamarin.Forms.VisualState)。

您可以將觸發程序直接指派給控制項，或將它新增至頁面層級或應用程式層級的資源字典，以套用至多個控制項。

## <a name="property-triggers"></a>屬性觸發器

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

### <a name="applying-a-trigger-using-a-style"></a>使用樣式應用觸發器

觸發程序也可以新增至控制項的 `Style` 宣告、頁面上或應用程式 `ResourceDictionary`。 此示例聲明隱式樣式(即設置否`Key`),這意味著它將應用於頁面上`Entry`的所有控件。

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

## <a name="data-triggers"></a>資料觸發器

資料觸發程序使用資料繫結來監視另一個控制項，導致呼叫 `Setter`。 請設定 `Binding` 屬性來監視指定的值，而不是屬性觸發程序中的 `Property` 屬性。

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

除了指定`Setter`s 外,您[`EnterActions``ExitActions`](#enteractions-and-exitactions)還可以提供 和 。

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

請注意，事件觸發程序不支援 `EnterActions` 和 ，`ExitActions`    [如下所述](#enteractions-and-exitactions)。

## <a name="multi-triggers"></a>多觸發器

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

這些螢幕擷取畫面顯示上述兩個多重觸發程序範例之間的差異。 在畫面頂端的文字輸入，只需要有一個 `Entry` 即可啟用 [儲存]**** 按鈕。
在畫面底部，[登入]**** 按鈕保持非使用中，直到兩個欄位都包含資料為止。

![](triggers-images/multi-requireall.png "MultiTrigger Examples")

## <a name="enteractions-and-exitactions"></a>EnterActions 和 ExitActions

另一種在觸發程序發生時實作變更的方法，是藉由新增 `EnterActions` 和 `ExitActions` 集合，並指定 `TriggerAction<T>` 實作。

集合[`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions)用於定義`IList`在滿足觸發器條件[`TriggerAction`](xref:Xamarin.Forms.TriggerAction)時 將調用的物件。 集合[`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions)用於定義`IList`在 不再滿足觸發`TriggerAction`器條件 后將調用的物件。

> [!NOTE]
> 類[`TriggerAction`](xref:Xamarin.Forms.TriggerAction)將忽略[`EventTrigger`](xref:Xamarin.Forms.EventTrigger)和`EnterActions``ExitActions`集合中定義的物件。    

您可以「同時」*在觸發程序中提供 * `EnterActions` 和 `ExitActions`，以及 `Setter`；但請注意，會立即呼叫 `Setter` (其不會等待 `EnterAction` 或 `ExitAction` 完成)。 或者，您可以在程式碼中執行一切，完全不使用 `Setter`。

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

## <a name="state-triggers"></a>狀態觸發器

狀態觸發器已在 Xamarin.Forms 4.5 中引入,是定義[`VisualState`](xref:Xamarin.Forms.VisualState)應用 的條件 的專用觸發器組。 但是,它們目前是實驗性的,只能通過將以下代碼行添加到*App.xaml.cs*檔來使用:

```csharp
Device.SetFlags(new string[]{ "StateTriggers_Experimental" });
```

狀態觸發器將添加到集合[`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers)[`VisualState`](xref:Xamarin.Forms.VisualState)中。 此集合可以包含單個狀態觸發器或多個狀態觸發器。 當[`VisualState`](xref:Xamarin.Forms.VisualState)集合中的任何狀態觸發器處於活動狀態時,將應用 。

當使用狀態觸發器來控制視覺狀態時,Xamarin.Forms 使用以下優先順序規則來確定哪個觸發器(和相應的[`VisualState`](xref:Xamarin.Forms.VisualState))將處於活動狀態:

1. 派生自[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)的任何觸發器。
1. 由於[`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)滿足[`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth)條件而啟動。
1. 由於[`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)滿足[`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight)條件而啟動。

如果多個觸發器同時處於活動狀態(例如,兩個自定義觸發器),則標記中聲明的第一個觸發器優先。

> [!NOTE]
> 狀態觸發器可以在[`Style`](xref:Xamarin.Forms.Style)中 設置,也可以直接設置在元素上。

有關視覺狀態的詳細資訊,請參閱[Xamarin.Forms 視覺化狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

### <a name="state-trigger"></a>狀態觸發器

派生[`StateTrigger`](xref:Xamarin.Forms.StateTrigger)自類[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)的 類[`IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive)具有可 綁定屬性。 當`StateTrigger`屬性變更[`VisualState`](xref:Xamarin.Forms.VisualState)值`IsActive`時, 將觸發變更。

類[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)是所有狀態觸發器的基類,具有[`IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive)屬性[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)和事件。 每當發生變更時,[`VisualState`](xref:Xamarin.Forms.VisualState)此事件都會觸發。

> [!IMPORTANT]
> 可[`StateTrigger.IsActive`](xref:Xamarin.Forms.StateTrigger.IsActive)綁定屬性隱藏繼承[`StateTriggerBase.IsActive`](xref:Xamarin.Forms.StateTriggerBase.IsActive)的屬性。

下面的 XAML 範例[`Style`](xref:Xamarin.Forms.Style)顯示[`StateTrigger`](xref:Xamarin.Forms.StateTrigger)包含 物件的:

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

在此示例中,隱式[`Style`](xref:Xamarin.Forms.Style)目標物件[`Grid`](xref:Xamarin.Forms.Grid)。 當繫結`IsToggled`物件的屬性為`true`時,背景`Grid`顏色設置為黑色。 當綁定`IsToggled`物件的屬性變`false`為 時,將[`VisualState`](xref:Xamarin.Forms.VisualState)觸發 更改,`Grid`並將的背景 顏色變為白色。

此外,每次發生[`VisualState`](xref:Xamarin.Forms.VisualState)更改時[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged), 都會觸發`VisualState`的事件。 每個`VisualState`事件註冊一個事件處理程式:

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

在此示例中,當觸發[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件的處理程式時,處理程式將輸出[`VisualState`](xref:Xamarin.Forms.VisualState)是否處於活動狀態。 例如,從`Checked`可視狀態更改`Unchecked`為 可視狀態時,將以下消息輸出到主控台視窗:

```
Checked state active: False
Unchecked state active: True
```

> [!NOTE]
> 可以通過派生[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)類 創建自定義狀態觸發器。

### <a name="adaptive-trigger"></a>自適應觸發器

當[`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)視窗指定[`VisualState`](xref:Xamarin.Forms.VisualState)高度 或寬度時,將觸發更改。 此觸發器具有兩個可綁定屬性:

- [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight)的類型`double`, 指示[`VisualState`](xref:Xamarin.Forms.VisualState)應用 的最小視窗高度。
- [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight)的類型`double`, 指示[`VisualState`](xref:Xamarin.Forms.VisualState)應用 的最小視窗寬度。

> [!NOTE]
> [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)衍生的自類別,[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)因此可以將事件處理程式附加到[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件 。

下面的 XAML 範例[`Style`](xref:Xamarin.Forms.Style)顯示[`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)包含 物件的:

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

在此示例中,隱式[`Style`](xref:Xamarin.Forms.Style)目標物件[`StackLayout`](xref:Xamarin.Forms.StackLayout)。 當視窗寬度介於 0 和 800`StackLayout`個與設備`Style`無關的單位之間時 ,應用的物件將具有垂直方向。 當視窗寬度>= 800 個與裝置[`VisualState`](xref:Xamarin.Forms.VisualState)無關的單位 時,將`StackLayout`觸發更改, 方向更改為水準:

![垂直堆疊佈局視覺狀態](triggers-images/adaptivetrigger-vertical.png "自適應觸發器範例")
![水平堆疊佈局視覺狀態](triggers-images/adaptivetrigger-horizontal.png "自適應觸發器範例")

和[`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight)[`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight)屬性可以單獨使用,也可以相互結合使用。 以下 XAML 顯示了設定這兩個屬性的範例:

```xaml
<AdaptiveTrigger MinWindowWidth="800"
                 MinWindowHeight="1200"/>
```

這個樣本,[`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)指示當目前的視窗寬度>[`VisualState`](xref:Xamarin.Forms.VisualState) = 800 個與設備無關的單位,並且目前的視窗高度為 >= 1200 個與設備無關的單位時,將應用相應的值。

### <a name="compare-state-trigger"></a>比較狀態觸發器

當[`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger)屬性等於[`VisualState`](xref:Xamarin.Forms.VisualState)特定值時,將觸發更改。 此觸發器具有兩個可綁定屬性:

- [`Property`](xref:Xamarin.Forms.CompareStateTrigger.Property)的類型`object`, 指示被觸發器比較的屬性。
- [`Value`](xref:Xamarin.Forms.CompareStateTrigger.Value)的`object`,[`VisualState`](xref:Xamarin.Forms.VisualState)指示 應用的值。

> [!NOTE]
> [`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger)衍生的自類別,[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)因此可以將事件處理程式附加到[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件 。

下面的 XAML 範例[`Style`](xref:Xamarin.Forms.Style)顯示[`CompareStateTrigger`](xref:Xamarin.Forms.CompareStateTrigger)包含 物件的:

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

在此示例中,隱式[`Style`](xref:Xamarin.Forms.Style)目標物件[`Grid`](xref:Xamarin.Forms.Grid)。 當[`IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked)的屬性[`CheckBox`](xref:Xamarin.Forms.CheckBox)`false`為 時,`Grid`背景顏色設置為白色。 當`CheckBox.IsChecked`屬性變`true`為[`VisualState`](xref:Xamarin.Forms.VisualState),將 觸發更改,`Grid`並且的背景 顏色變為黑色:

[![在 iOS 和 Android 上觸發的視覺狀態更改的螢幕截圖,在 iOS 和 Android](triggers-images/comparestatetrigger-unchecked.png "比較狀態觸發示例")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "比較狀態觸發示例")
[![上觸發的視覺狀態更改](triggers-images/comparestatetrigger-checked.png "比較狀態觸發示例")](triggers-images/comparestatetrigger-unchecked-large.png#lightbox "比較狀態觸發示例")

### <a name="device-state-trigger"></a>裝置狀態觸發器

根據[`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger)應用運行的設備[`VisualState`](xref:Xamarin.Forms.VisualState)平臺觸發更改。 此觸發器具有單個可綁定屬性:

- [`Device`](xref:Xamarin.Forms.DeviceStateTrigger.Device)的類型`string`,指示應應用[`VisualState`](xref:Xamarin.Forms.VisualState)的設備 平臺。

> [!NOTE]
> [`DeviceStateTrigger`](xref:Xamarin.Forms.DeviceStateTrigger)衍生的自類別,[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)因此可以將事件處理程式附加到[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件 。

下面的 XAML 範例[`Style`](xref:Xamarin.Forms.Style)顯示`DeviceStateTrigger`包含 物件的:

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

在此示例中,顯式[`Style`](xref:Xamarin.Forms.Style)目標[`ContentPage`](xref:Xamarin.Forms.ContentPage)物件。 `ContentPage`使用樣式的物件在 iOS 上將其背景顏色設置為銀色,在 Android 上設置為淡藍色,在 UWP 上設置為海藍寶石。 以下螢幕截圖顯示了 iOS 和 Android 上生成的頁面:

[![iOS 和 Android 上觸發的視覺狀態更改的螢幕截圖](triggers-images/devicestatetrigger.png "裝置狀態觸發範例")](triggers-images/devicestatetrigger-large.png#lightbox "裝置狀態觸發範例")

### <a name="orientation-state-trigger"></a>方向狀態觸發器

當[`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger)設備方向更改[`VisualState`](xref:Xamarin.Forms.VisualState)時,將觸發更改。 此觸發器具有單個可綁定屬性:

- [`Orientation`](xref:Xamarin.Forms.OrientationStateTrigger.Orientation)的類型[`DeviceOrientation`](xref:Xamarin.Forms.Internals.DeviceOrientation),指示 應[`VisualState`](xref:Xamarin.Forms.VisualState)套用的方向 。

> [!NOTE]
> [`OrientationStateTrigger`](xref:Xamarin.Forms.OrientationStateTrigger)衍生的自類別,[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)因此可以將事件處理程式附加到[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件 。

下面的 XAML 範例[`Style`](xref:Xamarin.Forms.Style)顯示`OrientationStateTrigger`包含 物件的:

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

在此示例中,顯式[`Style`](xref:Xamarin.Forms.Style)目標[`ContentPage`](xref:Xamarin.Forms.ContentPage)物件。 `ContentPage`當方向為縱向時,使用樣式的物件將其背景顏色設置為銀色,並在方向為橫向時將其背景顏色設置為白色。

## <a name="related-links"></a>相關連結

- [觸發程序範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)
- [Xamarin.表單可視化狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
- [Xamarin.表單觸發器 API](xref:Xamarin.Forms.TriggerAction`1)
