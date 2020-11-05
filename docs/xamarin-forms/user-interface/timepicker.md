---
title: Xamarin.Forms TimePicker
description: TimePicker 是 Xamarin.Forms 可讓使用者選取時間的視圖。 本文說明如何在應用程式中使用 TimePicker Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2f8452ea36d6fe376880b8882652c59fcb2ed23b
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371881"
---
# <a name="no-locxamarinforms-timepicker"></a>Xamarin.Forms TimePicker

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_Xamarin.Forms允許使用者選取時間的視圖。_

會叫 Xamarin.Forms [`TimePicker`](xref:Xamarin.Forms.TimePicker) 用平臺的時間選擇器控制項，並允許使用者選取時間。 `TimePicker` 會定義下列屬性：

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) 的型別 `TimeSpan` ，這是選取的時間，預設值為 `TimeSpan` 0。 此 `TimeSpan` 類型表示從午夜起算的持續時間。
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) 型 `string` 別為的 [標準](/dotnet/standard/base-types/standard-date-and-time-format-strings/) 或 [自訂](/dotnet/standard/base-types/custom-date-and-time-format-strings/) .net 格式化字串，預設為 "t"，簡短時間模式。
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) 型別 [`Color`](xref:Xamarin.Forms.Color) ，用來顯示所選時間的色彩，預設值為 [`Color.Default`](xref:Xamarin.Forms.Color.Default) 。
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) 的型別 [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) ，預設為 [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) 。
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) 的型別 `string` ，預設為 `null` 。
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) 的類型 `double` ，預設為-1.0。
- `CharacterSpacing`的類型 `double` 是文字字元之間的間距 `TimePicker` 。

