---
title: 棒棒糖功能
description: 本文對 Android 5.0(棒棒糖)中引入的新功能進行了高級別概述。 這些功能包括稱為"材料主題"的新用戶界面樣式,以及動畫、視圖陰影和可繪製著色等新支援功能。 Android 5.0 還包括增強的通知、兩個新的 UI 小部件、一個新的作業計劃程式以及一些用於改進存儲、網路、連接和多媒體功能的新 API。
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 297c7806ce8a880d65c38ef0e4672e41fee5acfe
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292262"
---
# <a name="lollipop-features"></a>棒棒糖功能

_本文對 Android 5.0(棒棒糖)中引入的新功能進行了高級別概述。這些功能包括稱為"材料主題"的新用戶界面樣式,以及動畫、視圖陰影和可繪製著色等新支援功能。Android 5.0 還包括增強的通知、兩個新的 UI 小部件、一個新的作業計劃程式以及一些用於改進存儲、網路、連接和多媒體功能的新 API。_

## <a name="lollipop-overview"></a>棒棒糖概述

Android 5.0(棒棒糖)引入了一種新的設計語言,*材料設計*,並用它來支援鑄造的新功能,使應用程式更容易和更直觀的使用。 與材料設計,Android 5.0不僅給Android手機一個整容;它還為基於 Android 的平板電腦、桌上型電腦、手錶和智慧電視提供了一套新的設計規則。 這些設計規則強調簡單性和極簡性,同時利用熟悉的觸覺屬性(如逼真的表面和邊緣提示)來幫助使用者快速直觀地理解介面。

*材料主題*是這些UI設計原則在Android的體現。 本文首先介紹了材料主題的支援功能:

- **動畫**&ndash;*觸控回饋*動畫、*活動過渡*動畫、*檢視狀態轉換*動畫與*顯示效果*。

- **視圖陰影和高程**&ndash;視圖現在`elevation`具有 屬性;  值較高的`elevation`視圖在背景上投射了更大的陰影。

- **顏色功能**&ndash;*可繪製的著色*使您能夠透過更改影像資源的顏色來重用影像資產,而*突出的顏色提取*可説明您根據影像中的顏色動態設置應用主題。

許多材料主題功能已內置於 Android 5.0 UI 體驗中,而其他功能必須顯式添加到應用中。 例如,某些標準檢視(如按鈕)已包含觸摸反饋動畫,而應用必須啟用大多數視圖陰影。

除了通過材料主題帶來的 UI 改進外,Android 5.0 還包括本文中介紹的其他幾個新功能:

- Android 5.0 中的**增強通知**&ndash;通知已顯著更新,具有新外觀、對鎖屏通知的支援以及新的*抬頭*通知表示格式。

- **新的 UI 小部件**&ndash;新`RecyclerView`小部件使應用程式能夠更輕鬆地傳達大型數據集和`CardView`複雜資訊,而新 小部件為顯示文本和圖像提供了簡化的卡片式表示格式。

- **新的 API** &ndash; Android 5.0 為多個網路支援添加了新的 API、改進的藍牙連接、更簡單的儲存管理以及更靈活的多媒體播放器和攝像機設備的控制。 新的作業計劃功能可用於在計劃時間非同步運行任務。 此功能有助於提高電池壽命,例如,將設備插入電源和充電時的任務安排在進行。

## <a name="requirements"></a>需求

在基於 Xamarin 的應用中使用新的 Android 5.0 功能需要以下操作:

- **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更高版本必須安裝並配置用於 Mac 的 Visual Studio 或視覺工作室。

- **Android SDK** &ndash; Android 5.0 (API 21) 或更高版本必須通過 Android SDK 管理器安裝。

