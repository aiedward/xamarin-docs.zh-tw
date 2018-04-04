---
title: 選擇器
description: 本指南涵蓋設計及 Xamarin.iOS 應用程式中使用選擇器。
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/02/2017
ms.openlocfilehash: e213124e870f1cca96a6078fd26bc7eeb1af55a1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="picker"></a>選擇器

選擇器控制項顯示 '滾輪類似' 控制項包含可捲動的值，選取要反白顯示的值清單。 使用者旋轉滾輪以選取他們想要的選項。

一個特定的使用者案例的選擇器設定的日期和/或時間。 若要提供此 apple 已建立稱為 UIDatePicker UIPickerView 類別的自訂子類別。

本文件涵蓋實作和使用[選擇器](#picker)和[日期選擇器](#datepicker)控制項。

<a name="picker" />

## <a name="picker"></a>選擇器

### <a name="implementing-a-picker"></a>實作選擇器

選擇器藉由具現化新[`UIPickerView`](https://developer.xamarin.com/api/type/UIKit.UIPickerView/):

```csharp
UIPickerView pickerView = new UIPickerView(
                            new CGRect(
                                UIScreen.MainScreen.Bounds.X-UIScreen.MainScreen.Bounds.Width, UIScreen.MainScreen.Bounds.Height -230, 
                                UIScreen.MainScreen.Bounds.Width, 
                                180));
```


### <a name="pickers-and-storyboards"></a>選擇器和分鏡腳本

如果您使用 iOS 設計工具建立 UI，選擇器可以加入至您的配置從 [工具箱]:

![](picker-images/image1.png)


### <a name="working-with-picker"></a>使用 選擇器

一旦您已在程式碼中是否建立為選擇器，或透過 分鏡腳本，您必須指派_模型_，以便傳遞，並與其互動資料。

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    var pickerModel = new PeopleModel(personLabel);

    personPicker.Model = pickerModel;
}
```

下列程式碼會顯示模型的範例：

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

首先，您必須將某些資料以提供使用者選取不同的選項。 當可能都會嘗試保持越短越好，這份清單，或視需要嘗試使用多個 [撥號] (稱為*元件*):

![具有兩個元件選擇器](picker-images/image3.png)

若要設定的元件數目，請覆寫`GetComponentCount`方法： 

```csharp
public override nint GetComponentCount(UIPickerView pickerView)
{
    return 2;
}
```

傳回值表示撥出將會有您的選擇器的數目。

### <a name="customizing-appearance"></a>自訂外觀
 
外觀`UIPickerView`可以使用自訂`UIPickerView.UIPickerViewAppearance`類別，或藉由覆寫`UIPickerViewModel.GetView`和`UIPickerViewModel.GetRowHeight`方法`UIPickerViewModel`。


<a name="datepicker" />

## <a name="date-picker"></a>日期選擇器

### <a name="implementing-a-date-picker"></a>實作日期選擇器

日期選擇器藉由具現化新[ `UIDatePickerView` ](https://developer.xamarin.com/api/type/UIKit.UIDatePicker/):

```csharp
UIPickerView pickerView = new UIPickerView(
                            new CGRect(
                                UIScreen.MainScreen.Bounds.X-UIScreen.MainScreen.Bounds.Width, UIScreen.MainScreen.Bounds.Height -230, 
                                UIScreen.MainScreen.Bounds.Width, 
                                180));
```


### <a name="date-pickers-and-storyboards"></a>日期選擇器和分鏡腳本

如果您使用 iOS 設計工具建立 UI，**日期選擇器**可以從 [工具箱] 加入至您的配置。 從屬性板，您可以調整下列屬性：

![](picker-images/image2.png)

* **模式**– 日期和時間模式。 這可以是日期、 時間、 日期和時間或倒數計時器。 
* **地區設定**– 日期選擇器的地區設定。 選擇**預設**設為系統預設值，或設為任何特定地區設定。
* **間隔**– 顯示時鐘選項會顯示增量。
* **日期、 最小日期、 最大日期**– 設定初始的日期選擇器會顯示和條件約束的可選取的日期。

### <a name="configuring-the-datepicker"></a>設定日期選擇器

您可以限制使用者可以使用從選取的日期範圍`MinimumDate`和`MaximumDate`屬性。 下列程式碼片段顯示如何設定介於 60 的年數前今日的範例：

```csharp
var calendar = new NSCalendar(NSCalendarType.Gregorian);
var currentDate = NSDate.Now;
var components = new NSDateComponents();

components.Year = -60;

NSDate minDate = calendar.DateByAddingComponents(components, NSDate.Now, NSCalendarOptions.None);

datePickerView.MinimumDate = minDate;
datePickerView.MaximumDate = NSDate.Now;
```

或者，您也可以使用.NET 控制項設定的最小和最大的日期範圍。 例如: 

```csharp
DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
```

您也可以設定`MinuteInterval`屬性設定的選擇器會顯示分鐘的時間間隔。 下列程式碼片段可以用來設定將時間間隔的 10 分鐘的時間選取器。

```csharp
datePickerView.MinuteInterval = 10;
```

有四種日期選擇器可以設定為使用的模式[ `UIDatePicker.Mode` ](https://developer.xamarin.com/api/property/UIKit.UIDatePicker.Mode/)屬性。 下列清單顯示每一項，以及如何實作該類別的範例：

#### <a name="time"></a>時間

時間模式顯示使用小時和分鐘的選取器和選擇性 AM 或 PM 指定的時間。 設定與`UIDatePickerMode.Time`屬性。 例如: 

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

下圖顯示這個日期選擇器模式的範例：

![](picker-images/image8.png)



#### <a name="date"></a>日期

日期模式顯示的日期與月份、 日期和年份的選取器。 設定與`UIDatePickerMode.Date`屬性。 例如: 

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

下圖顯示這個日期選擇器的範例：

![](picker-images/image7.png)

選取器的順序而定的地區設定`UIDatePicker`。 依預設這會設定為系統預設值。 上面的影像顯示在選取器的配置`en_US`地區設定，但若要將它變更為一天 |月 |年份版面配置，您可以使用下列程式碼設定的地區設定：

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![](picker-images/image9.png)


#### <a name="date-and-time"></a>日期和時間

如果使用 12 或 24 小時制的日期和時間模式顯示日期的時間，以小時和分鐘和選擇性的 AM 或 PM 指定 dependings shortend 檢視上。 設定與`UIDatePickerMode.DateAndTime`屬性。 例如: 

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

下圖顯示這個日期選擇器的範例：

![](picker-images/image6.png)

如同[日期](#Date)，選取器的順序和 12 或 24 小時制的使用取決於地區設定的`UIDatePicker`。

#### <a name="countdown-timer"></a>倒數計時器

小時和分鐘值，則會顯示倒數計時器模式。 設定與`UIDatePickerMode.CountDownTimer`屬性。 例如: 

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

下圖顯示這個日期選擇器的範例：

![](picker-images/image5.png)

您可以使用`CountDownDuration`來擷取值 dispayed 倒數計時日期選擇器的屬性。 例如，若要加入倒數計時值為目前的日期，您可以使用下列程式碼：

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="formatting"></a>格式化 

可以使用擷取的時間、 日期和 DateAndTime 模式值`Date`UIDatePicker 屬性 (例如： `datePickerView.Date`)，NSDate 類型。 若要將這個日期格式化成人們可讀取多個項目，使用[ `NSDateFormatter` ](https://developer.xamarin.com/api/type/Foundation.NSDateFormatter/)。 下列範例會示範如何於此類別使用某些可用的屬性。

`DateFormat`做為字串設定為代表應該用來顯示日期的方式：

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

`TimeStyle`屬性集`NSDateFormatterStyle`:

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

欄位`NSDateFormatterStyle`顯示，如下所示：

![](picker-images/timestyle.png)

`DateStyle`屬性集`NSDateFormatterStyle`:

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

欄位`NSDateFormatterStyle`顯示，如下所示：

![](picker-images/datestyle.png)

您接著可以使用下列程式碼輸出格式化為字串 NSDate:

```csharp
dateLabel.Text = dateTimeformat.ToString(datePickerView.Date);
```

## <a name="related-links"></a>相關連結

- [PickerControl （範例）](https://developer.xamarin.com/samples/monotouch/PickerControl/)
