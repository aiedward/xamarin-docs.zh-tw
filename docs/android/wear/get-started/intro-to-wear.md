---
title: Android Wear 簡介
description: 隨著 Google 的 Android 磨損引進，在開發絕佳的 Android 應用程式時，您就不再只限于手機和平板電腦。 Xamarin 支援 Android 磨損，讓您可以在手腕上執行C#程式碼！ 本簡介提供 Android 磨損的基本總覽、描述其主要功能，並提供 Android 磨損2.0 中可用功能的總覽。 它會列出一些較熱門的 Android 磨損裝置，並提供基本 Google Android 磨損檔的連結，供進一步閱讀。
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 80c24765022a916fa36e97aaf47b36435b3f7a7b
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "70758504"
---
# <a name="introduction-to-android-wear"></a>Android Wear 簡介

_隨著 Google 的 Android 磨損引進，在開發絕佳的 Android 應用程式時，您就不再只限于手機和平板電腦。Xamarin 支援 Android 磨損，讓您可以在手腕上執行C#程式碼！本簡介提供 Android 磨損的基本總覽、描述其主要功能，並提供 Android 磨損2.0 中可用功能的總覽。它會列出一些較熱門的 Android 磨損裝置，並提供基本 Google Android 磨損檔的連結，供進一步閱讀。_

## <a name="overview"></a>總覽

Android 磨損會在各種不同的裝置上執行，包括第一代的 Motorola 360、LG 的 G 監看式和 Samsung 齒輪直播。 第二代（包括索尼的 SmartWatch 3）也已發行，還有其他功能，包括內建 GPS 和離線音樂播放。 針對 Android 磨損2.0，Google 已將兩個新監看式的 LG 組成： LG Watch 運動和 LG Watch 樣式。

![Android 磨損2.0 裝置](intro-to-wear-images/hero-image.png "範例 Android 磨損2.0 裝置")

Xamarin. Android 5.0 和更新版本透過我們的 Android 4.4 W （API 20）支援和 NuGet 套件（可新增其他特定的 UI 控制項），支援 Android 磨損。 Xamarin. Android 5.0 和更新版本也包含封裝您的磨損應用程式的功能。 NuGet 套件也適用于 Android 磨損2.0，如本指南稍後所述。

## <a name="android-wear-basics"></a>Android 磨損基本概念

Android 磨損具有與 Android 掌上應用程式不同的使用者介面模式。 第一波的磨損應用程式是設計成以某種方式擴充隨附的掌上型應用程式，但從 Android 磨損2.0 開始，您可以單獨使用磨損應用程式。 當您部署磨損應用程式時，它會與隨附的掌上型應用程式一起封裝。 因為大部分的磨損應用程式相依于掌上型應用程式，所以它們需要某種方式來與掌上型應用程式通訊。 下列各節將說明這些使用案例，並概述基本的 Android 磨損功能。 

### <a name="usage-scenarios"></a>使用案例

第一版的 Android 磨損主要著重于使用增強的通知來擴充目前的掌上型應用程式，並在掌上型應用程式與穿戴式應用程式之間同步資料。 因此，這些案例的執行相當簡單。

#### <a name="wearable-notifications"></a>穿戴式通知

支援 Android 磨損最簡單的方式，就是利用掌上型和穿戴式裝置之間通知的共用性質。 藉由使用支援 v4 通知 API 和 `WearableExtender` 類別（可在[Xamarin Android 支援程式庫](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)中取得），您可以利用平臺的原生功能，例如收件匣樣式卡片或語音輸入。 [RecipeAssistant](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-recipeassistant)範例提供範例程式碼，示範如何將通知清單傳送至 Android 磨損裝置。 

#### <a name="companion-applications"></a>隨附應用程式

另一個策略是建立一個完整的應用程式，它會在穿戴式裝置上以原生方式執行，並搭配隨附的掌上型應用程式。 這種方法的一個良好範例是[測驗](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-quiz)範例應用程式，示範如何建立可在掌上型裝置上執行的測驗，並在穿戴式裝置上詢問測驗問題。 

### <a name="user-interface"></a>使用者介面

「磨損」的主要瀏覽模式是垂直排列的一系列卡片。 這些卡片每個都可以有相關聯的動作，並在相同的資料列上分層。 @No__t_0 類別提供這種功能;它遵守與 `ListView` 相同的介面卡概念。 您通常會將 `GridViewPager` 與 `FragmentGridPagerAdaptor` （或 `GridPagerAdaptor`）相關聯，讓您將每個資料列和資料行儲存格表示為 `Fragment`： 

