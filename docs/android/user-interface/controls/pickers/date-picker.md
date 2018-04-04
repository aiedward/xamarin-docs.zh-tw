---
title: 日期選擇器
description: 選取使用 DatePickerDialog 和 DialogFragment 行事曆日期
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/22/2018
ms.openlocfilehash: 916a9c74fa28b99e799eef80db822e86cfda617d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="date-picker"></a>日期選擇器

## <a name="overview"></a>總覽

有一些情況下，當使用者沒有輸入資料至 Android 應用程式。 為了協助進行此設定，提供 Android 架構[ `DatePicker` ](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/) widget 和[ `DatePickerDialog` ](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/) 。 `DatePicker`可讓使用者跨裝置和應用程式一致的介面中選取的年、 月和日。 `DatePickerDialog`是封裝協助程式類別`DatePicker`對話方塊中。

現代的 Android 應用程式應該顯示`DatePickerDialog`中[ `DialogFragment` ](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)。 這可讓應用程式顯示日期選擇器快顯對話方塊中，或內嵌在活動中。 此外，`DialogFragment`將管理的生命週期和顯示對話方塊中，減少必須實作的程式碼數量。

本指南將示範如何使用`DatePickerDialog`、 包裝在`DialogFragment`。 範例應用程式會顯示`DatePickerDialog`為強制回應對話方塊，當使用者按一下活動上的按鈕。 當日期會設定由使用者`TextView`會更新以所選取的日期。

[![選取日期的螢幕擷取畫面 按鈕，後面接著日期選擇器對話方塊](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>需求

本指南中的範例應用程式的目標 Android 4.1 （API 層級
16) 或更新版本，但適用於 Android 3.0 （API 層級 11 或更高版本）。 它可支援舊版本的 Android，加上的 Android 支援的程式庫 v4，對專案和某些程式碼變更。

## <a name="using-the-datepicker"></a>使用 日期選擇器

這個範例會將擴充`DialogFragment`。 子類別會裝載，並顯示`DatePickerDialog`:

![經過的日期選擇器對話方塊](date-picker-images/image-02.png)

當使用者選取日期並按一下**確定** 按鈕，`DatePickerDialog`會呼叫方法[ `IOnDateSetListener.OnDateSet` ](https://developer.xamarin.com/api/member/Android.App.DatePickerDialog+IOnDateSetListener.OnDateSet/p/Android.Widget.DatePicker/System.Int32/System.Int32/System.Int32/)。
這個介面由裝載實作`DialogFragment`。 如果使用者按一下**取消**按鈕，則片段和對話方塊也會關閉本身。

有數種方式`DialogFragment`可以裝載活動傳回選取的日期：

1. **叫用方法或設定屬性**&ndash;屬性或方法，特別為此值設定，可以提供的活動。

2. **引發事件** &ndash; `DialogFragment`可以定義事件將會引發的時機`OnDateSet`叫用。

3. **使用`Action`**  &ndash; `DialogFragment`可以叫用`Action<DateTime>`活動中顯示的日期。 活動會提供`Action<DateTime`具現化時`DialogFragment`。 這個範例會使用第三種方法，並要求活動提供`Action<DateTime>`至`DialogFragment`。



### <a name="extending-dialogfragment"></a>擴充 DialogFragment

顯示的第一個步驟`DatePickerDialog`是子類別化`DialogFragment`，並讓它實作`IOnDateSetListener`介面：

```csharp
public class DatePickerFragment : DialogFragment, 
                                  DatePickerDialog.IOnDateSetListener
{
    // TAG can be any string of your choice.
    public static readonly string TAG = "X:" + typeof (DatePickerFragment).Name.ToUpper();
    
    // Initialize this value to prevent NullReferenceExceptions.
    Action<DateTime> _dateSelectedHandler = delegate { };
    
    public static DatePickerFragment NewInstance(Action<DateTime> onDateSelected)
    {
        DatePickerFragment frag = new DatePickerFragment();
        frag._dateSelectedHandler = onDateSelected;
        return frag;
    }
    
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        DateTime currently = DateTime.Now;
        DatePickerDialog dialog = new DatePickerDialog(Activity, 
                                                       this, 
                                                       currently.Year, 
                                                       currently.Month - 1,
                                                       currently.Day);
        return dialog;
    }
    
    public void OnDateSet(DatePicker view, int year, int monthOfYear, int dayOfMonth)
    {
        // Note: monthOfYear is a value between 0 and 11, not 1 and 12!
        DateTime selectedDate = new DateTime(year, monthOfYear + 1, dayOfMonth);
        Log.Debug(TAG, selectedDate.ToLongDateString());
        _dateSelectedHandler(selectedDate);
    }
}
```

`NewInstance`方法會叫用來具現化新`DatePickerFragment`。 這個方法會採用`Action<DateTime>`，將會叫用時使用者按下**確定**按鈕`DatePickerDialog`。

Android 顯示片段時，會呼叫方法`OnCreateDialog`。 這個方法會建立新`DatePickerDialog`物件，並初始化與目前的日期和回呼物件 (這是目前的執行個體`DatePickerFragment`)。


> [!NOTE]
> 請注意，月份值時`IOnDateSetListener.OnDateSet`叫用在 0 到 11，並不是 1 到 12 的範圍內。 月份天數會在 1 到 31，（依據月份已選取） 的範圍內。



### <a name="showing-the-datepickerfragment"></a>顯示 DatePickerFragment

既然`DialogFragment`已實作之後，本節將會檢驗如何使用活動中的片段。 範例應用程式隨附本指南，活動會具現化`DialogFragment`使用`NewInstance`factory 方法，然後顯示它叫用`DialogFragment.Show`。 具現化的一部份`DialogFragment`，活動會傳遞`Action<DateTime>`，這會顯示在日期`TextView`活動所裝載：

```csharp
[Activity(Label = "@string/app_name", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
    TextView _dateDisplay;
    Button _dateSelectButton;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);

        _dateDisplay = FindViewById<TextView>(Resource.Id.date_display);
        _dateSelectButton = FindViewById<Button>(Resource.Id.date_select_button);
        _dateSelectButton.Click += DateSelect_OnClick;
    }

    void DateSelect_OnClick(object sender, EventArgs eventArgs)
    {
        DatePickerFragment frag = DatePickerFragment.NewInstance(delegate(DateTime time)
                                                                 {
                                                                     _dateDisplay.Text = time.ToLongDateString();
                                                                 });
        frag.Show(FragmentManager, DatePickerFragment.TAG);
    }
}
```


## <a name="summary"></a>總結

此範例中會討論如何顯示`DatePicker`為快顯功能表強制回應對話方塊的 Android 活動一部分的 widget。 它提供範例 DialogFragment 實作，並討論`IOnDateSetListener`介面。 此範例也示範如何 DialogFragment 可能會與主應用程式以顯示所選的日期的活動互動。


## <a name="related-links"></a>相關連結

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [DatePicker](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/)
- [DatePickerDialog](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/)
- [DatePickerDialog.IOnDateSetListener](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog+IOnDateSetListener/)
- [選取的日期](https://github.com/xamarinhttps://developer.xamarin.com/recipes/tree/master/android/controls/datepicker/select_a_date)
