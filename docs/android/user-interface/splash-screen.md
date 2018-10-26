---
title: 啟動顯示畫面
description: Android 應用程式需要一些時間才能啟動，尤其是當裝置第一次啟動應用程式。 啟動顯示畫面可能會顯示開始增加到使用者，或表示商標的進度。
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/06/2018
ms.openlocfilehash: f21eca052ef13fd0e3d6efa261e1ff70b3b14372
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120706"
---
# <a name="splash-screen"></a>啟動顯示畫面

_Android 應用程式需要一些時間才能啟動，尤其是當裝置第一次啟動應用程式。啟動顯示畫面可能會顯示開始增加到使用者，或表示商標的進度。_


## <a name="overview"></a>總覽

Android 應用程式需要一些時間才能啟動，尤其是在第一次在裝置上執行應用程式 (有時候這稱為_冷啟動_)。 啟動顯示畫面可能會顯示給使用者，進行啟動，或它可能會顯示識別並提升應用程式的商標資訊。

本指南會討論實作啟動顯示畫面的 Android 應用程式中的其中一種技術。 它涵蓋了下列步驟：

1.  建立可繪製資源啟動顯示畫面。

2.  定義新的佈景主題，將會顯示可繪製資源。

3.  將用於在上一個步驟中建立的佈景主題所定義的啟動顯示畫面的應用程式中加入新的活動。

