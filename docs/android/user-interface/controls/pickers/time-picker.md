---
title: 時間選擇器
description: 使用 TimePickerDialog 和 DialogFragment 選取時間
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 0f6082476a7fe4ebd8a6a3f52c23951d58ac1383
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453738"
---
# <a name="android-time-picker"></a>Android 時間選擇器

若要提供使用者選取時間的方法，您可以使用 [TimePicker](xref:Android.Widget.TimePicker)。 Android 應用程式通常會搭配使用 `TimePicker` [TimePickerDialog](xref:Android.App.TimePickerDialog) 來選取時間值， &ndash; 這有助於確保跨裝置和應用程式的一致介面。 `TimePicker` 允許使用者選取一天中的時間（24小時或12小時上午/下午）模式。
`TimePickerDialog` 是在對話方塊中封裝的 helper 類別 `TimePicker` 。

[![作用中時間選擇器對話方塊的範例螢幕擷取畫面](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>概觀

新式的 Android 應用程式會 `TimePickerDialog` 在 [DialogFragment](xref:Android.App.DialogFragment)中顯示。 如此一來，應用程式就可以將顯示 `TimePicker` 為快顯對話方塊，或將它內嵌在活動中。 此外，也會 `DialogFragment` 管理對話方塊的生命週期和顯示，減少必須執行的程式碼數量。

本指南示範如何使用 `TimePickerDialog` 包裝在中的 `DialogFragment` 。 當使用者按一下活動上的按鈕時，範例應用程式會將顯示 `TimePickerDialog` 為強制回應對話方塊。 當使用者設定此時間時，對話方塊會結束，且處理常式 `TextView` 會在已選取時間的情況下，更新活動畫面上的。

## <a name="requirements"></a>需求

本指南的範例應用程式以 Android 4.1 (API 層級為目標
16) 或更高版本，但可搭配 Android 3.0 (API level 11 或更高版本的) 使用。 您可以支援舊版 Android，並將 Android 支援程式庫 v4 新增至專案，並變更一些程式碼。

## <a name="using-the-timepicker"></a>使用 TimePicker

這個範例會擴充 `DialogFragment` ; (的子類別實 `DialogFragment` `TimePickerFragment`) 裝載，並顯示 `TimePickerDialog` 。 當範例應用程式第一次啟動時，它會在上方顯示的 [ **挑選時間** ] 按鈕， `TextView` 用來顯示所選時間：

[![初始範例應用程式畫面](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

當您按一下 [ **選擇時間** ] 按鈕時，範例應用程式就會啟動， `TimePickerDialog` 如下列螢幕擷取畫面所示：

[![應用程式顯示的預設時間選擇器對話方塊螢幕擷取畫面](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

在中 `TimePickerDialog` 選取時間，然後按一下 [ **確定]** 按鈕，將會叫用 `TimePickerDialog` 方法 [IOnTimeSetListener. OnTimeSet](xref:Android.App.TimePickerDialog.IOnTimeSetListener.OnTimeSet*)。
此介面是由主控 (所 `DialogFragment` 執行 `TimePickerFragment` ，如下所述) 。 按一下 [ **取消** ] 按鈕，就會關閉片段和對話方塊。

`DialogFragment` 以下列三種方式之一，將選取的時間傳回裝載活動：

1. 叫**用方法或設定屬性** &ndash;活動可以提供專門用來設定此值的屬性或方法。

2. **引發事件** &ndash;`DialogFragment`可以定義叫用時將引發的事件 `OnTimeSet` 。

3. **使用 `Action` **&ndash; `DialogFragment` 可以叫 `Action<DateTime>` 用，以顯示活動中的時間。 當具現化時，活動會提供 `Action<DateTime` `DialogFragment` 。

此範例會使用第三個技術，這需要活動提供的 `Action<DateTime>` 處理常式 `DialogFragment` 。

## <a name="start-an-app-project"></a>啟動應用程式專案

如果您不熟悉如何建立 Xamarin Android 專案，請啟動名為 **TimePickerDemo** (的新 Android 專案，請參閱 [Hello，android](~/android/get-started/hello-android/hello-android-quickstart.md) 以瞭解如何建立新專案) 。

編輯 **Resources/layout/Main. .axml** ，並將其內容取代為下列 XML：

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

這是具有[TextView](xref:Android.Widget.TextView)的基本[LinearLayout](xref:Android.Widget.LinearLayout) ，可顯示開啟的時間和[按鈕](xref:Android.Widget.Button) `TimePickerDialog` 。 請注意，此配置使用硬式編碼的字串和維度，讓應用程式更簡單且更容易瞭解 &ndash; 生產環境應用程式通常會使用這些值的資源 (如 [DatePicker](https://github.com/xamarin/recipes/blob/master/Recipes/android/controls/datepicker/select_a_date/Resources/layout/Main.axml) 程式碼範例) 所示。

編輯 **MainActivity.cs** ，並將其內容取代為下列程式碼：

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

當您建立並執行此範例時，您應該會看到類似下列螢幕擷取畫面的初始畫面：

[![初始應用程式畫面](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

按一下 [ **挑選時間** ] 按鈕並不會執行任何動作，因為尚未 `DialogFragment` 執行來顯示 `TimePicker` 。
下一步是建立此 `DialogFragment` 。

## <a name="extending-dialogfragment"></a>擴充 DialogFragment

若要擴充以搭配 `DialogFragment` 使用 `TimePicker` ，則必須建立衍生自和的子類別 `DialogFragment` `TimePickerDialog.IOnTimeSetListener` 。 將下列類別新增至 **MainActivity.cs**：

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

這個 `TimePickerFragment` 類別會細分成較小的片段，並在下一節中說明。

### <a name="dialogfragment-implementation"></a>DialogFragment 執行

`TimePickerFragment` 會執行數個方法： factory 方法、對話具現化方法，以及 `OnTimeSet` 所需的處理常式方法 `TimePickerDialog.IOnTimeSetListener` 。

- `TimePickerFragment` 這是的子類別 `DialogFragment` 。 它也會執行 `TimePickerDialog.IOnTimeSetListener` 介面 (也就是說，它會提供所需的 `OnTimeSet` 方法) ：

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

- `TAG` 是為了記錄目的而初始化 (*MyTimePickerFragment* 可以變更為您想要使用) 的任何字串。 `timeSelectedHandler`動作會初始化為空的委派，以防止 null 參考例外狀況：

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

- `NewInstance`呼叫 factory 方法來具現化新的 `TimePickerFragment` 。 `Action<DateTime>`當使用者按一下中的 [**確定]** 按鈕時，這個方法會採用所叫用的處理常式 `TimePickerDialog` ：

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

- 當片段顯示時，Android 會呼叫 `DialogFragment` 方法 [OnCreateDialog](xref:Android.App.DialogFragment.OnCreateDialog*)。
    這個方法會建立新的 `TimePickerDialog` 物件，並使用活動、回呼物件 (，也就是目前的 `TimePickerFragment`) 實例和目前的時間，來將它初始化：

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

- 當使用者變更對話方塊中的時間設定時 `TimePicker` ， `OnTimeSet` 就會叫用方法。 `OnTimeSet``DateTime`使用目前的日期建立物件，並在使用者選取的時間 (小時和分鐘合併) ：

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```

- 這個 `DateTime` 物件會傳遞至在 `timeSelectedHandler` `TimePickerFragment` 建立時向物件註冊的。 `OnTimeSet` 叫用這個處理常式，將活動的時間顯示更新為選取的時間 (此處理程式會在下一節中執行) ：

    ```csharp
    timeSelectedHandler (selectedTime);
    ```

## <a name="displaying-the-timepickerfragment"></a>顯示 TimePickerFragment

現在已完成 `DialogFragment` ，現在可以使用 factory 方法來具現化，並藉由叫用 `DialogFragment` `NewInstance` DialogFragment 來顯示它 [。 Show](xref:Android.App.DialogFragment.Show*)：

將下列方法新增至 `MainActivity`：

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

在 `TimeSelectOnClick` 具現化之後 `TimePickerFragment` ，它會在匿名方法的委派中建立並傳遞，該方法會使用傳入的時間值來更新活動的時間顯示。 最後，它會透過 `TimePicker`) 來啟動對話方塊片段 (， `DialogFragment.Show` 以向 `TimePicker` 使用者顯示。

在方法的結尾 `OnCreate` ，新增下列程式程式碼，將事件處理常式附加至啟動對話方塊的 [ **選擇時間** ] 按鈕：

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

按一下 [ **選擇時間** ] 按鈕時， `TimeSelectOnClick` 將會叫用將 `TimePicker` 對話片段顯示給使用者。

## <a name="try-it"></a>請嘗試

建置並執行應用程式。 當您按一下 [ **選擇時間** ] 按鈕時， `TimePickerDialog` 會以預設的啟用時間格式顯示 (在此案例中為12小時上午/下午) ：

[![時間對話方塊會以 AM/PM 模式顯示](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
當您在對話方塊中按一下 **[確定** ] 時 `TimePicker` ，處理常式會 `TextView` 以選擇的時間更新活動，然後結束：

[![活動 TextView 中會顯示 A/M 時間](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

接下來，在宣告 `OnCreateDialog` 和初始化之後，立即新增下列程式程式碼 `is24HourFormat` ：

```csharp
is24HourFormat = true;
```

這項變更會強制將旗標傳遞至函 `TimePickerDialog` 式，以 `true` 使用24小時模式，而不是裝載活動的時間格式。 當您重新建立並執行應用程式時，請按一下 [ **選擇時間** ] 按鈕， `TimePicker` 對話方塊現在會顯示24小時格式：

[![24小時制的 TimePicker 對話方塊](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

因為處理常式會呼叫 [ToShortTimeString](xref:System.DateTime.ToShortDateString*) 來將時間列印到活動的時間 `TextView` ，所以仍會以預設的12小時 AM/PM 格式來列印時間。

## <a name="summary"></a>摘要

本文說明如何 `TimePicker` 從 Android 活動將 widget 顯示為快顯強制回應對話方塊。 它提供了範例 `DialogFragment` 的執行，並討論了 `IOnTimeSetListener` 介面。 此範例也示範了如何 `DialogFragment` 與主機活動互動以顯示選取的時間。

## <a name="related-links"></a>相關連結

- [DialogFragment](xref:Android.App.DialogFragment)
- [TimePicker](xref:Android.Widget.TimePicker)
- [TimePickerDialog](xref:Android.App.TimePickerDialog)
- [TimePickerDialog.IOnTimeSetListener](xref:Android.App.TimePickerDialog.IOnTimeSetListener)
- [TimePickerDemo (範例) ](/samples/xamarin/monodroid-samples/userinterface-timepickerdemo)