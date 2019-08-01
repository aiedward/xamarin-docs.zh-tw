---
title: 日期選擇器
description: 使用 DatePickerDialog 和 DialogFragment 選取行事曆日期
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/22/2018
ms.openlocfilehash: ef9abbd60fc83622631b916c50f4993c1c848b00
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510247"
---
# <a name="android-date-picker"></a>Android 日期選擇器

## <a name="overview"></a>總覽

有時候, 使用者必須將資料輸入至 Android 應用程式。 為協助解決此情況, Android framework 提供[`DatePicker`](xref:Android.Widget.DatePicker) widget [`DatePickerDialog`](xref:Android.App.DatePickerDialog)和。 `DatePicker`可讓使用者在裝置和應用程式之間的一致介面中選取年、月和日。 是在對話方塊中封裝的`DatePicker` helper 類別。 `DatePickerDialog`

新式 Android 應用程式應該會`DatePickerDialog` [`DialogFragment`](xref:Android.App.DialogFragment)在中顯示。 這可讓應用程式將 DatePicker 顯示為快顯對話方塊, 或內嵌在活動中。 此外, `DialogFragment`將會管理對話方塊的生命週期和顯示, 減少必須執行的程式碼數量。

本指南將示範如何使用包裝`DatePickerDialog` `DialogFragment`在中的。 範例應用程式會在使用者`DatePickerDialog`按一下活動上的按鈕時, 將顯示為強制回應對話方塊。 當使用者設定日期時, `TextView`將會以選取的日期更新。

[![[選擇日期] 按鈕的螢幕擷取畫面, 後面接著 [日期選擇器] 對話方塊](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>需求

本指南的範例應用程式以 Android 4.1 (API 層級) 為目標
16) 或更高版本, 但適用于 Android 3.0 (API 層級11或更高版本)。 您可以在專案中加入 Android 支援程式庫 v4, 以支援舊版的 Android, 並變更一些程式碼。

## <a name="using-the-datepicker"></a>使用 DatePicker

這個範例將會`DialogFragment`擴充。 子`DatePickerDialog`類別會裝載並顯示:

![日期選擇器對話方塊特寫](date-picker-images/image-02.png)

當使用者選取日期並按一下 [**確定]** 按鈕時, `DatePickerDialog`會呼叫方法[`IOnDateSetListener.OnDateSet`](xref:Android.App.DatePickerDialog.IOnDateSetListener.OnDateSet*)。
這個介面是由裝載`DialogFragment`所執行。 如果使用者按一下 [**取消**] 按鈕, 則片段和對話方塊會自行解除。

有數種方式可以`DialogFragment`將選取的日期傳回給裝載活動:

1. 叫**用方法或設定屬性**&ndash;活動可以提供特別用來設定此值的屬性或方法。

2. **引發事件**可以定義會在叫用時`OnDateSet`引發的事件。 &ndash; `DialogFragment`

3. 使用可以叫用來顯示活動中的日期。`Action<DateTime>` **`Action`** &ndash; `DialogFragment` `Action<DateTime`當具現`DialogFragment`化時, 活動將會提供。 這個範例會使用第三個技術, 並要求活動將提供`Action<DateTime>` `DialogFragment`給。

### <a name="extending-dialogfragment"></a>擴充 DialogFragment

顯示的第一個步驟是`DatePickerDialog`將子類別`DialogFragment`化`IOnDateSetListener` , 並讓它執行介面:

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

會叫用`DatePickerFragment`方法,以具現`NewInstance`化新的。 這個方法會採用`Action<DateTime>` , 當使用者按一下中`DatePickerDialog`的 **[確定]** 按鈕時, 就會叫用。

當片段顯示時, Android 會呼叫方法`OnCreateDialog`。 這個方法會建立新`DatePickerDialog`的物件, 並使用目前的日期和回呼物件 (也就是的目前實例`DatePickerFragment`) 將它初始化。

> [!NOTE]
> 請注意, 叫用時`IOnDateSetListener.OnDateSet`的月份值位於0到11的範圍內, 而不是1到12。 月份的日期會在1到31的範圍內 (視選取的月份而定)。

### <a name="showing-the-datepickerfragment"></a>顯示 DatePickerFragment

現在已經實`DialogFragment`作為, 本節將探討如何在活動中使用片段。 在本指南隨附的範例應用程式中, 活動會`DialogFragment` `NewInstance`使用 factory 方法具現化, 然後顯示其`DialogFragment.Show`叫用。 在具現化的`DialogFragment`過程中, 活動會`Action<DateTime>`傳遞, 這會`TextView`顯示活動所裝載之中的日期:

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

這個範例討論了如何將`DatePicker` widget 顯示為快顯強制回應對話方塊, 做為 Android 活動的一部分。 它提供了範例 DialogFragment 的執行, 並`IOnDateSetListener`討論了介面。 此範例也會示範 DialogFragment 如何與主機活動互動, 以顯示選取的日期。

## <a name="related-links"></a>相關連結

- [DialogFragment](xref:Android.App.DialogFragment)
- [DatePicker](xref:Android.Widget.DatePicker)
- [DatePickerDialog](xref:Android.App.DatePickerDialog)
- [DatePickerDialog.IOnDateSetListener](xref:Android.App.DatePickerDialog.IOnDateSetListener)
- [選取日期](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/datepicker/select_a_date)
