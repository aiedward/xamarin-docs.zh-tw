---
title: Xamarin.Forms DatePicker
description: DatePicker 是 Xamarin.Forms 檢視，可讓使用者選取日期。 這篇文章說明如何使用 Xamarin.Forms 應用程式中的日期選擇器。
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: b5d01aa451c9ab9be081b666e8c7ce51f58a3096
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172310"
---
# <a name="xamarinforms-datepicker"></a>Xamarin.Forms DatePicker

_Xamarin.Forms 檢視，可讓使用者選取日期。_

Xamarin.Forms [ `DatePicker` ](xref:Xamarin.Forms.DatePicker)叫用平台的日期選擇器控制項，並可讓使用者選取日期。 `DatePicker` 定義八個屬性：

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) 型別的[ `DateTime` ](xref:System.DateTime)，預設為 1900 年的第一天。
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) 型別的`DateTime`，2100年的當年的最後一天的預設值。
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) 型別的`DateTime`、 選取的日期會預設為值[ `DateTime.Today` ](xref:System.DateTime.Today)。
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) 型別的`string`，則[標準](/dotnet/standard/base-types/standard-date-and-time-format-strings/)或是[自訂](/dotnet/standard/base-types/custom-date-and-time-format-strings/).NET 格式字串，預設值為"D"，長時間的日期模式。
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor) 型別的[ `Color` ](xref:Xamarin.Forms.Color)，用來顯示選取的日期會預設為色彩[ `Color.Default` ](xref:Xamarin.Forms.Color.Default)。
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes) 型別的[ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes)，預設為[ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None)。
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily) 型別的`string`，預設為`null`。
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize) 型別的`double`，預設為-1.0。

`DatePicker`引發[ `DateSelected` ](xref:Xamarin.Forms.DatePicker.DateSelected)事件，當使用者選取日期。

> [!WARNING]
> 設定時`MinimumDate`並`MaximumDate`，請確定`MinimumDate`一定會小於或等於`MaximumDate`。 否則，`DatePicker`會引發例外狀況。

就內部而言，`DatePicker`可確保`Date`之間`MinimumDate`和`MaximumDate`（包含頭尾)。 如果`MinimumDate`或是`MaximumDate`設定，讓`Date`之間，不是`DatePicker`會調整的值`Date`。

所有的八個屬性都會受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示它們可自訂樣式，而且屬性可以是資料繫結的目標。 `Date`屬性具有預設繫結模式[ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)，這表示它可以是資料繫結中使用的應用程式的目標[Model View ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構。

## <a name="initializing-the-datetime-properties"></a>初始化的日期時間屬性

您可以在程式碼，來初始化`MinimumDate`， `MaximumDate`，並`Date`屬性值的型別`DateTime`:

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

當`DateTime`值會指定 XAML，XAML 剖析器會使用`DateTime.Parse`方法`CultureInfo.InvariantCulture`引數，以將字串轉換為`DateTime`值。 必須指定日期，以精確的格式： 兩位數月份、 兩位數的天數，並以斜線分隔的四位數年份：

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

如果`BindingContext`的屬性`DatePicker`設定為執行個體的 ViewModel，包含屬性的型別`DateTime`名為`MinDate`， `MaxDate`，和`SelectedDate`（舉例來說），您可以具現化`DatePicker`如下所示:

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

在此範例中，所有三個屬性會初始化為 ViewModel 中對應的屬性。 因為`Date`屬性已繫結模式的`TwoWay`、 ViewModel 會自動反映使用者選取的任何新日期。

如果`DatePicker`不包含繫結在其`Date`屬性，應用程式應該附加至處理常式`DateSelected`事件會通知當使用者選取新的日期。

如需設定字型屬性的詳細資訊，請參閱[字型](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="datepicker-and-layout"></a>DatePicker 和版面配置

您可使用不受限制的水平版面配置選項，例如`Center`， `Start`，或`End`使用`DatePicker`:

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

不過，這不建議。 視設定而定`Format`所選取的屬性的日期可能會需要不同的顯示寬度。 例如，"D"格式字串會導致`DateTime`來顯示日期時間的格式及"Wednesday，年 9 月 12，2018 」 需要更高的顯示寬度比"Friday，4，2018 年 」。 根據平台，這項差異可能會導致`DateTime`檢視 來變更在配置中，或被截斷的顯示寬度。

> [!TIP]
> 最好是使用預設`HorizontalOptions`設定`Fill`具有`DatePicker`，且不使用的寬度`Auto`當放置`DatePicker`中`Grid`資料格。

## <a name="datepicker-in-an-application"></a>應用程式中的 DatePicker

[ **DaysBetweenDates** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker)範例包含兩個`DatePicker`其頁面上的檢視。 這些可以用來選取兩個日期，並計劃會計算這些日期之間的天數。 程式不會變更的設定`MinimumDate`和`MaximumDate`屬性，因此兩個日期必須介於 1900年和 2100年。

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

每個`DatePicker`指派`Format`"D"屬性，以完整日期格式。 也請注意`endDatePicker`擁有繫結目標物件，其`MinimumDate`屬性。 繫結來源是選取`Date`屬性`startDatePicker`物件。 這可確保的結束日期一律之後或等於開始日期。 除了這兩個`DatePicker`物件，`Switch`會標示為 「 總共包含這兩天 」。

這兩個`DatePicker`檢視會有附加至處理常式`DateSelected`事件，而`Switch`有處理常式附加至其`Toggled`事件。 這些事件處理常式的程式碼後置檔案中，並觸發新的計算兩個日期之間的天數：

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

此範例第一次執行時，同時`DatePicker`檢視會初始化為今天的日期。 下列螢幕擷取畫面顯示在 iOS、 Android 和通用 Windows 平台上執行的程式：

[![開始日期之間的幾天](datepicker-images/DaysBetweenDatesStart.png "開始的日期之間的幾天")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "開始日期之間的幾天")

點選其中一種`DatePicker`顯示叫用平台的日期選擇器。 平台實作此日期選擇器以非常不同的方式，但是每一種方法是該平台的使用者熟悉：

[![選取的日期之間的天數](datepicker-images/DaysBetweenDatesSelect.png "天的日期之間選取")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "選取日期之間的天數")

> [!TIP]
> 在 Android 上， `DatePicker`  對話方塊可以藉由覆寫自訂`CreateDatePickerDialog`中自訂轉譯器的方法。 這可讓，比方說，可以加入至對話方塊中的其他按鈕。

選取兩個日期之後，應用程式會顯示這些日期之間的天數：

[![日期結果之間的天數](datepicker-images/DaysBetweenDatesResult.png "日期結果之間的天數")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "日期結果之間的天數")

## <a name="related-links"></a>相關連結

- [DaysBetweenDates 範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker)
- [DatePicker API](xref:Xamarin.Forms.DatePicker)
