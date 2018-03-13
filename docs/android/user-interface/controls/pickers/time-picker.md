---
title: "時間選擇器"
description: "選取 使用 TimePickerDialog 和 DialogFragment 時間"
ms.topic: article
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 93a2effd42432d13767dad05a47548aebc9a0b93
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="time-picker"></a>時間選擇器

若要提供方法，讓使用者選取的時間，您可以使用[Timeupdown](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/)。 Android 應用程式通常會使用`TimePicker`與[TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/)選取時間值&ndash;這有助於確保跨裝置和應用程式一致的介面。 `TimePicker` 可讓使用者在 12 小時制或 24 小時制的 AM/PM 模式中選取一天的時間。
`TimePickerDialog` 是 helper 類別來封裝`TimePicker`對話方塊中。

[![時間選擇器 對話方塊，在動作中的範例螢幕擷取畫面](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>總覽

現代的 Android 應用程式顯示`TimePickerDialog`中[DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)。 這可讓應用程式顯示`TimePicker`為快顯對話方塊或將它內嵌在活動中。 此外，`DialogFragment`管理生命週期和顯示的對話方塊中，減少必須實作的程式碼數量。

本指南示範如何使用`TimePickerDialog`、 包裝在`DialogFragment`。 範例應用程式會顯示`TimePickerDialog`為強制回應對話方塊，當使用者按一下活動上的按鈕。 當使用者設定時，對話方塊會結束作業，並更新處理常式`TextView`與所選取的時間 [活動] 畫面上。

## <a name="requirements"></a>需求

本指南中的範例應用程式的目標 Android 4.1 （API 層級
16) 或更新版本，但適用於 Android 3.0 （API 層級 11 或更高版本）。 它可支援舊版本的 Android，加上的 Android 支援的程式庫 v4，對專案和某些程式碼變更。

## <a name="using-the-timepicker"></a>使用 Timeupdown

此範例將擴充`DialogFragment`; 的子類別實作`DialogFragment`(稱為`TimePickerFragment`下方) 裝載和顯示`TimePickerDialog`。 範例應用程式第一次啟動時，它會顯示**挑選時間**上面的按鈕`TextView`，將會用來顯示選取的時間：

[![初始範例應用程式畫面](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

當您按一下**挑選時間**按鈕，範例應用程式會啟動`TimePickerDialog`這個螢幕擷取畫面所示：

[![應用程式所顯示的預設時間選擇器 對話方塊的螢幕擷取畫面](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

在`TimePickerDialog`、 選取一次，然後按一下**確定**按鈕原因`TimePickerDialog`叫用方法[IOnTimeSetListener.OnTimeSet](https://developer.xamarin.com/api/member/Android.App.TimePickerDialog+IOnTimeSetListener.OnTimeSet/p/Android.Widget.TimePicker/System.Int32/System.Int32/System.Int32/)。
這個介面由裝載實作`DialogFragment`(`TimePickerFragment`，如下所述)。 按一下**取消**按鈕會使片段，以關閉對話方塊。

`DialogFragment` 要在其中一種方式裝載 Actvity 會傳回選取的時間：

1. **叫用方法或設定屬性**&ndash;屬性或方法，特別為此值設定，可以提供的活動。

2. **引發事件** &ndash; `DialogFragment`可以定義事件將會引發的時機`OnTimeSet`叫用。

3. **使用`Action`**  &ndash; `DialogFragment`可以叫用`Action<DateTime>`來顯示在活動中的時間。 活動會提供`Action<DateTime`具現化時`DialogFragment`。 

這個範例會使用第三種方法，其需要的活動提供`Action<DateTime>`處理常式，以`DialogFragment`。



## <a name="start-an-app-project"></a>啟動應用程式專案

啟動新的 Android 專案呼叫**TimePickerDemo** (如果您不熟悉如何建立 Xamarin.Android 專案，請參閱[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)以了解如何建立新的專案)。

編輯**Resources/layout/Main.axml** ，並以下列 XML 取代其內容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center_horizontal"
    android:padding="16dp">
    <Button
        android:id="@+id/select_button"
        android:paddingLeft="24dp"
        android:paddingRight="24dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="PICK TIME"
        android:textSize="20dp" />
    <TextView
        android:id="@+id/time_display"
        android:layout_height="wrap_content"
        android:layout_width="match_parent"
        android:paddingTop="22dp"
        android:text="Picked time will be displayed here"
        android:textSize="24dp" />
</LinearLayout>
```

這是基本[線性](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)與[TextView](https://developer.xamarin.com/api/type/Android.Widget.TextView/)所顯示的時間和[按鈕](https://developer.xamarin.com/api/type/Android.Widget.Button/)開啟`TimePickerDialog`。 請注意此配置命名為更簡單且更易於了解，讓應用程式，使用硬式編碼的字串和維度&ndash;生產環境應用程式通常會使用資源，這些值 (如下所示[日期選擇器](https://github.com/xamarin/recipes/blob/master/android/controls/datepicker/select_a_date/Resources/layout/Main.axml)程式碼範例)。

編輯**Weatherapp** ，並以下列程式碼取代它的內容：

```csharp
using Android.App;
using Android.Widget;
using Android.OS;
using System;
using Android.Util;
using Android.Text.Format;

namespace TimePickerDemo
{
    [Activity(Label = "TimePickerDemo", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        TextView timeDisplay;
        Button timeSelectButton;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            SetContentView(Resource.Layout.Main);
            timeDisplay = FindViewById<TextView>(Resource.Id.time_display);
            timeSelectButton = FindViewById<Button>(Resource.Id.select_button);
        }
    }
}
```

當您建置並執行此範例中時，您應該會看到類似下列的螢幕擷取畫面初始畫面上：

[![初始應用程式畫面](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

按一下**挑選時間**按鈕不做任何動作因為`DialogFragment`尚未實作以顯示`TimePicker`。
下一個步驟是建立此`DialogFragment`。



## <a name="extending-dialogfragment"></a>擴充 DialogFragment

若要擴充`DialogFragment`搭配`TimePicker`，則需要建立的子類別衍生自`DialogFragment`並實作`TimePickerDialog.IOnTimeSetListener`。 將下列類別加入**Weatherapp**:

```csharp
public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
{
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };

    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }

    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }

    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
        Log.Debug(TAG, selectedTime.ToLongTimeString());
        timeSelectedHandler (selectedTime);
    }
}
```

這`TimePickerFragment`類別是已細分成較小的片段，而且下一節所述。


### <a name="dialogfragment-implementation"></a>DialogFragment 實作

`TimePickerFragment` 實作數個方法： 的 factory 方法，對話方塊具現化方法，而`OnTimeSet`所需的處理常式方法`TimePickerDialog.IOnTimeSetListener`。

-   `TimePickerFragment` 是的子類別`DialogFragment`。 它也會實作`TimePickerDialog.IOnTimeSetListener`介面 (也就是提供所需`OnTimeSet`方法):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

-   `TAG` 初始化記錄檔 (*MyTimePickerFragment*可以變更您想要使用任何字串)。 `timeSelectedHandler`動作會初始化為空的委派，以避免 null 參考例外狀況：

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

-   `NewInstance`呼叫 factory 方法來具現化新`TimePickerFragment`。 這個方法會採用`Action<DateTime>`使用者時叫用處理常式**確定**按鈕`TimePickerDialog`:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

-   Android 顯示片段時，呼叫`DialogFragment`方法[OnCreateDialog](https://developer.xamarin.com/api/member/Android.App.DialogFragment.OnCreateDialog/p/Android.OS.Bundle/)。 
    這個方法會建立新`TimePickerDialog`物件，並初始化活動時，回呼物件 (這是目前的執行個體`TimePickerFragment`)，和目前時間：

    ```csharp
    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }
    ```

-   當使用者變更的時間設定`TimePicker` 對話方塊中，`OnTimeSet`叫用方法。 `OnTimeSet` 建立`DateTime`物件使用目前的日期和使用者所選取合併的時間 （小時和分鐘）：

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```


-   這`DateTime`物件傳遞至`timeSelectedHandler`，向`TimePickerFragment`在建立時的物件。 `OnTimeSet` 會叫用這個處理常式，以更新活動的時間顯示為選取的時間 （此處理常式實作下一節內）：

    ```csharp
    timeSelectedHandler (selectedTime);
    ```



## <a name="displaying-the-timepickerfragment"></a>顯示 TimePickerFragment

既然`DialogFragment`已實作之後，就可以具現化開始`DialogFragment`使用`NewInstance`factory 方法，並將其顯示叫用[DialogFragment.Show](https://developer.xamarin.com/api/member/Android.App.DialogFragment.Show/p/Android.App.FragmentManager/System.String/):

將下列方法加入`MainActivity`:

```csharp
void TimeSelectOnClick (object sender, EventArgs eventArgs)
{
    TimePickerFragment frag = TimePickerFragment.NewInstance (
        delegate (DateTime time)
        {
            timeDisplay.Text = time.ToShortTimeString();
        });

    frag.Show(FragmentManager, TimePickerFragment.TAG);
}
```

之後`TimeSelectOnClick`具現化`TimePickerFragment`，它會建立並傳遞委派的匿名方法的傳入的時間值以更新活動的時間顯示。 最後，它會啟動`TimePicker`對話方塊片段 (透過`DialogFragment.Show`) 以顯示`TimePicker`給使用者。

在結尾`OnCreate`方法，將下列行加入附加事件處理常式**挑選時間**會啟動對話方塊的按鈕：

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

當**挑選時間**按一下按鈕時，`TimeSelectOnClick`將要叫用以顯示`TimePicker`對話方塊給使用者的片段。



## <a name="try-it"></a>請嘗試

建置和執行應用程式。 當您按一下**挑選時間** 按鈕，`TimePickerDialog`活動 （在此種情況下，12 小時制 AM/PM 模式中） 以預設的時間格式顯示：

[![時間 對話方塊隨即出現在 AM/PM 模式](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
當您按一下**確定**中`TimePicker` 對話方塊中，此處理常式更新活動的`TextView`與選定的時間，然後結束：

[![A/M 時間會顯示在活動 TextView](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

接下來，加入下列一行程式碼`OnCreateDialog`之後立即`is24HourFormat`是宣告並初始化：

```csharp
is24HourFormat = true;
```

這項變更會強制旗標傳遞至`TimePickerDialog`建構函式是`true`讓該 24 小時制模式會用來取代 「 裝載 」 活動的時間格式。 當您建置並再次執行應用程式時，按一下**挑選時間** 按鈕，`TimePicker`對話方塊現在會顯示在 24 小時格式：

[![24 小時制的小時 Timeupdown 對話方塊](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

因為處理常式會呼叫[DateTime.ToShortTimeString](https://msdn.microsoft.com/en-us/library/system.datetime.toshortdatestring%28v=vs.110%29.aspx)列印活動的時間`TextView`，時間仍會列印在預設 12 小時制 AM/PM 格式。



## <a name="summary"></a>總結

本文說明如何顯示`TimePicker`widget 為 Android 的 Activity 從快顯功能表強制回應對話方塊。 它提供範例`DialogFragment`實作，討論`IOnTimeSetListener`介面。 此範例也示範如何`DialogFragment`可以與主機活動，以顯示選取的時間互動。


## <a name="related-links"></a>相關連結

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/)
- [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/)
- [TimePickerDialog.IOnTimeSetListener](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog+IOnTimeSetListener/)
- [TimePickerDemo (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/TimePickerDemo)
