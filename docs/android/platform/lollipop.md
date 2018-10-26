---
title: 棒棒糖符號功能
description: 本文章提供 Android 5.0 (Lollipop) 中所引進的新功能的高階概觀。 這些功能包括新的使用者介面樣式，稱為材料佈景主題，以及新支援的功能，例如動畫、 檢視陰影和可繪製濃淡。 Android 5.0 也包含增強的通知、 兩個新的 UI widget、 新的工作排程器，以及少數幾個新的 Api 來改善儲存體、 網路、 連線和多媒體功能。
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d79c0563d1dc9a2cfe75b702300982bb4d38553b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117859"
---
# <a name="lollipop-features"></a>棒棒糖符號功能

_本文章提供 Android 5.0 (Lollipop) 中所引進的新功能的高階概觀。這些功能包括新的使用者介面樣式，稱為材料佈景主題，以及新支援的功能，例如動畫、 檢視陰影和可繪製濃淡。Android 5.0 也包含增強的通知、 兩個新的 UI widget、 新的工作排程器，以及少數幾個新的 Api 來改善儲存體、 網路、 連線和多媒體功能。_

## <a name="lollipop-overview"></a>棒棒糖符號概觀

Android 5.0 (Lollipop) 導入了新的設計語言， *Material Design*，並使用它的支援轉換的新功能，可讓應用程式更容易且更直覺地使用。 材料設計 Android 5.0 不僅提供 Android 手機的外觀;它也會提供一組新的設計規則 for android 平板電腦、 桌上型電腦、 監看式和智慧型電視。 這些設計規則強調簡易性和 minimalism 同時透過使用熟悉的 tactile 屬性 （例如實際的介面和 edge 提示） 來協助使用者快速和直覺地了解介面。

*材料佈景主題*是在 Android 中這些 UI 設計原則的體現。 這篇文章一開始會涵蓋材料佈景主題的支援功能：

-   **動畫** &ndash; *觸控的意見反應*動畫*活動轉換*動畫*檢視狀態轉換*動畫以及*顯示效果*。

-   **檢視陰影和提高權限**&ndash;檢視現在具有`elevation`屬性，以檢視較高`elevation`值轉型的較大的陰影的背景。

-   **色彩功能** &ndash; *Drawable 濃淡*可讓您藉由變更其色彩，重複使用的影像資產並*顯著色彩擷取*可協助您以動態方式您的應用程式根據影像中的色彩佈景主題。

許多功能已內建的材料佈景主題 Android 5.0 UI 體驗，而其他人必須明確地新增至應用程式。 例如，部分標準檢視 （例如按鈕） 已經包含觸控的意見反應動畫，而應用程式必須啟用大部分的檢視陰影。

除了帶來透過材料佈景主題 UI 增強功能的情況下，Android 5.0 也包含數個其他新功能涵蓋這篇文章中：

-   **增強型通知** &ndash; Android 5.0 中的通知已大幅更新為新的外觀、 支援鎖定畫面通知，以及新*抬頭*通知的呈現格式。

-   **新的 UI widget** &ndash;新`RecyclerView`小工具可讓傳達大型資料集和複雜資訊，以及新的應用程式輕鬆`CardView`widget 可提供簡化的類似卡的呈現格式來顯示文字和映像。

-   **新的 Api** &ndash; Android 5.0 將新的 Api，如需多個網路支援，改善藍芽連線、 方便的儲存體管理和多媒體播放程式和網路攝影機裝置的更有彈性的控制項。 新的工作排程功能是可執行工作，以非同步方式在排定的時間。 這項功能有助於改善電池壽命，比方說，排程工作，當裝置插入電源時與收費。


## <a name="requirements"></a>需求

需要下列項目以 Xamarin 為基礎的應用程式中使用新的 Android 5.0 功能：

-   **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更新版本必須安裝並設定與 Visual Studio 或 Visual Studio for mac。 

-   **Android SDK** &ndash; Android 5.0 (API 21) 或更新版本必須安裝 Android SDK 管理員透過。