- **Java 開發人員工具組**&ndash;Xamarin.Android 需要[JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高版本(如果您正在開發 API 級別 24 或更高(JDK 1.8 還支援早於 24 的 API 級別,包括棒棒糖)。 如果使用自定義控制件或表單預覽器,則需要 64 位元版本的 JDK 1.8。

如果您專門為 API 級別 23 或更早開發,則可以繼續使用[JDK 1.7。](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)

## <a name="setting-up-an-android-50-project"></a>設定 Android 5.0 專案

要創建 Android 5.0 專案,必須安裝最新的工具和 SDK 包。 使用以下步驟設定針對 Android 5.0 的 Xamarin.Android 專案:

1. 安裝 Xamarin.Android 工具並啟動您的 Xamarin 許可證。 有關安裝 Xamarin.Android 的詳細資訊,請參閱[設定和安裝](~/android/get-started/installation/index.md)。

2. 如果您使用的是 Mac 的 Visual Studio,請安裝最新的 Android 5.0 更新。

3. 啟動 Android SDK 管理員(在 Mac 視覺化工作室中,使用**&gt;工具&hellip;打開 Android SDK 管理員**),然後安裝 Android SDK 工具 23.0.5 或更高版本:

    [![在 Android SDK 管理員中選擇 Android SDK 工具](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   此外,安裝最新的 Android 5.0 SDK 套件(API 21 或更高版本):

    [![在 Android SDK 管理員安裝 Android 5.0 SDK 套件](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   有關使用 Android SDK 管理員的詳細資訊,請參閱[SDK 管理員](https://developer.android.com/tools/help/sdk-manager.html)。

4. 創建新的 Xamarin.安卓專案。 如果您是使用 Xamarin 的 Android 開發的新使用者,請參閱[Hello、Android](~/android/get-started/hello-android/index.md)以瞭解創建 Android 專案。 創建 Android 專案時,請確保為 Android 5.0 配置版本設置。
   在 Mac 的視覺化工作室中,導航**到&gt;專案選項&gt;生成常規**,並將**目標框架**設定為 Android **5.0(棒棒糖)** 或更高版本:

    ![將目標弗拉姆韋工作設置為安卓 5.0 棒棒糖](lollipop-images/target-framework.png)

   在**專案選項&gt;&gt;建構 Android 應用程式**, 設定最小和目標Android版本自動 **- 使用目標框架版本**:

    ![將最小和目標 Android 版本設置為"自動"](lollipop-images/minimum-android-version.png)

5. 配置模擬器或 Android 設備以測試你的應用。 如果您使用的是模擬器,請參閱[Android 模擬器安裝程式](~/android/get-started/installation/android-emulator/index.md),瞭解如何配置 Android 模擬器,以便與 Xamarin Studio 或 Visual Studio 一起使用。 如果您使用的是 Android 設備,請參閱[設定預覽 SDK](https://developer.android.com/preview/setup-sdk.html)以瞭解如何為 Android 5.0 更新設備。 要設定 Android 裝置以執行和除錯 Xamarin.Android 應用程式,請參閱[設定裝置進行開發](~/android/get-started/installation/set-up-device-for-development.md)。

注意:如果您正在更新針對 Android L 預覽版的現有 Android 專案,則必須將**目標框架**和**Android 版本**更新為上述值。

## <a name="important-changes"></a>重要變更

以前發佈的 Android 應用可能會受到 Android 5.0 中的更改的影響。 特別是,Android 5.0 使用新的運行時和顯著更改的通知格式。

### <a name="android-runtime"></a>安卓運行時

Android 5.0 使用新的 Android 運行時 (ART) 作為預設運行時,而不是達爾維克。 ART 實現了幾個主要新功能:

- **提前 (AOT) 編譯**&ndash;AOT 可以通過在首次啟動應用之前編譯應用代碼來提高應用性能。 安裝應用後,ART 會為目標設備生成已編譯的應用可執行檔。

- **改進的垃圾回收 (GC)** &ndash; ART GC 改進還可以提高應用性能。 垃圾回收現在使用一個 GC 暫停而不是兩個,併發 GC 操作以更及時的方式完成。

- **改進的應用調試**&ndash;ART提供了更多的診斷詳細資訊,以説明分析異常和崩潰報告。

現有應用應在 ART&ndash;下不進行更改的情況下工作,但利用以前 Dalvik 運行時特有的技術的應用除外,這些技術在 ART 下可能不起作用。 有關這些更改的詳細資訊,請參閱在[Android 執行時 (ART) 上驗證應用行為](https://developer.android.com/guide/practices/verifying-apps-art.html)。

### <a name="notification-changes"></a>通知變更

Android 5.0 中通知發生了顯著變化:

- **音效與振動的處理方式不同**&ndash;,`Notification.Builder`現在`Ringtone`由`MediaPlayer`而不是和來處理聲音和`Vibrator`振動。

- **新的色彩配置**&ndash;根據材質主題,通知在白色或非常淺的背景上以深色文本呈現。 此外,Android 可以修改通知圖示中的 Alpha 通道,以便與系統色彩配置進行協調。

- **鎖定螢幕通知**&ndash;通知現在可以顯示在設備鎖定螢幕上。

- **當**&ndash;設備解鎖並打開螢幕時,抬頭高優先順序通知現在顯示在一個小浮動視窗中(抬頭通知)。

在大多數情況下,將現有應用通知功能移植到 Android 5.0 需要以下步驟:

1. 將代碼轉換為使用`Notification.Builder`(`NotificationsCompat.Builder`或 ) 建立通知。

2. 驗證現有通知資產在新的材質主題色彩配置中是否可查看。

3. 確定通知在鎖屏介面上顯示時應具有的可見性。 如果通知不公開,鎖屏上應顯示哪些內容?

4. 設置通知的類別,以便在新的 Android 5.0*請勿打擾*模式下正確處理通知。

如果通知顯示傳輸控制項、顯示媒體播放狀態、使用`RemoteControlClient`或呼`ActivityManager.GetRecentTasks`叫 ,請參閱[「重要行為更改」,](https://developer.android.com/preview/api-overview.html#Behaviors)瞭解有關更新 Android 5.0 通知的詳細資訊。

有關在 Android 中建立通知的資訊,請參閱[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。

## <a name="material-theme"></a>材質佈景主題

新的Android 5.0材料主題帶來了Android用戶介面的外觀和感覺的徹底變化。 視覺元素現在使用觸覺表面,這些曲面採用基於列印設計的粗體圖形、排版和明亮的顏色。 以下螢幕截圖中描述了材料主題的範例:

[![材料主題主畫面、應用螢幕和設定螢幕的螢幕截圖](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 在左側顯示主屏幕來問候您。 中心螢幕截圖是應用清單的第一個螢幕,右側的螢幕截圖是 **「設置」** 螢幕。 Google 的材料[設計](https://material.io/guidelines/material-design/introduction.html)規範解釋了新材料主題概念背後的基本設計規則。

材料主題包括三種內建風格,您可以在應用中使用:`Theme.Material`深色主題(預設`Theme.Material.Light`)、`Theme.Material.Light.DarkActionBar`主題和 主題:

[![黑暗、淺色和暗行動欄主題的螢幕截圖](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

有關在 Xamarin.Android 應用程式中使用材料主題功能的更多資訊,請參閱[材料主題](~/android/user-interface/material-theme.md)。

## <a name="animations"></a>Animations

Android 5.0 提供觸摸反饋動畫、活動過渡動畫和查看狀態過渡動畫,使應用介面更直觀。 此外,Android 5.0 應用程式可以使用*顯示效果*動畫來隱藏或顯示視圖。 您可以使用*曲線運動*設定來配置動畫的呈現速度或速度。

### <a name="touch-feedback-animations"></a>觸控回饋動畫

觸摸反饋動畫在觸摸視圖時為使用者提供視覺反饋。 例如,按鈕現在在觸摸&ndash;時顯示紋波效果,這是 Android 5.0 中的預設觸摸反饋動畫。 波紋動畫由新`RippleDrawable`類實現。 波紋效應可以配置為在檢視的邊界結束或超出視圖的邊界。 例如,以下螢幕截圖序列說明了觸摸動畫期間按鈕中的波紋效果:

![按鈕上波紋動畫的幀截圖](lollipop-images/touch-animation.png)

與按鈕的初始觸摸接觸發生在左側的第一個圖像中,而其餘序列(從左到右)說明瞭紋波效果如何擴散到按鈕的邊緣。 波紋動畫結束時,視圖將返回到其原始外觀。 默認紋波動畫只需幾分之一秒即可進行,但動畫的長度可以針對更長或更短的時間長度進行自定義。

有關 Android 5.0 中的觸控反饋動畫的更多,請參閱[自訂觸控回饋](https://developer.android.com/training/material/animations.html#Touch)。

### <a name="activity-transition-animations"></a>活動轉換動畫

當一個活動轉換到另一個活動時,活動過渡動畫為使用者提供視覺連續性的感覺。 套用可以指定三種類型的過渡動畫:

- **輸入轉換**&ndash;當活動進入場景時。

- **退出過渡**&ndash;當活動退出場景時。

- **共用元素轉換**&ndash;當兩個活動共有的視圖隨著第一個活動轉換為下一個活動而更改時。

例如,以下螢幕截圖序列說明了共用元素轉換:

[![共用元素過渡動畫的畫面截圖](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

共用元素(毛毛蟲的照片)是第一個活動中的多個視圖之一;當第一個活動轉換為第二個活動時,它放大成為第二個活動中的唯一視圖。

#### <a name="enter-transition-animation-types"></a>輸入過渡動畫類型

對於輸入過渡,Android 5.0 提供三種類型的動畫:

- **爆炸動畫**&ndash;從場景中心放大視圖。

- **幻燈片動畫**&ndash;從場景的一個邊緣移動視圖中。

- **淡入淡出動畫**&ndash;將視圖淡入場景中。

#### <a name="exit-transition-animation-types"></a>離開過渡動畫類型

對於退出過渡,Android 5.0 提供三種類型的動畫:

- **爆炸動畫**&ndash;將視圖縮小到場景的中心。

- **幻燈片動畫**&ndash;將視圖移出到場景的一個邊緣。

- **淡入淡出動畫**&ndash;將視圖淡出場景。

#### <a name="shared-element-transition-animation-types"></a>共用元素轉換動畫類型

共用元素轉換支援多種類型的動畫,例如:

- 更改檢視的佈局或剪輯邊界。

- 更改檢視的縮放和旋轉。

- 更改檢視的大小和縮放類型。

有關 Android 5.0 中的活動過渡動畫,請參閱[自訂活動過渡](https://developer.android.com/training/material/animations.html#Transitions)。

### <a name="view-state-transition-animations"></a>檢視狀態轉換動畫

Android 5.0 使動畫在視圖狀態更改時能夠運行。 您可以使用以下技術之一為檢視狀態轉換設定動畫:

- 創建可繪製的可繪製項,為與特定檢視關聯的狀態更改設置動畫。 新`AnimatedStateListDrawable`類允許您創建在檢視狀態更改之間顯示動畫的可繪製。

- 定義檢視狀態更改時運行的動畫功能。 新`StateListAnimator`類別允許您定義在檢視狀態更改時執行的動畫器。

有關 Android 5.0 中檢視狀態轉換動畫的更多,請參閱[Animate 檢視狀態更改](https://developer.android.com/training/material/animations.html#ViewState)。

### <a name="reveal-effect"></a>揭示效果

*顯示效果*是一個剪切圓,用於更改半徑以顯示或隱藏檢視。 您可以通過設定剪切圓的初始半徑和最終半徑來控制此效果。 以下螢幕截圖序列說明了螢幕中心的顯示效果動畫:

[![顯示動畫的幀按幀螢幕截圖](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

下一個序列展示了從螢幕左下角發生的顯示效果動畫:

[![剪輯動畫的幀按幀螢幕截圖](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

可以反轉顯示動畫;也就是說,剪切圈可以縮小以隱藏檢視,而不是放大以顯示檢視。

有關 Android 5.0 顯示效果的詳細資訊,請參閱[使用"顯示效果](https://developer.android.com/training/material/animations.html#Reveal)"。

### <a name="curved-motion"></a>彎曲運動

除了這些動畫功能外,Android 5.0 還提供新的 API,使您能夠指定動畫的時間和運動曲線。 Android 5.0 使用這些曲線在動畫期間插值時空運動。 Android 5.0 定義了三條曲線:

- **快速\_快速\_加速\_中**&ndash;快速出線性,並繼續加速,直到動畫結束。

- **快\_\_出\_慢 在**&ndash;加速快速和緩慢減速接近動畫的結尾。

- **線性\_\_出\_向 慢在**&ndash;以峰值速度開始,並緩慢減速到動畫的末尾。

可以使用新`PathInterpolator`類指定運動插值的方式。 `PathInterpolator`是一種插值器,根據指定的控制點和運動曲線遍歷動畫路徑。 有關如何在 Android 5.0 中指定曲線運動設定的詳細資訊,請參閱[使用曲線運動](https://developer.android.com/training/material/animations.html#CurvedMotion)。

## <a name="view-shadows--elevation"></a>檢視陰影&高程

在 Android 5.0*elevation*中,`Z`您可以通過設定新 屬性來指定檢視的高程。 值越大`Z`,視圖在背景上投射較大陰影,使視圖看起來會顯示在背景上方。 您可以通過在佈局中配置檢視`elevation`的屬性來設置視圖的初始高程。

下面的範例展示了空`TextView`控制檔在高程屬性分別設定為 2dp、4dp 和 6dp 時投射的陰影:

[![預兆較大檢視陰影的螢幕截圖](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

視圖陰影設置可以是靜態的(如上所示),也可以用於動畫,使視圖看起來暫時高於視圖的背景。 可以使用`ViewPropertyAnimator`類 為視圖的高程設置動畫。 視圖的高程是其佈局`elevation`設置加上`translationZ``ViewPropertyAnimator`可以通過 方法調用設置的屬性的總和。

有關 Android 5.0 中的檢視陰影,請參閱[定義陰影和剪切檢視](https://developer.android.com/training/material/shadows-clipping.html)。

## <a name="color-features"></a>色彩特徵

Android 5.0 提供了兩個新功能,用於管理應用中的顏色:

- *可繪製的著色*允許您透過更改佈局屬性來更改圖像資產的顏色。

- *顯著的顏色提取*使您能夠動態自定義應用的顏色主題,以便與顯示圖像的調色板進行協調。

### <a name="drawable-tinting"></a>可繪製的著色

Android 5.0 佈局可`tint`識別一 個新屬性,您可以使用該屬性來設置可繪製的顏色,而無需創建這些資產的多個版本來顯示不同顏色。 要使用此功能,請將位圖定義為 Alpha 蒙版,`tint`並使用 屬性定義資產的顏色。 這樣,您就可以創建一次資產並在佈局中著色以匹配主題。

在下面的範例中,單個影像資產&ndash;具有透明&ndash;背景 的白色徽標用於建立色調變體:

![白色夏馬林標誌,帶透明背景](lollipop-images/xamarin-logo-white.png)

此徽標顯示在藍色圓形背景上方,如以下示例所示。 左方影像為標誌標示在沒有設定的情況下顯示`tint`的方式 。 在中心圖像中,徽標的屬性`tint`設置為深灰色。 在右邊的影像中`tint`, 設定為淺灰色:

![具有不同色調設定的上述徽標示例](lollipop-images/drawable-tinting.png)

有關 Android 5.0 中可繪製著色的更多,請參閱[可繪製著色](https://developer.android.com/training/material/drawables.html#DrawableTint)。

### <a name="prominent-color-extraction"></a>突顯的顏色擷取

新的 Android 5.0`Palette`類允許您從影像中提取顏色,以便可以動態地將它們應用於自訂調色板。 該`Palette`類別從影像中提取六種顏色,並根據顏色飽和度和亮度的相對水平對這些顏色進行標註:

- 充滿 活力

- 充滿活力的黑暗

- 充滿活力的光線

- 溫和

- 靜音黑暗

- 靜音光

例如,在以下螢幕截圖中,照片查看應用從顯示的影像中提取突出的顏色,並使用這些顏色調整應用程式的色彩配置以符合影像:

[![綠色、粉紅色和藍色主題顏色提取的螢幕截圖](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

在上面的螢幕截圖中,動作欄設置為提取的"活力光"顏色,背景設置為提取的"充滿活力的深色"顏色。 在上面的每個示例中,都包含一排小顏色方塊來說明從圖像中提取的調色板顏色。

有關 Android 5.0 中顏色提取的更多,請參閱[從影像中提取突出顏色](https://developer.android.com/training/material/drawables.html#ColorExtract)。

## <a name="new-ui-widgets"></a>新的 UI 小元件

Android 5.0 引入了兩個新的 UI 小部件:

- `RecyclerView`&ndash;顯示可滾動項清單的檢視組。

- `CardView`&ndash;具有圓角的基本佈局。

這兩個小部件都包括烘焙式支援材料主題功能;例如,`RecyclerView`使用動畫添加和刪除檢視,並使用`CardView`檢視陰影使每張卡片顯示在背景上方。 這些新小元件的範例顯示在以下螢幕截圖中:

[![使用資源檢視建構的應用程式的螢幕擷取](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

左側的螢幕截圖是電子郵件應用中使用的示例`RecyclerView`,右側的螢幕截圖是旅行預訂應用中使用的示例`CardView`。

### <a name="recyclerview"></a>RecyclerView

`RecyclerView`與類似`ListView,`,但它更適合具有動態更改的元素的大型檢視集或清單。 就像`ListView,`指定一個適配器來訪問基礎數據集一樣。 但是,與`ListView,`使用*佈局管理器*在`RecyclerView`中 定位項不同。 佈局管理員還負責檢視回收;它管理不再對用戶可見的項視圖的重用。

使用`RecyclerView`小部件時,必須指定`LayoutManager`和適配器。 如圖所示,`LayoutManager`配接器與`RecyclerView`之間的中間關係是 :

![資源檢視,支援佈局管理員、配接器和資料集](lollipop-images/recyclerview-diagram.png)

以下螢幕截圖說明了`RecyclerView`包含 100 個專案的 (`ImageView`每個專案由`TextView`與組成 ):

[![資源回收程式檢視應用程式捲軸影像的螢幕擷取](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView`處理此大型資料集,從&ndash;清單的開頭輕鬆滾動到此示例應用中列表的末尾,只需幾秒鐘。 `RecyclerView`還支持動畫;事實上,默認情況下啟用用於添加和刪除項的動畫。 當專案新增到 中`RecyclerView`時 ,它淡入中,如以下螢幕截圖序列所示:

[![逐幀截圖的照片項目淡入](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

有關,`RecyclerView`請參考[資源回收器檢視](~/android/user-interface/layouts/recycler-view/index.md)。

### <a name="cardview"></a>CardView

`CardView`是類比具有圓角的浮動卡片的簡單視圖。 由於`CardView`具有內置視圖陰影,因此為您提供了向應用添加視覺深度的簡便方法。 以下螢幕截圖顯示以下三個面向文字的範例`CardView`:

[![使用資源回收器檢視使用基於 CardView 的項目的應用程式範例螢幕截圖](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

上面範例每個卡片都包含一個`TextView`。背景顏色通過`cardBackgroundColor`屬性設置。

有關,`CardView`請參考[卡檢視](~/android/user-interface/controls/card-view.md)。

## <a name="enhanced-notifications"></a>增強通知

Android 5.0 中的通知系統已顯著更新,具有新的視覺格式和新功能。 通知在 Android 5.0 中具有新的外觀。 例如,Android 5.0 中的通知現在在淺色背景上使用深色文本:

![未展開的 Android 5.0 通知範例](lollipop-images/expanded-notification-contracted.png)

當通知中顯示大圖示時(如上例所示),Android 5.0 會將小圖示顯示為大圖示上的徽章。

在 Android 5.0 中,通知也可以顯示在設備鎖定螢幕上。
例如,下面是帶有單個通知的鎖屏介面的範例螢幕截圖:

[![鎖定螢幕上發生的通知螢幕截圖](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

用戶可以雙擊鎖屏上的通知以解鎖設備並跳轉到發出該通知的應用,或輕掃以關閉通知。 通知具有新的*可見性*設置,用於確定鎖屏介面上可以顯示的內容量。 用戶可以選擇是否允許在鎖屏通知中顯示敏感內容。

Android 5.0 引入了一種新的高優先順序通知演示格式 *,稱為抬頭*。 抬頭通知從螢幕頂部向下滑動幾秒鐘,然後回落到屏幕頂部的通知陰影。 抬頭通知使系統 UI 能夠在不中斷當前正在運行的活動的情況下將重要資訊放在使用者面前。
下面的範例演示了顯示在應用頂部的簡單抬頭通知:

[![抬頭通知的範例](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

抬頭通知通常用於以下事件:

- 下一條訊息

- 來電

- 電池電力不足指示

- 報警器

Android 5.0 僅在具有高優先順序或最大優先順序設置時以抬頭格式顯示通知。

在 Android 5.0 中,您可以提供通知元數據,以説明 Android 更智慧地排序和顯示通知。 Android 5.0 根據優先順序、可見性和類別組織通知。
通知類別用於篩選設備處於 *「請勿打擾」* 模式時可以顯示的通知。

有關使用最新的 Android 5.0 功能建立和啟動通知的詳細資訊,請參閱[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)。

## <a name="new-apis"></a>新的 API

除了上述新的外觀功能外,Android 5.0 還添加了新的 API,可擴展現有多媒體、存儲和無線/連接功能的功能。 此外,Android 5.0 還包括新的 API,這些 API 支援新的作業計畫程式功能。

### <a name="camera"></a>相機

Android 5.0 提供了多種新的 API,用於增強相機功能。 新的`Android.Hardware.Camera2`命名空間包括用於訪問連接到 Android 設備的各個攝像機設備的功能。 此外,`Android.Hardware.Camera2`將每個攝像機設備建模為管道:它接受捕獲請求,捕獲圖像,然後輸出結果。 此方法使應用能夠對攝像機設備的多個捕獲請求進行排隊。

以下 API 使這些新功能成為可能:

- `CameraManager.GetCameraIdList`&ndash;説明您以程式設計方式訪問攝影機設備;用於`CameraManager.OpenCamera`連接到特定攝像機設備。

- `CameraCaptureSession`&ndash;從相機設備捕獲或流式傳輸圖像。 實現一個`CameraCaptureSession.CaptureListener`介面來處理新的圖像捕獲事件。

- `CaptureRequest`&ndash;定義捕獲參數。

- `CaptureResult`&ndash;提供圖像捕獲操作的結果。

有關 Android 5.0 中新相機 API 的更多,請參閱[媒體](https://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="audio-playback"></a>音訊播放

Android 5.0`AudioTrack`更新類,以更好地播放音訊:

- `ENCODING_PCM_FLOAT`&ndash;配置為`AudioTrack`以浮點格式接受音訊數據,從而獲得更好的動態範圍、更大的空間和質量(由於精度的提高)。 此外,浮點格式有助於避免音訊剪輯。

- `ByteBuffer`&ndash;現在,您可以將音訊數據`AudioTrack`作為位元組提供。

- `WRITE_NON_BLOCKING`&ndash;此選項簡化了某些應用的緩衝和多線程。

有關`AudioTrack`Android 5.0 改進的更多改進,請參閱[媒體](https://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="media-playback-control"></a>媒體播放控制

Android 5.0`Android.Media.MediaController`引入了 新的類,`RemoteControlClient`它取代了 。 `Android.Media.MediaController`提供簡化的傳輸控制 API,並提供在 UI 上下文之外的線程安全播放控制。 以下新的 API 處理傳輸控制:

- `Android.Media.Session.MediaSession`&ndash;處理多個控制器的媒體控制會話。 調用`MediaSession.GetSessionToken`以請求應用用於與會話交互的權杖。

- `MediaController.TransportControls`&ndash;處理傳輸命令,如**播放**、**停止**與**跳過**。

此外,您還可以使用新`Android.App.Notification.MediaStyle`類將媒體會話與豐富的通知內容(如提取和顯示專輯封面)相關聯。

有關 Android 5.0 中的新媒體播放控制功能,請參閱[媒體](https://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="storage"></a>儲存體

Android 5.0 更新儲存存取框架,使應用程式更易於使用目錄和文件:

- 要選擇目錄子樹,可以生成併發送`Android.Intent.Action.OPEN_DOCUMENT_TREE`意圖。 此意圖導致系統顯示支援子樹選擇的所有提供程式實例;因此,系統將顯示支援子樹選擇的所有提供程式實例。然後,用戶流覽並選擇目錄。

- 要在`CreateDocument`子樹下的任何位置創建和管理新文件或目錄,請使用新的、`RenameDocument``DeleteDocument``DocumentsContract`方法。

- 要取得所有分享存放裝置上的媒體目錄的路徑,請呼叫新方法`Android.Content.Context.GetExternalMediaDirs`。

有關 Android 5.0 中新的儲存 API,請參閱[儲存](https://developer.android.com/preview/api-overview.html#Storage)。

### <a name="wireless--connectivity"></a>無線&連接

Android 5.0 為無線和連接添加了以下 API 增強功能:

- 新的*多網路*API 使應用能夠在建立連接之前查找和選擇具有特定功能的網路。

- 藍牙廣播功能,使Android 5.0 設備能夠充當低功耗藍牙外圍設備。

- NFC 增強功能,使使用近場通信功能與其他設備共享數據變得更加容易。

有關 Android 5.0 中新的無線和連接 API 的更多資訊,請參閱[無線和連接](https://developer.android.com/preview/api-overview.html#Wireless)。

### <a name="job-scheduling"></a>工作排程

Android 5.0`JobScheduler`引入了一個新的 API,通過安排某些任務僅在設備插入並充電時運行,從而幫助使用者最大限度地減少電池消耗。 此作業計畫程式功能還可用於在條件更適合該任務時安排任務以運行,例如,當設備透過 Wi-Fi 網路而不是按流量計費的網路連接時下載大型檔。

有關 Android 5.0 中新作業計劃 API 的更多,請參閱[計畫作業](https://developer.android.com/preview/api-overview.html#JobScheduler)。

## <a name="summary"></a>總結

This article provided an overview of important new features in Android 5.0 for Xamarin.Android app developers:

- 材質佈景主題

- Animations

- 檢視陰影與高程

- 顏色特徵,如可繪製的著色和突出的顏色擷取

- 新的`RecyclerView`與`CardView`小元件

- 通知增強功能

- 用於攝影機、音訊播放、媒體控制、儲存、無線/連接和作業日程安排的新 API

如果您是 Xamarin Android 開發的新使用者,請閱讀[設置和安裝](~/android/get-started/installation/index.md),以説明您開始使用 Xamarin.Android。
[你好,Android](~/android/get-started/hello-android/index.md)是學習如何創建Android項目的絕佳介紹。

## <a name="related-links"></a>相關連結

- [安卓 L 開發者預覽](https://developer.android.com/preview/index.html)
- [取得 Android SDK](https://developer.android.com/sdk/index.html#Other)
- [材料設計](https://developer.android.com/preview/material/index.html)
