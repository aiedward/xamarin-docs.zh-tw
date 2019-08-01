---
title: 啟動顯示畫面
description: Android 應用程式需要一些時間才能啟動, 特別是當應用程式第一次在裝置上啟動時。 啟動顯示畫面可能會顯示使用者的開始進度, 或表示商標。
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/06/2018
ms.openlocfilehash: 30e471174d30e58b126c174fd4eacfdbe87d97b0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646054"
---
# <a name="splash-screen"></a>啟動顯示畫面

_Android 應用程式需要一些時間才能啟動, 特別是當應用程式第一次在裝置上啟動時。啟動顯示畫面可能會顯示使用者的開始進度, 或表示商標。_


## <a name="overview"></a>總覽

Android 應用程式需要一些時間才會啟動, 特別是在第一次在裝置上執行應用程式時 (有時這稱為 _「冷啟動」_ )。 啟動顯示畫面可能會顯示使用者的開始進度, 也可能會顯示商標資訊以識別並推廣應用程式。

本指南討論在 Android 應用程式中執行啟動顯示畫面的一項技巧。 其涵蓋下列步驟:

1.  建立啟動顯示畫面的可繪製資源。

2.  定義會顯示可繪製資源的新主題。

3.  將新活動新增至應用程式, 以做為上一個步驟中所建立主題所定義的啟動顯示畫面。