-   **Java Developer Kit** &ndash; Xamarin.Android 需要[JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本，如果您正在開發的 API 層級 24 或更大 （JDK 1.8 也支援 API 層級早於 24，包括棒棒糖符號）。 如果您使用自訂控制項或表單預覽程式需要 64 位元版本的 JDK 1.8。

您可以繼續使用[JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)如果您是開發特別針對 API 層級 23 或更早版本。


## <a name="setting-up-an-android-50-project"></a>設定 Android 5.0 專案

若要建立 Android 5.0 專案時，您必須安裝最新工具和 SDK 封裝。 您可以使用下列步驟來設定 Xamarin.Android 專案目標 Android 5.0:

1. 安裝 Xamarin.Android 工具，並啟用 Xamarin 授權。 請參閱[設定與安裝](~/android/get-started/installation/index.md)如需安裝 Xamarin.Android 的詳細資訊。

2. 如果您使用 Visual Studio for Mac，請安裝最新的 Android 5.0 更新。

3. 啟動 Android SDK 管理員 (在 Visual Studio for Mac 中，使用**工具&gt;開啟 Android SDK 管理員&hellip;**) 並安裝 Android SDK Tools 23.0.5 或更新版本：

    [![選取 Android SDK Manager 中的 Android SDK 工具](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   此外，安裝最新的 Android 5.0 SDK 套件 (API 21 或更新版本):

    [![在 Android SDK 管理員安裝 Android 5.0 SDK 套件](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   如需使用 Android SDK 管理員的詳細資訊，請參閱[SDK 管理員](http://developer.android.com/tools/help/sdk-manager.html)。

4. 建立新的 Xamarin.Android 專案。 如果您是使用 Xamarin 進行 Android 開發的新手，請參閱[Hello，Android](~/android/get-started/hello-android/index.md)來了解如何建立 Android 專案。 當您建立 Android 專案時，請務必設定 Android 5.0 的版本設定。
   在 Visual Studio for Mac 中，瀏覽至**專案選項&gt;建置&gt;一般**並設定**目標 framework**至**Android 5.0 (Lollipop)** 或更新版本：

    ![目標 Framwework 設為 Android 5.0 Lollipop](lollipop-images/target-framework.png)

   底下**專案選項&gt;建置&gt;Android 應用程式**，設定 最小值和目標 Android 版本設**自動-使用目標 framework 版本**:

    ![為 自動設定的最小值和目標 Android 版本](lollipop-images/minimum-android-version.png)

5. 設定模擬器或 Android 裝置來測試您的應用程式。 如果您正使用模擬器，請參閱[Android Emulator 安裝](~/android/get-started/installation/android-emulator/index.md)以了解如何使用 Xamarin Studio 或 Visual Studio 中設定 Android 模擬器使用。 如果您使用 Android 裝置，請參閱[設定註冊預覽 SDK](https://developer.android.com/preview/setup-sdk.html)以了解如何為 Android 5.0 更新您的裝置。 若要設定 Android 裝置的執行和偵錯 Xamarin.Android 應用程式，請參閱[設定裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)。

注意： 如果您要更新現有的 Android 專案的目標 Android L 預覽，您必須更新**目標 Framework**並**Android 版本**上面所述的值。

## <a name="important-changes"></a>重要變更

先前已發行的 Android 應用程式可能會影響 Android 5.0 中的變更。 特別是，Android 5.0 會使用新的執行階段，並大幅變更的通知格式。

### <a name="android-runtime"></a>Android 執行階段

Android 5.0 會使用預設執行階段，而不是 Dalvik 新 Android 執行階段 （藝術）。 美工圖案會實作數個主要的新功能：

-   **預先-(AOT) 編譯** &ndash; AOT 可以改善應用程式效能，藉由編譯應用程式程式碼之前先啟動應用程式。 安裝應用程式時，圖案會產生已編譯的應用程式可執行檔的目標裝置。

-   **改善記憶體回收 (GC)** &ndash;圖案中的 GC 增強功能也可以改善應用程式效能。 記憶體回收集合現在會使用一個 GC 暫停而非兩個，以及並行 GC 作業完成更 neodpověděl včas.。

-   **改善應用程式偵錯**&ndash;圖案提供更多診斷的詳細資訊，以協助分析例外狀況和當機報告。

現有的應用程式不在封面變更用於&ndash;除了惡意探索技術特有先前 Dalvik 執行階段的應用程式，這可能無法運作下美工圖案。 如需有關這些變更的詳細資訊，請參閱 <<c0> [ 驗證應用程式行為上 Android 執行階段 （藝術）](http://developer.android.com/guide/practices/verifying-apps-art.html)。


### <a name="notification-changes"></a>通知變更

通知已大幅變更，在 Android 5.0:

-   **以不同方式處理音效和震動**&ndash;通知音效和振動的情況下會立即由`Notification.Builder`而不是`Ringtone`， `MediaPlayer`，和`Vibrator`。

-   **新的色彩配置**&ndash;根據材料佈景主題，通知會以深色的文字轉譯透過白色或非常輕微的背景。 此外，Android 協調系統色彩配置可能修改通知圖示中的 alpha 色板。 

-   **鎖定畫面通知**&ndash;現在會在裝置鎖定畫面上顯示通知。

-   **抬頭**&ndash;高優先順序通知現在會出現在小型的浮動視窗 （抬頭通知） 時解除鎖定裝置為止，並已開啟 螢幕。

在大部分情況下，移植現有的應用程式通知功能，以 Android 5.0 需要下列步驟：

1.  若要使用的程式碼轉換`Notification.Builder`(或`NotificationsCompat.Builder`) 來建立通知。 

2.  請確認您現有的通知資產是可在 新的材料佈景主題色彩配置。

3.  決定哪些時出現在鎖定畫面上，應該有您的通知的可見性。 通知不是公用的如果內容應該鎖定畫面上顯示？

4.  設定通知的類別，讓它們能夠正確處理新的 Android 5.0 *「 請勿打擾 」* 模式。

如果您的通知會顯示傳輸控制項，顯示媒體播放狀態，請使用`RemoteControlClient`，或致電`ActivityManager.GetRecentTasks`，請參閱[重要的行為變更](http://developer.android.com/preview/api-overview.html#Behaviors)如需有關更新您適用於 Android 的通知5.0。

如需建立通知，在 Android 中的資訊，請參閱[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。 [相容性](~/android/app-fundamentals/notifications/local-notifications.md#compatibility)這篇文章的一節說明如何建立通知，向下相容舊版的 Android。


## <a name="material-theme"></a>材料佈景主題

新的 Android 5.0 材料佈景主題的外觀及操作 Android 的 UI 帶來重大的變更。 視覺項目現在會使用 tactile 介面上，採取粗體的圖形、 印刷樣式，以及列印型設計的明亮的色彩。 材料佈景主題的範例如下列螢幕擷取畫面所述：

[![材料佈景主題主畫面及應用程式 畫面中，設定畫面的螢幕擷取畫面](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 會回應您與主畫面左邊所示。 Center 螢幕擷取畫面是 [應用程式] 清單中的第一個畫面和螢幕擷取畫面，在右側**設定**螢幕。 Google [Material Design](https://material.io/guidelines/material-design/introduction.html)規格會說明新材料佈景主題概念背後的基本設計規則。

材料佈景主題包含您可以使用您的應用程式中的三種內建類型：`Theme.Material`暗色調佈景主題 （預設值），`Theme.Material.Light`佈景主題，而`Theme.Material.Light.DarkActionBar`佈景主題： 

[![暗的螢幕擷取畫面、 光線及 DarkActionBar 佈景主題](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

如需有關在 Xamarin.Android 應用程式中使用材料佈景主題功能的詳細資訊，請參閱[材料佈景主題](~/android/user-interface/material-theme.md)。


## <a name="animations"></a>Animations

Android 5.0 提供觸控的意見反應動畫、 活動轉換動畫，以及讓應用程式介面更直覺地使用的檢視狀態轉換動畫。 此外，Android 5.0 應用程式可以使用*顯示效果*來隱藏或顯示檢視動畫。 您可以使用*弧形影片*呈現設定來設定快或慢動畫。


### <a name="touch-feedback-animations"></a>觸控的意見反應的動畫

當檢視已接觸到，觸控的意見反應動畫會提供使用者視覺化回饋。 例如，按鈕現在會顯示產生漣漪效果時，它們接觸&ndash;這是 Android 5.0 預設觸控意見反應的動畫。 Ripple 動畫藉由新`RippleDrawable`類別。 產生漣漪效果可設定為在檢視的範圍結束，或超出檢視的範圍中。 例如，以下的螢幕擷取畫面順序說明按鈕中產生漣漪效果觸控動畫播放期間：

![框架的漣波動畫按鈕上的框架螢幕擷取畫面](lollipop-images/touch-animation.png)

初始的觸控與按鈕會在左邊的第一個映像，而其餘的順序 （從左到右） 說明如何產生漣漪效果分散至按鈕的邊緣。 Ripple 動畫結束時，此檢視會傳回原始外觀。 預設 ripple 動畫發生在幾分之一秒，但還長或短的時間長度，可以自訂動畫的長度。

如需詳細資訊 」 的 「 修改 Android 5.0 中的意見反應動畫，請參閱 <<c0> [ 自訂觸控的意見反應](http://developer.android.com/training/material/animations.html#Touch)。


### <a name="activity-transition-animations"></a>活動轉換動畫

活動轉換動畫為使用者提供視覺化的持續性的了解當一個活動轉換到另一個。 應用程式可以指定三種類型的轉換動畫：

-   **輸入轉換**&ndash;的活動時進入場景。

-   **結束轉換**&ndash;的活動結束的場景時。

-   **共用項目轉換**&ndash;的第一個活動轉換到下一個通用的兩個活動檢視的變更時。

例如，以下的螢幕擷取畫面順序說明共用的項目轉換：

[![框架的共用項目過場動畫畫面格螢幕擷取畫面](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

共用的項目 （毛蟲的相片） 是其中一個第一次的活動; 中的數個檢視它會放大成為唯一的檢視，在第二個的第一個活動轉換的第二個活動。

#### <a name="enter-transition-animation-types"></a>輸入轉換的動畫類型

Enter 轉換 Android 5.0 提供三種動畫類型：

-   **Explode 動畫**&ndash;放大檢視，以從場景的中心。

-   **投影片動畫**&ndash;移動的檢視，其中一個場景的邊緣。

-   **淡出動畫**&ndash;淡場景的檢視。

#### <a name="exit-transition-animation-types"></a>結束轉換動畫類型

結束轉換 Android 5.0 提供三種動畫類型：

-   **Explode 動畫**&ndash;至場景的中心縮小檢視。

-   **投影片動畫**&ndash;場景的邊緣的其中一個移的檢視。

-   **淡出動畫**&ndash;淡出場景的檢視。

#### <a name="shared-element-transition-animation-types"></a>共用項目轉換動畫類型

共用的項目轉換支援多種類型的動畫，例如：

-   變更檢視的版面配置或剪輯的界限。

-   變更縮放比例和檢視的旋轉。

-   變更檢視的大小和縮放比例類型。

如需有關 Android 5.0 中的活動轉換動畫的詳細資訊，請參閱[來自訂活動轉換](http://developer.android.com/training/material/animations.html#Transitions)。


### <a name="view-state-transition-animations"></a>檢視狀態轉換動畫

Android 5.0 可讓檢視狀態變更時執行的動畫。 您可以使用下列技巧的其中一個，以動畫顯示檢視狀態轉換：

-   建立可繪製，而資源來以動畫顯示的特定檢視相關聯的狀態變更。 新`AnimatedStateListDrawable`類別可讓您建立可繪製資源來顯示 檢視狀態變更之間的動畫。

-   定義的檢視狀態變更時執行的動畫功能。 新`StateListAnimator`類別可讓您定義的檢視狀態變更時執行動畫。

如需有關 Android 5.0 中的檢視狀態轉換動畫的詳細資訊，請參閱[以動畫顯示的檢視狀態變更](http://developer.android.com/training/material/animations.html#ViewState)。


### <a name="reveal-effect"></a>顯示效果

*顯示效果*會裁剪圓形來顯示或隱藏檢視該變更 radius。 您可以設定初始和最終裁剪圓形的半徑，以控制此效果。 以下循序的螢幕擷取畫面說明從中心螢幕的顯示效果動畫：

[![依畫面格的螢幕擷取畫面顯示動畫的框架](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

下一步 的循序說明發生從畫面的左下角的顯示效果動畫：

[![框架的裁剪動畫畫面格螢幕擷取畫面](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

顯示可反轉動畫;亦即裁剪圓形可以壓縮成隱藏檢視而放大視覺效果以顯示檢視。

如需有關中的 Android 5.0 顯示效果的詳細資訊，請參閱[使用的顯示效果](http://developer.android.com/training/material/animations.html#Reveal)。


### <a name="curved-motion"></a>曲線的動作

除了這些動畫功能，Android 5.0 也提供新的 Api，可讓您指定的時間和移動動畫的曲線。 Android 5.0 會使用這些曲線時態表和空間移動進行插補，動畫期間。 Android 5.0 中，會定義三個曲線：

-   **快速\_放大\_線性\_中**&ndash;快速加速和加速動畫結束之前會繼續。

-   **快速\_放大\_緩慢\_中** &ndash; Accelerates 緩慢又減速的動畫的結尾。

-   **線性\_放大\_緩慢\_中**&ndash;與尖峰速度緩慢的開始減速的動畫結尾。

您可以使用新`PathInterpolator`類別，以指定動畫內插補點進行的方式。 `PathInterpolator` 是會根據指定的控制點和影片曲線動畫路徑周遊 interpolator。 如需如何在 Android 5.0 中指定曲線的動作設定的詳細資訊，請參閱[使用曲線影片](http://developer.android.com/training/material/animations.html#CurvedMotion)。


## <a name="view-shadows--elevation"></a>檢視陰影] & [提高權限

在 Android 5.0 中，您可以指定*提高權限*藉由設定 新檢視的`Z`屬性。 大於`Z`值會導致要大上投射陰影的背景，製作檢視似乎背景上較高的浮點數的檢視。 您可以藉由設定來設定檢視的初始權限提升其`elevation`版面配置中的屬性。

下列範例說明轉型為空的陰影`TextView`控制當它的提高權限屬性設定為 2dp、 4dp，且 6dp，分別：

[![Progessively 較大的檢視陰影的螢幕擷取畫面](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

檢視陰影設定可以是靜態 （如上所示），或用於動畫進行暫時提高，檢視的背景上方顯示的檢視。 您可以使用`ViewPropertyAnimator`類別以動畫顯示檢視的提高權限。 檢視的權限提高為其配置的總和`elevation`設定加上`translationZ`屬性，您可以透過設定`ViewPropertyAnimator`方法呼叫。

如需有關 Android 5.0 中的檢視陰影的詳細資訊，請參閱[定義的陰影和裁剪檢視](http://developer.android.com/training/material/shadows-clipping.html)。


## <a name="color-features"></a>色彩功能

Android 5.0 提供兩項新功能，來管理應用程式的色彩：

-   *可繪製濃淡*可讓您藉由變更版面配置屬性來變更色彩的影像資產。

-   *顯著色彩擷取*可讓您以動態方式自訂您的應用程式來協調與顯示的影像的色彩調色盤的色彩佈景主題。


### <a name="drawable-tinting"></a>可繪製濃淡

Android 5.0 的版面配置辨識新`tint`屬性可供您的色彩可繪製資源來設定而不需要建立多個版本的這些資產，以顯示不同的色彩。 若要使用這項功能，您必須定義點陣圖的 alpha 遮罩與使用`tint`屬性來定義資產的色彩。 這項功能可讓您一次建立資產，並在您的版面配置，以符合您的佈景主題色彩它們。

在下列範例中，單一映像的資產&ndash;透明背景的白色標誌&ndash;用來建立濃淡變化：

![具有透明背景的白色 Xamarin 標誌](lollipop-images/xamarin-logo-white.png)

此標誌會顯示藍色圓形的背景上方，如下列範例所示。 在左側影像是標誌的顯示不`tint`設定。 在中央映像，標誌的`tint`屬性設定為暗灰色。 在右側，影像中`tint`設定為淺灰色：

![使用不同的濃淡設定上述的標誌的範例](lollipop-images/drawable-tinting.png)

如需詳細資訊可繪製濃淡 Android 5.0 中，請參閱 < [Drawable 濃淡](http://developer.android.com/training/material/drawables.html#DrawableTint)。


### <a name="prominent-color-extraction"></a>顯著色彩擷取

新的 Android 5.0`Palette`類別可讓您從映像擷取色彩，以便您可以動態地將它們套用至自訂的調色盤。 `Palette`類別六種色彩擷取映像和標籤根據其相對的層級的色彩飽和度和亮度這些色彩：

-   充滿活力

-   活躍的深色

-   活躍的光線

-   靜音

-   靜音的深色

-   靜音的光線

比方說，在下列螢幕擷取畫面，相片瀏覽應用程式上顯示的映像中擷取重要的色彩，並使用這些色彩調整應用程式的色彩配置以符合映像：

[![綠色、 粉紅色，和藍色佈景主題色彩擷取的螢幕擷取畫面](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

在上述螢幕擷取畫面中，動作列設定為擷取的"活躍 light"色彩和背景設定為擷取 「 活躍暗色調 」 色彩。 在每個上述範例中，小型彩色方塊的資料列會包含說明從映像擷取的調色盤色彩。

如需 Android 5.0 中的色彩擷取相關資訊，請參閱 <<c0> [ 從映像擷取顯著色彩](http://developer.android.com/training/material/drawables.html#ColorExtract)。


## <a name="new-ui-widgets"></a>新的 UI Widget

Android 5.0 導入了兩個新的 UI widget:

-   `RecyclerView` &ndash; 顯示可捲動的項目清單檢視群組。

-   `CardView` &ndash; 具有圓角的基本版面配置。

這兩個小工具包括了內建支援材料佈景主題功能;例如，`RecyclerView`動畫用於加入及移除檢視，和`CardView`使用檢視進行似乎一直浮在背景每張卡片的陰影。 這些新的小工具的範例如下列螢幕擷取畫面所示：

[![RecyclerView 以建置應用程式的螢幕擷取畫面](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

在左側螢幕擷取畫面是範例`RecyclerView`上用於電子郵件應用程式和螢幕擷取畫面右邊是舉例說明`CardView`旅遊預約應用程式中使用。


### <a name="recyclerview"></a>RecyclerView

`RecyclerView` 類似於`ListView,`但它比較適合大量檢視或動態變更的項目清單。 例如`ListView,`指定配接器以存取基礎資料集。 不過，不同於`ListView,`您使用*配置管理員*往內的項目`RecyclerView`。 配置管理員也會負責回收檢視;它會管理已不再對使用者顯示的項目檢視重複使用。

當您使用`RecyclerView`小工具之後，您必須指定`LayoutManager`和配接器。 上圖所示`LayoutManager`是配接器之間的媒介和`RecyclerView`:

![圖表的 RecyclerView 與支援 LayoutManager、 配接器和資料集](lollipop-images/recyclerview-diagram.png)

下列螢幕擷取畫面說明`RecyclerView`包含 100 個項目 (每個項目組成`ImageView`和`TextView`):

[![捲動 映像的 RecyclerView 應用程式的螢幕擷取畫面](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView` 輕鬆地處理這個大型的資料集&ndash;捲動從一開始来結束之清單的清單在此範例中的應用程式只需要幾秒鐘。 `RecyclerView` 也支援動畫;事實上，預設會啟用動畫來新增和移除項目。 當項目加入至`RecyclerView`，它的淡入淡在這一系列的螢幕擷取畫面所示：

[![由框架螢幕擷取畫面中的相片項目淡出的框架](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

如需詳細資訊`RecyclerView`，請參閱 < [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)。


### <a name="cardview"></a>CardView

`CardView` 是會模擬具有圓角浮動卡片的簡單檢視。 因為`CardView`具有內建檢視陰影，它提供簡單的方法，以視覺化的深度加入您的應用程式。 下列螢幕擷取畫面顯示三個文字導向的範例`CardView`:

[![應用程式使用 CardView 型項目 RecyclerView 範例螢幕擷取畫面](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

在上述範例中的卡片的每一個都包含`TextView`; 的背景色彩透過設定`cardBackgroundColor`屬性。

如需詳細資訊`CardView`，請參閱 < [CardView](~/android/user-interface/controls/card-view.md)。


## <a name="enhanced-notifications"></a>增強的通知

新的視覺格式與新的功能已大幅更新 Android 5.0 的通知系統。 通知會在 Android 5.0 中的新的外觀。 例如，Android 5.0 中的通知現在會使用深色的文字在淺色背景上：

![未展開的 Android 5.0 通知的範例](lollipop-images/expanded-notification-contracted.png)

當大型圖示會顯示在通知中 （如上述範例所示） 時，則 Android 5.0 會顯示以徽章形式之小圖示大型圖示上方。 

在 Android 5.0 中，也可以在裝置鎖定畫面上出現通知。
例如，以下是單一的通知與鎖定畫面中的範例螢幕擷取畫面：

[![在鎖定畫面上顯示通知的螢幕擷取畫面](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

使用者可以點選來解除鎖定裝置，並跳至產生該通知，應用程式鎖定畫面上的通知或撥動以關閉通知。 有新的通知*可視性*決定設定鎖定畫面上，可以顯示多少內容。 使用者可以選擇是否允許要在鎖定畫面通知中顯示的敏感性內容。

Android 5.0 引進新的高優先順序通知呈現格式，稱為*抬頭*。 抬頭通知從畫面頂端滑向下幾秒鐘的時間，且然後撤退回到在畫面頂端的通知陰影效果。 抬頭通知可讓系統 UI，而不會中斷目前正在執行的活動放置在使用者之前的重要資訊。 下列範例說明簡單的預告通知應用程式最上層顯示：

[![Heads-up 通知範例](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

抬頭通知通常會使用下列事件：

-   新的下一個訊息

-   連入的通話

-   電力偏低表示

-   警示

Android 5.0 抬頭格式顯示通知，只有當它具有高或最大的優先順序設定時。

在 Android 5.0 中，您可以提供通知的中繼資料，以協助 Android 排序及更有效地顯示通知。 Android 5.0 會組織根據優先順序、 可見性和類別目錄的通知。
通知類別用來篩選可以顯示的通知，當裝置處於 *「 請勿打擾 」* 模式。

如需建立和啟動通知的最新的 Android 5.0 功能的詳細資訊，請參閱[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。


## <a name="new-apis"></a>新的 API

除了上面所述的新外觀和操作功能，Android 5.0 會加入新的 Api 來延伸現有的多媒體功能、 儲存和無線/連線功能。 此外，Android 5.0 包含新的 Api，提供新的工作排程器功能的支援。

### <a name="camera"></a>觀景窗

Android 5.0 提供增強的相機功能的數個新的 Api。 新`Android.Hardware.Camera2`命名空間包含的功能，如存取個別的網路攝影機裝置連接到 Android 裝置。 此外，`Android.Hardware.Camera2`模型做為管線的每個網路攝影機裝置： 它會接受擷取要求、 擷取映像，並再將結果輸出。 這種方法可讓多個相機的裝置擷取要求排入佇列的應用程式。

下列 Api 讓這些新功能：

-   `CameraManager.GetCameraIdList` &ndash; 可協助您以程式設計方式存取網路攝影機裝置;您使用`CameraManager.OpenCamera`連接到特定的網路攝影機裝置。

-   `CameraCaptureSession` &ndash; 擷取，或從網路攝影機裝置的影像資料流。 您實作`CameraCaptureSession.CaptureListener`介面，以處理新的映像擷取事件。

-   `CaptureRequest` &ndash; 定義擷取參數。

-   `CaptureResult` &ndash; 提供映像擷取作業的結果。

如需新的數位相機 Android 5.0 中的 Api 相關資訊，請參閱 <<c0> [ 媒體](http://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="audio-playback"></a>音訊播放

Android 5.0 更新`AudioTrack`更好的音訊播放的類別：

-   `ENCODING_PCM_FLOAT` &ndash; 設定`AudioTrack`接受更好的動態範圍、 更高的空餘空間，以及 （多虧有更高的精確度） 的更高品質的浮點格式的音訊資料。 此外，浮點數格式有助於避免音訊剪輯。

-   `ByteBuffer` &ndash; 您現在可以提供要的音訊資料`AudioTrack`做為位元組陣列。

-   `WRITE_NON_BLOCKING` &ndash; 此選項可讓您簡化緩衝處理和多執行緒對於某些應用程式。

如需詳細資訊`AudioTrack`改善 Android 5.0 版，請參閱[媒體](http://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="media-playback-control"></a>媒體播放控制項

Android 5.0 引進新`Android.Media.MediaController`類別，取代了`RemoteControlClient`。 `Android.Media.MediaController` 提供簡化的傳輸控制 Api，並提供具備執行緒安全控制項的播放的 UI 內容之外。 下列新的 Api 來處理傳輸控制項：

-   `Android.Media.Session.MediaSession` &ndash; 媒體控制工作階段會處理多個控制站。 您呼叫`MediaSession.GetSessionToken`要求您的應用程式會使用與工作階段互動的權杖。

-   `MediaController.TransportControls` &ndash; 處理傳輸命令，例如**播放**，**停止**，並**略過**。

此外，您可以使用新`Android.App.Notification.MediaStyle`類別 （例如擷取和顯示專輯封面） 的豐富內容通知內容相關聯的媒體工作階段。

如需有關 Android 5.0 的新的媒體播放控制項功能的詳細資訊，請參閱[媒體](http://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="storage"></a>存放裝置

Android 5.0 更新儲存體存取架構，以簡化應用程式，以使用目錄與文件：

-   若要選取子樹狀目錄，您可以建立並傳送`Android.Intent.Action.OPEN_DOCUMENT_TREE`意圖。 此意圖會導致系統顯示 支援的樹狀子目錄選取; 的所有提供者執行個體然後使用者會瀏覽並選取目錄。

-   若要建立及管理新的文件] 或 [任何位置下的樹狀子目錄的目錄，您使用新`CreateDocument`， `RenameDocument`，並`DeleteDocument`方法`DocumentsContract`。

-   若要取得所有的共用的存放裝置上的媒體的目錄路徑，請呼叫新`Android.Content.Context.GetExternalMediaDirs`方法。

如需有關新的儲存體 Android 5.0 中的 Api 的詳細資訊，請參閱[儲存體](http://developer.android.com/preview/api-overview.html#Storage)。

### <a name="wireless--connectivity"></a>無線與連線能力

Android 5.0 新增無線及連線的下列 API 增強功能：

-   新*多重網路*讓應用程式，以尋找並選取特定功能的網路進行連線之前的 Api。

-   可讓 Android 5.0 裝置，以做為低電源的藍牙周邊設備的藍芽廣播功能。

-   NFC 增強功能可讓您更輕鬆地共用資料與其他裝置使用近通訊功能。

如需詳細資訊，新的無線及 Android 5.0 中的 Api 連線，請參閱 <<c0> [ 無線及連線](http://developer.android.com/preview/api-overview.html#Wireless)。

### <a name="job-scheduling"></a>工作排程

Android 5.0 引進新`JobScheduler`API，可協助使用者減少電池清空裝置插入電源時，才執行特定工作的排程和收費。 這個工作排程器功能也可以用於排程要條件更適合該工作，例如裝置透過 Wi-fi 網路，而非計量付費網路連線時下載大型檔案時執行的工作。

如需詳細資訊的新的工作排程在 Android 5.0 中的 Api，請參閱 <<c0> [ 排程工作](http://developer.android.com/preview/api-overview.html#JobScheduler)。

## <a name="summary"></a>總結

這篇文章適用於 Xamarin.Android 應用程式開發人員提供 Android 5.0 中的重要新功能的概觀：

-   材料佈景主題

-   Animations

-   檢視陰影或提高權限

-   色彩功能，例如可繪製濃淡及顯著色彩擷取

-   新`RecyclerView`和`CardView`widget

-   通知的增強功能

-   新的 Api，如相機、 音訊播放、 media 控制項、 儲存體、 無線/連線和工作排程

如果您是 Xamarin Android 開發的新手，閱讀[設定與安裝](~/android/get-started/installation/index.md)可協助您開始使用 Xamarin.Android。
[Hello，Android](~/android/get-started/hello-android/index.md)是絕佳的簡介，以了解如何建立 Android 專案。



## <a name="related-links"></a>相關連結

- [Android 的 L 開發人員預覽](http://developer.android.com/preview/index.html)
- [取得 Android SDK](https://developer.android.com/sdk/index.html#Other)
- [材料設計](http://developer.android.com/preview/material/index.html)
- [材料設計原則](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
