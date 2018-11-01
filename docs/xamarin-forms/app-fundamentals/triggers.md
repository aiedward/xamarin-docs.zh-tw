---
title: Xamarin.Forms 的觸發程序
description: 這篇文章說明如何使用 Xamarin.Forms 觸發程序來回應具有 XAML 使用者介面的變更。 觸發程序可讓您以宣告方式在 XAML 中變更的事件或屬性變更為基礎的控制項外觀的動作。
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: e9ec9288e2b8ea991ef8d41f9b601d0897631b9d
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675207"
---
# <a name="xamarinforms-triggers"></a>Xamarin.Forms 的觸發程序

觸發程序可讓您以宣告方式在 XAML 中變更的事件或屬性變更為基礎的控制項外觀的動作。

您可以直接為控制項中，指定觸發程序，或將它新增至要套用至多個控制項的頁面層級或應用程式層級資源字典。

有四種類型的觸發程序：

* [屬性觸發程序](#property)-發生於控制項上的屬性設定為特定值。

* [資料觸發程序](#data)-使用資料繫結至另一個控制項的屬性為基礎的觸發程序。

* [事件觸發程序](#event)-控制項上發生事件時，就會發生。

* [多個觸發程序](#multi)-可讓多個觸發程序條件在動作發生之前設定。

<a name="property" />

## <a name="property-triggers"></a>屬性觸發程序

簡單的觸發程序完全以 XAML，新增`Trigger`至控制項的項目觸發程序的集合。
此範例示範變更的觸發程序`Entry`收到焦點時，背景色彩：

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

觸發程序的重要部分是宣告的：

* **TargetType** -觸發程序套用至的控制項類型。

* **屬性**-監視控制項上的屬性。

* **值**-的值，針對受監視的內容時，導致啟動的觸發程序。

* **Setter** -集合`Setter`可以加入項目和觸發程序條件符合時。 您必須指定`Property`和`Value`設定。

* **EnterActions 和 ExitActions** （未顯示）： 以程式碼撰寫，並可用除了 （或 instead of）`Setter`項目。 它們[下述](#enterexit)。

### <a name="applying-a-trigger-using-a-style"></a>套用樣式的觸發程序

觸發程序也可以加入至`Style`控制項，在頁面上或應用程式上的宣告`ResourceDictionary`。 這個範例會宣告隱含樣式 (亦即。 沒有`Key`設定) 表示不會套用至所有`Entry`頁面上的控制項。

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

資料觸發程序來監視另一個控制項，讓使用資料繫結`Setter`來呼叫。 而不是`Property`屬性的屬性觸發程序，請設定`Binding`屬性，以監視指定的值。

下列範例會使用資料繫結語法 `{Binding Source={x:Reference entry}, Path=Text.Length}`
這是指另一個控制項的屬性。 當長度`entry`為零，在啟動觸發程序。 在此範例中的觸發程序停用按鈕，當輸入是空的。

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

提示： 評估時`Path=Text.Length`一律提供目標屬性 （例如預設值。 `Text=""`) 否則它將會因為`null`和觸發程序將無法運作如您預期。

除了指定`Setter`您也可以提供的 s [ `EnterActions`並`ExitActions` ](#enterexit)。

<a name="event" />

## <a name="event-triggers"></a>事件觸發程序

`EventTrigger`項目只需要`Event`屬性，例如`"Clicked"`在下列範例中。

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

請注意，有沒有`Setter`元素，但是而不是所定義的類別的參考`local:NumericValidationTriggerAction`需要`xmlns:local`在頁面中宣告的 XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

此類別本身會實作`TriggerAction`表示不應提供的覆寫`Invoke`每次觸發程序事件發生時呼叫的方法。

觸發程序動作實作應該：

* 實作泛型`TriggerAction<T>`類別，且對應的觸發程序將會套用至的控制項類型的泛型參數。 您可以使用例如 superclasses`VisualElement`撰寫使用各種控制項，或指定的控制項類型的觸發程序動作`Entry`。

* 覆寫`Invoke`-這呼叫方法時符合觸發程序準則。

* 選擇性地公開 （expose) 可以在觸發程序宣告時在 XAML 中設定的屬性 (例如`Anchor`， `Scale`，和`Length`在此範例中)。

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

觸發程序動作所公開的屬性可以設定在 XAML 宣告，如下所示：

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

共用觸發程序中的時請小心`ResourceDictionary`，將會在控制項之間共用一個執行個體，因此設定一次的任何狀態將會套用至全部。

請注意，不支援事件觸發程序`EnterActions`並`ExitActions`[下述](#enterexit)。

<a name="multi" />

## <a name="multi-triggers"></a>多個觸發程序

A`MultiTrigger`看起來類似`Trigger`或`DataTrigger`除了可以有多個條件。 所有條件必須都為 true 之前`Setter`s 會觸發。

以下是範例會將繫結至兩個不同的輸入的按鈕觸發程序 (`email`和`phone`):

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

`Conditions`可能也會包含集合`PropertyCondition`項目如下所示：

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>建置 「 需要所有 「 多重觸發程序

多個觸發程序在所有條件都都成立時，只會更新其控制項。 測試 「 所有的欄位長度為零 」 （例如必須完成所有輸入的登入頁面） 相當困難，因為您想要條件 」 其中 Text.Length > 0"，但這不表示在 XAML 中。

這可以透過完成`IValueConverter`。 轉換器以下的程式碼轉換`Text.Length`繫結至`bool`，指出欄位是否為空白：

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

若要在多個觸發程序中使用這個轉換子，先將其新增至頁面的資源字典 (以及自訂`xmlns:local`命名空間定義):

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

XAML 如下所示。 請注意下列差異的第一個多重觸發程序範例：

* 按鈕具有`IsEnabled="false"`預設設定。
* 多重觸發條件使用轉換器來開啟`Text.Length`值貼`boolean`。
* 在所有條件都都`true`，setter 會讓按鈕的`IsEnabled`屬性`true`。

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

這些螢幕擷取畫面顯示上述的兩個多重觸發程序範例之間的差異。 在畫面頂端的文字輸入中只有一個`Entry`即可啟用**儲存** 按鈕。
在畫面底部**登入**按鈕保持非使用中，直到兩個欄位包含資料。

![](triggers-images/multi-requireall.png "MultiTrigger 範例")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions 和 ExitActions

若要實作變更，觸發程序發生時的另一個方法是藉由新增`EnterActions`並`ExitActions`集合，並指定`TriggerAction<T>`實作。

您可以提供*兩者*`EnterActions`並`ExitActions`，以及`Setter`中觸發程序，但請注意， `Setter`s 會立即呼叫 (它們不會等待`EnterAction`或`ExitAction`來完成）。 或者，您便可以在程式碼中執行的所有項目上，並不使用`Setter`完全 s。

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

如往常，當參考類別在 XAML 中應該這類宣告的命名空間`xmlns:local`如下所示：

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

注意︰`EnterActions`並`ExitActions`上會忽略**事件觸發程序**。



## <a name="related-links"></a>相關連結

- [觸發程序範例](https://developer.xamarin.com/samples/WorkingWithTriggers)
- [Xamarin.Forms API 文件](xref:Xamarin.Forms.TriggerAction`1)
