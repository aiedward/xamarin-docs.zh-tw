---
title: 觸發程序
description: 回應具有 XAML 使用者介面變更
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: af5912736e2a2bd7d3347d4aa199faa3fdfe41c7
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846441"
---
# <a name="triggers"></a>觸發程序

觸發程序可讓您表示在 XAML 中以宣告方式變更的事件或變更屬性，依據控制項的外觀的動作。

您可以直接為控制項中，指定觸發程序，或將它加入至要套用至多個控制項的頁面層級或應用程式層級的資源字典。

有四種類型的觸發程序：

* [屬性觸發程序](#property)-發生於控制項的屬性設定為特定值。

* [資料觸發程序](#data)-使用資料繫結至觸發程序根據另一個控制項的屬性。

* [事件觸發程序](#event)-在控制項上發生事件時，就會發生。

* [多重觸發程序](#multi)-可讓多個觸發程序條件在動作發生之前設定。

<a name="property" />

## <a name="property-triggers"></a>屬性觸發程序

簡單的觸發程序可以表示只在 XAML 中，加入`Trigger`項目，來控制觸發程序的集合。
這個範例會示範變更觸發程序`Entry`收到焦點時，背景色彩：

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

重要的組件的觸發程序的宣告如下：

* **TargetType** -適用於觸發程序的控制項類型。

* **屬性**-監視控制項上的屬性。

* **值**-值受監視的屬性時，會導致啟動觸發程序。

* **Setter** -集合`Setter`可以加入項目，而且當觸發程序符合條件。 您必須指定`Property`和`Value`設定。

* **EnterActions 和 ExitActions** （未顯示）-撰寫的程式碼，而且可用除了 （或 instead of）`Setter`項目。 它們是[下述](#enterexit)。

### <a name="applying-a-trigger-using-a-style"></a>套用樣式的觸發程序

觸發程序也可以加入至`Style` 頁面上或應用程式中，將控制項上的宣告`ResourceDictionary`。 這個範例會宣告為隱含的樣式 (ie。 沒有`Key`設定) 表示不會套用至所有`Entry`頁面上的控制項。

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

資料觸發程序監視會造成另一個控制項使用資料繫結`Setter`來呼叫。 而不是`Property`屬性觸發程序中的屬性，設定`Binding`屬性，以監視指定的值。

下列範例會使用資料繫結語法`{Binding Source={x:Reference entry}, Path=Text.Length}`也就是我們如何參考另一個控制項的屬性。 當長度`entry`是零，則會啟動觸發程序。 在此範例中觸發程序停用按鈕時輸入是空的。

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

提示： 評估時`Path=Text.Length`一律提供預設值 （例如之目標屬性 `Text=""`) 否則它將會因為`null`和觸發程序將無法運作如您預期。

除了指定`Setter`s，您也可以提供[`EnterActions`和`ExitActions` ](#enterexit)。

<a name="event" />

## <a name="event-triggers"></a>事件觸發程序

`EventTrigger`元素只需要`Event`屬性，例如`"Clicked"`在下列範例中。

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

請注意，有任何`Setter`元素，但是所定義類別而不是參考`local:NumericValidationTriggerAction`需要`xmlns:local`頁面中宣告的 XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

此類別本身會實作`TriggerAction`這表示它應該提供的覆寫`Invoke`在觸發程序事件發生時呼叫的方法。

觸發程序動作實作應該：

* 實作泛型`TriggerAction<T>`類別，與對應的觸發程序將會套用到控制項類型的泛型參數。 您可以使用超級類別，例如`VisualElement`撰寫使用各種不同的控制項，或指定的控制項類型的觸發程序動作`Entry`。

* 覆寫`Invoke`方法-這每當呼叫符合觸發程序準則。

* 選擇性地公開觸發程序是在宣告可以在 XAML 中設定的屬性 (例如`Anchor`， `Scale`，和`Length`在此範例中)。

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

觸發程序動作所公開的屬性可以設定中的 XAML 宣告，如下所示：

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

共用觸發程序中的時請小心`ResourceDictionary`，將控制項之間共用一個執行個體，因此任何設定一次的狀態將會套用至全部。

請注意，事件觸發程序不支援`EnterActions`和`ExitActions`[下述](#enterexit)。    

<a name="multi" />

## <a name="multi-triggers"></a>多重觸發程序

A`MultiTrigger`看起來類似`Trigger`或`DataTrigger`除了可以有多個條件。 所有條件都必須成立之前`Setter`s 觸發。

以下是一個按鈕，將繫結至兩個不同輸入的觸發程序的範例 (`email`和`phone`):

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

`Conditions`集合也可以包含`PropertyCondition`項目如下所示：

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>建置 「 需要所有 「 多重觸發程序

所有條件都都成立時，多重觸發程序只會更新它的控制項。 因為您想要的條件測試 「 所有的欄位長度為零 」 （例如必須完成所有輸入的登入頁面） 是很難解釋"其中 Text.Length > 0"，但這不表示在 XAML 中。

這可以透過`IValueConverter`。 轉換器下列程式碼轉換`Text.Length`繫結到`bool`，指出欄位是否為空白：


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

若要使用這個轉換子中的多重觸發程序，先將它加入至頁面的資源字典 (以及自訂`xmlns:local`命名空間定義):

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

XAML 會如下所示。 請注意第一個多重觸發程序範例中的下列差異：

* 按鈕的`IsEnabled="false"`預設設定。
* 多重觸發條件使用轉換程式來開啟`Text.Length`值轉換為 boolean。
* 當所有條件都都`true`，setter 使按鈕的`IsEnabled`屬性`true`。

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

這些螢幕擷取畫面顯示上述兩個多重觸發程序範例之間的差異。 文字輸入中只有一個螢幕的上半部，`Entry`即已足夠讓**儲存** 按鈕。
在畫面的下半部**登入**按鈕保持非使用中，直到這兩個欄位包含的資料。


![](triggers-images/multi-requireall.png "MultiTrigger 範例")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions 和 ExitActions

若要實作變更時觸發程序，就會發生另一個方法是加入`EnterActions`和`ExitActions`集合，並指定`TriggerAction<T>`實作。

您可以提供*兩者*`EnterActions`和`ExitActions`以及`Setter`中觸發程序，但請注意， `Setter`s 會立即呼叫 (它們不會等待`EnterAction`或`ExitAction`至完成）。 或者，您便可以在程式碼中執行的所有項目上，並不使用`Setter`所有 s。

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

因為一律，當類別參考在 XAML 中應該這類宣告命名空間`xmlns:local`如下所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

`FadeTriggerAction`程式碼如下所示：

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

注意：`EnterActions`和`ExitActions`會略過**事件觸發程序**。



## <a name="related-links"></a>相關連結

- [觸發程序範例](https://developer.xamarin.com/samples/WorkingWithTriggers)
- [Xamarin.Forms 應用程式開發介面文件](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerAction%3CT%3E/)
