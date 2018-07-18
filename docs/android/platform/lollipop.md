---
title: 棒棒糖符號功能
description: 本文章提供 Android 5.0 （棒棒糖符號） 中導入的新功能的高階概觀。 這些功能包括新的使用者介面樣式，稱為材料佈景主題，以及新支援的功能，例如動畫、 檢視陰影和 drawable 濃淡。 Android 5.0 也包含增強的通知、 兩個新的 UI widgets、 新的工作排程器和改善儲存體、 網路、 連接及多媒體功能少數幾個新的 Api。
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: cdef611525abbe4f066959c0ac56380b1c617747
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773611"
---
# <a name="lollipop-features"></a>棒棒糖符號功能

_本文章提供 Android 5.0 （棒棒糖符號） 中導入的新功能的高階概觀。這些功能包括新的使用者介面樣式，稱為材料佈景主題，以及新支援的功能，例如動畫、 檢視陰影和 drawable 濃淡。Android 5.0 也包含增強的通知、 兩個新的 UI widgets、 新的工作排程器和改善儲存體、 網路、 連接及多媒體功能少數幾個新的 Api。_

## <a name="lollipop-overview"></a>棒棒糖符號概觀

Android 5.0 （棒棒糖符號） 導入了新的設計語言，*材料設計*，並使用它支援轉換的新功能，使應用程式更容易且更具直覺性使用。 利用資料的設計，Android 5.0 不僅可提供 Android 手機的外觀。它也提供一組新的設計規則 android 平板電腦、 桌上型電腦、 監看和智慧電視。 這些設計規則強調簡化和 minimalism 時進行，並使用熟悉的 tactile 屬性 （例如實際的介面和邊緣提示） 來協助使用者快速並直覺的方式了解介面。

*材料佈景主題*是 Android UI 設計原則的體現。 本文一開始會涵蓋材料佈景主題支援的功能：

-   **動畫** &ndash; *觸控意見反應*動畫*活動轉換*動畫*檢視狀態轉換*動畫和*顯示效果*。

-   **檢視陰影和提高權限**&ndash;檢視現在有`elevation`屬性，則使用檢視較高`elevation`值轉型的較大的陰影的背景。

-   **色彩功能** &ndash; *Drawable 濃淡*可讓您變更其色彩，以重複使用的影像資產和*顯著色彩擷取*可協助您以動態方式您的應用程式基礎映像中的色彩佈景主題。

許多功能已內建的材料佈景主題 Android 5.0 UI 體驗，而其他人必須明確加入至應用程式。 例如，部分標準檢視 （例如按鈕） 已經包含觸控意見反應動畫，而應用程式必須啟用大部分檢視陰影。

除了帶動透過材料佈景主題 UI 增強功能的情況下，Android 5.0 也包含數個其他新功能涵蓋在本文中：

-   **增強型通知** &ndash; Android 5.0 中的通知已大幅更新為新的外觀、 鎖定畫面通知的支援和新*抬頭*通知呈現格式。

-   **新的 UI widgets** &ndash;新`RecyclerView`widget 讓傳達大型資料集和複雜資訊，以及新的應用程式更容易`CardView`widget 提供簡化的類似賀卡的呈現格式來顯示文字和映像。

-   **新的 Api** &ndash; Android 5.0 將新的 Api，針對多個網路支援，改進了藍芽連線、 方便的儲存體管理及多媒體播放程式和網路攝影機裝置更有彈性的控制項。 新的工作排程功能是可執行工作以非同步方式在排定的時間。 這項功能有助於改善電池壽命，例如，排程工作，當裝置已插入電源時以及充電。


## <a name="requirements"></a>需求

需要下列項目在 Xamarin 應用程式中使用新的 Android 5.0 功能：

-   **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更新版本必須安裝並設定 Visual Studio 或 Visual Studio for mac。 

-   **Android SDK** &ndash; Android 5.0 (API 21) 或更新版本必須安裝 Android SDK Manager 透過。

