---
title: Xamarin.Forms 觸發程序
description: 本文說明如何使用 Xamarin.Forms 觸發程序，以 XAML 回應使用者介面變更。 觸發程序可讓您用 XAML 以宣告方式表達動作，根據事件或屬性變更改變控制項的外觀。
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 9e49dfa99ccb6aae49a72ce044bb8071c210336e
ms.sourcegitcommit: 76f930ce63b193ca3f7f85f768b031e59cb342ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198573"
---
# <a name="xamarinforms-triggers"></a>Xamarin.Forms 觸發程序

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)

觸發程序可讓您用 XAML 以宣告方式表達動作，根據事件或屬性變更改變控制項的外觀。

您可以將觸發程序直接指派給控制項，或將它新增至頁面層級或應用程式層級的資源字典，以套用至多個控制項。

觸發程序可分為四種類型：

- [屬性觸發程序](#property) - 發生於控制項上的屬性設定為特定值時。

- [資料觸發程序](#data) - 使用資料繫結，根據另一個控制項的屬性觸發。

- [事件觸發程序](#event) - 發生於控制項上發生事件時。

- [多個觸發程序](#multi) - 允許在動作發生之前，設定多個觸發程序條件。

<a name="property" />

## <a name="property-triggers"></a>屬性觸發程序

簡單的觸發程序完全以 XAML 表達，新增 `Trigger` 項目至控制項的觸發程序集合。
此範例示範在 `Entry` 收到焦點時，變更其背景色彩的觸發程序：

```xaml
<Entry Placeholder="enter name">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
             Property="IsFocused" Value="True">
            <Setter Property="BackgroundColor" Value="Yellow" />
        </Trigger>
    </Entry.Triggers>
</Entry>
```

觸發程序宣告的重要部分包括：

- **TargetType** - 觸發程序適用的控制項類型。

- **Property** - 控制項上受監視的屬性。

- **Value** - 針對受監視的屬性發生時，導致啟動觸發程序的值。

- **Setter** - 符合觸發程序條件時可以新增 `Setter` 項目的集合。 您必須指定要設定的 `Property` 和 `Value`。

- **EnterActions 和 ExitActions** (未顯示) - 以程式碼撰寫，且可以和 `Setter` 項目一起使用，或是用來代替它。 其[描述如下](#enterexit)。

### <a name="applying-a-trigger-using-a-style"></a>使用樣式套用觸發程序

觸發程序也可以新增至控制項的 `Style` 宣告、頁面上或應用程式 `ResourceDictionary`。 此範例宣告隱含的樣式 (亦即，未設定 `Key`)，這表示它將適用於頁面上的所有 `Entry` 控制項。

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style TargetType="Entry">
                        <Style.Triggers>
                <Trigger TargetType="Entry"
                         Property="IsFocused" Value="True">
                    <Setter Property="BackgroundColor" Value="Yellow" />
                </Trigger>
            </Style.Triggers>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>
```

<a name="data" />

## <a name="data-triggers"></a>資料觸發程序

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
        </DataTrigger>
    </Button.Triggers>
</Button>
```

> [!TIP]
> 在評估 `Path=Text.Length` 時，一律為目標屬性提供預設值 (例如， `Text=""`)，否則它將會是 `null` 且觸發程序將無法如您預期運作。

除了指定 `Setter` 之外，您也可以提供 [`EnterActions` 和 `ExitActions`](#enterexit)。

<a name="event" />

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

- 在宣告觸發程式時，選擇性地公開可在 XAML 中設定的屬性。 如需這種情況的範例， `VisualElementPopTriggerAction`請參閱隨附範例應用程式中的類別。

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

然後，可以從 XAML 取用事件觸發程式：

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

在 `ResourceDictionary` 中共用觸發程序時請小心，將會在控制項之間共用一個執行個體，因此設定一次的任何狀態將會適用於全部。

請注意，事件觸發程序不支援 `EnterActions` 和 `ExitActions`，[如下所述](#enterexit)。

<a name="multi" />

## <a name="multi-triggers"></a>多重觸發程序

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

![](triggers-images/multi-requireall.png "MultiTrigger 範例")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions 和 ExitActions

另一種在觸發程序發生時實作變更的方法，是藉由新增 `EnterActions` 和 `ExitActions` 集合，並指定 `TriggerAction<T>` 實作。

您可以「同時」在觸發程序中提供 `EnterActions` 和 `ExitActions`，以及 `Setter`；但請注意，`Setter` 會立即呼叫 (其不會等待 `EnterAction` 或 `ExitAction` 完成)。 或者，您可以在程式碼中執行一切，完全不使用 `Setter`。

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
    public FadeTriggerAction() {}

    public int StartsFrom { set; get; }

    protected override void Invoke (VisualElement visual)
    {
            visual.Animate("", new Animation( (d)=>{
                var val = StartsFrom==1 ? d : 1-d;
                visual.BackgroundColor = Color.FromRgb(1, val, 1);

            }),
            length:1000, // milliseconds
            easing: Easing.Linear);
    }
}
```

注意︰會在**事件觸發程序**上忽略`EnterActions` 和 `ExitActions`。

## <a name="related-links"></a>相關連結

- [觸發程序範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithtriggers)
- [Xamarin.Forms API 文件](xref:Xamarin.Forms.TriggerAction`1)
