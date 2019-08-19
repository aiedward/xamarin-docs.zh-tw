---
title: 時間選擇器
description: 使用 TimePickerDialog 和 DialogFragment 選取時間
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: fea96ab645b2d01b774f691402a5796eec1f1dba
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644962"
---
# <a name="android-time-picker"></a>Android 時間選擇器

若要提供使用者選取時間的方法, 您可以使用[TimePicker](xref:Android.Widget.TimePicker)。 Android 應用程式通常`TimePicker`會使用搭配[TimePickerDialog](xref:Android.App.TimePickerDialog)來選取時間&ndash;值, 這有助於確保裝置和應用程式之間的一致介面。 `TimePicker`可讓使用者以24小時或12小時 AM/PM 模式來選取當日時間。
`TimePickerDialog`是在對話方塊中封裝的`TimePicker` helper 類別。

[![作用中時間選擇器對話方塊的範例螢幕擷取畫面](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>總覽

新式 Android 應用程式會`TimePickerDialog`在[DialogFragment](xref:Android.App.DialogFragment)中顯示。 如此一來, 應用程式就可以將顯示`TimePicker`為快顯對話方塊, 或將它內嵌在活動中。 此外, `DialogFragment`也會管理對話方塊的生命週期和顯示, 減少必須執行的程式碼數量。

本指南示範如何使用包裝`TimePickerDialog` `DialogFragment`在中的。 範例應用程式會在`TimePickerDialog`使用者按一下活動的按鈕時, 將顯示為強制回應對話方塊。 當使用者設定時間時, 對話方塊就會結束, 而且處理常式會`TextView`在活動畫面上以選取的時間更新。

## <a name="requirements"></a>需求

本指南的範例應用程式以 Android 4.1 (API 層級) 為目標
16) 或更高版本, 但可與 Android 3.0 (API 層級11或更高版本) 搭配使用。 您可以在專案中加入 Android 支援程式庫 v4, 以支援舊版的 Android, 並變更一些程式碼。

## <a name="using-the-timepicker"></a>使用 TimePicker

這個範例會`DialogFragment`擴充; 的子類別`DialogFragment`執行 ( `TimePickerFragment`稱為底下), 並顯示`TimePickerDialog`。 第一次啟動範例應用程式時, 它會在將用來顯示`TextView`所選時間的上方顯示 [**挑選時間**] 按鈕:

[![初始範例應用程式畫面](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

當您按一下 [**選擇時間**] 按鈕時, 範例應用程式`TimePickerDialog`會啟動, 如下列螢幕擷取畫面所示:

[![應用程式所顯示之預設時間選擇器對話方塊的螢幕擷取畫面](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

在中 `TimePickerDialog` , 選取時間, 然後按一下 [確定] 按鈕, 會讓叫用方法[IOnTimeSetListener. OnTimeSet。](xref:Android.App.TimePickerDialog.IOnTimeSetListener.OnTimeSet*) `TimePickerDialog`
這個介面是由裝載`DialogFragment` (`TimePickerFragment`, 如下所述) 所執行。 按一下 [**取消**] 按鈕會關閉片段和對話方塊。

`DialogFragment`以三種方式的其中一種, 將選取的時間傳回給裝載活動:

1. 叫**用方法或設定屬性**&ndash;活動可以提供特別用來設定此值的屬性或方法。

2. **引發事件**可以定義會在叫用時`OnTimeSet`引發的事件。 &ndash; `DialogFragment`

3. 使用可以叫用來顯示活動中的時間。`Action<DateTime>` **`Action`** &ndash; `DialogFragment` `Action<DateTime`當具現`DialogFragment`化時, 活動將會提供。

這個範例會使用第三個技術, 這需要活動提供`Action<DateTime>`處理常式`DialogFragment`給。

## <a name="start-an-app-project"></a>啟動應用程式專案

啟動新的 Android 專案, 稱為**TimePickerDemo** (如果您不熟悉如何建立 Xamarin android 專案, 請參閱[Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md)以瞭解如何建立新專案)。

編輯**Resources/layout/axml** , 並將其內容取代為下列 XML:

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

這是具有[TextView](xref:Android.Widget.TextView)的基本[LinearLayout](xref:Android.Widget.LinearLayout) , 可顯示時間和開啟`TimePickerDialog`的[按鈕](xref:Android.Widget.Button)。 請注意, 此配置會使用硬式編碼的字串和維度, 讓應用程式更簡單&ndash;且更容易瞭解生產應用程式通常會使用這些值的資源 (如[DatePicker](https://github.com/xamarin/recipes/blob/master/Recipes/android/controls/datepicker/select_a_date/Resources/layout/Main.axml)程式碼範例所示)。

編輯**MainActivity.cs** , 並將其內容取代為下列程式碼:

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

當您建立並執行此範例時, 您應該會看到類似下列螢幕擷取畫面的初始畫面:

[![初始應用程式畫面](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

按一下 [**挑選時間**] 按鈕並不會`DialogFragment`執行任何操作, 因為尚未實作為`TimePicker`顯示。
下一個步驟是建立此`DialogFragment`。

## <a name="extending-dialogfragment"></a>擴充 DialogFragment

若要`DialogFragment`擴充以搭配`TimePicker`使用, 則必須建立衍生自`DialogFragment`並`TimePickerDialog.IOnTimeSetListener`執行的子類別。 將下列類別新增至**MainActivity.cs**:

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

這個`TimePickerFragment`類別會細分成較小的部分, 並在下一節中說明。

### <a name="dialogfragment-implementation"></a>DialogFragment 執行

`TimePickerFragment`會執行數個方法: factory 方法、對話方塊具現化方法, 以及`OnTimeSet` `TimePickerDialog.IOnTimeSetListener`所需的處理常式方法。

-   `TimePickerFragment`是的`DialogFragment`子類別。 它也`TimePickerDialog.IOnTimeSetListener`會執行介面 (也就是, 它會提供`OnTimeSet`所需的方法):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

-   `TAG`會針對記錄目的進行初始化 (*MyTimePickerFragment*可以變更為您想要使用的任何字串)。 `timeSelectedHandler`動作會初始化為空的委派, 以避免 null 參考例外狀況:

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

-   呼叫 factory 方法, 以具現化新`TimePickerFragment`的。 `NewInstance` 當使用者在中`Action<DateTime>`按一下 [**確定]** 按鈕時, 這個方法會使用一個叫`TimePickerDialog`用的處理常式:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

-   當片段顯示時, Android 會呼叫`DialogFragment` [OnCreateDialog](xref:Android.App.DialogFragment.OnCreateDialog*)方法。
    這個方法會建立新`TimePickerDialog`的物件, 並使用活動、回呼物件 (也就是的目前實例`TimePickerFragment`) 來初始化它, 以及目前的時間:

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

-   當使用者變更`TimePicker`對話方塊中的時間設定時`OnTimeSet` , 會叫用方法。 `OnTimeSet`使用目前的日期建立物件,並在使用者選取的時間(小時和分鐘)內合併:`DateTime`

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```


-   這個`DateTime`物件會傳遞至在`timeSelectedHandler`建立時向`TimePickerFragment`物件註冊的。 `OnTimeSet`叫用此處理程式, 將活動的時間顯示更新為選取的時間 (此處理程式會在下一節中執行):

    ```csharp
    timeSelectedHandler (selectedTime);
    ```

## <a name="displaying-the-timepickerfragment"></a>顯示 TimePickerFragment

現在已經實作為, 接下來要`DialogFragment`使用`NewInstance` factory 方法具現化, 並藉由叫用 DialogFragment 來顯示它[。 Show](xref:Android.App.DialogFragment.Show*): `DialogFragment`

將下列方法新增至`MainActivity`:

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

在`TimeSelectOnClick`具現`TimePickerFragment`化之後, 它會在匿名方法的委派中建立並傳遞, 並以傳入的時間值更新活動的時間顯示。 最後, 它會啟動`TimePicker`對話片段 (via `DialogFragment.Show`), 以向`TimePicker`使用者顯示。

在`OnCreate`方法的結尾, 新增下列這一行, 將事件處理常式附加至啟動對話方塊的 [**選擇時間**] 按鈕:

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

按一下 [**選擇時間** `TimePicker` ] 按鈕時, `TimeSelectOnClick`將會叫用以向使用者顯示對話方塊片段。

## <a name="try-it"></a>請嘗試

建置和執行應用程式。 當您按一下 [**選擇時間**] 按鈕時`TimePickerDialog` , 會以活動的預設時間格式顯示 (在此案例中為12小時 AM/PM 模式):

[![時間對話方塊會以 AM/PM 模式顯示](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
當您在`TimePicker`對話方塊中按一下 **[確定]** 時, 處理常式會`TextView`使用所選的時間來更新活動, 然後結束:

[![[活動 TextView] 中會顯示/M 時間](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

接下來, 在宣告並初始化之後`OnCreateDialog` `is24HourFormat` , 立即將下列程式碼新增至:

```csharp
is24HourFormat = true;
```

這項變更會`TimePickerDialog` `true`強制將傳遞至此函式的旗標設為, 以便使用24小時模式, 而不是裝載活動的時間格式。 當您再次建立並執行應用程式時, 按一下 [**選擇時間** `TimePicker` ] 按鈕, 對話方塊現在會以24小時制顯示:

[![24小時制的 TimePicker 對話方塊](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

由於處理常式會呼叫[ToShortTimeString](xref:System.DateTime.ToShortDateString*)來將時間列印到活動的`TextView`, 因此仍會以預設的12小時 AM/PM 格式來列印時間。

## <a name="summary"></a>總結

本文說明如何將`TimePicker` widget 顯示為來自 Android 活動的快捷強制回應對話方塊。 它提供了範例`DialogFragment`的執行, 並`IOnTimeSetListener`討論了介面。 這個範例也示範了`DialogFragment`如何與主機活動互動, 以顯示選取的時間。

## <a name="related-links"></a>相關連結

- [DialogFragment](xref:Android.App.DialogFragment)
- [TimePicker](xref:Android.Widget.TimePicker)
- [TimePickerDialog](xref:Android.App.TimePickerDialog)
- [TimePickerDialog.IOnTimeSetListener](xref:Android.App.TimePickerDialog.IOnTimeSetListener)
- [TimePickerDemo (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-timepickerdemo)
