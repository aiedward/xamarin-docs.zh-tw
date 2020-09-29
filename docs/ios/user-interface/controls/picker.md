---
title: Xamarin 中的選擇器控制項
description: 本檔說明如何在 Xamarin iOS 應用程式中設計和使用選擇器控制項。 它會討論如何在程式碼和 iOS 設計工具中執行選擇器。
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/14/2018
ms.openlocfilehash: 108d7752c51a6dffd28107b87d29198443827ed2
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436849"
---
# <a name="picker-control-in-xamarinios"></a>Xamarin 中的選擇器控制項

， [`UIPickerView`](xref:UIKit.UIPickerView) 可讓您藉由滾動滾輪介面的個別元件來挑選清單中的值。

選擇器經常用來選取日期和時間;Apple 提供 [`UIDatePicker`](xref:UIKit.UIDatePicker)
適用于此用途的類別。

本文描述如何執行和，以及如何使用 `UIPickerView` 和 `UIDatePicker` 控制項。

## <a name="uipickerview"></a>UIPickerView

### <a name="implementing-a-picker"></a>執行選擇器

藉由具現化新的來執行選擇器 `UIPickerView` ：

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

若要在**IOS 設計****工具**中建立選擇器，請從 [工具箱] 將 [**選擇器] 視圖**拖曳至設計介面。

![將選擇器視圖拖曳至設計介面](picker-images/image1.png "將選擇器視圖拖曳至設計介面")

### <a name="working-with-a-picker-control"></a>使用選擇器控制項

