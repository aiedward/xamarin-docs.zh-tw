---
title: 逐步解說 - 儲存活動狀態
description: 我們已涵蓋背後活動生命週期指南; 中儲存狀態的理論現在，讓我們逐步解說範例。
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: c8f92e55648dff469227cc3bad981ad5f6e6d0ac
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61019126"
---
# <a name="walkthrough---saving-the-activity-state"></a>逐步解說 - 儲存活動狀態

_我們已涵蓋背後活動生命週期指南; 中儲存狀態的理論現在，讓我們逐步解說範例。_

## <a name="activity-state-walkthrough"></a>活動狀態的逐步解說

讓我們開啟**ActivityLifecycle_Start**專案 (在[ActivityLifecycle](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)範例)，建置它，並予以執行。 這是非常簡單的專案，有兩個活動，來示範活動開發週期，以及如何呼叫各種的生命週期方法。 當您啟動應用程式的畫面`MainActivity`會顯示： 

[![活動的畫面](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>檢視狀態轉換

在此範例中的每個方法將寫入至 IDE 應用程式的輸出視窗，來表示活動的狀態。 (在 Visual Studio 中開啟 [輸出] 視窗，輸入**CTRL ALT-O**; 若要 for Mac，然後在 Visual Studio 中開啟 [輸出] 視窗中，按一下**檢視 > 板 > 應用程式輸出**。)

當應用程式第一次啟動時，[輸出] 視窗會顯示的狀態變更*活動 A*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

當我們按一下**啟動活動 B**按鈕，我們看到*活動 A*暫停和停止時*活動 B*經歷其狀態變更： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

如此一來，*活動 B*已啟動並顯示取代*活動 A*: 

[![活動 B 畫面](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

當我們按一下**回** 按鈕，*活動 B*終結並*活動 A*繼續： 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```
### <a name="adding-a-click-counter"></a>加入按一下計數器

接下來，我們要變更應用程式，讓我們有一個按鈕，會計算並顯示它按下的次數。 首先，我們要加上`_counter`執行個體變數，以`MainActivity`:

```csharp
int _counter = 0;
```

接下來，讓我們編輯**Resource/layout/Main.axml**版面配置檔案，並加入新`clickButton`顯示使用者已按一下按鈕的次數。 產生**Main.axml**應該如下所示： 

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

讓我們將下列程式碼新增至結尾[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)方法中的`MainActivity`&ndash;這段程式碼控點按事件`clickButton`:

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

當我們所建置，並再次執行應用程式，新按鈕會出現並遞增，並顯示的值`_counter`上每按一下：

[![新增觸控計數](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

但是，當我們在旋轉成橫向模式的裝置時，此計數會遺失：

[![旋轉成橫式設定設回零的計數](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

檢查應用程式的輸出，我們會看到*活動 A*已暫停、 停止、 終結、 重新建立、 重新啟動，然後從直式旋轉成橫向模式期間繼續執行： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

因為*活動 A*就會毀損及重新建立一次旋轉裝置時，其執行個體狀態將會遺失。 接下來，我們將加入程式碼儲存與還原執行個體的狀態。

### <a name="adding-code-to-preserve-instance-state"></a>程式碼加入至保留執行個體狀態

讓我們新增方法以`MainActivity`儲存執行個體的狀態。 之前*活動 A*是損毀，Android 會自動會呼叫[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) ，並傳遞[配套](https://developer.xamarin.com/api/type/Android.OS.Bundle/)我們可以使用來儲存我們的執行個體狀態。 讓我們用來儲存我們點擊計數的整數值：

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

當*活動 A*重新建立，並繼續，Android 會將傳遞`Bundle`回我們`OnCreate`方法。 讓我們加入程式碼來`OnCreate`還原`_counter`值從傳入的`Bundle`。 新增下列程式碼行前面，只是其中`clickbutton`定義： 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

建置並執行應用程式一次，然後按一下第二個按鈕幾次。 當我們在旋轉成橫向模式的裝置時，會保留計數 ！

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

再[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/)已呼叫方法，新`OnSaveInstanceState`方法呼叫來儲存`_counter`中的值`Bundle`。 Android 傳遞這`Bundle`回到我們呼叫時我們`OnCreate`方法，而我們能夠用來還原`_counter`講的值。


## <a name="summary"></a>總結

在此逐步解說中，我們使用我們所知的活動開發週期來保留狀態的資料。 



## <a name="related-links"></a>相關連結

- [ActivityLifecycle （範例）](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)
- [活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Android 活動](https://developer.xamarin.com/api/type/Android.App.Activity/)