[![磨損導覽](intro-to-wear-images/2d-picker-sml.png "磨損導覽")](intro-to-wear-images/2d-picker.png#lightbox)

磨損也會使用動作按鈕，其中包含具有小型描述文字（如上所示）的大彩色圓形。  [GridViewPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager)範例會示範如何在磨損應用程式中使用 `GridViewPager` 和 `GridPagerAdapter`。

Android 磨損2.0 會將導覽選單、動作選單和內嵌動作按鈕新增至磨損使用者介面。 如需 Android 磨損2.0 使用者介面元素的詳細資訊，請參閱 Android[剖析](https://www.google.com/design/spec-wear/system-overview/anatomy.html)主題。 

### <a name="communications"></a>通訊

Android 磨損提供兩個不同的通訊 Api，以促進穿戴式應用程式與隨附掌上型應用程式之間的通訊： 

此 API &ndash; 的**資料 api**類似于穿戴式裝置與掌上型裝置之間的同步處理資料存放區。 Android 會負責在穿戴式和掌上傳播變更，這是最佳做法。 當穿戴式超出範圍時，它會在稍後將同步處理排入佇列。 此 API 的主要進入點是 `WearableClass.DataApi`。 如需此 API 的詳細資訊，請參閱 Android[同步處理資料項目](https://developer.android.com/training/wearables/data-layer/data-items.html)主題。 

**訊息 api** &ndash; 此 api 可讓您使用較低層級的通訊路徑：小型承載會以單向方式傳送，而不會在掌上型和穿戴式應用程式之間進行同步處理。
此 API 的主要進入點是 `WearableClass.MessageApi`。
如需此 API 的詳細資訊，請參閱 Android 傳送[和接收訊息](https://developer.android.com/training/wearables/data-layer/messages.html)主題。

您可以選擇註冊回呼，以透過每個 API 接聽程式介面來接收這些訊息，或者在您的應用程式中，執行衍生自 `WearableListenerService` 的服務。
Android 磨損會自動將此服務具現化。
[FindMyPhone](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-findmyphonesample)範例說明如何執行 `WearableListenerService`。

### <a name="deployment"></a>部署

每個穿戴式應用程式都會部署自己的 APK 檔案，並內嵌在主要應用程式 APK 內。 此封裝會在 Xamarin. Android 5.0 和更新版本中自動處理，但必須針對版本5.0 之前的 Xamarin 版本手動執行。 
[使用封裝](~/android/wear/deploy-test/packaging.md)會更詳細地說明部署。 

## <a name="going-further"></a>進一步瞭解 

熟悉 Android 磨損的最佳方式，就是建立並測試您的第一個應用程式。 下列清單提供建議的讀取順序，協助您快速入門：

1. [安裝 & 安裝](~/android/wear/get-started/installation.md)會提供詳細的指示，說明如何安裝和設定開發環境以建立 Xamarin. Android 磨損應用程式。 

2. 在您安裝必要的套件並設定模擬器或裝置之後，請參閱[Hello，磨損](~/android/wear/get-started/hello-wear.md)的逐步指示，說明如何建立小型 Android 磨損專案來處理按鈕的點擊，並在磨損上顯示 click 計數器主設備. 

3. [部署 & 測試](~/android/wear/deploy-test/index.md)會提供有關設定及部署至模擬器和裝置的詳細資訊，包括如何透過藍牙將應用程式部署至磨損裝置的指示。

4. 使用[螢幕大小](~/android/wear/screen-sizes.md)說明如何針對磨損裝置上的各種可用螢幕大小，預覽和優化使用者介面。 

5. [使用封裝](~/android/wear/deploy-test/packaging.md)描述手動封裝要在 Google Play 上散發之磨損應用程式的步驟。

建立第一個磨損應用程式之後，您可能會想要嘗試建立自訂監看面以進行 Android 磨損。 
[建立監看式臉部](~/android/wear/platform/creating-a-watchface.md)提供逐步指示和程式碼範例，可用於開發已去除的數位監看式臉部服務，再加上更多程式碼，將其增強為類比樣式的監看面與額外的功能。 

## <a name="android-wear-20"></a>Android 磨損2。0

Android 磨損2.0 引進了各種新特性和功能，例如*複雜性*、彎曲版面配置、導覽和動作抽屜，以及展開的通知。 此外，磨損2.0 也可以讓您建立獨立應用程式，以獨立于掌上型應用程式的方式工作。 新的*手腕手勢*功能可與您的應用程式進行一次互動。 下列各節將強調這些功能，並提供連結，協助您開始在應用程式中使用它們。

### <a name="install-wear-20-packages"></a>安裝磨損2.0 套件

若要使用 Xamarin 建立磨損2.0 應用程式，您必須在專案中新增 Xamarin. a **v 2.0**套件（按一下 [**流覽]** 索引標籤）：

[![Xamarin。](intro-to-wear-images/wear-nuget-2.0-sml.png "安裝 Xamarin v2.0 NuGet")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

此 NuGet 套件包含 Android 支援穿戴式和磨損相容性程式庫的系結。

除了**GooglePlayServices. 穿戴式**NuGet**以外，我們**也建議您安裝： 

[![GooglePlayServices. 穿戴式](intro-to-wear-images/gpsw-nuget-sml.png "安裝 GooglePlayServices. 穿戴式 NuGet")](intro-to-wear-images/gpsw-nuget.png#lightbox)

### <a name="key-features-of-wear-20"></a>磨損2.0 的主要功能

Android 磨損2.0 是 Android 磨損的最大更新，因為它最初是在2014中啟動。 下列各節強調 Android 磨損2.0 的主要功能，並提供連結以協助您開始在應用程式中使用這些新功能。 

#### <a name="complications"></a>複雜功能

*複雜*的是，您可以一目了然的小型監看式臉部 widget，而不需要刷監看表面。 複雜之處與桌面樣式的儀表板小工具類似;它們會顯示天氣、電池壽命、行事曆事件和健身應用程式統計資料等資訊： 

![複雜範例](intro-to-wear-images/complications.png "複雜範例")

如需複雜的詳細資訊，請參閱 Android[監看臉部複雜](https://developer.android.com/wear/preview/features/complications.html)的主題。 

#### <a name="navigation-and-action-drawers"></a>導覽和動作抽屜 

有兩個新的抽屜包含在磨損2.0 中。 出現在畫面頂端的*導覽*選單，可讓使用者在應用程式視圖之間流覽（如下方所示）。 出現在畫面底部的 [*動作*] 選單（如右側所示），可讓使用者從動作清單中進行選擇。 

![導覽和動作抽屜](intro-to-wear-images/drawers.png "導覽和動作抽屜")

如需這兩個新互動式抽屜的詳細資訊，請參閱 Android[磨損導覽和動作](https://developer.android.com/wear/preview/features/ui-nav-actions.html)主題。 

#### <a name="curved-layouts"></a>曲線版面配置 

磨損2.0 引進了新的功能，可在圓形磨損裝置上顯示彎曲的版面配置。 具體來說，新的 `WearableRecyclerView` 類別已經過優化，可在圓形顯示上顯示垂直專案清單： 

![曲線版面配置範例](intro-to-wear-images/curved-layout.png "曲線版面配置範例")

`WearableRecyclerView` 擴充 `RecyclerView` 類別，以支援曲線版面配置和迴圈滾動手勢。 如需詳細資訊，請參閱 Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) API 檔。 

#### <a name="standalone-apps"></a>獨立應用程式 

Android 磨損2.0 應用程式可以獨立于掌上型應用程式使用。 這表示，即使隨附的掌上型裝置已關閉或遠離穿戴式裝置，智慧監看仍然可以繼續提供完整的功能。 如需這項功能的詳細資訊，請參閱 Android[獨立應用程式](https://developer.android.com/wear/preview/features/standalone-apps.html)主題。

#### <a name="wrist-gestures"></a>手腕手勢 

手腕手勢可以讓使用者與您的應用程式互動，而不需要使用觸控式螢幕 &ndash; 使用者可以使用單一手指來回應應用程式。 支援兩種手腕手勢： 

- 筆鋒手腕
- 筆鋒中的手腕

如需詳細資訊，請參閱 Android[手腕手勢](https://developer.android.com/wear/preview/features/gestures.html)主題。 

還有更多的磨損2.0 功能，例如內嵌動作、智慧型回復、遠端輸入、展開的通知，以及通知的新橋接模式。 如需新的磨損2.0 功能的詳細資訊，請參閱 Android [API 總覽](https://developer.android.com/wear/preview/api-overview.html)。 

## <a name="devices"></a>裝置

以下是一些可執行 Android 磨損的裝置範例：

- [Motorola 360](https://moto360.motorola.com/)
- [LG G 監看](http://www.lg.com/us/smart-watches/lg-W100-g-watch)
- [LG G Watch R](http://www.lg.com/us/smartwatch/g-watch-r)
- [Samsung 齒輪直播](http://www.samsung.com/global/microsite/gear/gearlive_design.html)
- [索尼 SmartWatch 3](http://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
- [ASUS ZenWatch](http://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)

## <a name="further-reading"></a>進一步閱讀

查看 Google 的 Android 磨損檔：

- [關於 Android 磨損](http://www.android.com/wear/)
- [Android 磨損應用程式設計](https://developer.android.com/design/wear/index.html)
- [穿戴式程式庫](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [Android 磨損2。0](https://developer.android.com/wear/preview/index.html)

## <a name="summary"></a>總結

本簡介提供 Android 磨損的總覽。 其中概述了 Android 磨損的基本功能，並包含 Android 磨損2.0 中引進的功能總覽。 它提供基本閱讀的連結，可協助開發人員開始使用 Xamarin。 Android 磨損開發，並列出目前市場上一些 Android 磨損裝置的範例。

## <a name="related-links"></a>相關連結

- [安裝和設定](~/android/wear/get-started/installation.md)
- [使用者入門](~/android/wear/get-started/index.md)
