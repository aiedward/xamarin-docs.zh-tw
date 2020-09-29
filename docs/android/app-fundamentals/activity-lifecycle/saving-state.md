---
title: 逐步解說 - 儲存活動狀態
description: 我們涵蓋了在活動生命週期指南中儲存狀態的理論;現在，讓我們逐步解說範例。
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: f6197c2d8ffc028f28291134efa0d3dd87375cc8
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456364"
---
# <a name="walkthrough---saving-the-activity-state"></a>逐步解說 - 儲存活動狀態

_我們涵蓋了在活動生命週期指南中儲存狀態的理論;現在，讓我們逐步解說範例。_

## <a name="activity-state-walkthrough"></a>活動狀態逐步解說

讓我們在[ActivityLifecycle](/samples/xamarin/monodroid-samples/activitylifecycle)範例) 中開啟**ActivityLifecycle_Start**專案 (，並加以建立並執行。 這是非常簡單的專案，其中有兩個活動可示範活動生命週期，以及如何呼叫各種生命週期方法。 當您啟動應用程式時，畫面上 `MainActivity` 會顯示：

[![活動 A 畫面](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>查看狀態轉換

此範例中的每個方法都會寫入 IDE 應用程式輸出視窗，以指出活動狀態。  (若要在 Visual Studio 中開啟 [輸出] 視窗，請輸入 **CTRL-ALT-O**;若要在 Visual Studio for Mac 中開啟 [輸出] 視窗，請按一下 [ **View > pad > 應用程式輸出**]。 ) 

當應用程式第一次啟動時，[輸出] 視窗會顯示 *活動 A*的狀態變更： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

當我們按一下 [ **開始活動 b** ] 按鈕時，我們會看到 *活動 A* 暫停和停止，而 *活動 B* 會經歷其狀態變更： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

如此一來，就會啟動並顯示 *活動 B* 來取代 *活動 a*： 

[![活動 B 畫面](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

當我們按一下 [ **上一步** ] 按鈕時， *活動 B* 會終結，而 *活動 A* 會繼續執行： 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```

### <a name="adding-a-click-counter"></a>加入 Click 計數器

接下來，我們要變更應用程式，讓我們有一個計數的按鈕，並顯示按下它的次數。 首先，讓我們將 `_counter` 執行個體變數新增至 `MainActivity` ：

```csharp
int _counter = 0;
```

接下來，讓我們編輯 **資源/配置/主要 .axml** 配置檔案，然後加入新 `clickButton` 的，以顯示使用者按一下按鈕的次數。 產生的 **主要 .axml** 應該如下所示： 

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

讓我們將下列程式碼新增至 [>oncreate](xref:Android.App.Activity.OnCreate*) 方法的結尾， `MainActivity` &ndash; 此程式碼會處理從下列程式碼中的 click 事件 `clickButton` ：

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

當我們重新建立並執行應用程式時，會出現新的按鈕，讓您 `_counter` 在每次點擊時，遞增並顯示的值：

[![新增觸控計數](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

但是，當我們將裝置旋轉為橫向模式時，此計數會遺失：

[![旋轉至橫向會將計數設定回零](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

檢查應用程式輸出時，我們看到 *活動 A* 已暫停、停止、損毀、重新建立、重新開機，然後在從直向模式輪替期間繼續進行： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

因為 *活動 A* 會在裝置旋轉時損毀並重新建立，所以其實例狀態會遺失。 接下來，我們將新增程式碼，以儲存及還原實例狀態。

### <a name="adding-code-to-preserve-instance-state"></a>加入程式碼以保留實例狀態

讓我們新增方法來 `MainActivity` 儲存實例狀態。 在 *活動 A* 終結之前，Android 會自動呼叫 [OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) ，並傳入可用於儲存實例 [狀態的組合](xref:Android.OS.Bundle) 。 讓我們使用它來將按一下計數儲存為整數值：

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

當 *活動 A 重新建立* 並繼續時，Android 會將此 `Bundle` 傳回給我們的 `OnCreate` 方法。 讓我們新增程式碼，以 `OnCreate` `_counter` 從傳入的還原值 `Bundle` 。 在定義的行之前，新增下列 `clickbutton` 程式碼： 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

再次建立並執行應用程式，然後按一下第二個按鈕數次。 當我們將裝置旋轉為橫向模式時，會保留計數！

[![旋轉螢幕會顯示四個保留的計數](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)

讓我們看看 [輸出] 視窗以查看發生的情況：

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

在呼叫 [OnStop](xref:Android.App.Activity.OnStop) 方法之前，我們會 `OnSaveInstanceState` 呼叫新方法，以將值儲存 `_counter` 在中 `Bundle` 。 `Bundle`當 Android 呼叫我們的方法時，會將此值傳回給我們 `OnCreate` ，而且我們可以用它來將 `_counter` 值還原到我們離開的位置。

## <a name="summary"></a>摘要

在此逐步解說中，我們已使用活動生命週期的知識來保存狀態資料。

## <a name="related-links"></a>相關連結

- [ActivityLifecycle (範例) ](/samples/xamarin/monodroid-samples/activitylifecycle)
- [活動開發週期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Android 活動](xref:Android.App.Activity)