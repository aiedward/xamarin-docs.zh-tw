---
title: 建立適用于 Android 磨損1.0 的監看式臉部
description: 本指南說明如何為 Android 磨損1.0 執行自訂監看式臉部服務。 提供逐步指示，可讓您建立一個去除的數位監看臉部服務，再加上更多程式碼來建立類比樣式的觀賞臉部。
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/23/2018
ms.openlocfilehash: 79dcab73c379cecb5108a88cc8bbb2eab33af05c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457103"
---
# <a name="creating-a-watch-face"></a>建立錶面

_本指南說明如何為 Android 磨損1.0 執行自訂監看式臉部服務。提供逐步指示，可讓您建立一個去除的數位監看臉部服務，再加上更多程式碼來建立類比樣式的觀賞臉部。_

## <a name="overview"></a>概觀

本逐步解說會建立基本的 watch 臉部服務，以說明建立自訂 Android 磨損1.0 監看臉部的基本概念。
初始 watch 臉部服務會顯示簡單的數位監看式，以小時和分鐘顯示目前時間：

[![數位觀賞臉部](creating-a-watchface-images/01-initial-face.png "初始數位監看臉部的範例螢幕擷取畫面")](creating-a-watchface-images/01-initial-face.png#lightbox)

在開發和測試此數位監看面之後，會新增更多程式碼，以將其升級至更精密的類比監看表面：

[![類比監看臉部](creating-a-watchface-images/02-example-watchface.png "最終類比監看臉部的範例螢幕擷取畫面")](creating-a-watchface-images/02-example-watchface.png#lightbox)

監看臉部服務已配套並安裝為磨損1.0 應用程式的一部分。 在下列範例中， `MainActivity` 只會包含來自磨損1.0 應用程式範本的程式碼，以便將 watch 臉部服務封裝並部署至智慧型監看式，作為應用程式的一部分。 實際上，此應用程式會單純作為車輛，讓 watch 臉部服務載入至磨損1.0 裝置 (或模擬器) 以進行偵測和測試。

## <a name="requirements"></a>需求

若要執行 watch 臉部服務，需要下列專案：

- 在磨損裝置或模擬器上，Android 5.0 (API 層級 21) 或更高版本。

- [Xamarin Android 磨損支援程式庫](https://www.nuget.org/packages/Xamarin.Android.Wear)必須新增至 Xamarin. android 專案。

雖然 Android 5.0 是執行 watch 臉部服務的最低 API 層級，但建議使用 Android 5.1 或更新版本。 執行 Android 5.1 (API 22) 或更高版本的 android 磨損裝置，可讓磨損應用程式在裝置處於低電源 *環境* 模式時，控制畫面上顯示的內容。 當裝置離開低電源 *環境* 模式時，它會處於 *互動* 模式。 如需這些模式的詳細資訊，請參閱 [讓應用程式保持可見](https://developer.android.com/training/wearables/apps/always-on.html)。

## <a name="start-an-app-project"></a>啟動應用程式專案

建立新的 Android 磨損1.0 專案，稱為 **WatchFace** (如需建立新的 Xamarin android 專案的詳細資訊，請參閱 [Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)) ：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![[新增專案] 對話方塊](creating-a-watchface-images/03-wear-project-vs-sml.png "在 [新增專案] 對話方塊中選取 [磨損應用程式]")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![[新增專案] 對話方塊](creating-a-watchface-images/03-wear-project-xs-sml.png "在 [新增專案] 對話方塊中選取 [磨損應用程式]")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----

將套件名稱設定為 `com.xamarin.watchface` ：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![封裝名稱設定](creating-a-watchface-images/04-package-name-vs.png "將套件名稱設定為 watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![封裝名稱設定](creating-a-watchface-images/04-package-name-xs.png "將套件名稱設定為 watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

此外，請向下滾動並啟用 **網際網路** 並 **WAKE_LOCK** 許可權：

[![所需的權限](creating-a-watchface-images/05-required-permissions-vs.png "啟用網際網路和 WAKE_LOCK 許可權")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

將最低 Android 版本設定為 **android 5.1 (API 層級 22) **。
此外，啟用 **網際網路** 和 **WakeLock** 許可權：

[![所需的權限](creating-a-watchface-images/05-required-permissions-xs.png "啟用網際網路和 WakeLock 許可權")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

接下來，下載 [preview.png](creating-a-watchface-images/preview.png) &ndash; 這將在本逐步解說稍後新增至 **可繪製資源** 資料夾。

## <a name="add-the-xamarinandroid-wear-package"></a>新增 Xamarin. Android 磨損套件

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

啟動 NuGet 封裝管理員 (Visual Studio 中，以滑鼠右鍵按一下**方案總管**中的 [**參考**]，然後選取 [**管理 NuGet 套件 ...** ]) 。將專案更新為最新穩定版本的**Xamarin**：

[![NuGet 封裝管理員新增](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "新增 Xamarin。")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

接下來，如果已安裝 **v13** ，請將它卸載：

[![NuGet 封裝管理員移除](creating-a-watchface-images/07-uninstall-v13-sml.png "移除 Xamarin. v13")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

啟動 [NuGet 封裝管理員 (] Visual Studio for Mac 中，以滑鼠右鍵按一下 [**解決方案] 窗格**中的 [**封裝**]，然後選取 [**新增套件 ...** ]) 。將專案更新為最新穩定版本的**Xamarin**：

[![NuGet 封裝管理員新增](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "新增 Xamarin。")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----

在磨損裝置或模擬器上建立並執行應用程式 (如需如何執行此作業的詳細資訊，請參閱 [消費者入門](~/android/wear/get-started/index.md) 指南) 。 您應該會在磨損裝置上看到下列應用程式畫面：

[![應用程式螢幕擷取畫面](creating-a-watchface-images/08-app-screen.png "在磨損裝置上的應用程式畫面")](creating-a-watchface-images/08-app-screen.png#lightbox)

此時，基本的「磨損」應用程式不會有 watch 臉部功能，因為它尚未提供 watch 臉部服務的執行。 接下來將新增此服務。

## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Android 磨損會透過類別實行監看臉部 `CanvasWatchFaceService` 。 `CanvasWatchFaceService` 衍生自 `WatchFaceService` ，其本身衍生自，如下 `WallpaperService` 圖所示：

[![繼承圖](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService 繼承圖")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` 包含 nested `CanvasWatchFaceService.Engine` ; 它會具現化 `CanvasWatchFaceService.Engine` 物件，該物件會執行繪製監看臉部的實際工作。 `CanvasWatchFaceService.Engine` 衍生自，如上 `WallpaperService.Engine` 圖所示。

這張圖中未顯示的 `Canvas` 是 `CanvasWatchFaceService` 使用來繪製 watch 臉部的， &ndash; 這 `Canvas` 會透過方法傳入， `OnDraw` 如下所述。

在下列各節中，將會依照下列步驟來建立自訂監看臉部服務：

1. 定義 `MyWatchFaceService` 衍生自的類別，稱為  `CanvasWatchFaceService` 。

2. 在中 `MyWatchFaceService` ，建立一個  `MyWatchFaceEngine` 衍生自的嵌套類別，稱為  `CanvasWatchFaceService.Engine` 。

3. 在中 `MyWatchFaceService` ，執行具現 `CreateEngine` 化 `MyWatchFaceEngine` 並傳回它的方法。

4. 在中 `MyWatchFaceEngine` ，請執行 `OnCreate` 方法來建立監看式臉部樣式，並執行任何其他初始化工作。

5. 執行的 `OnDraw` 方法 `MyWatchFaceEngine` 。 每當需要重新繪製 watch 臉部時，就會呼叫這個方法， (也就是 *無效* 的) 。 `OnDraw` 是一種方法，可繪製 (和重新繪製) 監看臉部元素，例如 hour、minute 和 second 手。

6. 執行的 `OnTimeTick` 方法 `MyWatchFaceEngine` 。
    `OnTimeTick` 在環境和互動模式中，至少每分鐘呼叫一次 (，) 或日期/時間變更時。

如需的詳細資訊 `CanvasWatchFaceService` ，請參閱 Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) API 檔。
同樣地， [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html) 也會說明監看表面的實際執行。

### <a name="add-the-canvaswatchfaceservice"></a>新增 CanvasWatchFaceService

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中加入名為**MyWatchFaceService.cs** (的新檔案，以滑鼠右鍵按一下**方案總管**中的 [ **WatchFace** ]，按一下 [新增] **> [新專案**]，然後選取 [**類別**) ]。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中加入名為 **MyWatchFaceService.cs** (的新檔案，以滑鼠右鍵按一下 [ **WatchFace** ] 專案，按一下 [ **新增 > 新**檔案 ...]，然後選取 [ **空白類別**) ]。

-----

以下列程式碼取代此檔案的內容：

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

`MyWatchFaceService` 衍生自) 的 (`CanvasWatchFaceService` 是 watch 臉部的「主要程式」。 `MyWatchFaceService` 只有一個方法， `OnCreateEngine` 它會具現化並傳回 `MyWatchFaceEngine` 物件 (`MyWatchFaceEngine` 衍生自 `CanvasWatchFaceService.Engine`) 。 具現化的 `MyWatchFaceEngine` 物件必須以傳回 `WallpaperService.Engine` 。 封裝 `MyWatchFaceService` 物件會傳遞至函式。

`MyWatchFaceEngine` 是實際的監看臉部實作為 &ndash; 它包含繪製 watch 臉部的程式碼。 它也會處理系統事件，例如螢幕變更 (環境/互動模式、螢幕關閉等 ) 。

### <a name="implement-the-engine-oncreate-method"></a>執行引擎 >oncreate 方法

`OnCreate`方法會初始化 watch 臉部。 將下欄欄位新增至 `MyWatchFaceEngine` ：

```csharp
Paint hoursPaint;
```

此 `Paint` 物件將用來繪製監看表面上的目前時間。 接下來，將下列方法新增至 `MyWatchFaceEngine` ：

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

`OnCreate` 在啟動之後不久就會呼叫 `MyWatchFaceEngine` 。 它會設定 `WatchFaceStyle` (，以控制磨損裝置如何與使用者互動) 並具現化 `Paint` 將用來顯示時間的物件。

的呼叫 `SetWatchFaceStyle` 會執行下列動作：

1. 將 *查看模式* 設定為 `PeekModeShort` ，讓通知顯示為螢幕上的小型「查看」卡片。

2. 將背景可見度設定為 `Interruptive` ，使查看卡片的背景只在代表造成作業中斷通知的情況下才會顯示。

3. 停用預設的系統 UI 時間，使其無法在監看表面上繪製，讓自訂監看臉部可以改為顯示時間。

如需這些和其他 watch 臉部樣式選項的詳細資訊，請參閱 Android [WatchFaceStyle Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) API 檔。

`SetWatchFaceStyle`完成之後， `OnCreate` () 將物件具現化， `Paint` `hoursPaint` 並將其色彩設定為白色，並將其文字大小設定為48圖元 ([TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29)必須以圖元) 指定。

### <a name="implement-the-engine-ondraw-method"></a>執行引擎 OnDraw 方法

`OnDraw`方法最重要的 `CanvasWatchFaceService.Engine` 方法 &ndash; ，可能是實際繪製監看臉部元素（例如數位和時鐘臉部）的方法。
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

當 Android 呼叫時 `OnDraw` ，它會傳入 `Canvas` 實例和可以繪製臉部的範圍。 在上述程式碼範例中， `DateTime` 是用來計算以12小時制的目前時間（以小時和分鐘為單位） () 。 使用方法在畫布上繪製產生的時間字串 `Canvas.DrawText` 。 字串會在左邊緣顯示70圖元，並從上邊緣顯示80圖元。

如需此方法的詳細資訊 `OnDraw` ，請參閱 Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine#ondraw) API 檔。

### <a name="implement-the-engine-ontimetick-method"></a>執行引擎 OnTimeTick 方法

Android 會定期呼叫 `OnTimeTick` 方法，以更新 watch 臉部所顯示的時間。 在環境和互動模式中，每分鐘至少呼叫一次 () ，或日期/時間或時區變更時。 將下列方法新增至 `MyWatchFaceEngine`：

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

這 `OnTimeTick` 只是呼叫的實作為 `Invalidate` 。 `Invalidate`方法會排定 `OnDraw` 要重繪監看式臉部。

如需此方法的詳細資訊 `OnTimeTick` ，請參閱 Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) API 檔。

## <a name="register-the-canvaswatchfaceservice"></a>註冊 CanvasWatchFaceService

`MyWatchFaceService` 必須在相關聯的磨損應用程式 **AndroidManifest.xml** 中註冊。 若要這樣做，請將下列 XML 新增至 `<application>` 區段：

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

這個 XML 會執行下列動作：

1. 設定 `android.permission.BIND_WALLPAPER` 許可權。 此許可權可讓 watch 臉部辨識服務變更裝置上的系統背景圖樣。 請注意，此許可權必須在區段中設定， `<service>` 而不是在外部區段中設定 `<application>` 。

2. 定義 `watch_face` 資源。 此資源是一個簡短的 XML 檔案，可宣告 `wallpaper` 資源 (在下一節) 將會建立此檔案。

3. 宣告一個稱為的可繪製影像 `preview` ，其將由監看式選擇器選取畫面顯示。

4. 包含 `intent-filter` ，讓 Android 知道將會  `MyWatchFaceService` 顯示 watch 臉部。

這會完成基本範例的程式碼 `WatchFace` 。 下一步是新增必要的資源。

## <a name="add-resource-files"></a>新增資源檔

在您可以執行 watch 服務之前，您必須先新增 **watch_face** 資源和預覽影像。 首先，在 **Resources/xml/watch_face.xml** 建立新的 xml 檔案，並將其內容取代為下列 XML：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

將此檔案的組建動作設為 **AndroidResource**：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![建立動作](creating-a-watchface-images/10-android-resource-vs.png "將組建動作設定為 AndroidResource")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![建立動作](creating-a-watchface-images/10-android-resource-xs.png "將組建動作設定為 AndroidResource")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

此資源檔 `wallpaper` 會定義將用於 watch 臉部的簡單元素。

如果您還沒有這麼做，請下載 [preview.png](creating-a-watchface-images/preview.png)。
將它安裝在 **資源/繪製/preview.png**。 請務必將此檔案新增至 `WatchFace` 專案。 此預覽影像會在磨損裝置上的 watch 臉部選擇器中顯示給使用者。 若要為您自己的監看式臉部建立預覽影像，您可以在執行時拍攝 watch 臉部的螢幕擷取畫面。  (如需從磨損裝置取得螢幕擷取畫面的詳細資訊，請參閱) [拍攝螢幕擷取畫面](~/android/wear/deploy-test/debug-on-device.md#screenshots) 。

## <a name="try-it"></a>試試看！

建立應用程式，並將其部署至磨損裝置。 您應該會看到 [磨損應用程式] 畫面如先前所示。 執行下列動作以啟用新的 watch 臉部：

1. 向右滑動直到您看到 [監看式] 畫面的背景。

2. 在畫面背景的任意位置按住並按住兩秒鐘。

3. 從左至右滑動，以流覽各種觀賞臉部。

4. 選取 [ **Xamarin 範例** 監看式臉部 (顯示在右側) ：

    [![Watchface 選擇器](creating-a-watchface-images/11-watchface-picker.png "滑動以找出 Xamarin 範例觀賞臉部")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5. 點擊 **Xamarin 範例** 監看臉部以選取它。

這會變更磨損裝置的監看面，以使用到目前為止所實行的自訂監看臉部服務：

[![數位觀賞臉部](creating-a-watchface-images/12-digital-watchface.png "在磨損裝置上執行的自訂數位監看式")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

這是相當粗糙的監看式臉部，因為應用程式的執行最小 (例如，它不包含 watch 臉部背景，也不會呼叫 `Paint` 反別名方法來改善外觀) 。
但是，它會執行建立自訂監看臉部所需的基本功能。

在下一節中，此監看式臉部將會升級為更精密的執行。

## <a name="upgrading-the-watch-face"></a>升級 watch 臉部

在本逐步解說的其餘部分， `MyWatchFaceService` 會升級以顯示類比樣式的監看式臉部，並擴充以支援更多功能。 將會新增下列功能來建立升級的監看式臉部：

1. 指出具有類比小時、分鐘和秒的時間。

2. 對可見度的變更做出回應。

3. 回應環境模式與互動模式之間的變更。

4. 讀取基礎磨損裝置的屬性。

5. 自動更新發生時區變更的時間。

在進行下列程式碼變更之前，請先下載 [drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true)、將它解壓縮，然後將解壓縮的 .png 檔案移至 **資源/可繪製** (覆寫先前的 **preview.png**) 。 將新的 .png 檔加入至 `WatchFace` 專案。

### <a name="update-engine-features"></a>更新引擎功能

下一個步驟是將 **MyWatchFaceService.cs** 升級為可繪製類比監看式臉部並支援新功能的實作為。 將 **MyWatchFaceService.cs** 的內容取代為 [MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) 中 watch 臉部代碼的類比版本 (您可以將此來源剪下並貼到現有的 **MyWatchFaceService.cs**) 。

此版本的 **MyWatchFaceService.cs** 會將更多程式碼新增至現有的方法，並包含其他覆寫的方法來加入更多功能。 下列各節提供原始程式碼的導覽。

#### <a name="oncreate"></a>OnCreate

更新後的 **>oncreate** 方法會將 watch 臉部樣式設定為先前，但還包含一些額外的步驟：

1. 將背景影像設定為位於**資源/可繪製-hDPI/xamarin_background.png**中的**xamarin_background**資源。

2. 初始化用 `Paint` 來繪製時數、每分鐘手和第二次的物件。

3. 初始化 `Paint` 物件，以在監看臉部的邊緣周圍繪製小時刻度。

4. 建立會呼叫 `Invalidate` (重繪) 方法的計時器，以便每秒重繪第二個手。 請注意，此計時器是必要的，因為 `OnTimeTick` `Invalidate` 每分鐘只會呼叫一次。

此範例只包含一個 **xamarin_background.png** 映射;不過，您可能會想要為自訂監看臉部所支援的每個螢幕密度建立不同的背景影像。

#### <a name="ondraw"></a>OnDraw

更新的 **OnDraw** 方法會使用下列步驟來繪製類比樣式的 watch 臉部：

1. 取得目前的時間，它現在會在物件中進行維護 `time` 。

2. 決定繪圖介面和其中心的範圍。

3. 繪製背景，並在繪製背景時縮放以符合裝置。

4. 在時鐘臉部周圍繪製12個 *刻度* (對應至時鐘臉部) 的時數。

5. 計算每個監看式的角度、旋轉和長度。

6. 在監看表面上繪製每個手。 請注意，如果 watch 處於環境模式，則不會繪製第二個手。

#### <a name="onpropertieschanged"></a>OnPropertiesChanged

呼叫此方法以通知 `MyWatchFaceEngine` 磨損裝置的屬性 (例如，低位環境模式和) 的燒錄保護。 在中 `MyWatchFaceEngine` ，此方法只會在低位環境模式下檢查低位環境模式 (，螢幕針對每個色彩) 支援較少的位。

如需此方法的詳細資訊，請參閱 Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) API 檔。

#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

當磨損裝置進入或離開環境模式時，會呼叫這個方法。 在 `MyWatchFaceEngine` 執行時，watch 臉部會在處於環境模式時停用消除鋸齒。

如需此方法的詳細資訊，請參閱 Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) API 檔。

#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

每當 watch 變成可見或隱藏時，就會呼叫這個方法。 在中 `MyWatchFaceEngine` ，這個方法會根據可見度狀態註冊/取消註冊時區接收者 (如下所述) 。

如需此方法的詳細資訊，請參閱 Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) API 檔。

### <a name="time-zone-feature"></a>時區功能

新的 **MyWatchFaceService.cs** 也包含功能，可在時區 (變更時（例如跨時區) 移動時）更新目前時間。 在 **MyWatchFaceService.cs**結束時，定義了時區變更 `BroadcastReceiver` 來處理時區變更的意圖物件：

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

`RegisterTimezoneReceiver` `UnregisterTimezoneReceiver` 方法會呼叫和方法 `OnVisibilityChanged` 。
`UnregisterTimezoneReceiver` 當 watch 臉部的可見度狀態變更為隱藏時呼叫。 當觀賞臉部再次顯示時， `RegisterTimezoneReceiver` 會呼叫， (查看 `OnVisibilityChanged`) 方法。

引擎 `RegisterTimezoneReceiver` 方法會宣告此時區接收者事件的處理常式， `Receive` 此處理程式 `time` 會在每次超過時區時，以新的時間更新物件：

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

系統會為時區接收者建立並註冊意圖篩選準則：

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

方法會取消 `UnregisterTimezoneReceiver` 註冊時區接收者：

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>執行改良的觀賞臉部

再次建立應用程式，並將其部署至磨損裝置。 如同之前一樣，選取 watch 臉部選擇器的監看式臉部。 [監看式] 選擇器中的預覽會顯示在左側，而新的 watch 臉部會顯示在右邊：

[![類比監看臉部](creating-a-watchface-images/13-analog-watchface.png "改善選擇器和裝置上的類比臉部")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

在此螢幕擷取畫面中，第二個手會每秒移動一次。 當您在磨損裝置上執行此程式碼時，當 watch 進入環境模式時，第二個手會消失。

## <a name="summary"></a>摘要

在本逐步解說中，已執行並測試自訂的 Android 磨損 1.0 watchface。 `CanvasWatchFaceService`引進了和 `CanvasWatchFaceService.Engine` 類別，並已實行引擎類別的基本方法來建立簡單的數位監看臉部。 這項處理已使用更多的功能來進行更新，以建立類比監看式臉部，並已實行其他方法來處理可見度、環境模式和裝置屬性的差異。 最後，已實行時區廣播接收器，讓監看式自動更新超過某個時區的時間。

## <a name="related-links"></a>相關連結

- [建立監看式臉部](https://developer.android.com/training/wearables/watch-faces/index.html)
- [WatchFace 範例](/samples/xamarin/monodroid-samples/wear-watchface)
- [WatchFaceService 引擎](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)