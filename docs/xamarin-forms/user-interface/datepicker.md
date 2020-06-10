---
title： " Xamarin.Forms DatePicker" 描述： "DatePicker 是 Xamarin.Forms 允許使用者選取日期的視圖。 本文說明如何在應用程式中使用 DatePicker Xamarin.Forms 。」
assetid： 68E8EF8A-42E7-4939-8ABE-64D060E609D9 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：06/04/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-datepicker"></a>Xamarin.FormsDatePicker

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)

_Xamarin.Forms允許使用者選取日期的視圖。_

會叫 Xamarin.Forms [`DatePicker`](xref:Xamarin.Forms.DatePicker) 用平臺的日期選擇器控制項，並允許使用者選取日期。 `DatePicker`定義八個屬性：

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate)類型的 [`DateTime`](xref:System.DateTime) ，預設為1900年的第一天。
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate)類型的 `DateTime` ，預設為2100年的最後一天。
- [`Date`](xref:Xamarin.Forms.DatePicker.Date)屬於類型的 `DateTime` ，這是選取的日期，預設值為 [`DateTime.Today`](xref:System.DateTime.Today) 。
- [`Format`](xref:Xamarin.Forms.DatePicker.Format)屬於類型的 `string` [標準](/dotnet/standard/base-types/standard-date-and-time-format-strings/)或[自訂](/dotnet/standard/base-types/custom-date-and-time-format-strings/).net 格式字串，預設為 "D"，完整日期模式。
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor)類型的 [`Color`](xref:Xamarin.Forms.Color) ，用來顯示所選日期的色彩，預設為 [`Color.Default`](xref:Xamarin.Forms.Color.Default) 。
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes)類型的 [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) ，預設為 [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) 。
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily)類型的 `string` ，預設為 `null` 。
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize)類型的 `double` ，預設為-1.0。
- `CharacterSpacing`，屬於類型 `double` ，這是文字字元之間的間距 `DatePicker` 。

`DatePicker` [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) 當使用者選取日期時，會引發事件。

> [!WARNING]
> 設定 `MinimumDate` 和時 `MaximumDate` ，請確定 `MinimumDate` 永遠小於或等於 `MaximumDate` 。 否則， `DatePicker` 會引發例外狀況。

`DatePicker`就內部而言， `Date` 會確保介於 `MinimumDate` 和 `MaximumDate` （含）之間。 如果 `MinimumDate` `MaximumDate` 設定了或，而 `Date` 不是在兩者之間， `DatePicker` 將會調整的值 `Date` 。

所有八個屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以樣式化，而屬性可以是資料系結的目標。 `Date`屬性具有的預設系結模式 [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) ，這表示它可以是使用[模型 ViewModel （MVVM）](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構之應用程式中的資料系結目標。

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

`DateTime`在 xaml 中指定值時，xaml 剖析器會使用 `DateTime.Parse` 具有引數的方法，將 `CultureInfo.InvariantCulture` 字串轉換成 `DateTime` 值。 日期必須以精確的格式指定：兩位數的月份、兩位數的天數，以及四位數年份，並以斜線分隔：

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

如果的 `BindingContext` 屬性 `DatePicker` 設定為 viewmodel 的實例，其中包含 `DateTime` 名為、和的型別屬性 `MinDate` `MaxDate` `SelectedDate` （例如），您可以具現化， `DatePicker` 如下所示：

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

在此範例中，所有三個屬性都會初始化為 viewmodel 中的對應屬性。 因為 `Date` 屬性具有的系結模式 `TwoWay` ，所以使用者選取的任何新日期都會自動反映在 viewmodel 中。

如果未 `DatePicker` 在其屬性上包含系結 `Date` ，應用程式應將處理常式附加至 `DateSelected` 事件，以便在使用者選取新日期時收到通知。

如需設定字型屬性的相關資訊，請參閱[Fonts](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="datepicker-and-layout"></a>DatePicker 和版面配置

您可以使用不受限制的水準配置選項，例如 `Center` 、 `Start` 或， `End` 搭配 `DatePicker` ：

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

不過，不建議您這樣做。 視屬性的設定而定 `Format` ，選取的日期可能會需要不同的顯示寬度。 例如，"D" 格式字串會導致 `DateTime` 以長格式顯示日期，而「2018年9月12日星期三」則需要比「星期五，5月4日，2018」更高的顯示寬度。 視平臺而定，這項差異可能會導致 `DateTime` 視圖變更版面配置中的寬度，或要截斷顯示。

> [!TIP]
> 最好是使用的預設值搭配 `HorizontalOptions` `Fill` `DatePicker` ，而不是在 `Auto` 放入資料格時使用的寬度 `DatePicker` `Grid` 。

## <a name="datepicker-in-an-application"></a>應用程式中的 DatePicker

[**DaysBetweenDates**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)範例 `DatePicker` 在其頁面上包含兩個視圖。 這些可以用來選取兩個日期，而程式會計算這些日期之間的天數。 此程式不會變更 `MinimumDate` 和屬性的設定 `MaximumDate` ，因此這兩個日期必須介於1900和2100之間。

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

每個 `DatePicker` 都會 `Format` 針對長日期格式指派 "D" 的屬性。 同時也請注意， `endDatePicker` 物件具有以其屬性為目標的系結 `MinimumDate` 。 系結來源是物件的已選取 `Date` 屬性 `startDatePicker` 。 這可確保結束日期一律晚于或等於開始日期。 除了兩個物件外 `DatePicker` ， `Switch` 會標示為「包含兩天的總計」。

這兩個 `DatePicker` 視圖具有附加至事件的處理常式 `DateSelected` ，而且 `Switch` 具有附加至其事件的處理常式 `Toggled` 。 這些事件處理常式位於程式碼後置檔案中，並觸發兩個日期之間的新天數計算：

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

點擊其中一個顯示會叫用 `DatePicker` 平臺日期選擇器。 平臺以非常不同的方式來實行此日期選擇器，但該平臺的使用者也熟悉每一種方法：

[![選取日期之間的天數](datepicker-images/DaysBetweenDatesSelect.png "選取日期之間的天數")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "選取日期之間的天數")

> [!TIP]
> 在 Android 上，您 `DatePicker` 可以在自訂轉譯器中覆寫方法，以自訂對話方塊 `CreateDatePickerDialog` 。 例如，這可讓您將其他按鈕新增至對話方塊。

選取兩個日期之後，應用程式會顯示這些日期之間的天數：

[![日期結果之間的天數](datepicker-images/DaysBetweenDatesResult.png "日期結果之間的天數")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "日期結果之間的天數")

## <a name="related-links"></a>相關連結

- [DaysBetweenDates 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)
- [DatePicker API](xref:Xamarin.Forms.DatePicker)
