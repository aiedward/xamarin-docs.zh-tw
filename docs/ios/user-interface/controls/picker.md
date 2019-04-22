---
title: 在 Xamarin.iOS 中的選擇器控制項
description: 本文件說明如何設計和使用 Xamarin.iOS 應用程式中的選擇器控制項。 它討論如何實作選擇器，在程式碼，並在 iOS 設計工具中。
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/14/2018
ms.openlocfilehash: 946cba08e1e504962c093f67e336d72b654a3a41
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58869997"
---
# <a name="picker-control-in-xamarinios"></a>在 Xamarin.iOS 中的選擇器控制項

A [ `UIPickerView` ](xref:UIKit.UIPickerView)讓您能夠捲動滾輪型介面的個別元件，以挑選清單中的值。

選擇器經常用來選取日期和時間;Apple 提供 [`UIDatePicker`](xref:UIKit.UIDatePicker)
針對此用途的類別。

本文說明如何實作及使用`UIPickerView`和`UIDatePicker`控制項。

## <a name="uipickerview"></a>UIPickerView

### <a name="implementing-a-picker"></a>實作選擇器

實作以具現化新的選擇器`UIPickerView`:

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width, 
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
    )
);
```

### <a name="pickers-and-storyboards"></a>選擇器和分鏡腳本

若要建立在選擇器**iOS 設計工具**，拖曳**選擇器檢視**從**工具箱**至設計介面。

![拖曳至設計介面的選擇器檢視](picker-images/image1.png "拖曳至設計介面的選擇器 檢視")

### <a name="working-with-a-picker-control"></a>使用選擇器控制項

選擇器會使用_模型_資料進行互動：

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    var pickerModel = new PeopleModel(personLabel);
    personPicker.Model = pickerModel;
}
```

[ `UIPickerViewModel` ](xref:UIKit.UIPickerViewModel)基底類別會實作兩個介面， [`IUIPickerDataSource`](xref:UIKit.IUIPickerViewDataSource)
並[ `IUIPickerViewDelegate` ](xref:UIKit.IUIPickerViewDelegate)，其宣告指定選擇器的資料的各種方法，並處理互動的方式：

```csharp
public class PeopleModel : UIPickerViewModel
{
    public string[] names = new string[] {
            "Amy Burns",
            "Kevin Mullins",
            "Craig Dunn",
            "Joel Martinez",
            "Charles Petzold",
            "David Britch",
            "Mark McLemore",
            "Tom Opegenorth",
            "Joseph Hill",
            "Miguel De Icaza"
        };

    private UILabel personLabel;

    public PeopleModel(UILabel personLabel)
    {
        this.personLabel = personLabel;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 2;
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return names.Length;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        if (component == 0)
            return names[row];
        else
            return row.ToString();
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        personLabel.Text = $"This person is: {names[pickerView.SelectedRowInComponent(0)]},\n they are number {pickerView.SelectedRowInComponent(1)}";
    }

    public override nfloat GetComponentWidth(UIPickerView picker, nint component)
    {
        if (component == 0)
            return 240f;
        else
            return 40f;
    }

    public override nfloat GetRowHeight(UIPickerView picker, nint component)
    {
        return 40f;
    }
```

選擇器可以有多個資料行，或_元件_。 元件資料選擇器分割成多個區段，以便更容易且更特定的資料選取範圍：

![具有兩個元件的選擇器](picker-images/image3.png "具有兩個元件的選擇器")

若要選擇器中指定的元件數目，請使用 [`GetComponentCount`](xref:UIKit.UIPickerViewModel.GetComponentCount(UIKit.UIPickerView)) 
方法。

### <a name="customizing-a-pickers-appearance"></a>自訂選擇器的外觀

若要自訂的選擇器外觀，請使用 [`UIPickerView.UIPickerViewAppearance`](xref:UIKit.UIPickerView.UIPickerViewAppearance)
類別或覆寫[ `GetView` ](xref:UIKit.UIPickerViewModel.GetView(UIKit.UIPickerView,System.nint,System.nint,UIKit.UIView))並[ `GetRowHeight` ](xref:UIKit.UIPickerViewModel.GetRowHeight(UIKit.UIPickerView,System.nint))中的方法`UIPickerViewModel`。

## <a name="uidatepicker"></a>UIDatePicker

### <a name="implementing-a-date-picker"></a>實作日期選擇器

