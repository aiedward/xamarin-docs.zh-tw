---
title: 啟動顯示畫面
description: Android 應用程式需要一些時間才能啟動，尤其是當應用程式第一次在裝置上啟動時。 啟動顯示畫面可能會向使用者顯示開始進度，或指出商標。
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/02/2019
ms.openlocfilehash: bd24fbf6ce99e87c6a7f4ccd8cceef6cbe826f0f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370529"
---
# <a name="splash-screen"></a>啟動顯示畫面

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/monodroid-samples/splashscreen)

_Android 應用程式需要一些時間才能啟動，尤其是當應用程式第一次在裝置上啟動時。啟動顯示畫面可能會向使用者顯示開始進度，或指出商標。_

## <a name="overview"></a>概觀

Android 應用程式需要一些時間才能啟動，尤其是在第一次在裝置上執行應用程式時 (有時這稱為 _冷啟動_ ) 。 啟動顯示畫面可能會向使用者顯示開始進度，也可能會顯示商標資訊來識別及升級應用程式。

本指南將討論在 Android 應用程式中實施啟動顯示畫面的一種技術。 其中涵蓋下列步驟：

1. 建立啟動顯示畫面的可繪製資源。

2. 定義會顯示可繪製資源的新主題。

3. 將新的活動加入至應用程式，以做為上一個步驟中建立的主題所定義的啟動顯示畫面。

