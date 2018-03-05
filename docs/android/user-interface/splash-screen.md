---
title: "啟動顯示畫面"
description: "Android 應用程式需要一些時間才啟動，特別是應用程式第一次啟動時在裝置上。 啟動顯示畫面可能會顯示開始向上進行使用者或指示商標。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 9acb1ad6ab1425edb98b938e8c03edc3704f50ae
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="splash-screen"></a>啟動顯示畫面

_Android 應用程式需要一些時間才啟動，特別是應用程式第一次啟動時在裝置上。啟動顯示畫面可能會顯示開始向上進行使用者或指示商標。_

<a name="overview" />

## <a name="overview"></a>總覽

Android 應用程式需要一些時間，以啟動，尤其是在第一次在裝置上執行應用程式 (有時候這指_冷啟動_)。 啟動顯示畫面可能會顯示進度，使用者啟動，或它可能會顯示以識別並升級應用程式的商標資訊。

本指南將告訴您在 Android 應用程式中實作的啟動顯示畫面的其中一種技術。 它包含在下列步驟：

1.  建立啟動顯示畫面的 drawable 資源。

2.  定義新的佈景主題，以顯示 drawable 資源。

3.  會使用先前步驟中建立的主題所定義的啟動顯示畫面作為應用程式中加入新的活動。

[![後面接著應用程式畫面範例 Xamarin 標誌啟動顯示畫面](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png)


<a name="requirements" />

## <a name="requirements"></a>需求

本指南假設應用程式為目標 Android API 層級 15 (Android 4.0.3) 或更高版本。 應用程式也必須擁有**Xamarin.Android.Support.v4**和**Xamarin.Android.Support.v7.AppCompat** NuGet 封裝加入至專案。

所有的程式碼和本指南中的 XML，請參閱[啟動顯示畫面](https://developer.xamarin.com/samples/monodroid/SplashScreen)本指南中的範例專案。

<a name="implement" />

## <a name="implementing-a-splash-screen"></a>實作的啟動顯示畫面

用於轉譯並顯示啟動顯示畫面最快方式是建立自訂佈景主題，並將其套用到活動中表現啟動顯示畫面。 轉譯活動時，它會載入佈景主題，並套用 drawable 資源 （佈景主題所參考） 至活動的背景。 這種方法可避免需要建立版面配置檔案。

啟動顯示畫面會實作為活動顯示加上品牌 drawable，會執行任何初始設定，並啟動任何工作。 一旦應用程式具有引導，啟動顯示畫面活動啟動主要的活動，並移除本身應用程式的上一頁堆疊。

<a name="drawable" />

### <a name="creating-a-drawable-for-the-splash-screen"></a>正在建立 「 Drawable 啟動顯示畫面

啟動顯示畫面會顯示在啟動顯示畫面的活動背景 drawable XML。 必須要用來顯示影像的點陣圖影像 （例如 JPG 或 PNG）。

在本指南中，我們使用[層清單](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)從左至中應用程式啟動顯示畫面影像。 下列程式碼片段是範例`drawable`資源使用`layer-list`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item>
    <color android:color="@color/splash_background"/>
  </item>
  <item>
    <bitmap
        android:src="@drawable/splash"
        android:tileMode="disabled"
        android:gravity="center"/>
  </item>
</layer-list>
```

這`layer-list`會置中啟動顯示畫面影像**splash.png**上所指定的背景`@color/splash_background`資源。
將這個檔案中的放**資源/drawable**資料夾 (例如， **Resources/drawable/splash_screen.xml**)。

啟動顯示畫面 drawable 建立之後下, 一個步驟是建立啟動顯示畫面的佈景主題。

<a name="theme" />

### <a name="implementing-a-theme"></a>實作佈景主題

若要建立自訂佈景主題的啟動顯示畫面的活動，請編輯 （或加入） 檔案**values/styles.xml**並建立新`style`啟動顯示畫面項目。 範例**values/style.xml**檔案如下所示使用`style`名為**MyTheme.Splash**:

```xml
<resources>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
  </style>

  <style name="MyTheme" parent="MyTheme.Base">
  </style>

  <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
    <item name="android:windowBackground">@drawable/splash_screen</item>
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowFullscreen">true</item>
  </style>
</resources>
```

**MyTheme.Splash**是非常斯巴達&ndash;它宣告視窗背景、 明確移除標題列視窗中，然後將宣告它是全螢幕。 如果您想要建立啟動顯示畫面，來模擬您的應用程式的 UI 活動 「 充氣 」 的第一個配置之前，您可以使用`windowContentOverlay`而不是`windowBackground`樣式定義中。 在此情況下，您也必須修改**splash_screen.xml** drawable，使其顯示您的 UI 的模擬。

<a name="activity" />

### <a name="create-a-splash-activity"></a>建立啟動顯示活動

現在我們需要有我們開頭顯示畫面影像，並執行任何啟動工作以啟動 Android 的新活動。 下列程式碼是完整的啟動顯示畫面實作的範例：

```csharp
[Activity(Theme = "@style/MyTheme.Splash", MainLauncher = true, NoHistory = true)]
public class SplashActivity : AppCompatActivity
{
    static readonly string TAG = "X:" + typeof(SplashActivity).Name;

    public override void OnCreate(Bundle savedInstanceState, PersistableBundle persistentState)
    {
        base.OnCreate(savedInstanceState, persistentState);
        Log.Debug(TAG, "SplashActivity.OnCreate");
    }

    // Launches the startup task
    protected override void OnResume()
    {
        base.OnResume();
        Task startupWork = new Task(() => { SimulateStartup(); });
        startupWork.Start();
    }

    // Simulates background work that happens behind the splash screen
    async void SimulateStartup ()
    {
        Log.Debug(TAG, "Performing some startup work that takes a bit of time.");
        await Task.Delay (8000); // Simulate a bit of startup work.
        Log.Debug(TAG, "Startup work is finished - starting MainActivity.");
        StartActivity(new Intent(Application.Context, typeof (MainActivity)));
    }
}
```

`SplashActivity` 明確使用已建立在前一個區段中，覆寫應用程式的預設佈景主題的主題。
不需要載入中的配置`OnCreate`如主題宣告 drawable 做為背景。

請務必設定`NoHistory=true`屬性，讓從上一頁堆疊移除的活動。 若要防止 [上一頁] 按鈕取消啟動程序，您也可以覆寫`OnBackPressed`，並讓它執行任何動作：

```csharp
public override void OnBackPressed() { }
```

以非同步方式在執行啟動工作`OnResume`。 這是必要的以便啟動工作不會變慢或延遲啟動螢幕的外觀。 當工作完成時，`SplashActivity`將會啟動`MainActivity`和使用者可能會開始與應用程式互動。

這個新`SplashActivity`設定做為啟動器活動的應用程式，藉由設定`MainLauncher`屬性`true`。 因為`SplashActivity`現在啟動器活動中，您必須編輯`MainActivity.cs`，並移除`MainLauncher`屬性從`MainActivity`:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

<a name="summary" />

## <a name="summary"></a>總結

本指南所討論的一種方式實作 Xamarin.Android 應用程式; 中的啟動顯示畫面也就將自訂的佈景主題套用至啟動活動。


## <a name="related-links"></a>相關連結

- [啟動顯示畫面 （範例）](https://developer.xamarin.com/samples/monodroid/SplashScreen)
- [圖層清單 Drawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [ 材料設計模式-啟動螢幕](https://www.google.com/design/spec/patterns/launch-screens.html)
