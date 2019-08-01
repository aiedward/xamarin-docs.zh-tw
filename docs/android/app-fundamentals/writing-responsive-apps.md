---
title: 撰寫回應式應用程式
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: e3f7d788e71718f4ca1336b7906cf3d63bf07f32
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509203"
---
# <a name="writing-responsive-applications"></a>撰寫回應式應用程式

維護回應式 GUI 的其中一個關鍵, 就是在背景執行緒上進行長時間執行的工作, 因此 GUI 不會遭到封鎖。 假設我們想要計算要向使用者顯示的值, 但是該值需要5秒來計算:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        SlowMethod ();
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

這會有效, 但在計算值時, 應用程式將會「停止回應」5秒。 在這段期間, 應用程式將不會回應任何使用者互動。 為了解決這個情況, 我們想要在背景執行緒上進行計算:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

現在, 我們會在背景執行緒上計算值, 讓 GUI 在計算期間保持回應。 不過, 當計算完成時, 我們的應用程式會當機, 並將其保留在記錄檔中:

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

這是因為您必須從 GUI 執行緒更新 GUI。 我們的程式碼會從 ThreadPool 執行緒更新 GUI, 導致應用程式損毀。 我們需要在背景執行緒上計算值, 但在 GUI 執行緒上進行更新時, 會使用[RunOnUIThread](xref:Android.App.Activity.RunOnUiThread*)進行處理:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        RunOnUiThread (() => textview.Text = "Method Complete");
    }
}
```

此程式碼會如預期般運作。 下限計算之後, 這個 GUI 會保持回應, 並適當地更新。

請注意, 這項技術不是用來計算昂貴的價值。 它可用於任何長時間執行的工作, 可在背景中完成, 例如 web 服務呼叫或下載網際網路資料。
