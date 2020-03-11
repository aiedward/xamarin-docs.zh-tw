---
title: 棒糖功能
description: 本文提供 Android 5.0 （棒棒）中引進之新功能的高階總覽。 這些功能包括新的使用者介面樣式，稱為材質主題，以及動畫、視圖陰影和繪製色調等新的支援功能。 Android 5.0 也包含增強的通知、兩個新的 UI widget、新的工作排程器，以及一些新的 Api，以改善儲存體、網路、連線能力和多媒體功能。
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 297c7806ce8a880d65c38ef0e4672e41fee5acfe
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "78292262"
---
# <a name="lollipop-features"></a>棒糖功能

_本文提供 Android 5.0 （棒棒）中引進之新功能的高階總覽。這些功能包括新的使用者介面樣式，稱為材質主題，以及動畫、視圖陰影和繪製色調等新的支援功能。Android 5.0 也包含增強的通知、兩個新的 UI widget、新的工作排程器，以及一些新的 Api，以改善儲存體、網路、連線能力和多媒體功能。_

## <a name="lollipop-overview"></a>棒糖總覽

Android 5.0 （棒糖）引進新的設計語言、*材質設計*，並支援新功能的轉型，讓應用程式更容易且更直覺地使用。 使用材質設計時，Android 5.0 不僅會為 Android 手機提供 facelift;它也為 Android 型平板電腦、桌上型電腦、監看和智慧型電視提供了一組新的設計規則。 這些設計規則強調簡單性和 minimalism，同時使用熟悉的觸覺屬性（例如實際的表面和邊緣提示），協助使用者快速且直覺地瞭解介面。

*材質主題*是這些 UI 設計原則在 Android 中的體現。 本文一開始會涵蓋材質主題的支援功能：

- **動畫**&ndash;*觸控意見*動畫、*活動轉換*動畫、*檢視狀態轉換*動畫，以及*顯示效果*。

- **視圖陰影和提高許可權**&ndash; 視圖現在具有 `elevation` 屬性;  具有較高 `elevation` 值的視圖會在背景上轉換較大的陰影。

- **色彩功能**&ndash; 可*繪製*的色彩，讓您可以藉由變更影像資產的顏色來重複使用它們，而*顯著的色彩提取*可協助您根據影像中的色彩，以動態方式建立應用程式的主題。

許多材質主題功能已內建于 Android 5.0 UI 體驗中，有些則必須明確地新增至應用程式。 例如，某些標準視圖（例如按鈕）已經包含觸控意見反應動畫，而應用程式必須啟用大部分的視圖陰影。

除了透過材質主題引進的 UI 改良功能之外，Android 5.0 也包含本文所涵蓋的其他幾項新功能：

- Android 5.0 中的**增強型通知**&ndash; 通知已透過新外觀、鎖定通知的支援，以及新的*標題*通知呈現格式進行大幅更新。

- 新的 `RecyclerView` widget &ndash;**新的 UI 小**工具可讓應用程式更輕鬆地傳達大型資料集和複雜資訊，而新的 `CardView` widget 則提供簡化的卡片呈現格式，以顯示文字和影像。

- Android 5.0 &ndash; 的**新 Api 新增**了多個網路支援的新 api、改良的藍牙連線能力、更輕鬆的存放裝置管理，以及更有彈性的多媒體播放機和相機裝置控制。 新的作業排程功能可在排程時間以非同步方式執行工作。 這項功能可協助改善電池壽命，例如，在裝置插入和收費時，排程工作會發生。

## <a name="requirements"></a>需求

若要在 Xamarin 應用程式中使用新的 Android 5.0 功能，需要下列各項：

- **&ndash; xamarin. android 4.20**或更新版本必須安裝，並以 Visual Studio 或 Visual Studio for Mac 設定。

- **Android SDK** &ndash; Android 5.0 （API 21）或更新版本必須透過 Android SDK 管理員進行安裝。

