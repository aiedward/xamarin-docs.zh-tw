---
title: Xamarin. Forms TimePicker
description: TimePicker 是一個 [Xamarin] 表單檢視，可讓使用者選取時間。 本文說明如何在 Xamarin. Forms 應用程式中使用 TimePicker。
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
ms.openlocfilehash: aae0791199b0e3042a3c619fcb11e7b877f52012
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695907"
---
# <a name="xamarinforms-timepicker"></a>Xamarin. Forms TimePicker

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_可讓使用者選取時間的 [Xamarin] 表單檢視。_

[Xamarin] [`TimePicker`](xref:Xamarin.Forms.TimePicker)會叫用平臺的時間選擇器控制項，並允許使用者選取時間。 `TimePicker` 會定義下列屬性：

- `TimeSpan` 類型的[`Time`](xref:Xamarin.Forms.TimePicker.Time) ，這是選取的時間，預設為 `TimeSpan` 0。 @No__t_0 類型表示從午夜起算的時間長度。
- `string` 類型的[`Format`](xref:Xamarin.Forms.TimePicker.Format) ，這是[標準](/dotnet/standard/base-types/standard-date-and-time-format-strings/)或[自訂](/dotnet/standard/base-types/custom-date-and-time-format-strings/)的 .net 格式字串，預設為 "t"，這是簡短的時間模式。
- [`Color`](xref:Xamarin.Forms.Color)類型的[`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) ，用來顯示所選時間的色彩，預設為[`Color.Default`](xref:Xamarin.Forms.Color.Default)。
- [`FontAttributes`](xref:Xamarin.Forms.FontAttributes)類型的[`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) ，預設為[`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None)。
- `string` 類型的[`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) ，預設為 `null`。
- `double` 類型的[`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) ，預設為-1.0。
- `CharacterSpacing`，屬於 `double` 類型，這是 `TimePicker` 文字字元之間的間距。

所有這些屬性都是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以樣式化，而屬性可以是資料系結的目標。 [@No__t_1](xref:Xamarin.Forms.TimePicker.Time)屬性具有[`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)的預設系結模式，這表示它可以是使用[模型 ViewModel （MVVM）](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構之應用程式中的資料系結目標。

[@No__t_1](xref:Xamarin.Forms.TimePicker)不包含表示新選取[`Time`](xref:Xamarin.Forms.TimePicker.Time)值的事件。 如果您需要收到這項通知，您可以加入[`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件的處理常式。

## <a name="initializing-the-time-property"></a>初始化 Time 屬性

在程式碼中，您可以將[`Time`](xref:Xamarin.Forms.TimePicker.Time)屬性初始化為 `TimeSpan` 類型的值：

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

在 XAML 中指定[`Time`](xref:Xamarin.Forms.TimePicker.Time)屬性時，此值會轉換成 `TimeSpan` 並經過驗證，以確保毫秒數大於或等於0，且小時數小於24。 時間元件應以冒號分隔：

```xaml
<TimePicker Time="4:15:26" />
```

如果[`TimePicker`](xref:Xamarin.Forms.TimePicker)的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)屬性設定為 ViewModel 的實例，其中包含名為 `SelectedTime` 之類型 `TimeSpan` 的屬性（例如），您可以將 `TimePicker` 具現化，如下所示：

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

在此範例中， [`Time`](xref:Xamarin.Forms.TimePicker.Time)屬性會初始化為 ViewModel 中的 `SelectedTime` 屬性。 因為 `Time` 屬性具有[`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)的系結模式，所以使用者選取的任何新時間都會自動傳播至 ViewModel。

如果[`TimePicker`](xref:Xamarin.Forms.TimePicker)不包含其[`Time`](xref:Xamarin.Forms.TimePicker.Time)屬性的系結，應用程式應將處理常式附加至[`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件，以便在使用者選取新時間時收到通知。

如需設定字型屬性的相關資訊，請參閱[Fonts](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="timepicker-and-layout"></a>TimePicker 和版面配置

您可以使用不受限制的水準配置選項，例如 `Center`、`Start` 或 `End` 與[`TimePicker`](xref:Xamarin.Forms.TimePicker)：

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

不過，不建議您這樣做。 視[`Format`](xref:Xamarin.Forms.TimePicker.Format)屬性的設定而定，選取的時間可能需要不同的顯示寬度。 例如，"T" 格式字串會導致[`TimePicker`](xref:Xamarin.Forms.TimePicker) view 以長格式顯示時間，而 "4:15:26 am" 所需的顯示寬度大於「4:15 am」的簡短時間格式（"T"）。 視平臺而定，這項差異可能會導致 `TimePicker` 視圖變更版面配置中的寬度，或顯示要被截斷。

> [!TIP]
> 最好是使用 `Fill` 的預設 `HorizontalOptions` 設定搭配[`TimePicker`](xref:Xamarin.Forms.TimePicker)，而不是在 `TimePicker` 資料格中放置 `Grid` 時，使用 `Auto` 的寬度。

## <a name="timepicker-in-an-application"></a>應用程式中的 TimePicker

[**SetTimer**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)範例包含頁面上的[`TimePicker`](xref:Xamarin.Forms.TimePicker)、 [`Entry`](xref:Xamarin.Forms.Entry)和[`Switch`](xref:Xamarin.Forms.Switch)視圖。 @No__t_0 可用來選取時間，而當該時間發生時，會顯示警示對話方塊，提醒使用者 `Entry` 中的文字（如果 `Switch` 已切換）。 以下是 XAML 檔案：

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

此[`Entry`](xref:Xamarin.Forms.Entry)可讓您輸入在所選取時間發生時所顯示的提醒文字。 [@No__t_1](xref:Xamarin.Forms.TimePicker)會被指派 "t" 的[`Format`](xref:Xamarin.Forms.TimePicker.Format)屬性，以提供完整時間格式。 它具有附加至[`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged)事件的事件處理常式，而且[`Switch`](xref:Xamarin.Forms.Switch)具有附加至其[`Toggled`](xref:Xamarin.Forms.Switch.Toggled)事件的處理常式。 這些事件處理常式會在程式碼後置檔案中，並呼叫 `SetTriggerTime` 方法：

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

@No__t_0 方法會根據 `DateTime.Today` 屬性值和[`TimePicker`](xref:Xamarin.Forms.TimePicker)傳回的 `TimeSpan` 值來計算計時器時間。 這是必要的，因為 `DateTime.Today` 屬性會傳回代表目前日期的 `DateTime`，但時間為午夜。 如果目前已通過計時器時間，則會假設為明天。

計時器每秒計時，執行 `OnTimerTick` 方法，檢查[`Switch`](xref:Xamarin.Forms.Switch)是否開啟，以及目前的時間是否大於或等於計時器時間。 當計時器時間發生時， [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*)方法會以提醒形式向使用者顯示警示對話方塊。

第一次執行範例時， [`TimePicker`](xref:Xamarin.Forms.TimePicker)視圖會初始化為11am。 點擊 `TimePicker` 會叫用平臺時間選擇器。 平臺以非常不同的方式來實行時間選擇器，但該平臺的使用者可以熟悉每一種方法：

[![選取時間](timepicker-images/timepicker-open.png "選取時間")](timepicker-images/timepicker-open-large.png#lightbox "選取時間")

> [!TIP]
> 在 Android 上，您可以在自訂轉譯器中覆寫 `CreateTimePickerDialog` 方法，以自訂 [ [`TimePicker`](xref:Xamarin.Forms.TimePicker) ] 對話方塊。 例如，這可讓您將其他按鈕新增至對話方塊。

選取時間之後，所選取的時間會顯示在  [`TimePicker`](xref:Xamarin.Forms.TimePicker)：

[![選取的時間](timepicker-images/timepicker-selected.png "選取的時間")](timepicker-images/timepicker-selected-large.png#lightbox "選取的時間")

如果[`Switch`](xref:Xamarin.Forms.Switch)切換到 [開啟] 位置，應用程式會顯示警示對話方塊，提醒使用者在選取的時間發生時， [`Entry`](xref:Xamarin.Forms.Entry)中的文字：

[![計時器快顯視窗](timepicker-images/timer-test.png "計時器快顯視窗")](timepicker-images/timer-test-large.png#lightbox "計時器快顯視窗")

一旦顯示 [警示] 對話方塊， [`Switch`](xref:Xamarin.Forms.Switch)就會切換到 [關閉] 位置。

## <a name="related-links"></a>相關連結

- [SetTimer 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [TimePicker API](xref:Xamarin.Forms.TimePicker)
