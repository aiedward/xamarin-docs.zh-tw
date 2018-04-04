---
title: 使用 日期選擇器
description: 允許使用者選取日期的 Xamarin.Forms 檢視
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/12/2018
ms.openlocfilehash: 0ab9d3c83b849e5ab5aac8bce9c581abd0312237
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="using-datepicker"></a>使用 日期選擇器

_允許使用者選取日期的 Xamarin.Forms 檢視_

Xamarin.Forms [ `DatePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/)叫用平台的日期選擇器控制項，並可讓使用者選取日期。 `DatePicker` 定義五個屬性：

- [`MinimumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/) 型別的[ `DateTime` ](https://developer.xamarin.com/api/type/System.DateTime/)，預設為 1900 年的第一天。
- [`MaximumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/) 型別的`DateTime`，一 2100 年的最後一天的預設值。
- [`Date`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/) 型別的`DateTime`、 將選取的日期，其預設值為[ `DateTime.Today` ](https://developer.xamarin.com/api/property/System.DateTime.Today/)。
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Format/) 型別的`string`、[標準](/dotnet/standard/base-types/standard-date-and-time-format-strings/)或[自訂](/dotnet/standard/base-types/custom-date-and-time-format-strings/).NET 格式字串，預設值為"D"，長時間日期模式。
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.TextColor/) 型別的[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)，用來顯示選取的日期會預設為色彩[ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/)。

`DatePicker`引發[ `DateSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.DatePicker.DateSelected/)事件，當使用者選取日期。

> [!WARNING]
> 設定時`MinimumDate`和`MaximumDate`，請確定`MinimumDate`一定會小於或等於`MaximumDate`。 否則，`DatePicker`會引發例外狀況。

就內部而言，`DatePicker`可確保`Date`之間`MinimumDate`和`MaximumDate`(含） 之間。 如果`MinimumDate`或`MaximumDate`設定以便`Date`之間，不是`DatePicker`會調整的值`Date`。

所有五個屬性都由[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)物件，這表示它們可以套用樣式，而且屬性可以做為目標的資料繫結。 `Date`屬性具有預設繫結模式[ `BindingMode.TwoWay` ](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/)，這表示它可以是資料繫結中使用的應用程式的目標[模型-檢視-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構。

## <a name="initializing-the-datetime-properties"></a>初始化日期時間屬性

您可以在程式碼，來初始化`MinimumDate`， `MaximumDate`，和`Date`屬性值的型別`DateTime`:

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

當`DateTime`值會指定在 XAML 中，XAML 剖析器會使用`DateTime.Parse`方法`CultureInfo.InvariantCulture`要轉換的字串引數`DateTime`值。 必須指定日期精確格式： 兩位數月份、 兩位數的天數，並以斜線分隔的四位數年份：

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

如果`BindingContext`屬性`DatePicker`設為執行個體包含屬性的型別 ViewModel`DateTime`名為`MinDate`， `MaxDate`，和`SelectedDate`（舉例來說），您可以具現化`DatePicker`以這種方式:

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

在此範例中，所有三個屬性會初始化為 ViewModel 中的對應屬性。 因為`Date`屬性已繫結模式的`TwoWay`、 ViewModel 會自動反映使用者選取任何新的日期。

如果`DatePicker`不包含繫結上其`Date`屬性，應用程式應該附加至處理常式`DateSelected`事件要了解當使用者選取新的日期。

## <a name="datepicker-and-layout"></a>日期選擇器和配置

您可使用未受限制的水平配置選項，例如`Center`， `Start`，或`End`與`DatePicker`:

```xaml
<DatePicker ··· 
            HorizontalOptions="Center" 
            ··· />
```

不過，建議您不要。 視設定而定`Format`所選取屬性的日期可能需要不同的顯示寬度。 例如，"D"格式字串會造成`DateTime`以長格式，以及 「 星期三，年 9 月 12，2018 」 顯示日期，需要較大的顯示寬度比"星期五，年 4，2018"。 根據平台，可能會造成這項差異`DateTime`檢視來變更在配置中，或被截斷的顯示寬度。

> [!TIP]
> 建議您最好使用預設`HorizontalOptions`設定`Fill`與`DatePicker`，且不使用在寬度為`Auto`當放置`DatePicker`中`Grid`儲存格。

## <a name="datepicker-in-an-application"></a>應用程式中的日期選擇器

[ **DaysBetweenDates** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker)範例包含兩個`DatePicker`其頁面上的檢視。 這些可以用來選取兩個日期，以及程式會計算這些日期之間的天數。 程式不會變更的設定`MinimumDate`和`MaximumDate`屬性，因此兩個日期必須介於 1900 年 2100年。

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

每個`DatePicker`指派`Format`完整日期格式的"D"屬性。 也請注意`endDatePicker`物件都有為目標的繫結其`MinimumDate`屬性。 繫結來源是所選`Date`屬性`startDatePicker`物件。 這可確保的結束日期稍後一律等於開始日期。 除了這兩個`DatePicker`物件`Switch`標示為 「 總共包含兩天 」。 

這兩個`DatePicker`檢視已附加至處理常式`DateSelected`事件，而`Switch`已處理常式附加至其`Toggled`事件。 這些事件處理常式的程式碼後置檔案中，並觸發新的計算的兩個日期之間的天數：

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

此範例第一次執行時，同時`DatePicker`檢視會初始化為今天的日期。 下列螢幕擷取畫面會顯示在 iOS、 Android 和通用 Windows 平台上執行的程式：

[![開始日期之間的幾天](datepicker-images/DaysBetweenDatesStart.png "開始日期之間的幾天")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "開始日期之間的幾天")

點選的`DatePicker`顯示叫用平台日期選擇器。 三個平台實作非常不同的方式，這個日期選擇器，而該平台的使用者所熟悉每一種方法：

[![選取日期之間的天數](datepicker-images/DaysBetweenDatesSelect.png "日期之間的天數選取")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "選取日期之間的天數")

選取兩個日期之後，應用程式會顯示這些日期之間的天數：

[![日期結果之間的天數](datepicker-images/DaysBetweenDatesResult.png "日期結果之間的天數")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "日期結果之間的天數")

## <a name="related-links"></a>相關的連結

- [DaysBetweenDates 範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker)
- [日期選擇器應用程式開發介面](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/)