[![範例 Xamarin 標誌啟動顯示畫面, 後面接著應用程式畫面](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)


## <a name="requirements"></a>需求

本指南假設應用程式是以 Android API 層級 15 (Android 4.0.3) 或更高版本為目標。 應用程式也必須將 AppCompat NuGet 套件新增至專案中, 並將其加入至**v7。**

本指南中的所有程式碼和 XML 均可在本指南的[SplashScreen](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen)範例專案中找到。


## <a name="implementing-a-splash-screen"></a>執行啟動顯示畫面

轉譯和顯示啟動顯示畫面的最快方式是建立自訂主題, 並將其套用至展示啟動顯示畫面的活動。 當活動呈現時, 它會載入主題, 並將可繪製的資源 (由主題參考) 套用至活動的背景。 這種方法可避免建立版面配置檔案的需求。

啟動顯示畫面會實作為活動, 以顯示品牌化的可繪製、執行任何初始化, 並啟動任何工作。 一旦應用程式啟動後, 啟動顯示畫面活動就會開始主要活動, 並從應用程式後端移除其本身。


### <a name="creating-a-drawable-for-the-splash-screen"></a>建立啟動顯示畫面的可繪製

啟動顯示畫面會在啟動顯示畫面活動的背景中顯示 XML 可繪製。 您必須使用點陣圖影像 (例如 PNG 或 JPG) 來顯示影像。

在本指南中, 我們會使用[圖層清單](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList), 將啟動顯示畫面影像置中在應用程式中。 下列程式碼片段是使用的`drawable` `layer-list`資源範例:

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

這`layer-list`會在`@color/splash_background`資源所指定的背景上, 將啟動顯示影像置中 **.png。** 將此 XML 檔案放在**resources/可繪製**資料夾中 (例如**resources/可繪製/splash_screen**)。

建立啟動顯示畫面之後, 下一個步驟是建立啟動顯示畫面的主題。


### <a name="implementing-a-theme"></a>執行主題

若要建立啟動顯示畫面活動的自訂主題, 請編輯 (或新增) [檔案**值/樣式 .xml** ], 然後`style`為啟動顯示畫面建立新的元素。 範例**值/樣式 .xml**檔案如下所示, `style`名為**MyTheme。啟動**顯示:

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

**MyTheme**是非常 spartan &ndash;的, 它會宣告視窗背景、明確地移除視窗中的標題列, 並宣告它是全螢幕。 如果您想要建立啟動顯示畫面, 以在活動擴大第一個版面配置之前模擬應用程式的 UI, 您可以`windowContentOverlay` `windowBackground`在樣式定義中使用, 而不是。 在此情況下, 您也必須修改**splash_screen**可繪製, 使其顯示 UI 的模擬。


### <a name="create-a-splash-activity"></a>建立啟動顯示活動

現在, 我們需要新的 Android 活動, 以啟動具有啟動顯示畫面的啟動程式, 並執行任何啟動工作。 下列程式碼是完整啟動顯示畫面執行的範例:

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

`SplashActivity`明確使用上一節中所建立的主題, 覆寫應用程式的預設主題。
當主題宣告可繪製為背景時, `OnCreate`不需要在中載入版面配置。

請務必設定`NoHistory=true`屬性, 以便將活動從後端堆疊中移除。 若要防止 [上一頁] 按鈕取消啟動程式, 您也可以覆`OnBackPressed`寫並讓它不執行任何動作:

```csharp
public override void OnBackPressed() { }
```

啟動工作是在中`OnResume`以非同步方式執行。 這是必要的, 讓啟動工作不會變慢或延遲啟動畫面的外觀。 當工作完成時, 將`SplashActivity`會啟動`MainActivity` , 且使用者可能會開始與應用程式互動。

這個新`SplashActivity`的會設定為應用程式的啟動器活動, 方法是`MainLauncher`將屬性`true`設定為。 因為`SplashActivity`現在是啟動器活動, 所以您必須編輯`MainActivity.cs`, 並從中`MainLauncher` `MainActivity`移除屬性:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>橫向模式

在先前步驟中所執行的啟動顯示畫面, 會在直向和橫向模式中正確顯示。 不過, 在某些情況下, 垂直和橫向模式必須有個別的啟動顯示畫面 (例如, 如果啟動顯示影像是全螢幕)。

若要新增橫向模式的啟動顯示畫面, 請使用下列步驟:

1. 在 [**資源/可繪製**] 資料夾中, 新增您想要使用之啟動顯示畫面影像的橫向版本。 在此範例中, **splash_logo_land**是在上述範例中使用之標誌的橫向版本 (它會使用白色字母, 而不是藍色)。

2. 在 [**資源/可繪製**] 資料夾中, 建立稍早`layer-list`定義之可繪製的橫向版本 (例如, **splash_screen_land**)。 在此檔案中, 將點陣圖路徑設定為啟動顯示畫面影像的橫向版本。 在下列範例中, **splash_screen_land**使用**splash_logo_land**:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
      <item>
        <color android:color="@color/splash_background"/>
      </item>
      <item>
        <bitmap
            android:src="@drawable/splash_logo_land"
            android:tileMode="disabled"
            android:gravity="center"/>
      </item>
    </layer-list>
    ```

3.  建立 [**資源]/[值]-[land** ] 資料夾 (如果尚未存在)。

4.  將檔案**colors**和**style .xml**新增至**值-land** (這些都可以從現有的**值/色彩. xml**和**值/樣式 .xml**檔案複製和修改)。

5.  修改**values-land/style .xml** , 使其使用的可繪製`windowBackground`的橫向版本。 在此範例中, 會使用**splash_screen_land** :

    ```xml
    <resources>
      <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
      </style>
        <style name="MyTheme" parent="MyTheme.Base">
      </style>
      <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
        <item name="android:windowBackground">@drawable/splash_screen_land</item>
        <item name="android:windowNoTitle">true</item>  
        <item name="android:windowFullscreen">true</item>  
        <item name="android:windowContentOverlay">@null</item>  
        <item name="android:windowActionBar">true</item>  
      </style>
    </resources>
    ```

6.  修改**values-land/colors** , 以設定要用於啟動顯示畫面之橫向版本的色彩。 在此範例中, 針對橫向模式, 啟動顯示背景色彩會變更為藍色:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <resources>
      <color name="primary">#2196F3</color>
      <color name="primaryDark">#1976D2</color>
      <color name="accent">#FFC107</color>
      <color name="window_background">#F5F5F5</color>
      <color name="splash_background">#3498DB</color>
    </resources>
    ```

7.  重新建立並執行應用程式。 將裝置旋轉為橫向模式, 同時顯示啟動顯示畫面。 啟動顯示畫面會變更為 [橫向] 版本:

    [![啟動顯示畫面到橫向模式的旋轉](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)


請注意, 使用橫向模式啟動顯示畫面不一定會提供順暢的體驗。 根據預設, Android 會以直向模式啟動應用程式, 並將其轉換成橫向模式, 即使裝置已處於橫向模式也一樣。 因此, 如果在裝置處於橫向模式時啟動應用程式, 則裝置會短暫呈現直向啟動顯示畫面, 然後從直向到橫向啟動顯示畫面的動畫上旋轉。 可惜的`ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape`是, 即使在啟動顯示活動的旗標中指定了, 還是會進行這個初始的直向橫向轉換。 解決這項限制的最佳方式, 就是建立單一啟動顯示畫面影像, 以便在直向和橫向模式中正確呈現。


## <a name="summary"></a>總結

本指南討論了在 Xamarin Android 應用程式中執行啟動顯示畫面的一種方式;亦即, 將自訂主題套用至啟動活動。


## <a name="related-links"></a>相關連結

- [SplashScreen (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen)
- [圖層清單繪製](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [材質設計模式-啟動畫面](https://material.io/design/communication/launch-screen.html#usage)
