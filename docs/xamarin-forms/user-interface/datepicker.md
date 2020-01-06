---
title: Xamarin. Forms DatePicker
description: DatePicker 是一個 [Xamarin] 表單檢視，可讓使用者選取日期。 本文說明如何在 Xamarin. Forms 應用程式中使用 DatePicker。
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: 9bc6d47e4f935e84ae675eefeabac023920a344a
ms.sourcegitcommit: 191f1f3b13a14e2afadcb95126c5f653722f126f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545600"
---
# <a name="xamarinforms-datepicker"></a>Xamarin. Forms DatePicker

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)

_可讓使用者選取日期的 [Xamarin] 表單檢視。_

[Xamarin] [`DatePicker`](xref:Xamarin.Forms.DatePicker)會叫用平臺的日期選擇器控制項，並允許使用者選取日期。 `DatePicker` 定義八個屬性：

- [`DateTime`](xref:System.DateTime)類型的[`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) ，預設為1900年的第一天。
- `DateTime`類型的[`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) ，預設為2100年的最後一天。
- [`Date`](xref:Xamarin.Forms.DatePicker.Date)類型 `DateTime`，這是選取的日期，預設為[`DateTime.Today`](xref:System.DateTime.Today)的值。
- `string`類型的[`Format`](xref:Xamarin.Forms.DatePicker.Format) ，這是[標準](/dotnet/standard/base-types/standard-date-and-time-format-strings/)或[自訂](/dotnet/standard/base-types/custom-date-and-time-format-strings/)的 .net 格式字串，預設為 "D"，完整日期模式。
- [`Color`](xref:Xamarin.Forms.Color)類型的[`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor) ，用來顯示所選日期的色彩，預設為[`Color.Default`](xref:Xamarin.Forms.Color.Default)。
- [`FontAttributes`](xref:Xamarin.Forms.FontAttributes)類型的[`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes) ，預設為[`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None)。
- `string`類型的[`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily) ，預設為 `null`。
- `double`類型的[`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize) ，預設為-1.0。
- `CharacterSpacing`，屬於 `double`類型，這是 `DatePicker` 文字字元之間的間距。

當使用者選取日期時，`DatePicker` 會引發[`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected)事件。

> [!WARNING]
> 設定 `MinimumDate` 並 `MaximumDate`時，請確定 `MinimumDate` 一律小於或等於 `MaximumDate`。 否則，`DatePicker` 將會引發例外狀況。

就內部而言，`DatePicker` 可確保 `Date` 介於 `MinimumDate` 和 `MaximumDate`（含）之間。 如果 `MinimumDate` 或 `MaximumDate` 設定為 `Date` 不在兩者之間，`DatePicker` 會調整 `Date`的值。