-   **Java Developer Kit** &ndash; Xamarin.Android 需要[JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本，如果您正在開發的應用程式開發介面層級 24 或更大 （JDK 1.8 也支援應用程式開發介面層級早於 24，包括棒棒糖符號）。 如果您使用自訂控制項或表單預覽程式需要 64 位元版本的 JDK 1.8。

您可以繼續使用[JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)如果您是開發應用程式開發介面層級 23 專為或更早版本。


## <a name="setting-up-an-android-50-project"></a>設定 Android 5.0 專案

若要建立 Android 5.0 專案時，您必須安裝的最新工具和 SDK 封裝。 若要設定目標為 Android 5.0 Xamarin.Android 專案中使用下列步驟：

1. 安裝 Xamarin.Android 工具並啟用您的 Xamarin 授權。 請參閱[設定及安裝](~/android/get-started/installation/index.md)如需有關安裝 Xamarin.Android。

2. 如果您使用 Visual Studio for Mac，安裝最新版的 Android 5.0 更新。

3. 啟動 Android SDK Manager (在 Visual Studio for Mac 使用**工具&gt;開啟 Android SDK Manager&hellip;**) 並安裝 Android SDK 工具 23.0.5 或更新版本：

    [![選取 Android SDK Manager 中的 Android SDK 工具](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   此外，安裝最新 Android 5.0 SDK 封裝 (API 21 或更新版本):

    [![在 Android SDK Manager 安裝 Android 5.0 SDK 封裝](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   如需使用 Android SDK Manager 的詳細資訊，請參閱[SDK Manager](http://developer.android.com/tools/help/sdk-manager.html)。

4. 建立新的 Xamarin.Android 專案。 如果您是使用 Xamarin Android 開發的新手，請參閱[Hello，Android](~/android/get-started/hello-android/index.md)來了解如何建立 Android 專案。 當您建立 Android 專案時，請務必設定 Android 5.0 的版本設定。
   在 Visual Studio for Mac，瀏覽至**專案選項&gt;建置&gt;一般**並設定**目標 framework**至**Android 5.0 （棒棒糖符號）** 或更新版本：

    ![設定目標 Framwework 至 Android 5.0 棒棒糖符號](lollipop-images/target-framework.png)

   在下**專案選項&gt;建置&gt;Android 應用程式**、 設定最小值和目標 Android 版本以**Automatic-使用目標 framework 版本**:

    ![最小值和目標 Android 版本設定為自動](lollipop-images/minimum-android-version.png)

5. 設定模擬器或 Android 裝置，若要測試您的應用程式。 如果您使用模擬器，請參閱[Android 模擬器設定](~/android/get-started/installation/android-emulator/index.md)以了解如何使用 Visual Studio 或 Xamarin Studio 來設定使用的 Android 模擬器。 如果您使用 Android 裝置，請參閱[設定註冊預覽 SDK](https://developer.android.com/preview/setup-sdk.html)以了解如何為 Android 5.0 更新您的裝置。 若要設定 Android 裝置執行及偵錯 Xamarin.Android 應用程式，請參閱[設定註冊裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)。

注意： 如果您要更新現有的 Android 專案的目標 Android L 預覽，您必須更新**目標 Framework**和**Android 版本**上面所述的值。

## <a name="important-changes"></a>重大變更

先前已發行的 Android 應用程式可能會影響 Android 5.0 中的變更。 Android 5.0 特別的是，會使用新的執行階段，並大幅變更的通知格式。

### <a name="android-runtime"></a>Android 的執行階段

Android 5.0 而 Dalvik 非預設的執行階段為使用新 Android 執行階段 （圖案）。 封面實作數個主要的新功能：

-   **前時間 (AOT) 編譯** &ndash; AOT 可以改善應用程式第一次啟動前編譯應用程式程式碼應用程式效能。 安裝應用程式時，圖案就會產生編譯的應用程式可執行檔的目標裝置。

-   **改進記憶體回收 (GC)** &ndash; GC 圖案中的增強功能也可以改善應用程式效能。 現在回收則使用兩個，而不是一個 GC 暫停和並行 GC 作業完成以更即時的方式。

-   **改善應用程式偵錯**&ndash;封面提供診斷詳細資料以協助分析例外狀況和當機報告。

現有的應用程式應該不會變更在封面運作&ndash;除了利用技術特有先前 Dalvik 執行階段的應用程式，這可能無法運作下美工圖案。 如需有關這些變更的詳細資訊，請參閱[驗證應用程式行為上 Android 執行階段 （圖案）](http://developer.android.com/guide/practices/verifying-apps-art.html)。


### <a name="notification-changes"></a>通知變更

通知已大幅變更，在 Android 5.0:

-   **音效及震動的處理方式不同**&ndash;通知聲音和振動的情況下現在由處理`Notification.Builder`而不是`Ringtone`， `MediaPlayer`，和`Vibrator`。

-   **新的色彩配置**&ndash;材料佈景主題，根據通知，以深色文字呈現透過白色或非常輕微的背景。 此外，Android 協調使用系統色彩配置可能會修改通知圖示中的 alpha 色板。 

-   **鎖定畫面通知**&ndash;現在會在裝置鎖定畫面上顯示通知。

-   **平視**&ndash;高優先順序通知現在會出現在小型的浮動視窗 （抬頭通知） 時解除鎖定裝置為止，並在螢幕就會開啟。

在大部分情況下，移轉至 Android 5.0 的現有應用程式通知功能需要下列步驟：

1.  若要使用的程式碼轉換`Notification.Builder`(或`NotificationsCompat.Builder`) 建立通知。 

2.  請確認您現有的通知資產是可在新的材料佈景主題色彩配置。

3.  決定在鎖定畫面上出現時，您的通知應該有哪些可見性。 通知不是公用的如果內容應該顯示在鎖定畫面上？

4.  設定您的通知的類別目錄，因此正確處理新的 Android 5.0*請勿打擾*模式。

如果您的通知會顯示傳輸控制項，顯示媒體播放狀態，請使用`RemoteControlClient`，或呼叫`ActivityManager.GetRecentTasks`，請參閱[重要行為變更](http://developer.android.com/preview/api-overview.html#Behaviors)如需有關更新您 android 的通知5.0。

在 Android 中建立通知相關資訊，請參閱[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。 [相容性](~/android/app-fundamentals/notifications/local-notifications.md#compatibility)本文一節說明如何建立通知，向下相容舊版的 Android。


## <a name="material-theme"></a>材料佈景主題

新的 Android 5.0 材料佈景主題的外觀與風格 Android 的 UI 帶來審慎的變更。 視覺項目現在會使用 tactile 粗體圖形、 印刷樣式和明亮色彩列印架構設計採取的介面。 下列螢幕擷取畫面中，但顯示材料佈景主題的範例：

[![材料佈景主題首頁畫面、 應用程式畫面上，並設定螢幕的螢幕擷取畫面](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 greets 您與顯示在左邊的主畫面。 Center 螢幕擷取畫面的第一個畫面的應用程式清單中，且螢幕擷取畫面，右邊**設定**螢幕。 Google[材料設計](https://material.io/guidelines/material-design/introduction.html)規格說明新的材料佈景主題概念背後的基本設計規則。

材料佈景主題包含您可以使用您的應用程式中的三種內建口味：`Theme.Material`暗色調佈景主題 （預設）、`Theme.Material.Light`佈景主題，而`Theme.Material.Light.DarkActionBar`佈景主題： 

[![深色螢幕擷取畫面、 光線及 DarkActionBar 佈景主題](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

如需詳細資訊 Xamarin.Android 應用程式中使用資料的佈景主題的功能，請參閱[材料佈景主題](~/android/user-interface/material-theme.md)。


## <a name="animations"></a>Animations

Android 5.0 提供觸控意見反應動畫、 活動轉換動畫，以及讓應用程式介面使用更具直覺性的檢視狀態轉換動畫。 此外，Android 5.0 應用程式可以使用*顯示效果*動畫來隱藏或顯示的檢視。 您可以使用*彎曲的影片*呈現設定來設定速度或很慢的動畫。


### <a name="touch-feedback-animations"></a>觸控意見反應動畫

檢視有接觸到的視覺化回饋使用者來執行提供觸控意見反應動畫。 例如，按鈕現在會顯示產生漣漪效果何時可接觸&ndash;這是預設觸控意見反應動畫 Android 5.0 版。 Ripple 動畫藉由新`RippleDrawable`類別。 在檢視的範圍結束或超出檢視的範圍可以設定漣漪效果。 例如，以下螢幕擷取畫面的順序說明按鈕中產生漣漪效果觸控動畫期間：

![框架的按鈕上的 ripple 動畫畫面格螢幕擷取畫面](lollipop-images/touch-animation.png)

初始觸控與按鈕會在左側的第一個影像，而其餘的順序 （從左到右） 說明如何產生漣漪效果分散至按鈕的邊緣。 Ripple 動畫結束時，檢視會傳回原始外觀。 預設 ripple 動畫發生在幾分之一秒，但可自訂的動畫長度長或短的時間長度。

如需有關觸控 Android 5.0 版的意見反應動畫，請參閱 <<c0> [ 自訂觸控意見反應](http://developer.android.com/training/material/animations.html#Touch)。


### <a name="activity-transition-animations"></a>活動轉換動畫

活動轉換動畫為使用者提供視覺化連續性的意義的上當活動轉換到另一個。 應用程式可以指定三種類型的轉換動畫：

-   **輸入轉換**&ndash;的活動時進入場景。

-   **結束轉換**&ndash;的活動時結束場景。

-   **共用的項目轉換**&ndash;為當兩個活動通用的檢視變更為第一個活動轉換到下一步。

例如，下列順序螢幕擷取畫面說明共用項目轉換：

[![框架的共用項目轉換動畫畫面格螢幕擷取畫面](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

共用的項目 （毛蟲相片） 是其中幾種檢視中的第一個活動。它會放大變成唯一的檢視中的第二個的第一個活動轉換的第二個活動。

#### <a name="enter-transition-animation-types"></a>輸入轉換動畫類型

Enter 轉換 Android 5.0 提供三種類型的動畫：

-   **分裂動畫**&ndash;放大的檢視，從場景的中心。

-   **投影片動畫**&ndash;移動檢視從一個場景的邊緣。

-   **淡出動畫**&ndash;淡場景的檢視。

#### <a name="exit-transition-animation-types"></a>結束轉換動畫型別

結束轉換 Android 5.0 提供三種類型的動畫：

-   **分裂動畫**&ndash;場景的中央縮小檢視。

-   **投影片動畫**&ndash;移檢視，場景的邊緣的其中一個。

-   **淡出動畫**&ndash;淡出場景的檢視。

#### <a name="shared-element-transition-animation-types"></a>共用項目轉換動畫類型

共用項目轉換支援多種類型的動畫，例如：

-   變更檢視的配置或剪輯的界限。

-   變更小數位數和檢視的旋轉。

-   變更檢視的大小和小數位數的類型。

如需活動轉換動畫 Android 5.0 的相關資訊，請參閱[自訂活動轉換](http://developer.android.com/training/material/animations.html#Transitions)。


### <a name="view-state-transition-animations"></a>檢視狀態轉換動畫

Android 5.0 讓執行檢視狀態變更時的動畫。 您可以使用下列技術之一，以動畫顯示檢視狀態轉換：

-   建立以動畫顯示與特定檢視相關聯的狀態變更的 drawables。 新`AnimatedStateListDrawable`類別可讓您建立 drawables 顯示檢視狀態變更之間的動畫。

-   定義動畫的功能執行的檢視狀態變更時。 新`StateListAnimator`類別可讓您定義的檢視狀態變更時會執行動畫。

如需詳細資訊檢視狀態轉換動畫 Android 5.0 版，請參閱[以動畫顯示的檢視狀態變更](http://developer.android.com/training/material/animations.html#ViewState)。


### <a name="reveal-effect"></a>顯示效果

*顯示效果*會裁剪圓形來顯示或隱藏檢視該變更 radius。 您可以設定初始和最終裁剪圓形的半徑，以控制這個效果。 以下螢幕擷取畫面的順序將說明從中心螢幕的顯示效果動畫：

[![框架的顯示動畫畫面格螢幕擷取畫面](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

下一個序列說明會從螢幕左下角顯示效果動畫：

[![框架的裁剪動畫畫面格螢幕擷取畫面](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

顯示可以反轉動畫。也就是，裁剪圓形可以壓縮成隱藏檢視而放大以顯示檢視。

如需中的 Android 5.0 顯示效果的詳細資訊，請參閱[使用顯示效果](http://developer.android.com/training/material/animations.html#Reveal)。


### <a name="curved-motion"></a>有個小曲形的影片

除了這些動畫功能，Android 5.0 也提供新的 Api，可讓您指定的時間與影片曲線的動畫。 Android 5.0 中使用這些曲線動畫期間進行插補，時態表和空間移動。 Android 5.0 中定義三個曲線：

-   **快速\_出\_線性\_中**&ndash;加速快速而加速動畫結束之前會繼續。

-   **快速\_出\_緩慢\_中** &ndash; Accelerates 緩時變迅速減速結束時的動畫。

-   **線性\_出\_緩慢\_中**&ndash;開始與尖峰速度緩慢減速動畫的結尾。

您可以使用新`PathInterpolator`類別，以指定如何移動插補會發生。 `PathInterpolator` 是 interpolator 周遊根據指定的控制點和影片曲線動畫路徑。 如需如何在 Android 5.0 中指定曲線的動作設定的詳細資訊，請參閱[使用曲線影片](http://developer.android.com/training/material/animations.html#CurvedMotion)。


## <a name="view-shadows--elevation"></a>檢視陰影和提高權限

在 Android 5.0 中，您可以指定*提高權限*藉由設定 新檢視的`Z`屬性。 大於`Z`值造成檢視較大陰影的背景，製作為 float 高背景上顯示的檢視。 您可以設定檢視的初始提高權限藉由設定其`elevation`版面配置中的屬性。

下列範例說明轉型為空的陰影`TextView`控制當其權限提高屬性設定為 2dp、 4dp 和 6dp，分別：

[![較大檢視陰影 progessively 的螢幕擷取畫面](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

檢視陰影設定可以是靜態 （如上所示），或用於動畫，讓檢視，似乎暫時超過檢視的背景。 您可以使用`ViewPropertyAnimator`類別以動畫方式顯示檢視的權限提高。 檢視的權限提高為其配置的總和`elevation`設定加上一個`translationZ`屬性，您可以透過設定`ViewPropertyAnimator`方法呼叫。

如需詳細資訊檢視陰影 Android 5.0 版，請參閱[定義 Shadows 和結束時間裁剪檢視](http://developer.android.com/training/material/shadows-clipping.html)。


## <a name="color-features"></a>色彩功能

Android 5.0 提供兩個新功能來管理應用程式中的色彩：

-   *Drawable 濃淡*可讓您修改影像資產的色彩配置屬性變更。

-   *顯著色彩擷取*可讓您以動態方式自訂您的應用程式來協調與顯示的影像的色彩調色盤的色彩佈景主題。


### <a name="drawable-tinting"></a>Drawable 濃淡

Android 5.0 配置辨識新`tint`屬性可讓您設定 drawables 的色彩，而不需要建立多個版本的這些資產，若要顯示不同的色彩。 若要使用此功能，您必須定義點陣圖為 alpha 遮罩和使用`tint`屬性來定義的資產的色彩。 這可讓您建立資產一次，並在您的配置，以符合您的佈景主題。

在下列範例中，單一影像資產&ndash;具有透明背景的白色標誌&ndash;用來建立濃淡變化：

![使用透明背景的白色 Xamarin 標誌](lollipop-images/xamarin-logo-white.png)

此標誌會顯示上面藍色圓形的背景，如下列範例所示。 左側影像是標誌會顯示不含`tint`設定。 在中央映像，標誌的`tint`屬性設定為暗灰色。 在右側，映像中`tint`設定為淺灰色：

![使用不同的濃淡設定上述的標誌的範例](lollipop-images/drawable-tinting.png)

如需 drawable 濃淡 Android 5.0 的相關資訊，請參閱[Drawable 濃淡](http://developer.android.com/training/material/drawables.html#DrawableTint)。


### <a name="prominent-color-extraction"></a>顯著色彩擷取

新的 Android 5.0`Palette`類別可讓您從映像擷取的色彩，以便您可以動態地將它們套用至自訂色彩調色盤。 `Palette`類別從映像擷取六種色彩和標籤它們相對的層級的色彩飽和度和亮度根據這些色彩：

-   起來的活躍

-   起來的活躍深色

-   起來的活躍 light

-   靜音

-   靜音的深色

-   光靜音

比方說，在下列螢幕擷取畫面，相片瀏覽應用程式上顯示的映像從擷取的顯著色彩，並使用這些色彩調整應用程式的色彩配置以符合影像：

[![綠色，和藍色佈景主題色彩擷取的螢幕擷取畫面](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

在上面的螢幕擷取畫面，動作列設定為擷取的 「 起來的活躍光源 」 設定為擷取 「 起來的活躍暗色調 」 色彩和背景色彩。 在上述每個範例中，資料列的小型色彩平方隨附說明已從映像擷取的調色盤色彩。

如需色彩解壓縮 Android 5.0 的相關資訊，請參閱[從映像擷取顯著色彩](http://developer.android.com/training/material/drawables.html#ColorExtract)。


## <a name="new-ui-widgets"></a>新的 UI Widget

Android 5.0 導入了兩個新的 UI widget:

-   `RecyclerView` &ndash; 檢視群組，以顯示可捲動項目的清單。

-   `CardView` &ndash; 具有圓角基本版面配置。

這兩個小工具包括燒材料佈景主題功能支援。例如，`RecyclerView`用於加入和移除檢視中，使用動畫和`CardView`使用檢視進行浮動背景上顯示每一張卡片的陰影。 這些新的 widget 的範例是以下列螢幕擷取畫面所示：

[![利用 RecyclerView 建置應用程式的螢幕擷取畫面](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

在左側螢幕擷取畫面是範例`RecyclerView`上使用電子郵件應用程式和螢幕擷取畫面中 」 的權限的範例`CardView`旅遊預約應用程式中使用。


### <a name="recyclerview"></a>RecyclerView

`RecyclerView` 類似於`ListView,`但它比較適合大型集的檢視或動態變更的項目清單。 像`ListView,`指定配接器以存取基礎資料集。 不過，不同於`ListView,`您使用*配置管理員*定位項目內`RecyclerView`。 配置管理員也會負責檢視回收。它會管理重複使用的是使用者可以看見的項目檢視。

當您使用`RecyclerView` widget 中，您必須指定`LayoutManager`和配接器。 上圖所示`LayoutManager`是配接器之間的媒介， `RecyclerView`:

![具有支援 LayoutManager、 配接器和資料集的 RecyclerView 圖表](lollipop-images/recyclerview-diagram.png)

下列螢幕擷取畫面說明`RecyclerView`，其中包含 100 個項目 (每個項目組成`ImageView`和`TextView`):

[![映像中捲動 RecyclerView 應用程式的螢幕擷取畫面](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView` 處理此大型資料集就能輕鬆&ndash;從來結束清單的開頭捲動清單，在此範例中的應用程式會採用只有幾秒鐘。 `RecyclerView` 也支援動畫。事實上，預設會啟用動畫加入和移除項目。 當項目加入`RecyclerView`，它會在這一系列的螢幕擷取畫面所示：

[![框架的相片項目淡出中框架螢幕擷取畫面](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

如需詳細資訊`RecyclerView`，請參閱[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)。


### <a name="cardview"></a>CardView

`CardView` 是簡單的檢視，可以模擬浮點卡片具有圓角。 因為`CardView`具有內建檢視陰影，它提供簡單的方法，以加入應用程式 visual 的深度。 下列螢幕擷取畫面顯示三個文字導向的範例`CardView`:

[![使用 RecyclerView CardView 型項目與應用程式的範例螢幕擷取畫面](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

在上述範例中的卡片的每一個都會包含`TextView`; 的背景色彩會設定透過`cardBackgroundColor`屬性。

如需詳細資訊`CardView`，請參閱[CardView](~/android/user-interface/controls/card-view.md)。


## <a name="enhanced-notifications"></a>增強的通知

使用新的視覺格式和新的功能已大幅更新 Android 5.0 通知系統。 通知 Android 5.0 中有新的外觀。 例如，Android 5.0 中的通知現在會使用深色的文字淺色背景上：

![Android 5.0 通知未展開的範例](lollipop-images/expanded-notification-contracted.png)

大型圖示會顯示通知 （如上述範例所示） 中，Android 5.0 會顯示之小圖示為徽章大型圖示上方。 

在 Android 5.0 中，也可以在裝置鎖定畫面上出現的通知。
例如，以下是具有單一通知鎖定畫面的範例螢幕擷取畫面：

[![在鎖定畫面上顯示通知的螢幕擷取畫面](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

使用者可以點選通知時鎖定畫面來解除鎖定裝置，並跳至的應用程式，產生通知，或若要關閉通知撥動。 有新的通知*可視性*決定設定多少內容可以在鎖定畫面上顯示。 使用者可以選擇是否允許在鎖定畫面通知中顯示的機密內容。

Android 5.0 引進新的高優先順序通知呈現格式，稱為*抬頭*。 平視通知向下滑動從畫面頂端的幾秒鐘的時間，然後回到通知時，其在畫面頂端撤退。 平視通知可讓系統將不會中斷目前正在執行的活動的重要資訊的 UI。 下列範例說明會顯示在應用程式頂端的簡單抬頭通知：

[![Heads-up 通知範例](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

平視通知通常會使用下列事件：

-   新的下一個訊息

-   內送的電話

-   電力偏低表示

-   警示

Android 5.0 抬頭格式顯示通知，只有當它具有很高或最大優先順序設定。

在 Android 5.0 中，您可以提供通知的中繼資料，以協助 Android 排序及更有效地顯示通知。 Android 5.0 組織根據優先順序、 可見性和類別目錄的通知。
通知類別用來篩選可以呈現的通知，當裝置為*請勿打擾*模式。

如需建立和啟動最新的 Android 5.0 功能通知的詳細資訊，請參閱[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。


## <a name="new-apis"></a>新的 API

除了上面所述的新外觀及操作功能，Android 5.0 將新的 Api 擴充功能的現有多媒體、 儲存和無線/連線功能。 此外，Android 5.0 包含新的 Api，以支援新的工作排程器功能。

### <a name="camera"></a>觀景窗

Android 5.0 提供增強的相機功能的數個新的應用程式開發介面。 新`Android.Hardware.Camera2`命名空間包含的功能，存取個別的網路攝影機裝置已連接到 Android 裝置。 此外，`Android.Hardware.Camera2`模型做為管線的每個網路攝影機裝置： 它會接受擷取要求、 擷取映像，並再將結果輸出。 這種方法可讓多個網路攝影機裝置擷取要求排入佇列的應用程式。

下列 Api 實現這些新功能：

-   `CameraManager.GetCameraIdList` &ndash; 可協助您以程式設計方式存取網路攝影機裝置。您使用`CameraManager.OpenCamera`連接到特定的網路攝影機裝置。

-   `CameraCaptureSession` &ndash; 擷取，或從攝影機裝置的影像資料流處理。 您實作`CameraCaptureSession.CaptureListener`介面，以處理新的映像擷取事件。

-   `CaptureRequest` &ndash; 定義擷取參數。

-   `CaptureResult` &ndash; 提供映像的擷取作業的結果。

如需有關新相機 Android 5.0 中的 Api，請參閱[媒體](http://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="audio-playback"></a>音訊播放

Android 5.0 更新`AudioTrack`更好的音訊播放類別：

-   `ENCODING_PCM_FLOAT` &ndash; 設定`AudioTrack`接受浮點格式更好的動態範圍、 更大的空餘空間，以及 （這點受惠增加有效位數） 的高品質的音訊資料。 此外，浮點格式有助於避免音訊剪輯。

-   `ByteBuffer` &ndash; 您現在可以提供要的音訊資料`AudioTrack`做為位元組陣列。

-   `WRITE_NON_BLOCKING` &ndash; 此選項可簡化緩衝和多執行緒的某些應用程式。

如需詳細資訊`AudioTrack`增強功能用於 Android 5.0 版，請參閱[媒體](http://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="media-playback-control"></a>媒體播放控制項

Android 5.0 引進新`Android.Media.MediaController`類別，取代了`RemoteControlClient`。 `Android.Media.MediaController` 提供簡化的傳輸控制應用程式開發介面，並且提供具備執行緒安全控制項的 UI 環境之外的播放。 下列新的 Api 處理傳輸控制：

-   `Android.Media.Session.MediaSession` &ndash; 媒體控制工作階段會處理多個控制站。 您呼叫`MediaSession.GetSessionToken`要求您的應用程式會使用與工作階段互動的語彙基元。

-   `MediaController.TransportControls` &ndash; 處理傳輸命令，例如**播放**，**停止**，和**略過**。

此外，您可以使用新`Android.App.Notification.MediaStyle`關聯 （例如擷取和顯示專輯封面） 的豐富通知內容的媒體工作階段的類別。

如需詳細資訊 Android 5.0 的新的媒體播放控制項功能，請參閱[媒體](http://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="storage"></a>存放裝置

Android 5.0 更新儲存體存取架構，以簡化應用程式，以使用目錄與文件：

-   若要選取子樹狀目錄，您可以建立並傳送`Android.Intent.Action.OPEN_DOCUMENT_TREE`意圖。 此意圖會導致系統顯示所有的提供者執行個體支援的樹狀子目錄選取;然後使用者會瀏覽並選取目錄。

-   若要建立及管理新的文件或目錄子樹狀結構底下的任何位置，您使用新`CreateDocument`， `RenameDocument`，和`DeleteDocument`方法`DocumentsContract`。

-   若要取得所有共用的存放裝置上的媒體的目錄路徑，您呼叫新`Android.Content.Context.GetExternalMediaDirs`方法。

如需新的存放裝置中 Android 5.0 Api 相關資訊，請參閱[儲存體](http://developer.android.com/preview/api-overview.html#Storage)。

### <a name="wireless--connectivity"></a>無線 （& s) 連線

Android 5.0 新增無線及連線的下列應用程式開發介面增強功能：

-   新*多重網路*Api 可讓應用程式，即可尋找並選取特定功能的網路進行連接之前。

-   啟用 Android 5.0 裝置，以做為低能源藍芽週邊設備的藍芽廣播功能。

-   NFC 增強功能可讓您更輕鬆地使用近通訊的功能與其他裝置共用資料。

如需詳細資訊的新的無線和連線中 Android 5.0 Api，請參閱[無線及連線](http://developer.android.com/preview/api-overview.html#Wireless)。

### <a name="job-scheduling"></a>工作排程

Android 5.0 引進新`JobScheduler`API，可協助使用者將電池清空排程裝置已插入電源時，才執行某些工作並充電降到最低。 這個工作排程器功能也可以用於排程要執行條件更適合該工作，例如裝置透過 Wi-fi 網路，而不是計量付費網路連線時下載大型檔案時的工作。

如需新的作業排程中 Android 5.0 Api 相關資訊，請參閱[排程作業](http://developer.android.com/preview/api-overview.html#JobScheduler)。

## <a name="summary"></a>總結

這篇文章 Xamarin.Android 應用程式開發人員提供 Android 5.0 中重要的新功能的概觀：

-   材料佈景主題

-   Animations

-   檢視陰影和提高權限

-   色彩功能，例如 drawable 濃淡和顯著色彩擷取

-   新`RecyclerView`和`CardView`widget

-   通知的增強功能

-   攝影機、 音訊播放、 媒體控制、 儲存體、 無線/連接性及工作排程新的 Api

如果您不熟悉 Xamarin Android 開發，讀取[設定及安裝](~/android/get-started/installation/index.md)可協助您開始使用 Xamarin.Android。
[Hello，Android](~/android/get-started/hello-android/index.md)是絕佳的簡介，以了解如何建立 Android 專案。



## <a name="related-links"></a>相關連結

- [L android 開發人員預覽](http://developer.android.com/preview/index.html)
- [取得 Android SDK](https://developer.android.com/sdk/index.html#Other)
- [材料設計](http://developer.android.com/preview/material/index.html)
- [材料設計原則](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
