---
title: 逐步解說-儲存活動狀態
description: 我們已涵蓋背後活動的生命週期指南; 中儲存狀態的理論現在，讓我們逐步解說的範例。
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: e282eeb8732bd5294da4ec4e3fe337e81107c8f3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30767423"
---
# <a name="walkthrough---saving-the-activity-state"></a>逐步解說-儲存活動狀態

_我們已涵蓋背後活動的生命週期指南; 中儲存狀態的理論現在，讓我們逐步解說的範例。_

## <a name="activity-state-walkthrough"></a>活動狀態逐步解說

讓我們來開啟**ActivityLifecycle_Start**專案 (在[ActivityLifecycle](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)範例)，建置它，並執行它。 這是非常簡單的專案具有兩個活動，以示範活動的生命週期與各種存留週期方法呼叫的方式。 當您啟動應用程式的螢幕`MainActivity`會顯示： 

[![活動的畫面](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>檢視狀態轉換

在此範例中的每個方法將寫入 IDE 應用程式輸出視窗，以表示活動的狀態。 (若要開啟輸出視窗 Visual Studio 中，輸入**CTRL ALT O**; 若要修正程式，Visual Studio 中開啟 [輸出] 視窗中，按一下**檢視 > 板 > 應用程式輸出**。)

當應用程式第一次啟動時，[輸出] 視窗會顯示的狀態變更的*活動 A*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

當我們按一下**開始活動 B**按鈕，我們會了解*活動 A*暫停和停止時*活動 B*閒內通過其狀態變更： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

如此一來，*活動 B*已啟動並顯示取代*活動 A*: 

[![活動 B 畫面](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

當我們按一下**回** 按鈕，*活動 B*終結和*活動 A*繼續： 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```
### <a name="adding-a-click-counter"></a>加入按一下計數器

接下來，我們變更應用程式，讓我們有會計算並顯示的次數已按下的按鈕。 首先，讓我們加入`_counter`執行個體變數`MainActivity`:

```csharp
int _counter = 0;
```

接下來，讓編輯**Resource/layout/Main.axml**配置檔案，然後加入新`clickButton`，顯示使用者已按一下按鈕的次數。 產生**Main.axml**應該類似如下： 

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/myButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/mybutton_text" />
    <Button
        android:id="@+id/clickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/counterbutton_text" />
</LinearLayout>
```

讓我們加入下列程式碼的結尾[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)方法中的`MainActivity`&ndash;這段程式碼控點按事件從`clickButton`:

```csharp
var clickbutton = FindViewById<Button> (Resource.Id.clickButton);
clickbutton.Text = Resources.GetString (
    Resource.String.counterbutton_text, _counter);
clickbutton.Click += (object sender, System.EventArgs e) =>
{
    _counter++;
    clickbutton.Text = Resources.GetString (
        Resource.String.counterbutton_text, _counter);
} ;
```

當我們建置並再次執行應用程式，新按鈕會出現並遞增，並顯示的值`_counter`每按一下上：

[![新增觸控計數](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

但是，當我們旋轉橫向模式裝置時，此計數會遺失：

[![旋轉橫印設定設回零的計數](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

檢查應用程式輸出，我們看到*活動 A*已暫停、 停止、 終結，重新建立、 重新啟動，然後繼續期間從直向旋轉橫向模式： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

因為*活動 A*會毀損及重新建立一次旋轉裝置時，其執行個體狀態將會遺失。 接下來，我們會加入程式碼來儲存和還原執行個體的狀態。

### <a name="adding-code-to-preserve-instance-state"></a>程式碼加入至 保留執行個體狀態

讓我們將方法加入`MainActivity`儲存執行個體的狀態。 之前*活動 A*已損毀，Android 會自動呼叫[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/)而傳入[配套](https://developer.xamarin.com/api/type/Android.OS.Bundle/)，我們可以使用我們的執行個體狀態儲存。 讓我們使用我們按一下計數儲存為整數值：

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

當*活動 A*重新建立，並繼續，Android 傳遞`Bundle`回我們`OnCreate`方法。 讓我們加入的程式碼`OnCreate`還原`_counter`值從傳入的`Bundle`。 加入下列程式碼行之前其中`clickbutton`定義： 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

建置並再次執行應用程式，然後按一下第二個按鈕幾次。 當我們旋轉橫向模式裝置時，會保留計數 ！

[![旋轉螢幕顯示的四個保留的計數](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)


讓我們看看 [輸出] 視窗，以查看發生了什麼事：
    
```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - Saving instance state
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - Recovered instance state
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
``` 

之前[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/)已呼叫方法，我們新`OnSaveInstanceState`方法呼叫來儲存`_counter`值`Bundle`。 Android 傳遞這`Bundle`我們呼叫時我們`OnCreate`方法，以及我們能夠用來還原`_counter`值我們離開的地方。


## <a name="summary"></a>總結

在此 walkthough，我們已使用我們的活動生命週期的知識來保留狀態的資料。 



## <a name="related-links"></a>相關連結

- [ActivityLifecycle （範例）](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)
- [活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Android 的活動](https://developer.xamarin.com/api/type/Android.App.Activity/)