所有八個屬性都是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以樣式化，而屬性可以是資料系結的目標。 `Date` 屬性具有[`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)的預設系結模式，這表示它可以是使用[模型 ViewModel （MVVM）](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構之應用程式中的資料系結目標。

## <a name="initializing-the-datetime-properties"></a>初始化 DateTime 屬性

在程式碼中，您可以將 `MinimumDate`、`MaximumDate`和 `Date` 屬性初始化為 `DateTime`類型的值：

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

當 XAML 中指定了 `DateTime` 值時，XAML 剖析器會使用具有 `CultureInfo.InvariantCulture` 引數的 `DateTime.Parse` 方法，將字串轉換成 `DateTime` 值。 日期必須以精確的格式指定：兩位數的月份、兩位數的天數，以及四位數年份，並以斜線分隔：

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

如果 `DatePicker` 的 `BindingContext` 屬性設定為 viewmodel 的實例，其中包含名稱為 `MinDate`、`MaxDate`和 `SelectedDate` 的類型 `DateTime` 屬性（例如），您可以將 `DatePicker` 具現化，如下所示：

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

在此範例中，所有三個屬性都會初始化為 viewmodel 中的對應屬性。 因為 `Date` 屬性具有 `TwoWay`的系結模式，所以使用者選取的任何新日期都會自動反映在 viewmodel 中。

如果 `DatePicker` 不包含其 `Date` 屬性的系結，應用程式應將處理常式附加至 `DateSelected` 事件，以便在使用者選取新日期時收到通知。

如需設定字型屬性的相關資訊，請參閱[Fonts](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="datepicker-and-layout"></a>DatePicker 和版面配置

您可以使用不受限制的水準配置選項，例如 `Center`、`Start`或 `End` 與 `DatePicker`：

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

不過，不建議您這樣做。 根據 `Format` 屬性的設定，選取的日期可能會需要不同的顯示寬度。 例如，"D" 格式字串會導致 `DateTime` 以長格式顯示日期，而「2018年9月12日星期三」則需要比「星期五，5月4日，2018」更高的顯示寬度。 視平臺而定，這項差異可能會導致 `DateTime` 視圖變更版面配置中的寬度，或顯示要被截斷。

> [!TIP]
> 最好是使用 `Fill` 的預設 `HorizontalOptions` 設定搭配 `DatePicker`，而不是在 `DatePicker` 資料格中放置 `Grid` 時，使用 `Auto` 的寬度。

## <a name="datepicker-in-an-application"></a>應用程式中的 DatePicker

[**DaysBetweenDates**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)範例在其頁面上包含兩個 `DatePicker` views。 這些可以用來選取兩個日期，而程式會計算這些日期之間的天數。 此程式不會變更 `MinimumDate` 和 `MaximumDate` 屬性的設定，因此這兩個日期必須介於1900到2100之間。

以下是 XAML 檔案：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DaysBetweenDates"
             x:Class="DaysBetweenDates.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <StackLayout Margin="10">
        <Label Text="Days Between Dates"
               Style="{DynamicResource TitleStyle}"
               Margin="0, 20"
               HorizontalTextAlignment="Center" />

        <Label Text="Start Date:" />

        <DatePicker x:Name="startDatePicker"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <Label Text="End Date:" />

        <DatePicker x:Name="endDatePicker"
                    MinimumDate="{Binding Source={x:Reference startDatePicker},
                                          Path=Date}"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <StackLayout Orientation="Horizontal"
                     Margin="0, 0, 0, 30">
            <Label Text="Include both days in total: "
                   VerticalOptions="Center" />
            <Switch x:Name="includeSwitch"
                    Toggled="OnSwitchToggled" />
        </StackLayout>

        <Label x:Name="resultLabel"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

每個 `DatePicker` 都會指派 "D" 的 `Format` 屬性，以取得長日期格式。 同時也請注意，`endDatePicker` 物件具有以其 `MinimumDate` 屬性為目標的系結。 系結來源是 `startDatePicker` 物件的已選取 `Date` 屬性。 這可確保結束日期一律晚于或等於開始日期。 除了兩個 `DatePicker` 物件外，`Switch` 會標示為「包含兩天的總計」。

這兩個 `DatePicker` 視圖具有附加至 `DateSelected` 事件的處理常式，而且 `Switch` 具有附加至其 `Toggled` 事件的處理常式。 這些事件處理常式位於程式碼後置檔案中，並觸發兩個日期之間的新天數計算：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }

    void OnDateSelected(object sender, DateChangedEventArgs args)
    {
        Recalculate();
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        Recalculate();
    }

    void Recalculate()
    {
        TimeSpan timeSpan = endDatePicker.Date - startDatePicker.Date +
            (includeSwitch.IsToggled ? TimeSpan.FromDays(1) : TimeSpan.Zero);

        resultLabel.Text = String.Format("{0} day{1} between dates",
                                            timeSpan.Days, timeSpan.Days == 1 ? "" : "s");
    }
}
```

第一次執行此範例時，兩個 `DatePicker` 視圖都會初始化為今天的日期。 下列螢幕擷取畫面顯示在 iOS 和 Android 上執行的程式：

[![開始日期之間的天數](datepicker-images/DaysBetweenDatesStart.png "開始日期之間的天數")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "開始日期之間的天數")

點擊其中一個 `DatePicker` 顯示會叫用平臺日期選擇器。 平臺以非常不同的方式來實行此日期選擇器，但該平臺的使用者也熟悉每一種方法：

[![選取日期之間的天數](datepicker-images/DaysBetweenDatesSelect.png "選取日期之間的天數")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "選取日期之間的天數")

> [!TIP]
> 在 Android 上，您可以在自訂轉譯器中覆寫 `CreateDatePickerDialog` 方法，以自訂 [`DatePicker`] 對話方塊。 例如，這可讓您將其他按鈕新增至對話方塊。

選取兩個日期之後，應用程式會顯示這些日期之間的天數：

[![日期結果之間的天數](datepicker-images/DaysBetweenDatesResult.png "日期結果之間的天數")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "日期結果之間的天數")

## <a name="related-links"></a>相關連結

- [DaysBetweenDates 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)
- [DatePicker API](xref:Xamarin.Forms.DatePicker)