所有這些屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以用樣式，而且屬性可以是資料系結的目標。 [`Time`](xref:Xamarin.Forms.TimePicker.Time)屬性具有的預設系結模式 [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) ，這表示它可以是應用程式中資料系結的目標，此應用程式會使用[模型視圖 ViewModel (MVVM) ](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構。

[`TimePicker`](xref:Xamarin.Forms.TimePicker)不包含指出新選取值的事件 [`Time`](xref:Xamarin.Forms.TimePicker.Time) 。 如果您需要收到此通知，您可以加入事件的處理常式 [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) 。

## <a name="initializing-the-time-property"></a>初始化時間屬性

在程式碼中，您可以將 [`Time`](xref:Xamarin.Forms.TimePicker.Time) 屬性初始化為類型的值 `TimeSpan` ：

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

當您 [`Time`](xref:Xamarin.Forms.TimePicker.Time) 在 XAML 中指定屬性時，此值會轉換為 `TimeSpan` 並經過驗證，以確保毫秒數大於或等於0，且時數小於24。 時間元件應以冒號分隔：

```xaml
<TimePicker Time="4:15:26" />
```

如果的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 屬性 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 是設定為 ViewModel 的實例，其中包含 `TimeSpan` 名為 (類型的屬性 `SelectedTime` ，例如) ，您可以具現化， `TimePicker` 如下所示：

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

在此範例中， [`Time`](xref:Xamarin.Forms.TimePicker.Time) 屬性會初始化為 `SelectedTime` ViewModel 中的屬性。 因為 `Time` 屬性具有的系結模式 [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) ，所以使用者選取的任何新時間都會自動傳播至 ViewModel。

如果 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 未在其屬性上包含系結 [`Time`](xref:Xamarin.Forms.TimePicker.Time) ，則應用程式應該將處理常式附加至 [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) 事件，以在使用者選取新的時間時收到通知。

如需設定字型屬性的相關資訊， [請參閱字型](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="timepicker-and-layout"></a>TimePicker 和版面配置

您可以使用不受限制的水準配置選項，例如 `Center` 、 `Start` 或 `End` [`TimePicker`](xref:Xamarin.Forms.TimePicker) 。

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

不過，這不是建議的做法。 視屬性的設定而定 [`Format`](xref:Xamarin.Forms.TimePicker.Format) ，選取的時間可能需要不同的顯示寬度。 例如，"T" 格式字串會讓 [`TimePicker`](xref:Xamarin.Forms.TimePicker) view 以長格式顯示時間，而 "4:15:26 am" 需要比短時間格式更大的顯示寬度， ( "t" ) "4:15 AM"。 視平臺而定，此差異可能會導致 `TimePicker` view 變更版面配置中的寬度，或是要截斷顯示。

> [!TIP]
> 最好是使用的預設 `HorizontalOptions` 設定 `Fill` [`TimePicker`](xref:Xamarin.Forms.TimePicker) ，而不是 `Auto` 在放置於資料格中時使用的寬度 `TimePicker` `Grid` 。

## <a name="timepicker-in-an-application"></a>應用程式中的 TimePicker

[**SetTimer**](/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)範例在 [`TimePicker`](xref:Xamarin.Forms.TimePicker) [`Entry`](xref:Xamarin.Forms.Entry) [`Switch`](xref:Xamarin.Forms.Switch) 其頁面上包含、和 views。 `TimePicker`可以用來選取時間，而當該時間發生時，會顯示警示對話方塊，以提醒使用者中的文字 `Entry` （前提 `Switch` 是已開啟）。 以下是 XAML 檔案：

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

[`Entry`](xref:Xamarin.Forms.Entry)可讓您輸入在選取的時間發生時將顯示的提醒文字。 [`TimePicker`](xref:Xamarin.Forms.TimePicker)會指派 [`Format`](xref:Xamarin.Forms.TimePicker.Format) "T" 的屬性給完整時間格式。 它有附加至事件的事件處理常式 [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) ，而且 [`Switch`](xref:Xamarin.Forms.Switch) 具有附加至其事件的處理常式 [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) 。 這些事件處理常式位於程式碼後端檔案中，並呼叫 `SetTriggerTime` 方法：

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

`SetTriggerTime`方法會根據 `DateTime.Today` 屬性值和 `TimeSpan` 從傳回的值來計算計時器時間 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 。 這是必要的，因為屬性會傳回 `DateTime.Today` `DateTime` 表示目前日期的，但在午夜的時間。 如果計時器時間今天已經過，則會假設為明天。

每秒的計時器刻度，執行 `OnTimerTick` 檢查是否 [`Switch`](xref:Xamarin.Forms.Switch) 為 on 以及目前時間是否大於或等於計時器時間的方法。 當計時器時間發生時， [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法會向使用者呈現警示對話方塊作為提醒。

第一次執行範例時， [`TimePicker`](xref:Xamarin.Forms.TimePicker) 會將視圖初始化為11am。 點擊即可叫用 `TimePicker` 平臺時間選擇器。 平臺會以非常不同的方式來執行時間選擇器，但該平臺的使用者很熟悉每個方法：

[![選取時間](timepicker-images/timepicker-open.png "選取時間")](timepicker-images/timepicker-open-large.png#lightbox "選取時間")

> [!TIP]
> 在 Android 上，您 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 可以藉由 `CreateTimePickerDialog` 在自訂轉譯器中覆寫方法來自訂對話方塊。 例如，這可讓您將其他按鈕新增至對話方塊。

選取時間之後，所選取的時間會顯示在 [`TimePicker`](xref:Xamarin.Forms.TimePicker) ：

[![選取的時間](timepicker-images/timepicker-selected.png "選取的時間")](timepicker-images/timepicker-selected-large.png#lightbox "選取的時間")

如果 [`Switch`](xref:Xamarin.Forms.Switch) 已切換至開啟位置，應用程式會顯示警示對話方塊，提醒使用者在 [`Entry`](xref:Xamarin.Forms.Entry) 選取的時間發生時的文字：

[![計時器快顯視窗](timepicker-images/timer-test.png "計時器快顯視窗")](timepicker-images/timer-test-large.png#lightbox "計時器快顯視窗")

一旦顯示警示對話方塊， [`Switch`](xref:Xamarin.Forms.Switch) 就會切換到 off 位置。

## <a name="related-links"></a>相關連結

- [SetTimer 範例](/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [TimePicker API](xref:Xamarin.Forms.TimePicker)