具現化日期選擇器來實作`UIDatePicker`:

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width,
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
     )
);
```

### <a name="date-pickers-and-storyboards"></a>日期選擇器和分鏡腳本

若要建立日期選擇器中的**iOS 設計工具**，拖曳**日期選擇器**從**工具箱**至設計介面。

![將日期選擇器拖曳至設計介面](picker-images/image2.png "拖曳至設計介面的日期選擇器")

### <a name="date-picker-properties"></a>日期選擇器內容

#### <a name="minimum-and-maximum-date"></a>最小值和最大日期

[`MinimumDate`](xref:UIKit.UIDatePicker.MinimumDate) 並[ `MaximumDate` ](xref:UIKit.UIDatePicker.MaximumDate)限制的可用日期選擇器中的日期範圍。 例如，下列程式碼會限制以六十年導致存在目前的日期選擇器：

```csharp
var calendar = new NSCalendar(NSCalendarType.Gregorian);
var currentDate = NSDate.Now;
var components = new NSDateComponents();
components.Year = -60;
NSDate minDate = calendar.DateByAddingComponents(components, NSDate.Now, NSCalendarOptions.None);
datePickerView.MinimumDate = minDate;
datePickerView.MaximumDate = NSDate.Now;
```

> [!TIP]
> 您可明確轉換`DateTime`至`NSDate`:
> ```csharp
> DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
> DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
> ```

#### <a name="minute-interval"></a>每分鐘的間隔

[ `MinuteInterval` ](xref:UIKit.UIDatePicker.MinuteInterval)屬性設定的選擇器會顯示分鐘的間隔：

```csharp
datePickerView.MinuteInterval = 10;
```

#### <a name="mode"></a>模式

日期選擇器支援四種[模式](xref:UIKit.UIDatePickerMode)，如下所述：

##### <a name="uidatepickermodetime"></a>UIDatePickerMode.Time

`UIDatePickerMode.Time` 會顯示以小時和分鐘的選取器和選擇性的 AM 或 PM 指定的時間：

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

![UIDatePickerMode.Time](picker-images/image8.png "UIDatePickerMode.Time")

##### <a name="uidatepickermodedate"></a>UIDatePickerMode.Date

`UIDatePickerMode.Date` 顯示的日期與月份、 日期和年份選擇器：

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

![UIDatePickerMode.Date](picker-images/image7.png "UIDatePickerMode.Date")

選取器的順序取決於將日期選擇器的地區設定，其預設使用系統地區設定。 上圖顯示中的選取器的配置`en_US`地區設定，但下列變更日期順序 |月 |年份：

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![日 |月 |年份](picker-images/image9.png "日 |月 |年份")

##### <a name="uidatepickermodedateandtime"></a>UIDatePickerMode.DateAndTime

`UIDatePickerMode.DateAndTime` 顯示簡短的日期的時間，以小時和分鐘的時間和選擇性的 AM 或 PM 指定 （取決於是否使用 12 或 24 小時制） 的檢視：

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

![UIDatePickerMode.DateAndTime](picker-images/image6.png "UIDatePickerMode.DateAndTime")

如同[ `UIDatePickerMode.Date` ](#uidatepickermodedate)，選取器的順序和 12 或 24 小時制的使用取決於地區設定的日期選擇器。

> [!TIP]
> 使用`Date`屬性來擷取值的日期選擇器模式`UIDatePickerMode.Time`， `UIDatePickerMode.Date`，或`UIDatePickerMode.DateAndTime`。 這個值會儲存為`NSDate`。

##### <a name="uidatepickermodecountdowntimer"></a>UIDatePickerMode.CountDownTimer

`UIDatePickerMode.CountDownTimer` 會顯示小時和分鐘值：

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

!["UIDatePickerMode.CountDownTimer"](picker-images/image5.png "UIDatePickerMode.CountDownTimer")

`CountDownDuration`屬性擷取值的日期選擇器中`UIDatePickerMode.CountDownTimer`模式。 例如，若要將倒數計時值新增至目前的日期：

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="nsdateformatter"></a>NSDateFormatter

若要格式化`NSDate`，使用[ `NSDateFormatter` ](xref:Foundation.NSDateFormatter)。

若要使用`NSDateFormatter`，呼叫其[ `ToString` ](xref:Foundation.NSDateFormatter.ToString(Foundation.NSDate))方法。 例如：

```csharp
var date = NSDate.Now;
var formatter = new NSDateFormatter();
formatter.DateStyle = NSDateFormatterStyle.Full;
formatter.TimeStyle = NSDateFormatterStyle.Full;
var formattedDate = formatter.ToString(d);
// Tuesday, August 14, 2018 at 11:20:42 PM Mountain Daylight Time
```

##### <a name="dateformat"></a>DateFormat

[ `DateFormat` ](xref:Foundation.NSDateFormatter.DateFormat)屬性 （字串）`NSDateFormatter`允許可自訂的日期格式規格：

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

##### <a name="timestyle"></a>TimeStyle

[ `TimeStyle` ](xref:Foundation.NSDateFormatter.TimeStyle)屬性 ( [ `NSDateFormatterStyle` ](xref:Foundation.NSDateFormatterStyle)的`NSDateFormatter`指定時間格式會根據預先決定的樣式：

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

各種`NSDateFormatterStyle`值會顯示時間，如下所示：

- `NSDateFormatterStyle.Full`：46： 下午 7:00 美加東部日光節約時間
- `NSDateFormatterStyle.Long`：7:47:00 PM EDT
- `NSDateFormatterStyle.Medium`：47： 下午 7:00
- `NSDateFormatterSytle.Short`：下午 7:47

##### <a name="datestyle"></a>DateStyle

[ `DateStyle` ](xref:Foundation.NSDateFormatter.DateStyle)屬性 ( `NSDateFormatterStyle`) 的`NSDateFormatter`指定日期格式會根據預先決定的樣式：

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

各種`NSDateFormatterStyle`值會顯示日期，如下所示：

- `NSDateFormatterStyle.Full`：星期三年 8 月 2日，2017 日下午 7:48
- `NSDateFormatterStyle.Long`：2017 年 8 月 2日日下午 7:49
- `NSDateFormatterStyle.Medium`：2017 年 8 月 2日日下午 7:49
- `NSDateFormatterStyle.Short`：8/2/17，下午 7 點 50

> [!NOTE]
> `DateFormat` 並`DateStyle` / `TimeStyle`提供不同的方式指定日期和時間格式。 最新設定屬性會決定日期格式器的輸出。

## <a name="related-links"></a>相關連結

- [PickerControl （範例）](https://developer.xamarin.com/samples/monotouch/PickerControl/)
