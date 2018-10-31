---
title: Xamarin.Forms TimePicker
description: TimPicker 是 Xamarin.Forms 檢視，可讓使用者選取的時間。 這篇文章說明如何使用 Xamarin.Forms 應用程式中的 TimePicker。
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
ms.openlocfilehash: 9a534c39d514fec9a0de4bc810f33c972453baa2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111552"
---
# <a name="xamarinforms-timepicker"></a>Xamarin.Forms TimePicker

_Xamarin.Forms 檢視，可讓使用者選取的時間。_

Xamarin.Forms [ `TimePicker` ](xref:Xamarin.Forms.TimePicker)叫用平台的時間選擇器控制項，並可讓使用者選取的時間。 `TimePicker` 定義下列屬性：

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) 型別的`TimeSpan`、 選取的時間預設為`TimeSpan`為 0。 `TimeSpan`類型指出持續時間為午夜的時間。
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) 型別的`string`，則[標準](/dotnet/standard/base-types/standard-date-and-time-format-strings/)或是[自訂](/dotnet/standard/base-types/custom-date-and-time-format-strings/).NET 格式字串，預設值為"t"、 簡短時間模式。
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) 型別的[ `Color` ](xref:Xamarin.Forms.Color)，用來顯示選取的時間預設為色彩[ `Color.Default` ](xref:Xamarin.Forms.Color.Default)。
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) 型別的[ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes)，預設為[ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None)。
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) 型別的`string`，預設為`null`。
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) 型別的`double`，預設為-1.0。

