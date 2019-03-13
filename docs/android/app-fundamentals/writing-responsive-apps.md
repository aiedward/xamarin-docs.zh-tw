---
title: 撰寫回應式應用程式
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: a1642c4cbb790cf09d2a31e629408afc61d5b7ab
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121772"
---
# <a name="writing-responsive-applications"></a>撰寫回應式應用程式

其中一個要維護回應性的 GUI 的索引鍵是在背景執行緒上的長時間執行工作，這樣並不會封鎖 GUI。 例如，假設我們想要計算要向使用者顯示的值，但此值需要 5 秒鐘才能計算：

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

這將會運作，但應用程式會 「 擱置 」 的 5 秒時計算的值。 在此期間，應用程式不會回應任何使用者互動。 若要解決這個問題，我們想要執行我們計算背景執行緒上：

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

現在我們計算背景執行緒上的值，以在計算期間，我們的 GUI 保持回應性。 不過，在計算完成時，我們的應用程式當機，保留此記錄檔中：

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

這是因為您必須更新從 GUI 執行緒 GUI。 我們的程式碼更新的 GUI，從執行緒集區的執行緒，導致應用程式損毀。 我們需要計算我們在背景執行緒上的值，但接著我們在 GUI 執行緒處理的更新[Activity.RunOnUIThread](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)):

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

此程式碼可以正常運作。 GUI 可保持回應性，並取得正確更新，一旦計算下限。

請注意這項技術不只是用來計算耗費資源的值。 它可以用於任何可以在背景中，例如 web 服務呼叫或網際網路資料下載完成的長時間執行工作。