- **JAVA 開發人員套件**&ndash; Xamarin。如果您要針對 API 層級24或更高版本進行開發，則需要[jdk 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本（JDK 1.8 也支援早于24的 api 層級，包括棒糖）。 如果您使用自訂控制項或表單預覽器，則需要 JDK 1.8 的64位版本。

如果您是特別針對 API 層級23或更早的版本進行開發，您可以繼續使用[JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 。

## <a name="setting-up-an-android-50-project"></a>設定 Android 5.0 專案

若要建立 Android 5.0 專案，您必須安裝最新的工具和 SDK 套件。 使用下列步驟來設定以 Android 5.0 為目標的 Xamarin Android 專案：

1. 安裝 Xamarin Android 工具並啟用您的 Xamarin 授權。 如需安裝 Xamarin 的詳細資訊，請參閱[設定和安裝](~/android/get-started/installation/index.md)。

2. 如果您使用 Visual Studio for Mac，請安裝最新的 Android 5.0 更新。

3. 啟動 Android SDK 管理員（在 Visual Studio for Mac 中，使用 [**工具] &gt; 開啟 Android SDK Manager&hellip;** ）並安裝 Android SDK Tools 23.0.5 或更新版本：

    [![在 Android SDK 管理員中選取 Android SDK 工具](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   此外，請安裝最新的 Android 5.0 SDK 套件（API 21 或更新版本）：

    [![在 Android SDK 管理員中安裝 Android 5.0 SDK 套件](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   如需使用 Android SDK 管理員的詳細資訊，請參閱[SDK 管理員](https://developer.android.com/tools/help/sdk-manager.html)。

4. 建立新的 Xamarin Android 專案。 如果您不熟悉使用 Xamarin 進行 Android 開發的新手，請參閱[Hello，Android](~/android/get-started/hello-android/index.md)以瞭解如何建立 android 專案。 當您建立 Android 專案時，請務必設定 Android 5.0 的版本設定。
   在 Visual Studio for Mac 中，流覽至 **[專案選項] &gt; [組建 &gt; 一般**]，並將 [**目標 framework** ] 設定為**Android 5.0 （棒糖）** 或更新版本：

    ![將目標 Framwework 設定為 Android 5.0 棒糖](lollipop-images/target-framework.png)

   在 **專案選項 底下 &gt; 組建 &gt; Android 應用程式**，將 最低和目標 Android 版本 設定為**自動使用的目標 framework 版本**：

    ![將最小和目標 Android 版本設定為自動](lollipop-images/minimum-android-version.png)

5. 設定模擬器或 Android 裝置來測試您的應用程式。 如果您使用模擬器，請參閱[Android Emulator 安裝程式](~/android/get-started/installation/android-emulator/index.md)，以瞭解如何設定 Android 模擬器以與 Xamarin Studio 或 Visual Studio 搭配使用。 如果您使用 Android 裝置，請參閱[設定預覽 SDK](https://developer.android.com/preview/setup-sdk.html) ，以瞭解如何為 android 5.0 更新您的裝置。 若要設定您的 Android 裝置來執行和偵測 Xamarin Android 應用程式，請參閱[設定裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)。

注意：如果您要更新的是以 Android L Preview 為目標的現有 Android 專案，您必須將**目標 Framework**和**Android 版本**更新為上述所述的值。

## <a name="important-changes"></a>重要變更

先前發佈的 Android 應用程式可能會受到 Android 5.0 中的變更所影響。 特別是，Android 5.0 會使用新的執行時間和明顯變更的通知格式。

### <a name="android-runtime"></a>Android 執行時間

Android 5.0 使用新的 Android 執行時間（美工）做為預設執行時間，而不是 Dalvik。 美工會實行數個主要的新功能：

- **預先（AOT）編譯**&ndash; 在第一次啟動應用程式之前，您可以藉由編譯應用程式程式碼，來改善應用程式的效能。 安裝應用程式時，美工圖案會為目標裝置產生已編譯的應用程式可執行檔。

- **改良的垃圾收集（gc）** &ndash; 的 GC 改善也可以改善應用程式效能。 垃圾收集現在會使用一個 GC 暫停，而不是兩個，而並行 GC 作業會以更及時的方式完成。

- **改良的應用程式調試**&ndash; 美工提供更多的診斷詳細資料，以協助分析例外狀況和當機報告。

現有的應用程式不需要 &ndash; 變更，就能正常執行，除非應用程式會利用舊版 Dalvik 執行時間獨有的技術，但這可能無法在美工圖案下工作。 如需這些變更的詳細資訊，請參閱在[Android 執行時間上驗證應用程式行為（美工）](https://developer.android.com/guide/practices/verifying-apps-art.html)。

### <a name="notification-changes"></a>通知變更

Android 5.0 中的通知已大幅變更：

- 音效**和震動的處理方式不同**&ndash; 通知音效，而 vibrations 現在由 `Notification.Builder` 處理，而不是 `Ringtone`、`MediaPlayer`和 `Vibrator`。

- **新的色彩配置**&ndash; 根據材質主題，通知會以深色文字呈現，而非白色或非常淺背景。 此外，在通知圖示中的 Alpha 聲道可能會由 Android 修改以與系統色彩配置協調。

- **鎖屏通知**&ndash; 通知現在會出現在裝置的鎖屏上。

- 當裝置已解除鎖定且螢幕已開啟時，會在小型的浮動視窗（外通知）中出現 &ndash; 高優先順序通知的**列印頭**。

在大部分情況下，將現有的代理程式更新功能移植到 Android 5.0 需要執行下列步驟：

1. 轉換您的程式碼，以使用 `Notification.Builder` （或 `NotificationsCompat.Builder`）來建立通知。

2. 確認現有的通知資產可以在新的 [材質主題色彩配置] 中看到。

3. 決定當通知出現在鎖屏時，應該有哪些可見度。 如果通知不是公用的，則會在鎖屏上顯示哪些內容？

4. 設定通知的類別，以便在新的 [Android 5.0] [*請勿打擾*] 模式中正確處理。

如果您的通知呈現了傳輸控制、顯示媒體播放狀態、使用 `RemoteControlClient`或呼叫 `ActivityManager.GetRecentTasks`，請參閱[重要行為變更](https://developer.android.com/preview/api-overview.html#Behaviors)，以取得有關更新 Android 5.0 通知的詳細資訊。

如需在 Android 中建立通知的詳細資訊，請參閱[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。

## <a name="material-theme"></a>材質佈景主題

新的 Android 5.0 材質主題為 Android UI 的外觀與風格帶來了最全面的變更。 視覺效果元素現在會使用以列印為基礎之設計的粗體圖形、印刷樣式和明亮色彩為觸覺的表面。 如下列螢幕擷取畫面所示，材質主題的範例：

[![材質主題主畫面、應用程式畫面和設定畫面的螢幕擷取畫面](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 問候左側顯示的主畫面。 中央螢幕擷取畫面是應用程式清單的第一個畫面，右側的螢幕擷取畫面是 [**設定**] 畫面。 Google 的[材質設計](https://material.io/guidelines/material-design/introduction.html)規格說明了新材質主題概念背後的基礎設計規則。

材質主題包含三個可在應用程式中使用的內建類型： `Theme.Material` 深色主題 （預設值）、`Theme.Material.Light` 主題 和 `Theme.Material.Light.DarkActionBar` 主題：

[![深色、淺色和 DarkActionBar 主題的螢幕擷取畫面](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

如需在 Xamarin Android 應用程式中使用材質主題功能的詳細資訊，請參閱[材質主題](~/android/user-interface/material-theme.md)。

## <a name="animations"></a>Animations

Android 5.0 提供觸控式意見反應動畫、活動轉換動畫，以及檢視狀態轉換動畫，讓應用程式介面更具直覺化可供使用。 此外，Android 5.0 應用程式也可以使用*顯示效果*動畫來隱藏或顯示 views。 您可以使用*彎曲的動作*設定來設定轉譯動畫的速度和速度。

### <a name="touch-feedback-animations"></a>觸控意見動畫

觸控回饋動畫可在觸及視圖時，為使用者提供視覺上的意見反應。 例如，按鈕在觸及時，現在會顯示 ripple 效果 &ndash; 這是 Android 5.0 中的預設觸控回饋動畫。 Ripple 動畫是由新的 `RippleDrawable` 類別所執行。 Ripple 效果可以設定為在視圖範圍外結束，或延伸到超出視野的範圍。 例如，下列螢幕擷取畫面的順序說明觸控動畫期間在按鈕中的 ripple 效果：

![按鈕上 ripple 動畫的框架並排螢幕擷取畫面](lollipop-images/touch-animation.png)

具有按鈕的初始 touch contact 會出現在左邊的第一個影像中，而剩餘的順序（由左至右）則說明 ripple 效果如何散佈至按鈕的邊緣。 當 ripple 動畫結束時，視圖會回到其原始外觀。 預設的 ripple 動畫會以一秒的分數進行，但是動畫的長度可以自訂為較長或較短的時間長度。

如需 Android 5.0 中觸控意見反應動畫的詳細資訊，請參閱[自訂 Touch 意見](https://developer.android.com/training/material/animations.html#Touch)反應。

### <a name="activity-transition-animations"></a>活動轉換動畫

活動轉換動畫可讓使用者在某個活動轉換至另一個活動時有視覺的持續性。 應用程式可以指定三種類型的轉換動畫：

- **輸入**活動進入場景時的轉換 &ndash;。

- 當活動結束場景時，結束**轉換**&ndash;。

- 當兩個活動共通的視圖因為第一個活動轉換至下一個而變更時，**共用專案轉換**&ndash;。

例如，下列螢幕擷取畫面的順序說明共用專案轉換：

[共用專案轉換動畫的 ![框架（依框架螢幕擷取畫面）](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

共用元素（caterpillar 的相片）是第一個活動中的數個視圖之一;它會放大為第二個活動的唯一顯示，因為第一個活動會轉換成第二個活動。

#### <a name="enter-transition-animation-types"></a>輸入轉換動畫類型

針對 enter 轉換，Android 5.0 提供三種類型的動畫：

- **分解動畫**&ndash; 從場景的中央放大視野。

- **滑動動畫**&ndash; 從場景的其中一個邊緣移動視圖。

- **淡出動畫**&ndash; 將視圖淡入場景中。

#### <a name="exit-transition-animation-types"></a>結束轉換動畫類型

針對結束轉換，Android 5.0 提供三種類型的動畫：

- **分解動畫**&ndash; 將視野縮小到場景的中心。

- **滑動動畫**&ndash; 將視圖向外移動到場景的其中一個邊緣。

- **淡出動畫**&ndash; 淡出場景的視圖。

#### <a name="shared-element-transition-animation-types"></a>共用元素轉換動畫類型

共用元素轉換支援多種動畫類型，例如：

- 變更視圖的版面配置或剪輯邊界。

- 變更視圖的縮放和旋轉。

- 變更視圖的大小和縮放比例類型。

如需 Android 5.0 中活動轉換動畫的詳細資訊，請參閱[自訂活動轉換](https://developer.android.com/training/material/animations.html#Transitions)。

### <a name="view-state-transition-animations"></a>檢視狀態轉換動畫

當視圖的狀態變更時，Android 5.0 可以讓動畫執行。 您可以使用下列其中一種技術，以動畫顯示狀態轉換：

- 建立可繪製資源，以建立與特定視圖相關聯之狀態變更的動畫。 新的 `AnimatedStateListDrawable` 類別可讓您建立可繪製資源，以顯示檢視狀態變更之間的動畫。

- 定義當視圖的狀態變更時所執行的動畫功能。 新的 `StateListAnimator` 類別可讓您定義在檢視狀態變更時執行的 animator。

如需 Android 5.0 中檢視狀態轉換動畫的詳細資訊，請參閱[動畫檢視狀態變更](https://developer.android.com/training/material/animations.html#ViewState)。

### <a name="reveal-effect"></a>顯示效果

*顯示效果*是變更半徑以顯示或隱藏視圖的裁剪圓形。 您可以藉由設定裁剪圓形的初始和最終半徑來控制此效果。 下列螢幕擷取畫面的順序說明從畫面中央顯示效果動畫：

[顯示動畫的 ![框架螢幕擷取畫面](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

下一個順序說明從畫面左下角進行的顯示效果動畫：

[裁剪動畫的 ![框架並排螢幕擷取畫面](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

您可以反轉顯示動畫;也就是說，裁剪圓形可以縮小以隱藏視圖，而不是放大以顯示視圖。

如需 Android 5.0 顯示效果在中的詳細資訊，請參閱[使用顯示效果](https://developer.android.com/training/material/animations.html#Reveal)。

### <a name="curved-motion"></a>彎曲動作

除了這些動畫功能，Android 5.0 也提供新的 Api，可讓您指定動畫的時間和動畫曲線。 Android 5.0 會使用這些曲線來插入動畫期間的時態和空間移動。 Android 5.0 中定義了三個曲線：

- &ndash; 快速加快 **\_\_線性\_** ，並繼續加速直到動畫結束為止。

- **快速\_\_緩慢的\_** ，&ndash; 加速到動畫結束時快速且緩慢的減速。

- **線性\_輸出 &ndash; 中的\_緩慢\_** 會從尖峰速度開始，並慢慢減速至動畫的結尾。

您可以使用新的 `PathInterpolator` 類別來指定動作插補的發生方式。 `PathInterpolator` 是根據指定的控制點和動作曲線來進行動畫路徑的插即用。 如需如何在 Android 5.0 中指定彎曲動作設定的詳細資訊，請參閱[使用彎曲動作](https://developer.android.com/training/material/animations.html#CurvedMotion)。

## <a name="view-shadows--elevation"></a>視圖陰影 & 提高許可權

在 Android 5.0 中，您可以藉由設定新的 `Z` 屬性來指定*高度的提升許可權*。 較大的 `Z` 值會導致視圖在背景上轉換較大的陰影，讓視圖顯示在背景上方的浮動位置。 您可以藉由在配置中設定其 `elevation` 屬性，來設定視圖的初始高度。

下列範例說明當其提高許可權屬性分別設定為2dp、4dp 和6dp 時，空的 `TextView` 控制項的陰影轉換：

[progessively 較大視圖陰影的 ![螢幕擷取畫面](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

視圖陰影設定可以是靜態的（如上所示），也可以在動畫中用來讓視圖顯示，暫時高於視圖的背景。 您可以使用 `ViewPropertyAnimator` 類別，以動畫顯示高度的提升許可權。 View 的高度是其配置的總和 `elevation` 設定加上您可以透過 `ViewPropertyAnimator` 方法呼叫來設定的 `translationZ` 屬性。

如需 Android 5.0 中視圖陰影的詳細資訊，請參閱[定義陰影和裁剪視圖](https://developer.android.com/training/material/shadows-clipping.html)。

## <a name="color-features"></a>色彩功能

Android 5.0 提供兩個新功能來管理應用程式中的色彩：

- 「可*繪製*色彩」可讓您藉由變更版面配置屬性來改變影像資產的色彩。

- *明顯的色彩提取*可讓您以動態方式自訂應用程式的色彩主題，以與所顯示影像的調色板進行協調。

### <a name="drawable-tinting"></a>繪製色調

Android 5.0 版面配置會辨識新的 `tint` 屬性，您可以用來設定可繪製資源的色彩，而不需要建立這些資產的多個版本來顯示不同的色彩。 若要使用這項功能，您可以將點陣圖定義為 Alpha mask，並使用 `tint` 屬性來定義資產的色彩。 如此一來，您就可以建立資產一次，並在版面配置中將其色彩配置，以符合您的主題。

在下列範例中，單一影像資產 &ndash; 具有透明背景 &ndash; 的白色標誌，用來建立色調變化：

![具有透明背景的白色 Xamarin 標誌](lollipop-images/xamarin-logo-white.png)

此標誌會以藍色迴圈背景顯示，如下列範例所示。 左邊的影像是標誌出現的方式，沒有 `tint` 設定。 在中央影像中，標誌的 `tint` 屬性會設定為暗灰色。 在右邊的影像中，`tint` 設定為淺灰色：

![使用不同色調設定的上述標誌範例](lollipop-images/drawable-tinting.png)

如需有關在 Android 5.0 中可繪製色調的詳細資訊，請參閱可[繪製的色調](https://developer.android.com/training/material/drawables.html#DrawableTint)。

### <a name="prominent-color-extraction"></a>顯著色彩的解壓縮

新的 Android 5.0 `Palette` 類別可讓您從影像中解壓縮色彩，讓您可以動態地將它們套用至自訂調色盤。 `Palette` 類別會從影像中解壓縮六種色彩，並根據色彩飽和度和亮度的相對層級來標記這些色彩：

- 活力

- 生動深

- 活躍光線

- 減弱

- 黑色深色

- 淺色燈

例如，在下列螢幕擷取畫面中，相片觀賞應用程式會從顯示影像中取出明顯的色彩，並使用這些色彩來調整應用程式的色彩配置，以符合影像：

[![綠色、粉紅色和藍色主題色提取的螢幕擷取畫面](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

在上述螢幕擷取畫面中，動作列設定為已解壓縮的「生動光源」色彩，而背景設定為已解壓縮的「生動暗」色彩。 在上述每個範例中，會包含一個小顏色方塊的資料列，以說明從影像中解壓縮的調色板色彩。

如需 Android 5.0 中色彩解壓縮的詳細資訊，請參閱[從影像中解壓縮顯著的色彩](https://developer.android.com/training/material/drawables.html#ColorExtract)。

## <a name="new-ui-widgets"></a>新的 UI widget

Android 5.0 引進兩個新的 UI 小工具：

- `RecyclerView` &ndash; 顯示可滾動專案清單的視圖群組。

- `CardView` &ndash; 具有圓角的基本版面配置。

這兩個小工具都包含材質主題功能的內建支援;例如，`RecyclerView` 會使用動畫來新增和移除視圖，而 `CardView` 會使用視圖陰影，讓每張卡片在背景上方顯示為浮動。 下列螢幕擷取畫面顯示這些新 widget 的範例：

[![以 RecyclerView 建立之應用程式的螢幕擷取畫面](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

左側的螢幕擷取畫面是在電子郵件應用程式中使用 `RecyclerView` 的範例，右側的螢幕擷取畫面是在旅遊保留應用程式中使用 `CardView` 的範例。

### <a name="recyclerview"></a>RecyclerView

`RecyclerView` 類似 `ListView,`，但較適合具有動態變更元素的大型視圖或清單集合。 就像 `ListView,` 您指定介面卡來存取基礎資料集。 不過，不同于 `ListView,` 您會使用*版面建構管理員*，將專案放在 `RecyclerView`內。 版面建構管理員也會負責查看回收;它會管理使用者不會再看到的專案流覽重複使用方式。

當您使用 `RecyclerView` widget 時，您必須指定 `LayoutManager` 和介面卡。 如上圖所示，`LayoutManager` 是介面卡和 `RecyclerView`之間的媒介：

![支援 LayoutManager、介面卡和資料集的 RecyclerView 圖](lollipop-images/recyclerview-diagram.png)

下列螢幕擷取畫面說明包含100專案的 `RecyclerView` （每個專案都包含一個 `ImageView` 和一個 `TextView`）：

[![透過影像滾動的 RecyclerView 應用程式螢幕擷取畫面](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView` 可以輕鬆地處理這個大型資料集，&ndash; 從清單的開頭到此範例應用程式中清單結尾的滾動，只需要幾秒鐘的時間。 `RecyclerView` 也支援動畫;事實上，預設會啟用新增和移除專案的動畫。 當專案新增至 `RecyclerView`時，它會淡入，如下列螢幕擷取畫面的順序所示：

[![框架中的相片專案螢幕擷取畫面影像淡入](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

如需 `RecyclerView`的詳細資訊，請參閱[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)。

### <a name="cardview"></a>CardView

`CardView` 是一個簡單的視圖，可模擬具有圓角的浮動卡片。 因為 `CardView` 具有內建的視圖陰影，所以可讓您輕鬆地在應用程式中加入視覺深度。 下列螢幕擷取畫面顯示三個文字導向的 `CardView`範例：

[使用 RecyclerView 搭配以 CardView 為基礎的專案來 ![應用程式的範例螢幕擷取畫面](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

上述範例中的每張卡片都包含 `TextView`;背景色彩是透過 `cardBackgroundColor` 屬性來設定。

如需 `CardView`的詳細資訊，請參閱[CardView](~/android/user-interface/controls/card-view.md)。

## <a name="enhanced-notifications"></a>增強的通知

Android 5.0 中的通知系統已使用新的視覺效果格式和新功能大幅更新。 通知在 Android 5.0 中有新的外觀。 例如，Android 5.0 中的通知現在會在淺背景使用深色文字：

![未展開的 Android 5.0 通知範例](lollipop-images/expanded-notification-contracted.png)

當通知中顯示大型圖示時（如上述範例所示），Android 5.0 會在大型圖示上以徽章呈現小圖示。

在 Android 5.0 中，通知也可能會出現在裝置上的鎖屏。
例如，以下是具有單一通知之鎖屏的範例螢幕擷取畫面：

[![鎖定畫面上顯示的通知螢幕擷取畫面](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

使用者可以在鎖屏上按兩下通知，以解除鎖定裝置，並跳至產生該通知的應用程式，或滑動以關閉通知。 通知會有新的*可見度*設定，可決定可以在鎖定上顯示多少內容。 使用者可以選擇是否允許在鎖屏通知中顯示機密內容。

Android 5.0 引進新的高優先順序通知簡報格式，稱為「*列印頭*」。 標題通知會從畫面頂端向下滑動幾秒鐘，然後回頭回到畫面頂端的通知網底。 標頭通知讓系統 UI 可以將重要資訊放在使用者前方，而不會中斷目前執行的活動。
下列範例說明在應用程式頂端顯示的簡單標題通知：

[![的列印頭通知範例](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

標頭通知通常用於下列事件：

- 新的下一個訊息

- 撥打電話

- 電力偏低指示

- 鬧鐘

Android 5.0 只有在具有高或最大優先順序設定時，才會以列印頭格式顯示通知。

在 Android 5.0 中，您可以提供通知中繼資料，協助 Android 排序並更聰明地顯示通知。 Android 5.0 會根據優先順序、可見度和類別來組織通知。
通知類別是用來篩選當裝置處於「*請勿打擾*」模式時，可以呈現哪些通知。

如需使用最新的 Android 5.0 功能來建立和啟動通知的詳細資訊，請參閱[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。

## <a name="new-apis"></a>新的 API

除了上述的新外觀與風格功能以外，Android 5.0 還新增了新的 Api，可擴充現有多媒體、存放裝置和無線/連線功能的功能。 此外，Android 5.0 包含新的 Api，可提供新作業排程器功能的支援。

### <a name="camera"></a>相機

Android 5.0 提供數個新的 Api，以增強相機功能。 新的 `Android.Hardware.Camera2` 命名空間包含存取連線至 Android 裝置之個別相機裝置的功能。 此外，`Android.Hardware.Camera2` 會將每個相機裝置當做管線來建立模型：它會接受 capture 要求、捕獲影像，然後輸出結果。 這種方法可讓應用程式將多個捕獲要求佇列至相機裝置。

下列 Api 可讓您實現這些新功能：

- `CameraManager.GetCameraIdList` &ndash; 可協助您以程式設計方式存取相機裝置;您可以使用 `CameraManager.OpenCamera` 來連接到特定的相機裝置。

- `CameraCaptureSession` &ndash; 會從相機裝置捕獲或串流影像。 您會執行 `CameraCaptureSession.CaptureListener` 介面來處理新的影像捕捉事件。

- `CaptureRequest` &ndash; 定義 capture 參數。

- `CaptureResult` &ndash; 提供影像捕獲作業的結果。

如需 Android 5.0 中新相機 Api 的詳細資訊，請參閱[媒體](https://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="audio-playback"></a>音訊播放

Android 5.0 會更新 `AudioTrack` 類別，以進行更好的音訊播放：

- `ENCODING_PCM_FLOAT` &ndash; 會將 `AudioTrack` 設定為接受浮點格式的音訊資料，以獲得更好的動態範圍、較大的空間，以及更高的品質（感謝增加的精確度）。 此外，浮點格式有助於避免音訊裁剪。

- `ByteBuffer` &ndash; 您現在可以將音訊資料提供給 `AudioTrack` 做為位元組陣列。

- `WRITE_NON_BLOCKING` &ndash; 此選項可簡化某些應用程式的緩衝和多執行緒處理。

如需 Android 5.0 中 `AudioTrack` 改良功能的詳細資訊，請參閱[媒體](https://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="media-playback-control"></a>媒體播放控制

Android 5.0 引進新的 `Android.Media.MediaController` 類別，它會取代 `RemoteControlClient`。 `Android.Media.MediaController` 提供簡化的傳輸控制 Api，並提供執行緒安全控制，以在 UI 內容之外播放。 下列新的 Api 會處理傳輸控制：

- `Android.Media.Session.MediaSession` &ndash; 處理多個控制器的媒體控制會話。 您會呼叫 `MediaSession.GetSessionToken` 來要求您的應用程式用來與會話互動的權杖。

- `MediaController.TransportControls` &ndash; 會處理傳輸命令，例如**播放**、**停止**和**略過**。

此外，您可以使用新的 `Android.App.Notification.MediaStyle` 類別，將媒體會話與豐富通知內容產生關聯（例如，解壓縮和顯示專輯封面）。

如需 Android 5.0 中新媒體播放控制功能的詳細資訊，請參閱[媒體](https://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="storage"></a>儲存體

Android 5.0 會更新儲存體存取架構，讓應用程式更容易使用目錄和檔：

- 若要選取目錄子樹狀結構，您可以建立並傳送 `Android.Intent.Action.OPEN_DOCUMENT_TREE` 的意圖。 此意圖會使系統顯示支援子樹選取的所有提供者實例;使用者接著流覽並選取目錄。

- 若要在子樹下的任何位置建立和管理新的檔或目錄，您可以使用 `DocumentsContract`的新 `CreateDocument`、`RenameDocument`和 `DeleteDocument` 方法。

- 若要取得所有共用存放裝置上的媒體目錄路徑，您可以呼叫新的 `Android.Content.Context.GetExternalMediaDirs` 方法。

如需 Android 5.0 中新儲存體 Api 的詳細資訊，請參閱[儲存體](https://developer.android.com/preview/api-overview.html#Storage)。

### <a name="wireless--connectivity"></a>無線 & 連線能力

Android 5.0 新增下列適用于無線和連線能力的 API 增強功能：

- 新的*多網路*api，可讓應用程式在建立連線之前，尋找並選取具有特定功能的網路。

- 可讓 Android 5.0 裝置做為低能源藍牙周邊的藍牙廣播功能。

- NFC 增強功能，可讓您更輕鬆地使用近距離無線通訊功能來與其他裝置共用資料。

如需 Android 5.0 中新無線和連線 Api 的詳細資訊，請參閱[無線和連線能力](https://developer.android.com/preview/api-overview.html#Wireless)。

### <a name="job-scheduling"></a>工作排程

Android 5.0 引進新的 `JobScheduler` API，可協助使用者將特定工作排程為只有在裝置插入和收費時才執行，以將電池清空降至最低。 這項工作排程器功能也可以用來排定工作，以便在條件較適合該工作時執行，例如在裝置透過 Wi-fi 網路（而非計量付費網路）連線時下載大型檔案。

如需 Android 5.0 中新作業排程 Api 的詳細資訊，請參閱[排程工作](https://developer.android.com/preview/api-overview.html#JobScheduler)。

## <a name="summary"></a>摘要

本文概述適用于 Xamarin 的 Android 5.0 重要新功能。 Android 應用程式開發人員：

- 材質佈景主題

- Animations

- 視圖陰影和提高許可權

- 色彩功能，例如繪製色彩和醒目顏色的抽取

- 新的 `RecyclerView` 和 `CardView` widget

- 通知增強功能

- 適用于相機、音訊播放、媒體控制、存放裝置、無線/連線和工作排程的新 Api

如果您不熟悉 Xamarin Android 開發，請閱讀[安裝和安裝](~/android/get-started/installation/index.md)，以協助您開始使用 Xamarin. Android。
[Hello，Android](~/android/get-started/hello-android/index.md)是學習如何建立 Android 專案的絕佳簡介。

## <a name="related-links"></a>相關連結

- [Android L 開發人員預覽](https://developer.android.com/preview/index.html)
- [取得 Android SDK](https://developer.android.com/sdk/index.html#Other)
- [材質設計](https://developer.android.com/preview/material/index.html)