所有這些屬性會受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示它們可自訂樣式，而且屬性可以是資料繫結的目標。 [ `Time` ](xref:Xamarin.Forms.TimePicker.Time)屬性具有預設繫結模式[ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)，這表示它可以是資料繫結中使用的應用程式的目標[Model View ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構。

[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)不包含表示新選取的事件[ `Time` ](xref:Xamarin.Forms.TimePicker.Time)值。 如果您需要的收到通知，您可以新增的處理常式[ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件。

## <a name="initializing-the-time-property"></a>初始化的時間屬性

您可以在程式碼，來初始化[ `Time` ](xref:Xamarin.Forms.TimePicker.Time)屬性設為值型別的`TimeSpan`:

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

當[ `Time` ](xref:Xamarin.Forms.TimePicker.Time)屬性已指定在 XAML 中，值會轉換成`TimeSpan`且經過驗證，若要確保的毫秒數大於或等於 0，而且的時數會少於 24 小時。 時間元件應該以冒號分隔：

```xaml
<TimePicker Time="4:15:26" />
```

如果[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)屬性[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)設定為包含類型的屬性的 ViewModel 的執行個體`TimeSpan`名為`SelectedTime`（舉例來說），您可以具現化`TimePicker`如下所示：

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

在此範例中， [ `Time` ](xref:Xamarin.Forms.TimePicker.Time)屬性會初始化為`SelectedTime`在 ViewModel 的屬性。 因為`Time`屬性已繫結模式[ `TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)，任何新的時間，使用者選取自動傳播到 ViewModel。

如果[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)不包含繫結在其[ `Time` ](xref:Xamarin.Forms.TimePicker.Time)屬性，應用程式應該附加至處理常式[ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件當使用者選取新的時間，則會被告知。

如需設定字型屬性的詳細資訊，請參閱[字型](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="timepicker-and-layout"></a>TimePicker 和版面配置

您可使用不受限制的水平版面配置選項，例如`Center`， `Start`，或`End`具有[ `TimePicker` ](xref:Xamarin.Forms.TimePicker):

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

不過，這不建議。 視設定而定[ `Format` ](xref:Xamarin.Forms.TimePicker.Format)所選取的屬性的時間可能需要不同的顯示寬度。 例如，"T"格式字串會導致[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)檢視可顯示時間很長的格式，和： 15:26 4AM 」 需要更高的顯示寬度比： 15 4AM 」 的簡短時間格式 ("t")。 根據平台，這項差異可能會導致`TimePicker`檢視 來變更在配置中，或被截斷的顯示寬度。

> [!TIP]
> 最好是使用預設`HorizontalOptions`設定`Fill`具有[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)，且不使用的寬度`Auto`當放置`TimePicker`中`Grid`資料格。

## <a name="timepicker-in-an-application"></a>應用程式中的 TimePicker

[ **SetTimer** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TimePicker/)範例包括[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)， [ `Entry` ](xref:Xamarin.Forms.Entry)，與[ `Switch` ](xref:Xamarin.Forms.Switch)其頁面上的檢視。 `TimePicker`可用來選取時間，以及警示的對話方塊會顯示，以提醒使用者中的文字時，時間就會發生`Entry`提供`Switch`上切換。 以下是 XAML 檔案：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SetTimer"
             x:Class="SetTimer.MainPage">
    <StackLayout>
        ...
        <Entry x:Name="_entry"
               Placeholder="Enter event to be reminded of" />
        <Label Text="Select the time below to be reminded at." />
        <TimePicker x:Name="_timePicker"
                    Time="11:00:00"
                    Format="T"
                    PropertyChanged="OnTimePickerPropertyChanged" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Enable timer:" />
            <Switch x:Name="_switch"
                    HorizontalOptions="EndAndExpand"
                    Toggled="OnSwitchToggled" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

[ `Entry` ](xref:Xamarin.Forms.Entry)可讓您輸入在選取的時間，就會發生時，會顯示的提示文字。 [ `TimePicker` ](xref:Xamarin.Forms.TimePicker)指派[ `Format` ](xref:Xamarin.Forms.TimePicker.Format)長的時間格式的"T"屬性。 它已附加至事件處理常式[ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件，而[ `Switch` ](xref:Xamarin.Forms.Switch)有處理常式附加至其[ `Toggled` ](xref:Xamarin.Forms.Switch.Toggled)事件。 這些事件處理常式會在程式碼後置檔案，並呼叫`SetTriggerTime`方法：

```csharp
public partial class MainPage : ContentPage
{
    DateTime _triggerTime;

    public MainPage()
    {
        InitializeComponent();

        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimerTick);
    }

    bool OnTimerTick()
    {
        if (_switch.IsToggled && DateTime.Now >= _triggerTime)
        {
            _switch.IsToggled = false;
            DisplayAlert("Timer Alert", "The '" + _entry.Text + "' timer has elapsed", "OK");
        }
        return true;
    }

    void OnTimePickerPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        if (args.PropertyName == "Time")
        {
            SetTriggerTime();
        }
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        SetTriggerTime();
    }

    void SetTriggerTime()
    {
        if (_switch.IsToggled)
        {
            _triggerTime = DateTime.Today + _timePicker.Time;
            if (_triggerTime < DateTime.Now)
            {
                _triggerTime += TimeSpan.FromDays(1);
            }
        }
    }
}
```

`SetTriggerTime`方法會計算為基礎的計時器時間`DateTime.Today`屬性值和`TimeSpan`傳回值[ `TimePicker` ](xref:Xamarin.Forms.TimePicker)。 這是必要的因為`DateTime.Today`屬性會傳回`DateTime`指出目前的日期，但時間為午夜。 如果計時器時間已超過目前，它會假設為明天。

計時器刻度執行的每秒`OnTimerTick`方法，以檢查是否[ `Switch` ](xref:Xamarin.Forms.Switch)是以及在目前的時間大於或等於計時器時間。 當計時器時間發生時， [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*)方法對提醒您使用者顯示警示對話方塊。

當第一次執行範例時， [ `TimePicker` ](xref:Xamarin.Forms.TimePicker)檢視會初始化為上午 11 點。 點選`TimePicker`叫用平台時間選擇器。 三個平台實作時間選擇器以非常不同的方式，但是每一種方法是以該平台的使用者熟悉：

[![選取時間](timepicker-images/timepicker-open.png "選取時間")](timepicker-images/timepicker-open-large.png#lightbox "選取時間")

> [!TIP]
> 在 Android 上， [ `TimePicker` ](xref:Xamarin.Forms.TimePicker)對話方塊可以藉由覆寫自訂`CreateTimePickerDialog`中自訂轉譯器的方法。 這可讓，比方說，可以加入至對話方塊中的其他按鈕。

選取一次之後, 在選取的時間會顯示在[ `TimePicker` ](xref:Xamarin.Forms.TimePicker):

[![選取時間](timepicker-images/timepicker-selected.png "時間")](timepicker-images/timepicker-selected-large.png#lightbox "選取時間")

前提[ `Switch` ](xref:Xamarin.Forms.Switch)切換到 on 的位置中，應用程式會顯示警示對話方塊之後提醒中文字的使用者[ `Entry` ](xref:Xamarin.Forms.Entry)在選取的時間就會發生：

[![計時器快顯](timepicker-images/timer-test.png "計時器快顯")](timepicker-images/timer-test-large.png#lightbox "計時器快顯視窗")

[警示] 對話方塊隨即顯示，如[ `Switch` ](xref:Xamarin.Forms.Switch)已切換為 off 的位置。

## <a name="related-links"></a>相關連結

- [SetTimer 範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TimePicker/)
- [TimePicker API](xref:Xamarin.Forms.TimePicker)
