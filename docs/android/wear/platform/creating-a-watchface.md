---
title: "建立 Watch 錶面"
description: "本指南說明如何實作自訂監看式朝服務針對 Android 戴上。 關閉數位監看式朝服務，後面接著建立類比樣式 watch 錶面更多程式碼建置等量會提供逐步指示。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: fb3a2a9e60bda2a99a719bf75d23c29d42a94bdb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="creating-a-watch-face"></a>建立 Watch 錶面

_本指南說明如何實作自訂監看式朝服務針對 Android 戴上。關閉數位監看式朝服務，後面接著建立類比樣式 watch 錶面更多程式碼建置等量會提供逐步指示。_

## <a name="overview"></a>總覽 

在本逐步解說中，以說明必要的建立自訂的 Android 穿 watch 錶面建立基本監看式朝服務。 初始監看式朝服務會顯示簡單數位監看式，以小時和分鐘為單位顯示目前的時間： 

[![數位 watch 錶面](creating-a-watchface-images/01-initial-face.png "的初始數位錶範例螢幕擷取畫面")](creating-a-watchface-images/01-initial-face.png#lightbox)

此數位 watch 錶面是開發及測試之後，將其升級為更複雜的三種指針的類比 watch 錶面加入更多程式碼： 

[![類比 watch 錶面](creating-a-watchface-images/02-example-watchface.png "的最終類比錶範例螢幕擷取畫面")](creating-a-watchface-images/02-example-watchface.png#lightbox)

監看式朝服務結合在一起，損耗應用程式一併安裝。 在下列範例中，`MainActivity`只包含損耗應用程式範本中的程式碼，讓監看式朝服務可以封裝，並隨應用程式部署至智慧監看式。 作用中，此應用程式將偵錯和測試做純粹做為一種載具，用於取得監看式朝服務載入損耗裝置 （或模擬器）。 

## <a name="requirements"></a>需求

若要實作監看式朝服務，將需要以下條件：

-   Android 5.0 (API level 21) 或更高版本上損耗裝置或模擬器。

-   [Xamarin Android 有支援文件庫](https://www.nuget.org/packages/Xamarin.Android.Wear)必須加入至 Xamarin.Android 專案。 

雖然 Android 5.0 是最小 API 層級實作監看式朝服務，Android 5.1 或更新版本，建議您使用。 Android 戴上執行 Android 5.1 (應用程式開發介面 22) 的裝置，或允許損耗應用程式來控制在低電源的裝置時顯示在螢幕上的高*環境*模式。 當裝置離開低電源*環境*模式中，它處於*互動式*模式。 如需有關這些模式的詳細資訊，請參閱[保留您的應用程式顯示](https://developer.android.com/training/wearables/apps/always-on.html)。 


## <a name="start-an-app-project"></a>啟動應用程式專案

建立新 Android 穿專案，稱為**WatchFace** (如需有關如何建立新的 Xamarin.Android 專案的詳細資訊，請參閱[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![新增專案 對話方塊](creating-a-watchface-images/03-wear-project-vs-sml.png "新增專案 對話方塊中選取有應用程式")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![新增專案 對話方塊](creating-a-watchface-images/03-wear-project-xs-sml.png "新增專案 對話方塊中選取有應用程式")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----


若要設定封裝名稱`com.xamarin.watchface`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![封裝名稱設定](creating-a-watchface-images/04-package-name-vs.png "設 com.xamarin.watchface 封裝名稱")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![封裝名稱設定](creating-a-watchface-images/04-package-name-xs.png "設 com.xamarin.watchface 封裝名稱")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

此外，向下捲動並啟用**網際網路**和**WAKE_LOCK**權限： 

[![必要的權限](creating-a-watchface-images/05-required-permissions-vs.png "啟用網際網路和 WAKE_LOCK 權限")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

設定 Android 最低版本為**Android 5.1 （API 層級 22）**。 此外，啟用**網際網路**和**WakeLock**權限：

[![必要的權限](creating-a-watchface-images/05-required-permissions-xs.png "啟用網際網路和 WakeLock 權限")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

接下來，下載[preview.png](creating-a-watchface-images/preview.png) &ndash;這將會加入至**drawables**稍後在本逐步解說的資料夾。


## <a name="add-the-xamarin-android-wear-package"></a>將 Xamarin Android 損耗封裝

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

啟動 NuGet 封裝管理員 (在 Visual Studio 中，以滑鼠右鍵按一下**參考**中**方案總管 中**選取**管理 NuGet 封裝...**).專案更新到最新穩定版本**Xamarin.Android.Wear**: 

[![NuGet 封裝管理員新增](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "新增 Xamarin.Android.Wear 封裝")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

接下來，如果**Xamarin.Android.Support.v13**已安裝，請將它解除安裝：

[![NuGet 套件管理員移除](creating-a-watchface-images/07-uninstall-v13-sml.png "移除 Xamarin.Support.v13")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

啟動 NuGet 封裝管理員 (在 Visual Studio for Mac，以滑鼠右鍵按一下**封裝**中**方案窗格**選取**新增封裝...**).專案更新到最新穩定版本**Xamarin.Android.Wear**: 

[![NuGet 封裝管理員新增](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "新增 Xamarin.Android.Wear 封裝")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----


建置和損耗裝置或模擬器上執行應用程式 (如需如何執行這項操作的詳細資訊，請參閱[入門](~/android/wear/get-started/index.md)指南)。 您應該會看到下列應用程式畫面損耗裝置上：

[![應用程式螢幕擷取畫面](creating-a-watchface-images/08-app-screen.png "損耗裝置上的應用程式畫面")](creating-a-watchface-images/08-app-screen.png#lightbox)

此時，基本損耗應用程式沒有監看式朝功能因為它尚未提供監看式朝服務實作。 接下來將會加入此服務。 

 
## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Android 損耗實作觀賞透過字體`CanvasWatchFaceService`類別。 `CanvasWatchFaceService` 衍生自`WatchFaceService`，而其本身衍生自`WallpaperService`下圖所示： 

[![繼承圖表](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService 繼承圖表")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` 包含巢狀`CanvasWatchFaceService.Engine`; 它會具現化`CanvasWatchFaceService.Engine`真正的繪圖 watch 錶面工作的物件。 `CanvasWatchFaceService.Engine` 衍生自`WallpaperService.Engine`上圖所示。 

不會顯示在此圖表是`Canvas`，`CanvasWatchFaceService`用於繪製 watch 錶面&ndash;這`Canvas`透過傳入`OnDraw`如下所述的方法。 

在下列章節中，會建立自訂的監看式朝服務依照下列步驟： 

1.  定義一種類別稱為`MyWatchFaceService`衍生自`CanvasWatchFaceService`。 

2.  內`MyWatchFaceService`，建立巢狀的類別，稱為`MyWatchFaceEngine`衍生自`CanvasWatchFaceService.Engine`。 

3.  在`MyWatchFaceService`，實作`CreateEngine`具現化的方法`MyWatchFaceEngine`並將它傳回。

4.  在`MyWatchFaceEngine`，實作`OnCreate`方法來建立監看式朝樣式，並執行任何其他初始設定工作。 

5.  實作`OnDraw`方法`MyWatchFaceEngine`。 每當 watch 錶面需要重新繪製時，會呼叫這個方法 (也就是*失效*)。 `OnDraw` 為繪製 （並重新繪製） 監看式朝項目，例如小時、 分鐘和第二個指針的方法。 

6.  實作`OnTimeTick`方法`MyWatchFaceEngine`。 
    `OnTimeTick` 會每分鐘 （在環境和互動式模式） 或日期/時間變更時呼叫至少一次。 

如需有關`CanvasWatchFaceService`，請參閱 Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) API 文件。
同樣地， [CanvasWatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html)說明 watch 錶面的實際實作。


### <a name="add-the-canvaswatchfaceservice"></a>新增 CanvasWatchFaceService

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

加入新的檔案稱為**MyWatchFaceService.cs** (在 Visual Studio 中，以滑鼠右鍵按一下**WatchFace**中**方案總管] 中**，按一下 [**新增 > 新項目...**，然後選取**類別**)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

加入新的檔案稱為**MyWatchFaceService.cs** (在 Visual Studio for Mac，以滑鼠右鍵按一下**WatchFace**專案中，按一下 **新增 > 新的檔案...**，然後選取**空類別**)。 

-----

此檔案的內容取代為下列程式碼： 

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

`MyWatchFaceService` (衍生自`CanvasWatchFaceService`) 是 「 主要程式 」 的 watch 錶面。 `MyWatchFaceService` 會實作一個方法， `OnCreateEngine`，會具現化，並傳回`MyWatchFaceEngine`物件 (`MyWatchFaceEngine`衍生自`CanvasWatchFaceService.Engine`)。 具現化`MyWatchFaceEngine`物件必須傳回做`WallpaperService.Engine`。 封裝`MyWatchFaceService`物件傳遞至建構函式。 

`MyWatchFaceEngine` 是實際的監看式朝實作&ndash;它包含繪製 watch 錶面程式碼。 它也會處理系統事件，例如畫面變更 （環境/互動模式中，畫面關閉等等）。 

 
### <a name="implement-the-engine-oncreate-method"></a>實作引擎 OnCreate 方法

`OnCreate`方法初始化 watch 錶面。 加入下列欄位`MyWatchFaceEngine`: 

```csharp
Paint hoursPaint;
```

這`Paint`物件會用來在 watch 錶面上繪製目前的時間。 接下來，加入下列方法加入`MyWatchFaceEngine`: 

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

`OnCreate` 呼叫之後儘速`MyWatchFaceEngine`已啟動。 它會設定`WatchFaceStyle`（這會控制損耗裝置與使用者互動的方式） 和具現化`Paint`物件，用於顯示的時間。 

若要呼叫`SetWatchFaceStyle`會進行下列作業：

1.  設定*查看模式*至`PeekModeShort`，因而導致顯示為小 」 瞄 」 卡片上顯示通知。 

2.  若要設定背景的可見性`Interruptive`，因而導致顯示僅簡單如果它代表防止通知查看卡片的背景。

3.  停用預設的系統 UI 時間從繪製 watch 錶面上，以便自訂錶可以改為顯示時間。

如需有關這些和其他監看式朝樣式選項的詳細資訊，請參閱 Android [WatchFaceStyle.Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) API 文件。

之後`SetWatchFaceStyle`完成時，`OnCreate`具現化`Paint`物件 (`hoursPaint`) 並將其色彩會設為白色、 文字大小以 48 像素 ([TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29)必須指定像素為單位)。 


### <a name="implement-the-engine-ondraw-method"></a>實作引擎 OnDraw 方法

`OnDraw`方法可能是最重要`CanvasWatchFaceService.Engine`方法&ndash;它是方法，實際繪製觀賞朝項目，例如數字和針字體。 在下列範例中，它會 watch 錶面上繪製的時間字串。
將下列方法加入`MyWatchFaceEngine`:

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str, 
        (float)(frame.Left + 70), 
        (float)(frame.Top  + 80), hoursPaint);
}
```

當呼叫 Android `OnDraw`，它會傳入`Canvas`執行個體，可以在其中繪製圖示的界限。 在上述程式碼範例中，`DateTime`用來計算目前的時間，以小時和分鐘 （12 小時制）。 產生的時間字串，會使用來繪製在畫布上`Canvas.DrawText`方法。 字串會顯示 70 像素上從左邊的緣和 80 像素為單位向下距離上邊緣。 

如需有關`OnDraw`方法，請參閱 Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html#onDraw(android.graphics.Canvas, android.graphics.Rect)) API 文件。


### <a name="implement-the-engine-ontimetick-method"></a>實作引擎 OnTimeTick 方法

Android 定期呼叫`OnTimeTick`方法，以更新由 watch 錶面所顯示的時間。 至少一次每分鐘 （在環境和互動式模式中），或已變更的日期/時間或時區時，它會在進行呼叫。 將下列方法加入`MyWatchFaceEngine`: 

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

這項實作`OnTimeTick`只會呼叫`Invalidate`。 `Invalidate`方法排程`OnDraw`重繪 watch 錶面。 

如需有關`OnTimeTick`方法，請參閱 Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) API 文件。

 
## <a name="register-the-canvaswatchfaceservice"></a>註冊 CanvasWatchFaceService

`MyWatchFaceService` 必須註冊在**AndroidManifest.xml**相關聯的損耗應用程式。 若要這樣做，請加入下列 XML 來`<application>`> 一節： 

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

這段 XML 會執行下列動作：

1.  設定`android.permission.BIND_WALLPAPER`權限。 此權限讓變更在裝置上的系統底色圖案的監看式朝服務權限。 請注意，必須在設定此權限`<service>`區段，而不是在外部`<application>`> 一節。 

2.  定義`watch_face`資源。 此資源是簡短的 XML 檔案宣告`wallpaper`（下一節中會建立此檔案） 的資源。 

3.  會宣告稱為 drawable 映像`preview`，會由監看式選擇器選取畫面上顯示。 

4.  包含`intent-filter`讓已了解 Android`MyWatchFaceSevice`要顯示 watch 錶面。 

完成基本的程式碼`WatchFace`範例。 下一個步驟是加入必要的資源。

 
## <a name="add-resource-files"></a>加入資源檔

您可以執行監看式服務之前，您必須加入**watch_face**資源和預覽影像。 首先，建立新的 XML 檔案，在**Resources/xml/watch_face.xml** ，並以下列 XML 取代其內容： 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

將此檔案的建置動作設定為**AndroidResource**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![建置動作](creating-a-watchface-images/10-android-resource-vs.png "組建置 AndroidResource 的動作")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![建置動作](creating-a-watchface-images/10-android-resource-xs.png "組建置 AndroidResource 的動作")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

此資源檔案會定義簡單`wallpaper`將用於 watch 錶面的項目。 

如果您尚未這樣做，下載[preview.png](creating-a-watchface-images/preview.png)。 安裝在**Resources/drawable/preview.png**。 務必要將這個檔案來加入`WatchFace`專案。 此預覽影像會顯示給使用者，以監看式朝選擇器，損耗裝置上。 若要建立您自己的 watch 錶面的預覽影像，您可以在執行時採取 watch 錶面螢幕擷取的畫面。 (如需有關從損耗裝置取得螢幕擷取畫面，請參閱[採取螢幕擷取畫面](~/android/wear/deploy-test/debug-on-device.md#screenshots))。 


## <a name="try-it"></a>請嘗試

建置並部署到損耗裝置的應用程式。 您應該會看到與之前出現損耗應用程式畫面。 執行下列命令以啟用新的 watch 錶面： 

1.  撥動至右方，直到您看到 [監看式] 畫面的背景。 

2.  接觸，而且任何位置上的螢幕背景保存兩秒。

3.  從左到右瀏覽各種監看式字體撥動。 

4.  選取**Xamarin 範例**觀賞字體 （顯示在右側）： 

    [![Watchface 選擇器](creating-a-watchface-images/11-watchface-picker.png "撥動，找到 Xamarin 範例 watch 錶面")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5.  點選**Xamarin 範例**觀賞圖示來選取它。 

這會變更監看式表面之損耗裝置使用到目前為止所實作的自訂監看式朝服務： 

[![數位 watch 錶面](creating-a-watchface-images/12-digital-watchface.png "損耗裝置上執行自訂數位監看式")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

這是相當粗糙 watch 錶面因為是因此最小的應用程式實作 (例如，它不會包含監看式表面背景，而不會呼叫`Paint`反別名方法，以改善外觀)。 不過，它實作的基本功能所需建立自訂 watch 錶面。 

在下一步 區段中，此 watch 錶面將會升級為更複雜的實作。 

 
## <a name="upgrading-the-watch-face"></a>升級 Watch 錶面

這個逐步解說的其餘部分`MyWatchFaceService`升級為顯示類比樣式 watch 錶面並擴充以支援更多的功能。 將建立升級的 watch 錶面加入下列功能： 

1.  表示與類比小時、 分鐘和第二個指針的時間。

2.  變更回應的可見度。

3.  環境的模式和互動式模式之間的變更作出回應。 

4.  讀取基礎損耗裝置的屬性。

5.  會自動更新時的時區變更會發生的時間。 

實作下列程式碼變更，之前，先下載[drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true)、 解壓縮，然後移動解壓縮的.png 檔案**資源/drawable** (覆寫先前**preview.png**). 加入新.png 檔案`WatchFace`專案。


### <a name="update-engine-features"></a>更新引擎功能

下一步是升級**MyWatchFaceService.cs**繪製類比 watch 錶面，並支援新功能的實作。 取代內容**MyWatchFaceService.cs**監看式朝程式碼中的類比版本[MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (您可以剪下並貼上此來源的現有**MyWatchFaceService.cs**)。 

這個版本的**MyWatchFaceService.cs**將更多程式碼加入至現有的方法，並包含額外的覆寫的方法，以新增更多的功能。 下列各節提供原始程式碼的導覽。

#### <a name="oncreate"></a>OnCreate

已更新**OnCreate**方法會如往常一般，設定監看式朝樣式，但包含一些額外的步驟： 

1.  若要設定的背景影像**xamarin_background**資源位於**Resources/drawable-hdpi/xamarin_background.png**。 

2.  初始化`Paint`繪圖時針、 秒針和秒針物件。

3.  初始化`Paint`繪製小時刻度 watch 錶面邊緣的物件。 

4.  呼叫會建立一個計時器`Invalidate`（重新繪製） 方法，以便將會重繪秒針每秒。 請注意，此計時器必要因為`OnTimeTick`呼叫`Invalidate`一次只能每隔一分鐘。

這個範例包含只有一個**xamarin_background.png**映像; 不過，您可以建立不同的背景影像將會支援您的自訂 watch 錶面每個螢幕密度。 

#### <a name="ondraw"></a>OnDraw

已更新**OnDraw**方法繪製的類比樣式 watch 錶面，使用下列步驟： 

1.  取得目前的時間，現在會保存在`time`物件。 

2.  決定繪圖介面，並對其中心的界限。

3.  繪製背景，調整為適合裝置繪製背景時。

4.  繪製十二個*刻度*周圍的時鐘 （對應到上的時鐘的時數） 的圖示。 

5.  計算角度、 旋轉和每一個監看式指針的長度。

6.  監看式介面上繪製每一個指針。 請注意是否監看式處於環境的模式，不繪製秒針。 

 
#### <a name="onpropertieschanged"></a>OnPropertiesChanged

呼叫此方法來通知`MyWatchFaceEngine`有關損耗裝置 （例如低位元環境模式和燒保護） 的屬性。 在`MyWatchFaceEngine`，這個方法只會檢查為低位元環境的模式 （低位元環境的模式，螢幕支援較少的位元為每個色彩）。 

如需有關這個方法的詳細資訊，請參閱 Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) API 文件。


#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

損耗裝置進入或離開環境模式時，會呼叫這個方法。 在`MyWatchFaceEngine`實作，watch 錶面停用消除鋸齒環境模式時。 

如需有關這個方法的詳細資訊，請參閱 Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) API 文件。


#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

顯示或隱藏，每當監看式變成會呼叫這個方法。 在`MyWatchFaceEngine`，這個方法註冊/取消註冊 （如下所述） 的可見性狀態根據時區收件者。 

如需有關這個方法的詳細資訊，請參閱 Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) API 文件。

 
### <a name="time-zone-feature"></a>時區功能

新**MyWatchFaceService.cs**也包含更新時區變更 （例如而旅行跨時區） 的目前時間的功能。 結尾附近的**MyWatchFaceService.cs**、 區域變更時間`BroadcastReceiver`定義可處理時區變更的意圖物件： 

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

`RegisterTimezoneReceiver`和`UnregisterTimezoneReceiver`方法呼叫`OnVisibilityChanged`方法。 
`UnregisterTimezoneReceiver` 時，呼叫 watch 錶面的可見性狀態變更為隱藏。 當 watch 錶面出現時，`RegisterTimezoneReceiver`呼叫 (請參閱`OnVisibilityChanged`方法)。 

引擎`RegisterTimezoneReceiver`方法宣告此時區收件者的處理常式`Receive`事件; 此處理常式更新`time`與新時間每當跨越時區的物件： 

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

建立和註冊時區接收者意圖的篩選器：

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

`UnregisterTimezoneReceiver`方法會移除註冊時區收件者：

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>執行改善的 Watch 錶面

建置並重新部署到損耗裝置的應用程式。 監看式朝選擇器為之前選取 watch 錶面。 監看式選擇器中的預覽會顯示在左邊，並在右側顯示新的 watch 錶面：

[![類比 watch 錶面](creating-a-watchface-images/13-analog-watchface.png "改善類比朝選擇器中，並在裝置上")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

在這個螢幕擷取畫面，秒針移動秒一次。 當您損耗裝置上執行此程式碼時，監看式進入環境的模式時，就會消失秒針。

 
## <a name="summary"></a>總結

在本逐步解說中，自訂的 Android 穿 watchface 已實作並測試。 `CanvasWatchFaceService`和`CanvasWatchFaceService.Engine`類別所導入，而且會引擎類別的重要方法已實作以建立簡單的數位 watch 錶面。 這項實作已更新具有多個功能，可建立類比 watch 錶面中，並實作其他方法來處理變更的可見性、 環境模式，以及裝置屬性的差異。 最後，時區廣播收件者已實作，使監看式會自動更新時跨越時區的時間。 


## <a name="related-links"></a>相關連結

- [建立監看式字體](https://developer.android.com/training/wearables/watch-faces/index.html)
- [WatchFace 範例](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)
- [WatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
