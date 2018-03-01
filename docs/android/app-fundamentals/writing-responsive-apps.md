---
title: "撰寫應用程式的回應"
ms.topic: article
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 67cad90f99614c3df90f483c9c6dbcec2df5113a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="writing-responsive-applications"></a>撰寫應用程式的回應

其中一個金鑰維護回應 GUI 是背景執行緒上的長時間執行工作，這樣 GUI 不會遭到封鎖。 例如，假設我們想要計算的值，以顯示給使用者，但不同之值會計算 5 秒：

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

這也能運作，但應用程式將會 「 擱置 」 5 秒時計算的值。 在此期間，應用程式不會回應任何使用者互動。 若要解決這個問題，我們想要在背景執行緒上的我們計算：

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

現在我們計算背景執行緒上的值，因此我們 GUI 會在計算期間保持回應。 不過，當計算完成時，我們的應用程式當機，保留此記錄檔中：

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

這是因為您必須更新從 GUI 執行緒 GUI。 我們的程式碼會更新從執行緒集區執行緒，造成損毀的應用程式的 GUI。 我們需要計算，在背景執行緒上的我們值，但接著我們更新 GUI，在執行緒上處理與[Activity.RunOnUIThread](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)):

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

此程式碼可以正常運作。 此 GUI 保持回應，並取得正確更新，一旦計算下限。

請注意這項技術不只用來計算耗費資源的值。 它可以用於任何長時間執行工作，可以在背景中，例如 web 服務呼叫或網際網路資料下載完成。
