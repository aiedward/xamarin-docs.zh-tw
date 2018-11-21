---
title: 建立 Android Wear 1.0 Watch 錶面
description: 本指南說明如何實作自訂的監看式臉部服務適用於 Android Wear 1.0。 數位監看式臉部服務，後面接著更多的程式碼，以建立類比樣式錶建置一個刪減功能會提供逐步的指示。
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/23/2018
ms.openlocfilehash: 067a39838fbfe3f1b33ac0d30b5069366b11e407
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171998"
---
# <a name="creating-a-watch-face"></a>建立錶面

_本指南說明如何實作自訂的監看式臉部服務適用於 Android Wear 1.0。數位監看式臉部服務，後面接著更多的程式碼，以建立類比樣式錶建置一個刪減功能會提供逐步的指示。_

## <a name="overview"></a>總覽

在本逐步解說，說明建立自訂的 Android Wear 1.0 watch 錶面 essentials 建立基本的監看式臉部服務。
初始的監看式臉部服務會顯示簡易數位監看式，顯示目前的時間，以小時和分鐘：

[![數位 watch 錶面](creating-a-watchface-images/01-initial-face.png "初始數位錶範例螢幕擷取畫面")](creating-a-watchface-images/01-initial-face.png#lightbox)

此數位 watch 錶面是開發及測試之後，會新增更多程式碼，將它升級到更複雜的類比的 watch 錶面，具有三個實際操作：

[![類比 watch 錶面](creating-a-watchface-images/02-example-watchface.png "最終的類比錶範例螢幕擷取畫面")](creating-a-watchface-images/02-example-watchface.png#lightbox)

監看式臉部服務結合在一起，安裝為 Wear 1.0 應用程式的一部分。 在下列範例中，`MainActivity`包含只從 Wear 1.0 應用程式範本的程式碼，以便監看式臉部服務可以封裝並部署至智慧型 watch 應用程式的一部分。 作用中，此應用程式將作為純粹開始監看式臉部服務載入 Wear 1.0 裝置 （或模擬器） 的工具進行偵錯和測試。

## <a name="requirements"></a>需求

若要實作監看式臉部服務，需要下列條件：

-   Android 5.0 (API level 21) 或更高版本，在 Wear 裝置或模擬器上。

-   [Xamarin Android Wear 支援程式庫](https://www.nuget.org/packages/Xamarin.Android.Wear)必須新增至 Xamarin.Android 專案。

雖然 Android 5.0 是最低 API 層級實作監看式臉部服務，而 Android 5.1 或更新版本，建議您使用。 Android Wear 裝置執行 Android 5.1 (API 22)，或允許 Wear 應用程式，以控制裝置處於低電源時顯示在螢幕上的高*環境*模式。 當裝置離開低耗電*環境*模式，它處於*互動式*模式。 如需有關這些模式的詳細資訊，請參閱[讓您的應用程式顯示](https://developer.android.com/training/wearables/apps/always-on.html)。


## <a name="start-an-app-project"></a>啟動應用程式專案

建立新的 Android Wear 1.0 專案，稱為**WatchFace** (如需建立新的 Xamarin.Android 專案的詳細資訊，請參閱[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![新增專案 對話方塊](creating-a-watchface-images/03-wear-project-vs-sml.png "新增專案 對話方塊中選取穿戴式應用程式")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![新增專案 對話方塊](creating-a-watchface-images/03-wear-project-xs-sml.png "新增專案 對話方塊中選取穿戴式應用程式")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----


將套件名稱設定為`com.xamarin.watchface`:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![封裝名稱 設定](creating-a-watchface-images/04-package-name-vs.png "設 com.xamarin.watchface 封裝名稱")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![封裝名稱 設定](creating-a-watchface-images/04-package-name-xs.png "設 com.xamarin.watchface 封裝名稱")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

此外，向下捲動，並啟用**網際網路**並**WAKE_LOCK**權限：

[![必要權限](creating-a-watchface-images/05-required-permissions-vs.png "啟用網際網路和 WAKE_LOCK 權限")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要設定的最低 Android 版本**Android 5.1 （API 層級 22）**。
此外，啟用**網際網路**並**WakeLock**權限：

[![必要權限](creating-a-watchface-images/05-required-permissions-xs.png "啟用網際網路和 WakeLock 權限")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

接下來，下載[preview.png](creating-a-watchface-images/preview.png) &ndash;這會新增至**可繪製資源來**稍後在本逐步解說中的資料夾。


## <a name="add-the-xamarinandroid-wear-package"></a>新增 Xamarin.Android Wear 套件

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

啟動 NuGet 套件管理員 (在 Visual Studio 中，以滑鼠右鍵按一下**參考**中**方案總管**，然後選取**管理 NuGet 套件...**).專案更新至最新穩定版本**Xamarin.Android.Wear**:

[![新增 NuGet 套件管理員](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "新增 Xamarin.Android.Wear 封裝")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

接下來，如果**Xamarin.Android.Support.v13**是安裝，請將它解除安裝：

[![NuGet 套件管理員移除](creating-a-watchface-images/07-uninstall-v13-sml.png "移除 Xamarin.Support.v13")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

啟動 NuGet 套件管理員 (在 Visual Studio for Mac，請以滑鼠右鍵按一下**封裝**中**方案窗格**，然後選取**新增套件...**).專案更新至最新穩定版本**Xamarin.Android.Wear**:

[![新增 NuGet 套件管理員](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "新增 Xamarin.Android.Wear 封裝")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----


建置並在 Wear 裝置或模擬器上執行應用程式 (如需如何執行這項操作的詳細資訊，請參閱[開始使用](~/android/wear/get-started/index.md)指南)。 您應該會在 Wear 裝置上看到下列的應用程式畫面：

[![應用程式螢幕擷取畫面](creating-a-watchface-images/08-app-screen.png "Wear 裝置上的應用程式畫面")](creating-a-watchface-images/08-app-screen.png#lightbox)

此時，基本的穿戴式應用程式沒有監看式臉部功能因為它尚未提供監看式臉部服務實作。 接下來將會新增這項服務。


## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Android Wear 實作監看透過臉部`CanvasWatchFaceService`類別。 `CanvasWatchFaceService` 衍生自`WatchFaceService`，而其本身衍生自`WallpaperService`如下圖所示：

[![繼承圖表](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService 繼承圖表")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` 包含巢狀`CanvasWatchFaceService.Engine`; 它會具現化`CanvasWatchFaceService.Engine`到實際執行工作的繪圖 watch 錶面的物件。 `CanvasWatchFaceService.Engine` 衍生自`WallpaperService.Engine`在上圖所示。

未顯示在此圖中是`Canvas`所`CanvasWatchFaceService`使用它們來繪製 watch 錶面&ndash;這`Canvas`透過傳入`OnDraw`方法，如下所述。

在下列章節中，自訂監看式臉部服務將會建立依照下列步驟：

1.  定義類別，稱為`MyWatchFaceService`衍生自`CanvasWatchFaceService`。

2.  內`MyWatchFaceService`，建立一個稱為巢狀的類別`MyWatchFaceEngine`衍生自`CanvasWatchFaceService.Engine`。

3.  在  `MyWatchFaceService`，實作`CreateEngine`具現化方法`MyWatchFaceEngine`並將它傳回。

4.  在  `MyWatchFaceEngine`，實作`OnCreate`方法來建立監看式臉部樣式，並執行任何其他的初始設定工作。

5.  實作`OnDraw`方法的`MyWatchFaceEngine`。 每當 watch 錶面需要重新繪製時，會呼叫這個方法 (亦即*失效*)。 `OnDraw` 為繪製 （並重新繪製） 監看式臉部項目，例如小時、 分鐘和第二個實際操作的方法。

6.  實作`OnTimeTick`方法的`MyWatchFaceEngine`。
    `OnTimeTick` 會每分鐘 （在環境和互動式模式） 或日期/時間已變更時呼叫一次。

如需詳細資訊`CanvasWatchFaceService`，請參閱 Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) API 文件。
同樣地， [CanvasWatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html)說明 watch 錶面的實際實作。


### <a name="add-the-canvaswatchfaceservice"></a>新增 CanvasWatchFaceService

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

加入新的檔名**MyWatchFaceService.cs** (在 Visual Studio 中，以滑鼠右鍵按一下**WatchFace**中**方案總管] 中**，按一下 [**新增 > 新增項目...**，然後選取**類別**)。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

加入新的檔名**MyWatchFaceService.cs** (在 Visual Studio for Mac，請以滑鼠右鍵按一下**WatchFace**專案，按一下 **新增 > 新增檔案...**，然後選取**空類別**)。

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

`MyWatchFaceService` (衍生自`CanvasWatchFaceService`) 是 「 主要程式 」 的 watch 錶面。 `MyWatchFaceService` 實作只有一個方法， `OnCreateEngine`，它會具現化，並傳回`MyWatchFaceEngine`物件 (`MyWatchFaceEngine`衍生自`CanvasWatchFaceService.Engine`)。 具現化`MyWatchFaceEngine`必須傳回物件，做為`WallpaperService.Engine`。 封裝`MyWatchFaceService`物件會傳遞至建構函式。

`MyWatchFaceEngine` 是實際的監看式臉部實作&ndash;它包含繪製 watch 錶面程式碼。 它也會處理畫面變更 （環境/互動模式中，螢幕關閉等）。 例如的系統事件。


### <a name="implement-the-engine-oncreate-method"></a>實作引擎 OnCreate 方法

`OnCreate`方法會初始化 watch 錶面。 加入下列欄位`MyWatchFaceEngine`:

```csharp
Paint hoursPaint;
```

這`Paint`物件將用來繪製 watch 錶面上的目前時間。 接下來，新增下列方法加入`MyWatchFaceEngine`:

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

`OnCreate` 不久之後呼叫`MyWatchFaceEngine`已啟動。 它會設定`WatchFaceStyle`（這會控制在 Wear 裝置與使用者之間的互動方式） 並具現化`Paint`會用來顯示時間的物件。

若要呼叫`SetWatchFaceStyle`會進行下列作業：

1.  設定*查看模式*到`PeekModeShort`，因而導致顯示為小的 「 查看 」 卡片上顯示的通知。

2.  若要設定背景的可見性`Interruptive`，因而導致顯示僅簡單如果它代表造成作業中斷的通知查看卡片的背景。

3.  停用，以便自訂的 watch 錶面可以改為顯示時，watch 錶面上所繪製的預設系統 UI 時間。

如需有關這些和其他 watch face 樣式選項的詳細資訊，請參閱 Android [WatchFaceStyle.Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) API 文件。

在後`SetWatchFaceStyle`完成時，`OnCreate`具現化`Paint`物件 (`hoursPaint`) 並設定其色彩為白色、 48 像素為單位的文字大小 ([TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29)必須指定像素為單位)。


### <a name="implement-the-engine-ondraw-method"></a>實作引擎 OnDraw 方法

`OnDraw`方法可能是最重要`CanvasWatchFaceService.Engine`方法&ndash;這是實際繪製觀看臉部項目，例如數字，時鐘表面的實際操作的方法。
在下列範例中，它會 watch 錶面上繪製的時間字串。
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

當呼叫 Android `OnDraw`，它就會傳遞`Canvas`執行個體，可以在其中繪製所面臨的界限。 在上述程式碼範例中，`DateTime`用來計算的目前的時間，以小時和分鐘 （12 小時制）。 使用畫布上繪製產生的時間字串`Canvas.DrawText`方法。 字串會顯示 70 個像素透過從左邊的緣和 80 的像素為單位向下的上緣。

如需詳細資訊`OnDraw`方法，請參閱 Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine#ondraw) API 文件。


### <a name="implement-the-engine-ontimetick-method"></a>實作引擎 OnTimeTick 方法

Android 會定期呼叫`OnTimeTick`方法來更新 watch 錶面所顯示的時間。 每分鐘 （在環境和互動式模式中），至少一次或日期/時間或時區已變更時，它會在進行呼叫。 將下列方法加入`MyWatchFaceEngine`:

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

這個實作`OnTimeTick`只會呼叫`Invalidate`。 `Invalidate`方法排程`OnDraw`重繪 watch 錶面。

如需詳細資訊`OnTimeTick`方法，請參閱 Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) API 文件。


## <a name="register-the-canvaswatchfaceservice"></a>註冊 CanvasWatchFaceService

`MyWatchFaceService` 必須在中註冊**AndroidManifest.xml**的相關聯的穿戴式應用程式。 若要這樣做，請新增下列 XML 來`<application>`區段：

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

1.  設定`android.permission.BIND_WALLPAPER`權限。 此權限可讓您監看式臉部服務權限，若要變更裝置上的系統底色圖案。 請注意，必須在設定此權限`<service>`區段，而不是在外部`<application>`一節。

2.  定義`watch_face`資源。 此資源是簡短的 XML 檔案會宣告`wallpaper`（下一節中，此檔案將會建立） 的資源。

3.  宣告的可繪製的映像，稱為`preview`，會顯示 [監看式選擇器選擇] 畫面。

4.  包含`intent-filter`可讓 Android 知道`MyWatchFaceService`將要顯示 watch 錶面。

完成基本的程式碼`WatchFace`範例。 下一個步驟是新增必要的資源。


## <a name="add-resource-files"></a>新增資源檔

您可以執行監看式服務之前，您必須新增**watch_face**資源和預覽影像。 首先，建立新的 XML 檔案，在**Resources/xml/watch_face.xml**並以下列 XML 取代其內容：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

將此檔案的建置動作設定為**AndroidResource**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![建置動作](creating-a-watchface-images/10-android-resource-vs.png "設定組建 AndroidResource 的動作")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![建置動作](creating-a-watchface-images/10-android-resource-xs.png "設定組建 AndroidResource 的動作")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

這個資源檔會定義簡單`wallpaper`項目，將會用於 watch 錶面。

如果您尚未這樣做，下載[preview.png](creating-a-watchface-images/preview.png)。
安裝在**Resources/drawable/preview.png**。 請務必將此檔案來新增`WatchFace`專案。 此預覽影像會顯示在 Wear 裝置上的監看式臉部選擇器中的使用者。 若要建立預覽影像錶面，，執行時，可能需要 watch 錶面螢幕擷取畫面。 (如需有關從 Wear 裝置取得螢幕擷取畫面的詳細資訊，請參閱[螢幕擷取畫面](~/android/wear/deploy-test/debug-on-device.md#screenshots))。


## <a name="try-it"></a>試試看 ！

建置並部署到在 Wear 裝置的應用程式。 您應該會看到與之前出現 Wear 應用程式畫面。 執行下列命令以啟用新的 watch 錶面：

1.  撥動至右方，直到您看到 [監看式] 畫面的背景。

2.  觸控並兩秒的任何位置按住畫面的背景。

3.  從左撥動以瀏覽各種監看式臉部的權限。

4.  選取  **Xamarin 範例**錶面 （右邊所示）：

    [![Watchface 選擇器](creating-a-watchface-images/11-watchface-picker.png "撥動以找出 Xamarin 範例 watch 錶面")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5.  點選**Xamarin 範例**錶面，以選取它。

這會變更在 Wear 裝置，若要使用自訂監看式臉部服務實作為止的 watch 錶面：

[![數位 watch 錶面](creating-a-watchface-images/12-digital-watchface.png "Wear 裝置上執行自訂數位監看式")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

這是相當粗糙錶面，因為應用程式的實作是因此最小 (比方說，它不包含監看式表面背景，而不會呼叫`Paint`圓滑方法，以改善外觀)。
不過，它會實作，才能建立自訂的錶面的基本功能。

在下一步 區段中，此 watch 錶面將會升級為更複雜的實作。


## <a name="upgrading-the-watch-face"></a>升級 watch 錶面

這項逐步解說的其餘部分`MyWatchFaceService`升級為顯示類比樣式錶並擴充以支援更多的功能。 若要建立已升級的 watch 錶面，就會新增下列功能：

1.  指出類比小時、 分鐘、 與第二個實際操作的時間。

2.  回應變更的可見度。

3.  回應環境的模式和互動式模式之間的變更。

4.  讀取基礎 Wear 裝置的屬性。

5.  會自動更新時的時區變更發生的時間。

實作下列程式碼變更之前, 下載[drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true)、 解壓縮，然後移動要解壓縮的.png 檔案**資源/drawable** (覆寫先前**preview.png**). 將新的.png 檔案，以新增`WatchFace`專案。


### <a name="update-engine-features"></a>更新引擎功能

下一個步驟是升級**MyWatchFaceService.cs**繪製類比 watch 錶面，並支援新功能的實作。 內容取代**MyWatchFaceService.cs**中的監看式臉部程式碼的類比版本[MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (您可以剪下並貼入此來源的現有**MyWatchFaceService.cs**)。

本版**MyWatchFaceService.cs**將更多程式碼加入至現有的方法，並包含額外的覆寫的方法，以新增更多的功能。 下列各節提供原始程式碼的導覽。

#### <a name="oncreate"></a>OnCreate

已更新**OnCreate**方法會如往常一般，設定監看式臉部樣式，但是它包含一些額外的步驟：

1.  若要設定的背景影像**xamarin_background**資源位於**Resources/drawable-hdpi/xamarin_background.png**。

2.  初始化`Paint`時針分針，秒針繪製的物件。

3.  初始化`Paint`繪製邊緣的 watch 錶面小時刻度的物件。

4.  建立計時器呼叫`Invalidate`（重繪） 方法，讓第二個交會重新繪製每隔一秒。 請注意，此計時器必須因為`OnTimeTick`呼叫`Invalidate`只要每隔一分鐘。

此範例包含一個**xamarin_background.png**映像; 不過，您可能想要建立將支援自訂錶面每個螢幕密度的不同的背景影像。

#### <a name="ondraw"></a>OnDraw

已更新**OnDraw**方法繪製的類比樣式 watch 錶面，使用下列步驟：

1.  取得目前的時間，現在會保留在`time`物件。

2.  判斷的繪圖介面和其中心點的界限。

3.  繪製背景，調整為適合的裝置，在繪製背景時。

4.  繪製十二*刻度*周圍時鐘表面的 （對應至在時鐘表面的時數）。

5.  計算角度、 旋轉和每一個監看式指針的長度。

6.  監看式介面上繪製每一個指針。 請注意是否監看式是環境的模式，不繪製秒針。


#### <a name="onpropertieschanged"></a>OnPropertiesChanged

會呼叫此方法以通知`MyWatchFaceEngine`需 Wear 裝置 （例如低位元環境的模式和燒機保護） 的屬性。 在  `MyWatchFaceEngine`，針對低位元環境的模式，這個方法只會檢查 （低位元環境的模式，螢幕支援較少的位元每一種色彩）。

如需有關這個方法的詳細資訊，請參閱 Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) API 文件。


#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

在 Wear 裝置進入或離開環境的模式時，會呼叫這個方法。 在 `MyWatchFaceEngine`實作，watch 錶面會停用消除鋸齒時在環境的模式。

如需有關這個方法的詳細資訊，請參閱 Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) API 文件。


#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

顯示或隱藏，每當監看式變成會呼叫這個方法。 在  `MyWatchFaceEngine`，此方法註冊/取消註冊 （如下所述） 的可見性狀態根據時區接收者。

如需有關這個方法的詳細資訊，請參閱 Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) API 文件。


### <a name="time-zone-feature"></a>時區的功能

新**MyWatchFaceService.cs**也包含更新時間區域變更 （例如當旅行跨時區） 的目前時間的功能。 結尾附近**MyWatchFaceService.cs**，區域變更的時間`BroadcastReceiver`定義處理時區變更 Intent 物件：

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

`RegisterTimezoneReceiver`並`UnregisterTimezoneReceiver`方法會呼叫`OnVisibilityChanged`方法。
`UnregisterTimezoneReceiver` 呼叫時所面臨的監看式的可見性狀態變更為隱藏。 Watch 錶面看到時再次`RegisterTimezoneReceiver`呼叫 (請參閱`OnVisibilityChanged`方法)。

引擎`RegisterTimezoneReceiver`方法會宣告此時區收件者的處理常式`Receive`事件; 此處理常式更新`time`與新時間時超出時區的物件：

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

建立並註冊時區接收者意圖篩選：

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

`UnregisterTimezoneReceiver`方法取消註冊時區接收者：

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>執行改善的 watch 錶面

建置並重新部署到在 Wear 裝置的應用程式。 Watch 錶面從監看式臉部選擇器選取做為之前。 監看式選擇器中的預覽會顯示在左邊，並新增 watch 錶面會顯示在右側：

[![類比 watch 錶面](creating-a-watchface-images/13-analog-watchface.png "改善在選擇器和裝置上的類比臉部")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

在這個螢幕擷取畫面，第二個手動移動每秒一次。 當您在 Wear 裝置上執行此程式碼時，監看式進入環境的模式時，就會消失秒針。


## <a name="summary"></a>總結

在本逐步解說中，自訂的 Android Wear 1.0 watchface 是實作和測試。 `CanvasWatchFaceService`和`CanvasWatchFaceService.Engine`類別所導入，並不可或缺的引擎類別方法的實作會將建立簡單的數位錶面。 此實作中已使用更多的功能，來建立類比錶面，更新和其他方法的實作會將處理變更可見性、 環境的模式和裝置屬性的差異。 最後，時區的廣播的接收器實作以便監看式，會自動更新何時跨越時區的時間。


## <a name="related-links"></a>相關連結

- [建立監看式臉部](https://developer.android.com/training/wearables/watch-faces/index.html)
- [WatchFace 範例](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)
- [WatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