選擇器會使用 _模型_ 來與資料互動：

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    var pickerModel = new PeopleModel(personLabel);
    personPicker.Model = pickerModel;
}
```

[`UIPickerViewModel`](xref:UIKit.UIPickerViewModel)基底類別會執行兩個介面，[`IUIPickerDataSource`](xref:UIKit.IUIPickerViewDataSource)
和 [`IUIPickerViewDelegate`](xref:UIKit.IUIPickerViewDelegate) ，宣告指定選擇器資料的各種方法，以及如何處理互動：

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

選擇器可以有多個資料行或 _元件_。 元件會將選擇器分割成多個區段，讓您可以更輕鬆且更明確地選取資料：

![具有兩個元件的選擇器](picker-images/image3.png "具有兩個元件的選擇器")

若要指定選擇器中的元件數目，請使用 [`GetComponentCount`](xref:UIKit.UIPickerViewModel.GetComponentCount(UIKit.UIPickerView)) 
方法。

### <a name="customizing-a-pickers-appearance"></a>自訂選擇器的外觀

若要自訂選擇器的外觀，請使用 [`UIPickerView.UIPickerViewAppearance`](xref:UIKit.UIPickerView.UIPickerViewAppearance)
類別或覆寫 [`GetView`](xref:UIKit.UIPickerViewModel.GetView(UIKit.UIPickerView,System.nint,System.nint,UIKit.UIView)) 中的和 [`GetRowHeight`](xref:UIKit.UIPickerViewModel.GetRowHeight(UIKit.UIPickerView,System.nint)) 方法 `UIPickerViewModel` 。

## <a name="uidatepicker"></a>UIDatePicker

### <a name="implementing-a-date-picker"></a>執行日期選擇器

藉由具現化來執行日期選擇器 `UIDatePicker` ：

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

若要在**IOS 設計**工具中建立日期選擇器，請從 [**工具箱**] 將 [**日期選擇器**] 拖曳至設計介面。

![將日期選擇器拖曳至設計介面](picker-images/image2.png "將日期選擇器拖曳至設計介面")

### <a name="date-picker-properties"></a>日期選擇器屬性

#### <a name="minimum-and-maximum-date"></a>最小和最大日期

[`MinimumDate`](xref:UIKit.UIDatePicker.MinimumDate) 並 [`MaximumDate`](xref:UIKit.UIDatePicker.MaximumDate) 限制日期選擇器中可用的日期範圍。 例如，下列程式碼會將日期選擇器限制在目前為止的60年：

```csharp
var calendar = new NSCalendar(NSCalendarType.Gregorian);
var currentDate = NSDate.Now;
var components = new NSDateComponents();
components.Year = -60;
NSDate minDate = calendar.DateByAddingComponents(components, currentDate, NSCalendarOptions.None);
datePickerView.MinimumDate = minDate;
datePickerView.MaximumDate = currentDate;
```

> [!TIP]
> 您可以明確地將轉換成 `DateTime` `NSDate` ：
>
> ```csharp
> DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
> DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
> ```

#### <a name="minute-interval"></a>分鐘間隔

[`MinuteInterval`](xref:UIKit.UIDatePicker.MinuteInterval)屬性會設定選擇器顯示分鐘的間隔：

```csharp
datePickerView.MinuteInterval = 10;
```

#### <a name="mode"></a>模式

日期選擇器支援四種 [模式](xref:UIKit.UIDatePickerMode)，如下所述：

##### <a name="uidatepickermodetime"></a>UIDatePickerMode。時間

`UIDatePickerMode.Time` 以小時和分鐘選取器顯示時間，以及選擇性的 AM 或 PM 指定：

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

![UIDatePickerMode。時間](picker-images/image8.png "UIDatePickerMode。時間")

##### <a name="uidatepickermodedate"></a>UIDatePickerMode。日期

`UIDatePickerMode.Date` 以 month、day 和 year 選取器顯示日期：

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

![UIDatePickerMode。日期](picker-images/image7.png "UIDatePickerMode。日期")

選取器的順序取決於日期選擇器的地區設定，預設會使用系統地區設定。 上圖顯示地區設定中的選取器版面配置 `en_US` ，但下列會將訂單變更為 Day |月 |年：

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![天 |月 |年](picker-images/image9.png "天 |月 |年")

##### <a name="uidatepickermodedateandtime"></a>UIDatePickerMode. >formatting.dateandtime.custom

`UIDatePickerMode.DateAndTime` 根據是否使用12或24小時制，顯示日期、時間（以小時和分鐘為單位）的簡短視圖，以及選擇性的 AM 或 PM 指定 () ：

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

![UIDatePickerMode. >formatting.dateandtime.custom](picker-images/image6.png "UIDatePickerMode. >formatting.dateandtime.custom")

如同 [`UIDatePickerMode.Date`](#uidatepickermodedate) ，選取器的順序以及12或24小時制的使用，取決於日期選擇器的地區設定。

> [!TIP]
> 您 `Date` 可以使用屬性，以模式、或來捕捉日期選擇器的值 `UIDatePickerMode.Time` `UIDatePickerMode.Date` `UIDatePickerMode.DateAndTime` 。 此值會儲存為 `NSDate` 。

##### <a name="uidatepickermodecountdowntimer"></a>UIDatePickerMode.CountDownTimer

`UIDatePickerMode.CountDownTimer` 顯示小時和分鐘值：

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

!["UIDatePickerMode.CountDownTimer"](picker-images/image5.png "UIDatePickerMode.CountDownTimer")

`CountDownDuration`屬性會在模式中捕捉日期選擇器的值 `UIDatePickerMode.CountDownTimer` 。 例如，若要將倒數值加入至目前的日期：

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="nsdateformatter"></a>NSDateFormatter

若要格式化 `NSDate` ，請使用 [`NSDateFormatter`](xref:Foundation.NSDateFormatter) 。

若要使用 `NSDateFormatter` ，請呼叫其 [`ToString`](xref:Foundation.NSDateFormatter.ToString(Foundation.NSDate)) 方法。 例如：

```csharp
var date = NSDate.Now;
var formatter = new NSDateFormatter();
formatter.DateStyle = NSDateFormatterStyle.Full;
formatter.TimeStyle = NSDateFormatterStyle.Full;
var formattedDate = formatter.ToString(d);
// Tuesday, August 14, 2018 at 11:20:42 PM Mountain Daylight Time
```

##### <a name="dateformat"></a>DateFormat

[`DateFormat`](xref:Foundation.NSDateFormatter.DateFormat)屬性 (的字串) `NSDateFormatter` ，允許可自訂的日期格式規格：

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

##### <a name="timestyle"></a>TimeStyle

[`TimeStyle`](xref:Foundation.NSDateFormatter.TimeStyle) (的屬性會 [`NSDateFormatterStyle`](xref:Foundation.NSDateFormatterStyle) `NSDateFormatter` 根據預先決定的樣式來指定時間格式：

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

不同 `NSDateFormatterStyle` 的值顯示時間，如下所示：

- `NSDateFormatterStyle.Full`： 7:46:00 PM 東部日光節約時間
- `NSDateFormatterStyle.Long`： 7:47:00 PM EDT
- `NSDateFormatterStyle.Medium`：下午7:47:00
- `NSDateFormatterSytle.Short`：下午7:47

##### <a name="datestyle"></a>DateStyle

[`DateStyle`](xref:Foundation.NSDateFormatter.DateStyle)屬性 (的 `NSDateFormatterStyle`) 會 `NSDateFormatter` 根據預先決定的樣式來指定日期格式：

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

不同 `NSDateFormatterStyle` 的值會顯示日期，如下所示：

- `NSDateFormatterStyle.Full`：2017年8月2日星期三下午7:48
- `NSDateFormatterStyle.Long`：2017年8月2日下午7:49
- `NSDateFormatterStyle.Medium`：2017年8月2日，7:49 PM
- `NSDateFormatterStyle.Short`：8/2/17、7:50 PM

> [!NOTE]
> `DateFormat`並 `DateStyle` / `TimeStyle` 提供不同的方式來指定日期和時間格式。 最近設定的屬性會決定日期格式器的輸出。

## <a name="related-links"></a>相關連結

- [PickerControl (範例) ](/samples/xamarin/ios-samples/pickercontrol)