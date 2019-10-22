---
title: 建立 Android 磨損1.0 的監看式臉部
description: 本指南說明如何針對 Android 磨損1.0 執行自訂監看式臉部服務。 提供逐步指示，可讓您建立一個去除的數位監看式臉部服務，再加上更多程式碼來建立類比樣式的監看面。
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/23/2018
ms.openlocfilehash: a6dfab949eb19708f69d838a7c792f2e7bbd76b3
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "70758508"
---
# <a name="creating-a-watch-face"></a>建立錶面

_本指南說明如何針對 Android 磨損1.0 執行自訂監看式臉部服務。提供逐步指示，可讓您建立一個去除的數位監看式臉部服務，再加上更多程式碼來建立類比樣式的監看面。_

## <a name="overview"></a>總覽

在本逐步解說中，會建立基本的監看式臉部服務，以說明建立自訂 Android 磨損1.0 監看面的基礎。
初始監看式臉部服務會顯示簡單的數位監看式，顯示目前時間（以小時和分鐘為單位）：

[![數位監看臉部](creating-a-watchface-images/01-initial-face.png "初始數位監看式臉部的範例螢幕擷取畫面")](creating-a-watchface-images/01-initial-face.png#lightbox)

在開發和測試此數位監看的臉部之後，會加入更多的程式碼，以將其升級為更精密的類比監看面，並有三個手：

[![類比監看臉部](creating-a-watchface-images/02-example-watchface.png "最終類比監看臉部的範例螢幕擷取畫面")](creating-a-watchface-images/02-example-watchface.png#lightbox)

監看臉部服務已配套並安裝為磨損1.0 應用程式的一部分。 在下列範例中，`MainActivity` 只包含來自磨損1.0 應用程式範本的程式碼，如此一來，您就可以將 watch 臉部服務封裝並部署到智慧型監看作為應用程式的一部分。 實際上，此應用程式只會作為車輛，讓監看式臉部服務載入至磨損1.0 裝置（或模擬器）以進行偵錯工具和測試。

## <a name="requirements"></a>需求

若要執行監看式臉部服務，需要下列專案：

- 在磨損裝置或模擬器上的 Android 5.0 （API 層級21）或更高版本。

- [Xamarin Android 磨損支援程式庫](https://www.nuget.org/packages/Xamarin.Android.Wear)必須新增至 [xamarin] 專案。

雖然 Android 5.0 是用來執行 watch 臉部服務的最低 API 層級，但建議使用 Android 5.1 或更新版本。 執行 Android 5.1 （API 22）或更高版本的 android 磨損裝置，可讓磨損應用程式在裝置處於低電力*環境*模式時，控制畫面上顯示的內容。 當裝置離開低電力*環境*模式時，它會處於*互動*模式。 如需這些模式的詳細資訊，請參閱[讓您的應用程式保持可見](https://developer.android.com/training/wearables/apps/always-on.html)。

## <a name="start-an-app-project"></a>啟動應用程式專案

建立名為**WatchFace**的新 Android 磨損1.0 專案（如需有關建立新的 Xamarin android 專案的詳細資訊，請參閱[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)）：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![[新增專案] 對話方塊](creating-a-watchface-images/03-wear-project-vs-sml.png "在 [新增專案] 對話方塊中選取 [磨損應用程式]")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![[新增專案] 對話方塊](creating-a-watchface-images/03-wear-project-xs-sml.png "在 [新增專案] 對話方塊中選取 [磨損應用程式]")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----

將封裝名稱設定為 `com.xamarin.watchface`：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![封裝名稱設定](creating-a-watchface-images/04-package-name-vs.png "將套件名稱設定為 watchface。")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![封裝名稱設定](creating-a-watchface-images/04-package-name-xs.png "將套件名稱設定為 watchface。")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

此外，請向下滾動並啟用 [**網際網路**] 和 [ **WAKE_LOCK** ] 許可權：

[![必要許可權](creating-a-watchface-images/05-required-permissions-vs.png "啟用網際網路和 WAKE_LOCK 許可權")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

將 Android 最低版本設定為**android 5.1 （API 層級22）** 。
此外，請啟用 [**網際網路**] 和 [ **WakeLock** ] 許可權：

[![必要許可權](creating-a-watchface-images/05-required-permissions-xs.png "啟用網際網路和 WakeLock 許可權")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

接下來，下載[preview](creating-a-watchface-images/preview.png) &ndash; 這會在本逐步解說稍後新增至**可繪製資源**資料夾。

## <a name="add-the-xamarinandroid-wear-package"></a>新增 Xamarin. Android 磨損套件

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

啟動 NuGet 套件管理員（在 Visual Studio 中，以滑鼠右鍵按一下**方案總管**中的 [**參考**]，然後選取 [**管理 NuGet 套件 ...** ]）。將專案更新為最新穩定版本的**Xamarin。**

[![NuGet 套件管理員新增](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "新增 [Xamarin] 套件")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

接下來，如果已安裝**v13** ，請將它卸載：

[![NuGet 套件管理員移除](creating-a-watchface-images/07-uninstall-v13-sml.png "移除 v13")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

啟動 NuGet 套件管理員（在 Visual Studio for Mac 中，以滑鼠右鍵按一下 [**解決方案] 窗格**中的 [**套件**]，然後選取 [**新增套件 ...** ]）。將專案更新為最新穩定版本的**Xamarin。**

[![NuGet 套件管理員新增](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "新增 [Xamarin] 套件")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----

在磨損裝置或模擬器上建立並執行應用程式（如需如何執行此動作的詳細資訊，請參閱[消費者入門](~/android/wear/get-started/index.md)指南）。 您應該會在磨損裝置上看到下列應用程式畫面：

[![應用程式螢幕擷取畫面](creating-a-watchface-images/08-app-screen.png "磨損裝置上的應用程式畫面")](creating-a-watchface-images/08-app-screen.png#lightbox)

此時，基本的磨損應用程式不會有監看式臉部功能，因為它尚未提供監看式臉部服務的執行。 接下來將會加入此服務。

## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Android 磨損會透過 `CanvasWatchFaceService` 類別來執行監看臉部。 `CanvasWatchFaceService` 衍生自 `WatchFaceService`，它本身衍生自 `WallpaperService`，如下圖所示：

[![繼承圖表](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService 繼承圖表")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` 包含一個嵌套的 `CanvasWatchFaceService.Engine`;它會具現化 `CanvasWatchFaceService.Engine` 物件，以執行繪製監看式臉部的實際工作。 `CanvasWatchFaceService.Engine` 衍生自 `WallpaperService.Engine`，如上圖所示。

此圖中未顯示的是 `Canvas`，`CanvasWatchFaceService` 用來繪製監看式臉部 &ndash; 此 `Canvas` 會透過 `OnDraw` 方法傳入，如下所述。

在下列各節中，將會依照下列步驟來建立自訂監看臉部服務：

1. 定義一個名為 `MyWatchFaceService` 的類別，衍生自 `CanvasWatchFaceService`。

2. 在 `MyWatchFaceService` 中，建立一個衍生自 `CanvasWatchFaceService.Engine` 的嵌套類別，稱為 `MyWatchFaceEngine`。

3. 在 `MyWatchFaceService` 中，執行具現化 `MyWatchFaceEngine` 的 `CreateEngine` 方法，並將其傳回。

4. 在 `MyWatchFaceEngine` 中，請執行 `OnCreate` 方法來建立監看式臉部樣式，並執行任何其他初始化工作。

5. 執行 `MyWatchFaceEngine` 的 `OnDraw` 方法。 每當需要重新繪製監看表面時（亦即*失效*），就會呼叫這個方法。 `OnDraw` 是繪製（和重新繪製）監看臉部元素（例如，小時、分鐘和第二次）的方法。

6. 執行 `MyWatchFaceEngine` 的 `OnTimeTick` 方法。
    `OnTimeTick` 至少每分鐘呼叫一次（在環境和互動模式中），或日期/時間已變更時。

如需 `CanvasWatchFaceService` 的詳細資訊，請參閱 Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) API 檔。
同樣地， [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html)也會說明監看表面的實際執行。

### <a name="add-the-canvaswatchfaceservice"></a>新增 CanvasWatchFaceService

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

新增名為**MyWatchFaceService.cs**的新檔案（在 Visual Studio 中，以滑鼠右鍵按一下**方案總管**中的 [ **WatchFace** ]，然後按一下 [**新增 > 新專案 ...** ] 和 [選取**類別**]）。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

新增名為**MyWatchFaceService.cs**的新檔案（在 Visual Studio for Mac 中，以滑鼠右鍵按一下 [ **WatchFace** ] 專案，然後按一下 [**加入 > 新增檔案 ...** ]，再選取 [**空類別**]）。

-----

將此檔案的內容取代為下列程式碼：

```csharp
using System;
using Android.Views;
using Android.Support.Wearable.Watchface;
using Android.Service.Wallpaper;
using Android.Graphics;

namespace WatchFace
{
    class MyWatchFaceService : CanvasWatchFaceService
    {
        public override WallpaperService.Engine OnCreateEngine()
        {
            return new MyWatchFaceEngine(this);
        }

        public class MyWatchFaceEngine : CanvasWatchFaceService.Engine
        {
            CanvasWatchFaceService owner;
            public MyWatchFaceEngine (CanvasWatchFaceService owner) : base(owner)
            {
                this.owner = owner;
            }
        }
    }
}
```

`MyWatchFaceService` （衍生自 `CanvasWatchFaceService`）是監看臉部的「主要程式」。 `MyWatchFaceService` 只會執行一個方法，`OnCreateEngine`，它會具現化並傳回 `MyWatchFaceEngine` 物件（`MyWatchFaceEngine` 衍生自 `CanvasWatchFaceService.Engine`）。 具現化的 `MyWatchFaceEngine` 物件必須以 `WallpaperService.Engine` 的形式傳回。 封裝式 `MyWatchFaceService` 物件會傳遞至函式。

`MyWatchFaceEngine` 是實際的監看式臉部實 &ndash; 它包含繪製監看式臉部的程式碼。 它也會處理系統事件，例如螢幕變更（環境/互動模式、螢幕關閉等）。

### <a name="implement-the-engine-oncreate-method"></a>執行引擎 OnCreate 方法

@No__t_0 方法會初始化監看式臉部。 將下欄欄位新增至 `MyWatchFaceEngine`：

```csharp
Paint hoursPaint;
```

這個 `Paint` 物件將用來繪製監看表面上的目前時間。 接下來，將下列方法新增至 `MyWatchFaceEngine`：

```csharp
public override void OnCreate(ISurfaceHolder holder)
{
    base.OnCreate (holder);

    SetWatchFaceStyle (new WatchFaceStyle.Builder(owner)
        .SetCardPeekMode (WatchFaceStyle.PeekModeShort)
        .SetBackgroundVisibility (WatchFaceStyle.BackgroundVisibilityInterruptive)
        .SetShowSystemUiTime (false)
        .Build ());

    hoursPaint = new Paint();
    hoursPaint.Color = Color.White;
    hoursPaint.TextSize = 48f;
}
```

`MyWatchFaceEngine` 啟動之後，很快就會呼叫 `OnCreate`。 它會設定 `WatchFaceStyle` （控制磨損裝置與使用者互動的方式），並具現化將用來顯示時間的 `Paint` 物件。

@No__t_0 的呼叫會執行下列動作：

1. 將 *查看模式*設定為 `PeekModeShort`，這會使通知在顯示器上顯示為小型的「查看」卡片。

2. 將背景可見度設定為 `Interruptive`，這會使查看卡片的背景只在代表造成作業中斷通知的情況下，才會短暫顯示。

3. 停用預設系統 UI 時間，使其無法在監看表面上繪製，讓自訂監看臉部可以改為顯示時間。

如需這些和其他 [監看式] 字型選項的詳細資訊，請參閱 Android [WatchFaceStyle. Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) API 檔。

@No__t_0 完成之後，`OnCreate` 會將 `Paint` 物件（`hoursPaint`）具現化，並將其色彩設定為白色，而其文字大小設為48圖元（[TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29)必須以圖元為單位指定）。

### <a name="implement-the-engine-ondraw-method"></a>執行引擎 OnDraw 方法

@No__t_0 的方法可能是最重要的 `CanvasWatchFaceService.Engine` 方法 &ndash; 它是實際繪製監看式臉部元素的方法，例如數位和時鐘臉部手。
在下列範例中，它會在監看表面上繪製時間字串。
將下列方法新增至 `MyWatchFaceEngine`：

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str,
        (float)(frame.Left + 70),
        (float)(frame.Top  + 80), hoursPaint);
}
```

當 Android 呼叫 `OnDraw` 時，它會傳入 `Canvas` 實例，以及可以繪製臉部的範圍。 在上述程式碼範例中，`DateTime` 用來計算以小時和分鐘為單位的目前時間（以12小時制）。 產生的時間字串會使用 `Canvas.DrawText` 方法繪製在畫布上。 字串會從左邊緣到70圖元，從上邊緣向下顯示80圖元。

如需 `OnDraw` 方法的詳細資訊，請參閱 Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine#ondraw) API 檔。

### <a name="implement-the-engine-ontimetick-method"></a>執行引擎 OnTimeTick 方法

Android 會定期呼叫 `OnTimeTick` 方法，以更新監看表面所顯示的時間。 每分鐘至少會呼叫一次（在環境和互動模式中），或日期/時間或時區變更時。 將下列方法新增至 `MyWatchFaceEngine`：

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

這項 `OnTimeTick` 的執行只會呼叫 `Invalidate`。 @No__t_0 方法會排定 `OnDraw` 以重新繪製監看式臉部。

如需 `OnTimeTick` 方法的詳細資訊，請參閱 Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) API 檔。

## <a name="register-the-canvaswatchfaceservice"></a>註冊 CanvasWatchFaceService

`MyWatchFaceService` 必須在相關聯的磨損應用程式的**androidmanifest.xml**中註冊。 若要這麼做，請將下列 XML 新增至 `<application>` 區段：

```xml
<service
    android:name="watchface.MyWatchFaceService"
    android:label="Xamarin Sample"
    android:allowEmbedded="true"
    android:taskAffinity=""
    android:permission="android.permission.BIND_WALLPAPER">
    <meta-data
        android:name="android.service.wallpaper"
        android:resource="@xml/watch_face" />
    <meta-data
        android:name="com.google.android.wearable.watchface.preview"
        android:resource="@drawable/preview" />
    <intent-filter>
        <action android:name="android.service.wallpaper.WallpaperService" />
        <category android:name="com.google.android.wearable.watchface.category.WATCH_FACE" />
    </intent-filter>
</service>
```

此 XML 會執行下列動作：

1. 設定 `android.permission.BIND_WALLPAPER` 許可權。 此許可權可讓「監看臉部」服務許可權變更裝置上的系統壁紙。 請注意，此許可權必須在 `<service>` 區段中設定，而不是在外部 `<application>` 區段中。

2. 定義 `watch_face` 資源。 此資源是一個簡短的 XML 檔案，會宣告 `wallpaper` 資源（此檔案將在下一節中建立）。

3. 宣告名為 `preview` 的可繪製影像，會由 [監看式選擇器選取] 畫面顯示。

4. 包含 `intent-filter`，讓 Android 知道 `MyWatchFaceService` 會顯示監看式臉部。

這會完成基本 `WatchFace` 範例的程式碼。 下一步是新增必要的資源。

## <a name="add-resource-files"></a>新增資源檔

您必須先新增**watch_face**資源和預覽影像，才能執行 watch 服務。 首先，在**Resources/xml/watch_face**建立新的 XML 檔案，並將其內容取代為下列 XML：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

將此檔案的 [組建] 動作設定為**AndroidResource**：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![組建動作](creating-a-watchface-images/10-android-resource-vs.png "將組建動作設定為 AndroidResource")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![組建動作](creating-a-watchface-images/10-android-resource-xs.png "將組建動作設定為 AndroidResource")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

此資源檔會定義將用於監看臉部的簡單 `wallpaper` 元素。

如果您還沒有這麼做，請下載[preview .png](creating-a-watchface-images/preview.png)。
將它安裝在**資源/繪製/預覽 .png**。 請務必將此檔案新增至 `WatchFace` 專案。 此預覽影像會向使用者顯示在磨損裝置上的監看式臉部選擇器中。 若要為自己的監看面建立預覽影像，您可以在執行時，取得監看表面的螢幕擷取畫面。 （如需從磨損裝置取得螢幕擷取畫面的詳細資訊，請參閱[拍攝螢幕擷取畫面](~/android/wear/deploy-test/debug-on-device.md#screenshots)）。

## <a name="try-it"></a>試試看吧！

建立應用程式並將其部署至磨損裝置。 您應該會看到 [磨損應用程式] 畫面如先前所示。 執行下列動作以啟用新的監看面：

1. 向右滑動直到您看到 [監看式] 畫面的背景為止。

2. 觸控並按住螢幕背景上的任何位置兩秒。

3. 從左至右滑動以流覽各種監看面。

4. 選取 [ **Xamarin 範例**監看式臉部] （顯示在右側）：

    [![Watchface 選擇器](creating-a-watchface-images/11-watchface-picker.png "滑動以尋找 Xamarin 範例監看面")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5. 請按**Xamarin 範例**watch 臉部加以選取。

這會變更磨損裝置的監看面，以使用自訂監看臉部服務到目前為止執行的動作：

[![數位監看臉部](creating-a-watchface-images/12-digital-watchface.png "在磨損裝置上執行的自訂數位監看式")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

這是相對較粗糙的監看面，因為應用程式的執行非常小（例如，它不包含監看式背景，也不會呼叫 `Paint` 的反別名方法來改善外觀）。
不過，它確實會執行建立自訂監看臉部所需的基本功能。

在下一節中，此監看式臉部會升級為更複雜的執行。

## <a name="upgrading-the-watch-face"></a>升級監看式臉部

在本逐步解說的其餘部分中，`MyWatchFaceService` 會升級為顯示類比樣式的監看面，並已擴充以支援更多功能。 將新增下列功能以建立升級的監看面：

1. 表示類比小時、分鐘和第二次手的時間。

2. 對可見度的變更做出回應。

3. 回應環境模式和互動模式之間的變更。

4. 讀取基礎磨損裝置的屬性。

5. 會自動更新發生時區變更的時間。

在執行下面的程式碼變更之前，請先下載可[繪製的 .zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true)、將它解壓縮，然後將已解壓縮的 .png 檔案移至**資源/可繪製**的（覆寫先前的**預覽 .png**）。 將新的 .png 檔案加入 `WatchFace` 專案。

### <a name="update-engine-features"></a>更新引擎功能

下一個步驟是將**MyWatchFaceService.cs**升級到繪製類比監看面並支援新功能的執行。 以[MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs)中的監看式臉部代碼的類比版本取代**MyWatchFaceService.cs**的內容（您可以將此來源剪下並貼到現有的**MyWatchFaceService.cs**中）。

這個版本的**MyWatchFaceService.cs**會將更多程式碼新增至現有的方法，並包含額外的覆寫方法來新增更多功能。 下列各節提供原始程式碼的導覽。

#### <a name="oncreate"></a>OnCreate

更新後的**OnCreate**方法會如先前一樣設定 watch 臉部樣式，但它還包含一些額外的步驟：

1. 將背景影像設定為位於**Resources/drawable-hDPI/xamarin_background**中的**xamarin_background**資源。

2. 初始化 `Paint` 物件，以繪製小時的手、分鐘和第二手勢。

3. 初始化 `Paint` 物件，以繪製監看表面邊緣的小時刻度。

4. 建立會呼叫 `Invalidate` （重繪）方法的計時器，以便每秒重新繪製第二次。 請注意，此計時器是必要的，因為 `OnTimeTick` 只會每分鐘呼叫 `Invalidate` 一次。

這個範例只包含一個**xamarin_background .png**影像;不過，您可能會想要為您的自訂監看臉部將支援的每個螢幕密度建立不同的背景影像。

#### <a name="ondraw"></a>OnDraw

更新的**OnDraw**方法會使用下列步驟來繪製類比樣式的監看式臉部：

1. 取得目前的時間，現在會在 `time` 物件中維護。

2. 決定繪圖介面和其中心的範圍。

3. 繪製背景，縮放以配合背景繪製時的裝置。

4. 在時鐘的臉部周圍繪製12個*刻度*（對應至時鐘臉部的時數）。

5. 計算每個監看手勢的角度、旋轉和長度。

6. 在監看表面上繪製每個手。 請注意，如果監看式處於環境模式，則不會繪製第二個手勢。

#### <a name="onpropertieschanged"></a>OnPropertiesChanged

呼叫此方法是為了通知 `MyWatchFaceEngine` 有關磨損裝置的屬性（例如，低位環境模式和燒錄保護）。 在 `MyWatchFaceEngine` 中，此方法只會檢查低位的環境模式（在低位環境模式中，螢幕支援每個色彩的位較少）。

如需此方法的詳細資訊，請參閱 Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) API 檔。

#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

當磨損裝置進入或離開環境模式時，會呼叫這個方法。 在 `MyWatchFaceEngine` 的執行中，監看式臉部會在處於環境模式時停用消除鋸齒功能。

如需此方法的詳細資訊，請參閱 Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) API 檔。

#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

每當監看式變成可見或隱藏時，就會呼叫這個方法。 在 `MyWatchFaceEngine` 中，這個方法會根據可見度狀態來註冊/取消註冊時區接收者（如下所述）。

如需此方法的詳細資訊，請參閱 Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) API 檔。

### <a name="time-zone-feature"></a>時區功能

新的**MyWatchFaceService.cs**也包含功能，可在時區變更時（例如在跨時區移動時）更新目前的時間。 接近**MyWatchFaceService.cs**結尾，會定義時區變更 `BroadcastReceiver`，以處理時區變更的意圖物件：

```csharp
public class TimeZoneReceiver: BroadcastReceiver
{
    public Action<Intent> Receive { get; set; }
    public override void OnReceive (Context context, Intent intent)
    {
        if (Receive != null)
            Receive (intent);
    }
}
```

@No__t_0 和 `UnregisterTimezoneReceiver` 方法是由 `OnVisibilityChanged` 方法所呼叫。
當監看臉部的可見度狀態變更為 [隱藏] 時，會呼叫 `UnregisterTimezoneReceiver`。 當監看式臉部再次顯示時，會呼叫 `RegisterTimezoneReceiver` （請參閱 `OnVisibilityChanged` 方法）。

引擎 `RegisterTimezoneReceiver` 方法會宣告這個時區接收者的 `Receive` 事件的處理常式;這個處理常式會在每次跨越時區時，以新的時間更新 `time` 物件：

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

針對時區接收者，會建立並註冊意圖篩選器：

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

@No__t_0 方法會取消註冊時區接收者：

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>執行改良的監看臉部

再次建立應用程式並將其部署至磨損裝置。 從 [監看臉部] 選擇器中選取 [監看式臉部]。 [監看式] 選擇器中的預覽會顯示在左側，而新的監看表面會顯示在右側：

[![類比監看臉部](creating-a-watchface-images/13-analog-watchface.png "已改善選擇器和裝置上的類比臉部")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

在此螢幕擷取畫面中，第二個手勢每秒移動一次。 當您在磨損裝置上執行此程式碼時，當監看進入環境模式時，第二個會消失。

## <a name="summary"></a>總結

在本逐步解說中，已實作為自訂的 Android 磨損 1.0 watchface 並進行測試。 已引進 `CanvasWatchFaceService` 和 `CanvasWatchFaceService.Engine` 類別，並已實作用引擎類別的基本方法來建立簡單的數位監看式臉部。 這項處理已更新為可建立類比監看面的更多功能，並已執行其他方法來處理可見度、環境模式和裝置屬性差異中的變更。 最後，已實行時區廣播接收器，讓監看式自動更新跨越時區的時間。

## <a name="related-links"></a>相關連結

- [建立監看式臉部](https://developer.android.com/training/wearables/watch-faces/index.html)
- [WatchFace 範例](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchface)
- [WatchFaceService 引擎](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
