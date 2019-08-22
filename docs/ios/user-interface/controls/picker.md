---
title: Xamarin 中的選擇器控制項
description: 本檔說明如何在 Xamarin iOS 應用程式中設計和使用選擇器控制項。 它討論如何在程式碼和 iOS 設計工具中執行選擇器。
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/14/2018
ms.openlocfilehash: c31f68245a459a99604633d23426c4fa1fe5e72c
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887390"
---
# <a name="picker-control-in-xamarinios"></a>Xamarin 中的選擇器控制項

[`UIPickerView`](xref:UIKit.UIPickerView)可讓您藉由滾動滾輪類介面的個別元件來挑選清單中的值。

選取器經常用來選取日期和時間;Apple 提供[`UIDatePicker`](xref:UIKit.UIDatePicker)
適用于此用途的類別。

本文說明如何執行`UIPickerView` `UIDatePicker`和控制項。

## <a name="uipickerview"></a>UIPickerView

### <a name="implementing-a-picker"></a>執行選擇器

藉由具現化新`UIPickerView`的來執行選擇器:

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

若要在**IOS 設計**工具中建立選擇器, 請將**選擇器視圖**從 [**工具箱**] 拖曳至設計介面。

![將選擇器視圖拖曳至設計介面](picker-images/image1.png "將選擇器視圖拖曳至設計介面")

### <a name="working-with-a-picker-control"></a>使用選擇器控制項