[![範例 Xamarin 標誌啟動顯示畫面，後面接著應用程式畫面](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)

## <a name="requirements"></a>需求

本指南假設應用程式是以 Android API 層級21或更高版本為目標。 應用程式也 **必須將** v7 新增至專案中，才能使用 **AppCompat** NuGet 套件的應用程式。

本指南中的所有程式碼和 XML 都可在本指南的 [SplashScreen](/samples/xamarin/monodroid-samples/splashscreen) 範例專案中找到。

## <a name="implementing-a-splash-screen"></a>執行啟動顯示畫面

轉譯和顯示啟動顯示畫面的最快速方式是建立自訂主題，並將其套用至展示啟動顯示畫面的活動。 當活動轉譯時，會載入主題，並將主題) 參考的可繪製資源 (套用至活動的背景。 這種方法可避免建立版面配置檔案的需求。

啟動顯示畫面會實作為活動，顯示品牌化可繪製、執行任何初始化，以及啟動任何工作。 當應用程式啟動之後，啟動顯示畫面活動會啟動主要活動，並從應用程式後端堆疊中移除本身。

### <a name="creating-a-drawable-for-the-splash-screen"></a>建立啟動顯示畫面的可繪製

啟動顯示畫面會在啟動顯示畫面活動的背景中顯示 XML 可繪製。 您必須使用點陣圖影像 (例如 PNG 或 JPG) ，才能顯示影像。

範例應用程式會定義一個稱為 **splash_screen.xml** 的繪製。 這個可繪製的會使用 [圖層清單](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) 來置入應用程式中的啟動顯示畫面影像，如下列 xml 所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item>
    <color android:color="@color/splash_background"/>
  </item>
  <item>
    <bitmap
        android:src="@drawable/splash_logo"
        android:tileMode="disabled"
        android:gravity="center"/>
  </item>
</layer-list>
```

這會將 `layer-list` 啟動顯示影像以資源所指定的背景色彩為中心 `@color/splash_background` 。 範例應用程式會在 **資源/值/colors.xml** 檔案中定義此色彩：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  ...
  <color name="splash_background">#FFFFFF</color>
</resources>
```

如需物件的詳細資訊， `Drawable` 請參閱 [Android 可繪製的 Google 檔](https://developer.android.com/reference/android/graphics/drawable/Drawable)。

### <a name="implementing-a-theme"></a>執行主題

若要建立啟動顯示畫面活動的自訂主題，請編輯 (或新增) 檔案 **值/styles.xml** ，並 `style` 為啟動顯示畫面建立新的元素。 範例 **值/style.xml** 檔案如下所示，其 `style` 名為 **MyTheme** ：

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
    <item name="android:windowContentOverlay">@null</item>  
    <item name="android:windowActionBar">true</item>  
  </style>
</resources>
```

**MyTheme** 會很 spartan 地宣告 &ndash; 視窗背景、明確地從視窗中移除標題列，並將它宣告為全螢幕。 如果您想要在活動擴大第一個配置之前，建立模擬應用程式 UI 的啟動顯示畫面，您可以 `windowContentOverlay` `windowBackground` 在樣式定義中使用而不是。 在此情況下，您也必須修改 **splash_screen.xml** 可繪製的，使其顯示 UI 的模擬。

### <a name="create-a-splash-activity"></a>建立啟動顯示活動

現在，我們需要新的 Android 啟動活動，其中包含啟動顯示映射並執行任何啟動工作。 下列程式碼是完整啟動顯示畫面的範例：

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

`SplashActivity` 明確地使用在上一節中建立的主題，覆寫應用程式的預設主題。
`OnCreate`因為主題將可繪製為背景的，所以不需要在中載入版面配置。

請務必設定 `NoHistory=true` 屬性，以便從後端堆疊中移除活動。 若要防止 [上一頁] 按鈕取消啟動進程，您也可以覆寫 `OnBackPressed` 並讓它不執行任何動作：

```csharp
public override void OnBackPressed() { }
```

啟動工作會在中以非同步方式執行 `OnResume` 。 這是必要的，如此一來，啟動的工作就不會變慢或延遲啟動畫面的外觀。 當工作完成時， `SplashActivity` 將會啟動， `MainActivity` 且使用者可以開始與應用程式互動。

這個新的 `SplashActivity` 設定為應用程式的啟動程式活動，方法是將 `MainLauncher` 屬性設為 `true` 。 由於 `SplashActivity` 現在是啟動程式活動，因此您必須編輯 `MainActivity.cs` 和移除 `MainLauncher` 中的屬性 `MainActivity` ：

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>橫向模式

在先前步驟中所執行的啟動顯示畫面，將在直向和橫向模式中正確顯示。 不過，在某些情況下，直向和橫向模式必須有個別的啟動顯示畫面 (例如，如果啟動顯示影像是全螢幕) 。

若要加入橫向模式的啟動顯示畫面，請使用下列步驟：

1. 在 [ **資源/可繪製** ] 資料夾中，新增您想要使用之啟動顯示畫面影像的橫向版本。 在此範例中， **splash_logo_land.png** 是上述範例中所使用之標誌的橫向版本 (它使用的是白色字母，而非藍色的) 。

2. 在 [ **資源/可繪製** ] 資料夾中，建立稍 `layer-list` 早定義之可繪製的橫向版本 (例如 **splash_screen_land.xml** ) 。 在此檔案中，將點陣圖路徑設定為啟動顯示畫面影像的橫向版本。 在下列範例中， **splash_screen_land.xml** 使用 **splash_logo_land.png** ：

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

3. 建立 **資源/值-land 資料夾（** 如果尚未存在）。

4. 將 **colors.xml** 和 **style.xml** 的檔案新增至 **值-land** (這些可以從現有的  **值/colors.xml** 和 **值/style.xml** 檔案) 複製和修改。

5. 修改 **值-land/style.xml** ，使其使用的可繪製的橫向版本 `windowBackground` 。 在此範例中，會使用 **splash_screen_land.xml** ：

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

6. 修改 **值-land/colors.xml** 設定您想要用於啟動顯示畫面之橫向版本的色彩。 在此範例中，會將啟動顯示背景色彩變更為藍色（適用于橫向模式）：

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

7. 再次建立並執行應用程式。 當啟動顯示畫面仍顯示時，將裝置旋轉為橫向模式。 啟動顯示畫面會變更為橫向版本：

    [![將啟動顯示畫面旋轉為橫向模式](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)

請注意，使用橫向模式的啟動顯示畫面不一定會提供順暢的體驗。 根據預設，Android 會在直向模式中啟動應用程式，並將其轉換為橫向模式，即使裝置已處於橫向模式也一樣。 如此一來，如果在裝置處於橫向模式時啟動應用程式，則裝置會短暫顯示直向啟動顯示畫面，然後以動畫顯示方式從縱向旋轉到橫向啟動顯示畫面。 可惜的是，即使 `ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape` 是在啟動顯示活動的旗標中指定，也會發生這種初始的直向橫向轉換。 解決這項限制的最佳方式是建立單一啟動顯示畫面影像，以在直向和橫向模式中正確呈現。

## <a name="summary"></a>總結

本指南討論了在 Xamarin Android 應用程式中實施啟動顯示畫面的一種方法;亦即，將自訂主題套用至啟動活動。

## <a name="related-links"></a>相關連結

- [SplashScreen (範例) ](/samples/xamarin/monodroid-samples/splashscreen)
- [圖層清單繪製](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [材質設計模式-啟動畫面](https://material.io/design/communication/launch-screen.html#usage)