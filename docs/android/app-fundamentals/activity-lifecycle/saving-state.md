---
title: 逐步解說 - 儲存活動狀態
description: 我們在活動生命週期指南中涵蓋了儲存狀態的理論;現在，讓我們逐步解說一個範例。
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: e449e6a62d0c8ca283f20c689477c1f1482611c5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017008"
---
# <a name="walkthrough---saving-the-activity-state"></a>逐步解說 - 儲存活動狀態

_我們在活動生命週期指南中涵蓋了儲存狀態的理論;現在，讓我們逐步解說一個範例。_

## <a name="activity-state-walkthrough"></a>活動狀態逐步解說

讓我們開啟**ActivityLifecycle_Start**專案（在[ActivityLifecycle](https://docs.microsoft.com/samples/xamarin/monodroid-samples/activitylifecycle)範例中）、建立它，然後加以執行。 這是一個非常簡單的專案，其中包含兩個活動來示範活動生命週期，以及如何呼叫各種生命週期方法。 當您啟動應用程式時，會顯示 `MainActivity` 的畫面：

[![活動的畫面](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>查看狀態轉換

這個範例中的每個方法都會寫入 IDE 應用程式輸出視窗，以指出活動狀態。 （若要在 Visual Studio 中開啟 [輸出] 視窗，請輸入**CTRL-ALT-O**; 若要在 Visual Studio for Mac 中開啟 [輸出] 視窗，請按一下 [ **View > pad] > [應用程式輸出**]）。

當應用程式第一次啟動時，[輸出] 視窗會顯示*活動 A*的狀態變更： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

當我們按一下 [**啟動活動 b** ] 按鈕時，我們會看到*活動在* *活動 b*經歷其狀態變更時暫停和停止： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

如此一來，*活動 B*就會啟動並顯示，以取代*活動 a*： 

[![活動 B 畫面](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

當我們按 [**上一步**] 按鈕時，會終結*活動 B*並繼續*活動 A* ： 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```

### <a name="adding-a-click-counter"></a>加入 Click 計數器

接下來，我們要變更應用程式，讓我們有一個會計算的按鈕，並顯示按一下的次數。 首先，讓我們將 `_counter` 執行個體變數新增至 `MainActivity`：

```csharp
int _counter = 0;
```

接下來，我們將編輯**資源/layout/axml**配置檔案，然後加入新的 `clickButton`，以顯示使用者按一下按鈕的次數。 產生的**axml**應該如下所示： 

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

讓我們在 `MainActivity` 中，將下列程式碼新增至[OnCreate](xref:Android.App.Activity.OnCreate*)方法的結尾，&ndash; 此程式碼會處理來自 `clickButton`的 click 事件：

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

當我們重新建立並執行應用程式時，會出現新的按鈕，會遞增並顯示每次按一下時的 `_counter` 值：

[![新增觸控計數](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

但當我們將裝置旋轉為橫向模式時，會遺失此計數：

[![旋轉至橫向會將計數設定回零](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

檢查應用程式輸出，我們看到*活動 A*已暫停、已停止、已損毀、重新建立、重新開機，然後在從直向到橫向模式的旋轉期間繼續執行： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

由於在旋轉裝置時，*活動 A*會終結並重新建立，因此其實例狀態會遺失。 接下來，我們將新增程式碼以儲存和還原實例狀態。

### <a name="adding-code-to-preserve-instance-state"></a>加入程式碼以保留實例狀態

讓我們新增方法來 `MainActivity` 以儲存實例狀態。 在*活動 A*終結之前，Android 會自動呼叫[OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) ，並傳入可用來儲存實例[狀態的組合。](xref:Android.OS.Bundle) 讓我們使用它將我們的點擊計數儲存為整數值：

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

重新*建立並繼續活動 A*時，Android 會將此 `Bundle` 回我們的 `OnCreate` 方法。 讓我們將程式碼新增至 `OnCreate`，以從傳入的 `Bundle`還原 `_counter` 值。 在定義 `clickbutton` 的那一行前面加入下列程式碼： 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

重新建立並執行應用程式，然後按一下第二個按鈕數次。 當我們將裝置旋轉為橫向模式時，會保留計數！

[![旋轉畫面時，會顯示四個保留的計數](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)

讓我們查看 [輸出] 視窗以查看發生了什麼狀況：

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

呼叫[OnStop](xref:Android.App.Activity.OnStop)方法之前，我們會呼叫新的 `OnSaveInstanceState` 方法，以在 `Bundle`中儲存 `_counter` 值。 當 Android 呼叫我們的 `OnCreate` 方法時，會將此 `Bundle` 回給我們，而我們可以使用它將 `_counter` 值還原到我們離開的地方。

## <a name="summary"></a>總結

在此逐步解說中，我們已使用活動生命週期的知識來保留狀態資料。

## <a name="related-links"></a>相關連結

- [ActivityLifecycle （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/activitylifecycle)
- [活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Android 活動](xref:Android.App.Activity)