選擇器會使用_模型_來與資料互動:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    var pickerModel = new PeopleModel(personLabel);
    personPicker.Model = pickerModel;
}
```

[`UIPickerViewModel`](xref:UIKit.UIPickerViewModel)基底類別會執行兩個介面,[`IUIPickerDataSource`](xref:UIKit.IUIPickerViewDataSource)
和[`IUIPickerViewDelegate`](xref:UIKit.IUIPickerViewDelegate), 宣告指定選擇器資料的各種方法, 以及它處理互動的方式:

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

選擇器可以有多個資料行或_元件_。 元件會將選擇器分割成多個區段, 讓您可以更輕鬆且更明確地選取資料:

![具有兩個元件的選擇器](picker-images/image3.png "具有兩個元件的選擇器")

若要指定選擇器中的元件數目, 請使用[`GetComponentCount`](xref:UIKit.UIPickerViewModel.GetComponentCount(UIKit.UIPickerView)) 
方法。

### <a name="customizing-a-pickers-appearance"></a>自訂選擇器的外觀

若要自訂選擇器的外觀, 請使用[`UIPickerView.UIPickerViewAppearance`](xref:UIKit.UIPickerView.UIPickerViewAppearance)
類別或覆寫[`GetView`](xref:UIKit.UIPickerViewModel.GetView(UIKit.UIPickerView,System.nint,System.nint,UIKit.UIView))中[`GetRowHeight`](xref:UIKit.UIPickerViewModel.GetRowHeight(UIKit.UIPickerView,System.nint)) `UIPickerViewModel`的和方法。

## <a name="uidatepicker"></a>UIDatePicker

### <a name="implementing-a-date-picker"></a>執行日期選擇器

藉由具現化`UIDatePicker`來執行日期選擇器:

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

若要在**IOS 設計**工具中建立日期選擇器, 請將**日期選擇器**從 [**工具箱**] 拖曳至設計介面。

![將日期選擇器拖曳至設計介面](picker-images/image2.png "將日期選擇器拖曳至設計介面")

### <a name="date-picker-properties"></a>日期選擇器屬性

#### <a name="minimum-and-maximum-date"></a>最小和最大日期

[`MinimumDate`](xref:UIKit.UIDatePicker.MinimumDate)和[`MaximumDate`](xref:UIKit.UIDatePicker.MaximumDate)會限制日期選擇器中可用的日期範圍。 例如, 下列程式碼會將日期選擇器限制為目前為止的60年:

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
> 您可以明確地將轉換`DateTime` `NSDate`成:
>
> ```csharp
> DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
> DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
> ```

#### <a name="minute-interval"></a>分鐘間隔

[`MinuteInterval`](xref:UIKit.UIDatePicker.MinuteInterval)屬性會設定選擇器顯示分鐘數的間隔:

```csharp
datePickerView.MinuteInterval = 10;
```

#### <a name="mode"></a>模式

日期選擇器支援四種[模式](xref:UIKit.UIDatePickerMode), 如下所述:

##### <a name="uidatepickermodetime"></a>UIDatePickerMode.Time

`UIDatePickerMode.Time`以小時和分鐘的選擇器顯示時間, 以及選擇性的 AM 或 PM 指定:

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

![UIDatePickerMode.Time](picker-images/image8.png "UIDatePickerMode.Time")

##### <a name="uidatepickermodedate"></a>UIDatePickerMode.Date

`UIDatePickerMode.Date`以 [月]、[日] 和 [年] 選取器顯示日期:

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

![UIDatePickerMode.Date](picker-images/image7.png "UIDatePickerMode.Date")

選取器的順序取決於日期選擇器的地區設定, 其預設會使用系統地區設定。 上圖顯示`en_US`地區設定中的選取器版面配置, 但下列各項會變更訂單的日期 |月 |歷年

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![Day |月 |年初](picker-images/image9.png "日 |月 |年")

##### <a name="uidatepickermodedateandtime"></a>UIDatePickerMode.DateAndTime

`UIDatePickerMode.DateAndTime`顯示日期、時間 (以小時和分鐘為單位), 以及選擇性的 AM 或 PM 指定 (視使用12或24小時制而定):

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

![UIDatePickerMode.DateAndTime](picker-images/image6.png "UIDatePickerMode.DateAndTime")

[`UIDatePickerMode.Date`](#uidatepickermodedate)如同, 選取器的順序以及12或24小時制的使用, 都取決於日期選擇器的地區設定。

> [!TIP]
> 使用屬性, 以模式`UIDatePickerMode.Time`、 `UIDatePickerMode.Date`或`UIDatePickerMode.DateAndTime`來捕捉日期選擇器的值。 `Date` 這個值會儲存為`NSDate`。

##### <a name="uidatepickermodecountdowntimer"></a>UIDatePickerMode.CountDownTimer

`UIDatePickerMode.CountDownTimer`顯示小時和分鐘的值:

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

!["UIDatePickerMode.CountDownTimer"](picker-images/image5.png "UIDatePickerMode.CountDownTimer")

屬性會在模式中`UIDatePickerMode.CountDownTimer`捕捉日期選擇器的值。 `CountDownDuration` 例如, 若要將倒數值新增至目前的日期:

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="nsdateformatter"></a>NSDateFormatter

若要格式化`NSDate`, 請[`NSDateFormatter`](xref:Foundation.NSDateFormatter)使用。

若要使用`NSDateFormatter`, 請呼叫[`ToString`](xref:Foundation.NSDateFormatter.ToString(Foundation.NSDate))其方法。 例如：

```csharp
var date = NSDate.Now;
var formatter = new NSDateFormatter();
formatter.DateStyle = NSDateFormatterStyle.Full;
formatter.TimeStyle = NSDateFormatterStyle.Full;
var formattedDate = formatter.ToString(d);
// Tuesday, August 14, 2018 at 11:20:42 PM Mountain Daylight Time
```

##### <a name="dateformat"></a>DateFormat

的屬性 (字串) `NSDateFormatter`允許可自訂的日期格式規格: [`DateFormat`](xref:Foundation.NSDateFormatter.DateFormat)

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

##### <a name="timestyle"></a>TimeStyle

屬性 (的會根據預先`NSDateFormatter`定義的樣式來指定時間格式: [`NSDateFormatterStyle`](xref:Foundation.NSDateFormatterStyle) [`TimeStyle`](xref:Foundation.NSDateFormatter.TimeStyle)

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

各種`NSDateFormatterStyle`值會顯示時間, 如下所示:

- `NSDateFormatterStyle.Full`：下午7:46:00 東部日光節約時間
- `NSDateFormatterStyle.Long`：7:47:00 PM EDT
- `NSDateFormatterStyle.Medium`：下午7:47:00
- `NSDateFormatterSytle.Short`：下午7:47

##### <a name="datestyle"></a>DateStyle

的屬性 () 會根據預先`NSDateFormatter`定義的樣式來指定日期格式: `NSDateFormatterStyle` [`DateStyle`](xref:Foundation.NSDateFormatter.DateStyle)

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

不同`NSDateFormatterStyle`的值會顯示日期, 如下所示:

- `NSDateFormatterStyle.Full`：2017年8月2日星期三下午7:48
- `NSDateFormatterStyle.Long`：2017年8月2日下午7:49
- `NSDateFormatterStyle.Medium`：2017年8月2日, 7:49 PM
- `NSDateFormatterStyle.Short`：8/2/17、7:50 PM

> [!NOTE]
> `DateFormat`和`DateStyle` 提供不同的`TimeStyle`方式來指定日期和時間格式。 / 最近設定的屬性會決定日期格式器的輸出。

## <a name="related-links"></a>相關連結

- [PickerControl (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/pickercontrol)
