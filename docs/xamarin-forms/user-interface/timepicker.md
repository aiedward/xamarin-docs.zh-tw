---
title: Xamarin.FormsTimePicker
description: TimePicker 是 Xamarin.Forms 可讓使用者選取時間的視圖。 本文說明如何在應用程式中使用 TimePicker Xamarin.Forms 。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7c2e6b97f2207ebb6543fb6a720cd430331f989b
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138225"
---
# <a name="xamarinforms-timepicker"></a>Xamarin.FormsTimePicker

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_Xamarin.Forms允許使用者選取時間的視圖。_

會叫 Xamarin.Forms [`TimePicker`](xref:Xamarin.Forms.TimePicker) 用平臺的時間選擇器控制項，並允許使用者選取時間。 `TimePicker` 會定義下列屬性：

- [`Time`](xref:Xamarin.Forms.TimePicker.Time)類型的 `TimeSpan` ，選取的時間，預設為 `TimeSpan` 0。 此 `TimeSpan` 類型表示自午夜起的時間長度。
- [`Format`](xref:Xamarin.Forms.TimePicker.Format)屬於類型的 `string` [標準](/dotnet/standard/base-types/standard-date-and-time-format-strings/)或[自訂](/dotnet/standard/base-types/custom-date-and-time-format-strings/).net 格式字串，預設為 "t"，簡短時間模式。
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor)類型的 [`Color`](xref:Xamarin.Forms.Color) ，用來顯示所選時間的色彩，預設為 [`Color.Default`](xref:Xamarin.Forms.Color.Default) 。
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes)類型的 [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) ，預設為 [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) 。
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily)類型的 `string` ，預設為 `null` 。
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize)類型的 `double` ，預設為-1.0。
- `CharacterSpacing`，屬於類型 `double` ，這是文字字元之間的間距 `TimePicker` 。