[![後面接著應用程式畫面的範例 Xamarin 標誌啟動顯示畫面](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)


## <a name="requirements"></a>需求

本指南假設應用程式的目標 Android API 層級 15 (Android 4.0.3) 或更高版本。 應用程式必須也**Xamarin.Android.Support.v4**並**Xamarin.Android.Support.v7.AppCompat** NuGet 套件新增至專案。

所有程式碼和本指南中的 XML 可能位於[SplashScreen](https://developer.xamarin.com/samples/monodroid/SplashScreen)本指南中的範例專案。


## <a name="implementing-a-splash-screen"></a>實作啟動顯示畫面

用於轉譯並顯示啟動顯示畫面的最快方式是建立自訂佈景主題，並將它套用至活動表現出啟動顯示畫面。 轉譯活動時，它會載入佈景主題，並適用於活動的背景 （參考佈景主題） 的可繪製資源。 這個方法可避免需要建立版面配置檔案。

啟動顯示畫面會實作為活動可顯示的品牌化可繪製，會執行任何初始設定，並啟動任何工作。 一旦應用程式已啟動，啟動顯示畫面活動就會開始主要活動，並從應用程式的上一頁堆疊移除本身。


### <a name="creating-a-drawable-for-the-splash-screen"></a>建立可繪製啟動顯示畫面

啟動顯示畫面會顯示 XML 可繪製背景的啟動顯示畫面活動。 必須要用來顯示的點陣圖影像 （例如 PNG 或 JPG） 映像。

在本指南中，我們會使用[層清單](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)中央應用程式中的啟動顯示畫面影像。 下列程式碼片段是舉例`drawable`資源使用`layer-list`:

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

這`layer-list`會將開頭顯示畫面影像置中**splash.png**所指定的背景`@color/splash_background`資源。 將在此 XML 檔案放**資源/drawable**資料夾 (例如**Resources/drawable/splash_screen.xml**)。

建立可繪製在啟動顯示畫面之後下, 一個步驟是建立啟動顯示畫面的佈景主題。


### <a name="implementing-a-theme"></a>實作佈景主題

若要建立自訂佈景主題，啟動顯示畫面的活動，編輯 （或加入） 檔案**values/styles.xml**並建立新`style`啟動顯示畫面項目。 樣本**values/style.xml**檔案會如下所示使用`style`名為**MyTheme.Splash**:

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

**MyTheme.Splash**是非常 spartan&ndash;宣告視窗背景、 明確移除的標題列在視窗中，以及宣告它是全螢幕。 如果您想要建立啟動顯示畫面，來模擬您的應用程式的 UI，活動會擴大的第一個配置之前，您可以使用`windowContentOverlay`而非`windowBackground`樣式定義中。 在此情況下，您也必須修改**splash_screen.xml**可繪製，以顯示模擬的 UI。


### <a name="create-a-splash-activity"></a>建立啟動顯示活動

接下來，我們的開頭顯示畫面影像，並執行任何啟動工作以啟動 Android 的新活動。 下列程式碼是完整的啟動顯示畫面實作的範例：

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

`SplashActivity` 明確地使用已建立在上一個區段中，覆寫應用程式的預設佈景主題的佈景主題。
不需要載入中的版面配置`OnCreate`如佈景主題會宣告可繪製的做為背景。

請務必設定`NoHistory=true`屬性，讓從上一頁堆疊移除的活動。 若要避免 [上一頁] 按鈕取消啟動程序，您也可以覆寫`OnBackPressed`，並讓它執行任何動作：

```csharp
public override void OnBackPressed() { }
```

啟動工作以非同步方式在執行`OnResume`。 這是必要的因此，啟動工作不會變慢或延遲啟動螢幕的外觀。 當工作完成時，`SplashActivity`將會啟動`MainActivity`和使用者可能會開始與應用程式互動。

這個新`SplashActivity`設定為應用程式的啟動器活動，藉由設定`MainLauncher`屬性設定為`true`。 因為`SplashActivity`啟動器活動中，您必須編輯現在`MainActivity.cs`，並移除`MainLauncher`屬性從`MainActivity`:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>橫向模式

在先前步驟中實作啟動顯示畫面會正確顯示在直向和橫向模式。 不過，在某些情況下就必須有個別的啟動顯示畫面直向和橫向模式 （例如，如果開頭顯示畫面影像是全螢幕）。

若要新增橫向模式的啟動顯示畫面，請使用下列步驟：

1. 在 **資源/drawable**資料夾中，加入啟動顯示畫面影像，您想要使用的架構版本。 在此範例中， **splash_logo_land.png**是上述範例中 （它會使用白色的傳送失敗，而不是藍色） 中所使用的標誌的橫向版本。

2. 中**資源/drawable**資料夾中，建立橫向版本`layer-list`可繪製，稍早定義 (例如**splash_screen_land.xml**)。 這個檔案中設定的點陣圖路徑橫向版本的啟動顯示畫面影像。 在下列範例中， **splash_screen_land.xml**會使用**splash_logo_land.png**:

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

3.  建立**資源/值-land**如果它尚未存在的資料夾。

4.  將檔案新增**colors.xml**並**style.xml**來**值 land** (這些可以複製及修改的現有**values/colors.xml**並**values/style.xml**檔案)。

5.  修改**值-land/style.xml**使其使用的可繪製為橫向版本`windowBackground`。 在此範例中， **splash_screen_land.xml**用：

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

6.  修改**值-land/colors.xml**來設定您想要啟動顯示畫面的橫向版本使用的色彩。 在此範例中，開頭顯示畫面背景色彩變更為藍色的橫向模式：

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

7.  建置並再次執行應用程式。 旋轉成橫式模式時仍會顯示啟動顯示畫面的裝置。 啟動顯示畫面變更為橫向版本：

    [![旋轉成橫向模式啟動顯示畫面的](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)


請注意，橫向模式啟動顯示畫面將永遠不會提供順暢的體驗。 根據預設，Android 會啟動 直向模式中的應用程式，並轉換以橫向模式，即使裝置已在橫向模式。 如此一來，如果裝置在橫向模式中時，會啟動應用程式，裝置將簡短地顯示 直向啟動顯示畫面，並從直式旋轉橫印啟動顯示畫面格會顯示動畫。 這個初始的直向來橫向轉換發生的不幸的是，即使`ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape`指定在啟動顯示活動的旗標。 若要解決這項限制，最好是在直向和橫向模式中正確建立呈現單一的啟動顯示畫面影像。


## <a name="summary"></a>總結

本指南討論在 Xamarin.Android 應用程式中實作啟動顯示畫面一種方式也就，請將自訂佈景主題套用至啟動活動。


## <a name="related-links"></a>相關連結

- [啟動顯示畫面 （範例）](https://developer.xamarin.com/samples/monodroid/SplashScreen)
- [圖層清單 Drawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [ 材料設計模式-啟動畫面](https://material.io/design/communication/launch-screen.html#usage)
