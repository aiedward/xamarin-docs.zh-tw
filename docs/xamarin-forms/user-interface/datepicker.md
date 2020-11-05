---
title: Xamarin.Forms DatePicker
description: DatePicker 是 Xamarin.Forms 可讓使用者選取日期的視圖。 本文說明如何在應用程式中使用 DatePicker Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f7cb79b5b90d586980f2e74cd6d2f04b82a8fdf3
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373688"
---
# <a name="no-locxamarinforms-datepicker"></a>Xamarin.Forms DatePicker

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)

_Xamarin.Forms可讓使用者選取日期的視圖。_

會叫 Xamarin.Forms [`DatePicker`](xref:Xamarin.Forms.DatePicker) 用平臺的日期選擇器控制項，並讓使用者選取日期。 `DatePicker` 定義八個屬性：

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) 型別的 [`DateTime`](xref:System.DateTime) ，預設為1900年的第一天。
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) 型別的 `DateTime` ，預設為2100年的最後一天。
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) 的類型 `DateTime` ，這是選取的日期，預設值為 [`DateTime.Today`](xref:System.DateTime.Today) 。
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) 型別為的 `string` [標準](/dotnet/standard/base-types/standard-date-and-time-format-strings/) 或 [自訂](/dotnet/standard/base-types/custom-date-and-time-format-strings/) .net 格式化字串，預設為 "D"，即完整日期模式。
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor) 型別 [`Color`](xref:Xamarin.Forms.Color) ，用來顯示選取日期的色彩，預設值為 [`Color.Default`](xref:Xamarin.Forms.Color.Default) 。
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes) 的型別 [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) ，預設為 [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) 。
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily) 的型別 `string` ，預設為 `null` 。
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize) 的類型 `double` ，預設為-1.0。
- `CharacterSpacing`的類型 `double` 是文字字元之間的間距 `DatePicker` 。

`DatePicker` [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) 當使用者選取日期時，會引發事件。

> [!WARNING]
> 設定 `MinimumDate` 和時 `MaximumDate` ，請確定 `MinimumDate` 永遠小於或等於 `MaximumDate` 。 否則， `DatePicker` 將會引發例外狀況。

在內部， `DatePicker` 可確保 `Date` 介於 `MinimumDate` 和 `MaximumDate` （含）之間。 如果 `MinimumDate` `MaximumDate` 設定或設定 `Date` 為不介於兩者之間，則 `DatePicker` 會調整的值 `Date` 。

所有八個屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以使用樣式，而且屬性可以是資料系結的目標。 `Date`屬性具有的預設系結模式 [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) ，這表示它可以是應用程式中資料系結的目標，此應用程式會使用[模型視圖 ViewModel (MVVM) ](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構。

## <a name="initializing-the-datetime-properties"></a>初始化 DateTime 屬性

在程式碼中，您可以將 `MinimumDate` 、 `MaximumDate` 和 `Date` 屬性初始化為類型的值 `DateTime` ：

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

當您 `DateTime` 在 XAML 中指定值時，xaml 剖析器會使用 `DateTime.Parse` 具有引數的方法， `CultureInfo.InvariantCulture` 將字串轉換成 `DateTime` 值。 日期必須以精確的格式指定：兩位數的月份、兩位數的天數，以及以斜線分隔的四位數年份：

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

如果的 `BindingContext` 屬性 `DatePicker` 設定為 viewmodel 的實例，其中包含名為、和的型別屬性（property）， `DateTime` `MinDate` `MaxDate` 而 `SelectedDate` (例如) ，您可以具現化， `DatePicker` 如下所示：

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

在此範例中，這三個屬性都會初始化為 viewmodel 中的對應屬性。 因為 `Date` 屬性具有的系結模式 `TwoWay` ，所以使用者選取的任何新日期都會自動反映在 viewmodel 中。

如果 `DatePicker` 未在其屬性上包含系結 `Date` ，則應用程式應該將處理常式附加至 `DateSelected` 事件，以在使用者選取新日期時收到通知。

如需設定字型屬性的相關資訊， [請參閱字型](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="datepicker-and-layout"></a>DatePicker 和版面配置

您可以使用不受限制的水準配置選項，例如 `Center` 、 `Start` 或 `End` `DatePicker` 。

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

不過，這不是建議的做法。 根據屬性的設定而定 `Format` ，選取的日期可能需要不同的顯示寬度。 例如，"D" 格式字串會導致 `DateTime` 以長格式顯示日期，而「星期三，2018年9月12日」的顯示寬度必須大於「星期五，5月4日2018」。 視平臺而定，此差異可能會導致 `DateTime` view 變更版面配置中的寬度，或是要截斷顯示。

> [!TIP]
> 最好是使用的預設 `HorizontalOptions` 設定 `Fill` `DatePicker` ，而不是 `Auto` 在放置於資料格中時使用的寬度 `DatePicker` `Grid` 。

## <a name="datepicker-in-an-application"></a>應用程式中的 DatePicker

[**DaysBetweenDates**](/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)範例在 `DatePicker` 其頁面上包含兩個視圖。 這些可以用來選取兩個日期，而程式會計算這些日期之間的天數。 此程式不會變更 `MinimumDate` 和屬性的設定 `MaximumDate` ，因此兩個日期必須介於1900到2100之間。

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

每個 `DatePicker` 都會 `Format` 針對長日期格式指派 "D" 屬性。 另外也請注意，該 `endDatePicker` 物件具有以其屬性為目標的系結 `MinimumDate` 。 系結來源是物件的選取 `Date` 屬性 `startDatePicker` 。 這可確保結束日期一律晚于或等於開始日期。 除了這兩個 `DatePicker` 物件，a `Switch` 會標示為「包含總天數」。

這兩個 `DatePicker` 視圖具有附加至事件的處理常式 `DateSelected` ，而且 `Switch` 有一個處理常式附加到其 `Toggled` 事件。 這些事件處理常式位於程式碼後端檔案中，並會觸發兩個日期之間的新計算天數：

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

第一次執行範例時，這兩個 `DatePicker` 視圖都會初始化為今天的日期。 下列螢幕擷取畫面顯示在 iOS 和 Android 上執行的程式：

[![開始日期之間的天數](datepicker-images/DaysBetweenDatesStart.png "開始日期之間的天數")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "開始日期之間的天數")

點擊任一顯示器會叫用 `DatePicker` 平臺日期選擇器。 平臺會以非常不同的方式來執行此日期選擇器，但該平臺的使用者很熟悉每個方法：

[![日期之間的天數 Select](datepicker-images/DaysBetweenDatesSelect.png "日期之間的天數 Select")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "日期之間的天數 Select")

> [!TIP]
> 在 Android 上，您 `DatePicker` 可以藉由 `CreateDatePickerDialog` 在自訂轉譯器中覆寫方法來自訂對話方塊。 例如，這可讓您將其他按鈕新增至對話方塊。

選取兩個日期之後，應用程式會顯示這些日期之間的天數：

[![日期結果之間的天數](datepicker-images/DaysBetweenDatesResult.png "日期結果之間的天數")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "日期結果之間的天數")

## <a name="related-links"></a>相關連結

- [DaysBetweenDates 範例](/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)
- [DatePicker API](xref:Xamarin.Forms.DatePicker)