所有這些屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以樣式化，而屬性可以是資料系結的目標。 [`Time`](xref:Xamarin.Forms.TimePicker.Time)屬性具有的預設系結模式 [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) ，這表示它可以是使用[模型 ViewModel （MVVM）](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構之應用程式中的資料系結目標。

[`TimePicker`](xref:Xamarin.Forms.TimePicker)不包含表示新選取之值的事件 [`Time`](xref:Xamarin.Forms.TimePicker.Time) 。 如果您需要收到這項通知，您可以加入事件的處理常式 [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) 。

## <a name="initializing-the-time-property"></a>初始化 Time 屬性

在程式碼中，您可以將 [`Time`](xref:Xamarin.Forms.TimePicker.Time) 屬性初始化為類型的值 `TimeSpan` ：

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

在 [`Time`](xref:Xamarin.Forms.TimePicker.Time) XAML 中指定屬性時，此值會轉換為 `TimeSpan` 並經過驗證，以確保毫秒數大於或等於0，且小時數小於24。 時間元件應以冒號分隔：

```xaml
<TimePicker Time="4:15:26" />
```

如果的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 屬性 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 設定為 ViewModel 的實例，其中包含名為之類型的屬性 `TimeSpan` `SelectedTime` （例如），則您可以具現化， `TimePicker` 如下所示：

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

在此範例中， [`Time`](xref:Xamarin.Forms.TimePicker.Time) 屬性會初始化為 `SelectedTime` ViewModel 中的屬性。 因為 `Time` 屬性具有的系結模式 [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) ，所以使用者選取的任何新時間都會自動傳播至 ViewModel。

如果未 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 在其屬性上包含系結 [`Time`](xref:Xamarin.Forms.TimePicker.Time) ，應用程式應將處理常式附加至 [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) 事件，以便在使用者選取新時間時收到通知。

如需設定字型屬性的相關資訊，請參閱[Fonts](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="timepicker-and-layout"></a>TimePicker 和版面配置

您可以使用不受限制的水準配置選項，例如 `Center` 、 `Start` 或， `End` 搭配 [`TimePicker`](xref:Xamarin.Forms.TimePicker) ：

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

不過，不建議您這樣做。 視屬性的設定而定 [`Format`](xref:Xamarin.Forms.TimePicker.Format) ，選取的時間可能需要不同的顯示寬度。 例如，"T" 格式字串會讓 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 視圖以長的格式顯示時間，而「4:15:26 am」則需要比「4:15 am」的簡短時間格式（"T"）更大的顯示寬度。 視平臺而定，這項差異可能會導致 `TimePicker` 視圖變更版面配置中的寬度，或要截斷顯示。

> [!TIP]
> 最好是使用的預設值搭配 `HorizontalOptions` `Fill` [`TimePicker`](xref:Xamarin.Forms.TimePicker) ，而不是在 `Auto` 放入資料格時使用的寬度 `TimePicker` `Grid` 。

## <a name="timepicker-in-an-application"></a>應用程式中的 TimePicker

[**SetTimer**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)範例會 [`TimePicker`](xref:Xamarin.Forms.TimePicker) [`Entry`](xref:Xamarin.Forms.Entry) [`Switch`](xref:Xamarin.Forms.Switch) 在其頁面上包含、和 views。 `TimePicker`可以用來選取時間，而當該時間發生時，會顯示警示對話方塊，提醒使用者中的文字 `Entry` （前提 `Switch` 是已切換開啟）。 以下是 XAML 檔案：

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

[`Entry`](xref:Xamarin.Forms.Entry)可讓您輸入將在所選時間發生時顯示的提醒文字。 [`TimePicker`](xref:Xamarin.Forms.TimePicker)會 [`Format`](xref:Xamarin.Forms.TimePicker.Format) 針對長時間格式指派 "T" 的屬性。 它具有附加至事件的事件處理常式 [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) ，而且 [`Switch`](xref:Xamarin.Forms.Switch) 具有附加至其事件的處理常式 [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) 。 這些事件處理常式會在程式碼後置檔案中，並呼叫 `SetTriggerTime` 方法：

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

方法會根據 `SetTriggerTime` `DateTime.Today` 屬性值和從傳回的值來計算計時器時間 `TimeSpan` [`TimePicker`](xref:Xamarin.Forms.TimePicker) 。 這是必要的，因為屬性會傳回 `DateTime.Today` `DateTime` ，指出目前的日期，但時間為午夜。 如果目前已通過計時器時間，則會假設為明天。

計時器每秒計時，執行 `OnTimerTick` 檢查是否 [`Switch`](xref:Xamarin.Forms.Switch) 開啟，以及目前時間是否大於或等於計時器時間的方法。 當計時器時間發生時， [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法會以提醒形式向使用者顯示警示對話方塊。

第一次執行此範例時， [`TimePicker`](xref:Xamarin.Forms.TimePicker) 會將此視圖初始化為11am。 點擊會叫用 `TimePicker` 平臺時間選擇器。 平臺以非常不同的方式來實行時間選擇器，但該平臺的使用者可以熟悉每一種方法：

[![選取時間](timepicker-images/timepicker-open.png "選取時間")](timepicker-images/timepicker-open-large.png#lightbox "選取時間")

> [!TIP]
> 在 Android 上，您 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 可以在自訂轉譯器中覆寫方法，以自訂對話方塊 `CreateTimePickerDialog` 。 例如，這可讓您將其他按鈕新增至對話方塊。

選取時間之後，所選取的時間會顯示在中 [`TimePicker`](xref:Xamarin.Forms.TimePicker) ：

[![選取的時間](timepicker-images/timepicker-selected.png "選取的時間")](timepicker-images/timepicker-selected-large.png#lightbox "選取的時間")

假設 [`Switch`](xref:Xamarin.Forms.Switch) 已切換至 [開啟] 位置，應用程式會顯示警示對話方塊，提醒使用者在 [`Entry`](xref:Xamarin.Forms.Entry) 選取的時間發生時的文字：

[![計時器快顯視窗](timepicker-images/timer-test.png "計時器快顯視窗")](timepicker-images/timer-test-large.png#lightbox "計時器快顯視窗")

一旦顯示 [警示] 對話方塊， [`Switch`](xref:Xamarin.Forms.Switch) 就會切換到 [關閉] 位置。

## <a name="related-links"></a>相關連結

- [SetTimer 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [TimePicker API](xref:Xamarin.Forms.TimePicker)
