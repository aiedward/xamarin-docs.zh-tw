---
title: Android Wear 簡介
description: 與 Google 的 Android Wear 導入，您已不再限制為只手機和平板電腦開發優質的 Android 應用程式時。 適用於 Android Wear 的 Xamarin.Android 的支援可讓您執行C#手腕上的程式碼 ！ 本簡介提供 Android Wear 的基本概觀，說明其重要功能，並提供用於 Android Wear 的 2.0 功能的概觀。 它會列出一些較受歡迎的 Android Wear 裝置，並提供基本的 Google Android Wear 文件，如需進一步閱讀的連結。
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a35cb82f4f6d20e91f45a782c73d3ef811947c3a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117781"
---
# <a name="introduction-to-android-wear"></a>Android Wear 簡介

_與 Google 的 Android Wear 導入，您已不再限制為只手機和平板電腦開發優質的 Android 應用程式時。適用於 Android Wear 的 Xamarin.Android 的支援可讓您執行C#手腕上的程式碼 ！本簡介提供 Android Wear 的基本概觀，說明其重要功能，並提供用於 Android Wear 的 2.0 功能的概觀。它會列出一些較受歡迎的 Android Wear 裝置，並提供基本的 Google Android Wear 文件，如需進一步閱讀的連結。_


## <a name="overview"></a>總覽

Android Wear 執行各種不同的裝置，包括第一代的 Motorola 360、 LG 的 G 監看式及 Samsung 齒輪 Live。 第二個層代，包括 Sony 的 SmartWatch 3，也已經發行與其他功能，包括內建的 GPS 和離線的音樂的播放。 Android Wear 2.0，Google 已組隊 LG 使用的兩個新的監看式： LG 監看式運動和 LG 監看式樣式。

![Android Wear 2.0 裝置](intro-to-wear-images/hero-image.png "範例 Android Wear 2.0 裝置")

Xamarin.Android 5.0 及更新版本支援 Android Wear 透過我們的 Android 4.4W (API 20) 支援，並新增其他的 NuGet 封裝 Wear 特定 UI 控制項。 Xamarin.Android 5.0 及更新版本也包含封裝 Wear 應用程式的功能。 NuGet 套件也可供 Android Wear 2.0 稍後在本指南中所述。


## <a name="android-wear-basics"></a>Android Wear 的基本概念

Android Wear 有不同於掌上型的 Android 應用程式的使用者介面典範。 第一波的 Wear 應用程式都設計成擴充隨附中部分的方式，但 Android Wear 2.0 開頭的手持式應用程式、 Wear 應用程式可以單獨使用。 當您部署穿戴式應用程式時，它是與隨附手持式應用程式一起封裝。 因為大部分穿戴式應用程式相依於掌上型隨附應用程式，他們需要某種方式來與掌上型應用程式進行通訊。 下列各節描述這些使用案例，並簡述的 Android Wear 的重要功能。 



### <a name="usage-scenarios"></a>使用案例

Android Wear 的第一個版本的主要著重於擴充目前的手持式應用程式，使用增強的通知，並將掌上型應用程式和到穿戴應用程式之間的資料同步處理。 因此，這些案例會相當容易實作。


#### <a name="wearable-notifications"></a>到穿戴通知

支援 Android Wear 的最簡單方式是本質的利用共用的掌上型和到穿戴裝置之間的通知。 使用支援 v4 通知 API 並`WearableExtender`類別 (用於[Xamarin Android 支援程式庫](https://www.nuget.org/packages/Xamarin.Android.Support.v4/))，您可以利用平台，例如收件匣樣式卡的原生功能或語音輸入。 [RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/)範例提供示範如何將 Android Wear 裝置傳送的通知清單的範例程式碼。 



#### <a name="companion-applications"></a>附屬應用程式

另一個策略是建立完整的應用程式到穿戴裝置上執行的原生和配對隨附手持式應用程式。 是這種方法的一個好例子[測驗](https://developer.xamarin.com/samples/monodroid/wear/Quiz/)範例應用程式，示範如何建立測驗在掌上型裝置上執行，且會問到穿戴裝置上的測驗問題。 



### <a name="user-interface"></a>使用者介面

Wear 的主要導覽模式是一系列的垂直排列的卡片。 每個這些卡片可以有關聯相同的資料列層級時的動作。 `GridViewPager`類別會提供這項功能; 它遵循相同的配接器概念`ListView`。 您通常將產生關聯`GridViewPager`具有`FragmentGridPagerAdaptor`(或`GridPagerAdaptor`)，可讓您代表每個資料列和資料行的儲存格合併為`Fragment`: 

[![穿戴式導覽](intro-to-wear-images/2d-picker-sml.png "穿戴式導覽")](intro-to-wear-images/2d-picker.png#lightbox)

Wear 也會包含大量的動作按鈕使用彩色圓形下方的小型的描述文字 （如上面所示）。  [GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/)範例示範如何使用`GridViewPager`和`GridPagerAdapter`穿戴式應用程式中。

Android Wear 2.0 會將瀏覽 drawer、 動作 下拉式清單，以及內嵌動作按鈕加入至 Wear 使用者介面。 如需 Android Wear 2.0 使用者介面項目相關資訊，請參閱 Android[剖析](https://www.google.com/design/spec-wear/system-overview/anatomy.html)主題。 



### <a name="communications"></a>通訊

Android Wear 提供兩個不同的通訊 Api，以利於進行到穿戴應用程式和附屬手持式應用程式之間的通訊： 

**資料 API** &ndash;此 API 即將到穿戴裝置與掌上型裝置之間的同步處理的資料存放區類似。 Android 會負責傳播到穿戴與掌上型之間的變更，最好是將執行這項操作時。 超出範圍的可穿戴式時，它排入佇列稍後時間同步的處理。 此 API 的主要進入點是`WearableClass.DataApi`。 如需有關此 API 的詳細資訊，請參閱 Android[同步處理資料項目](https://developer.android.com/training/wearables/data-layer/data-items.html)主題。 

**訊息 API** &ndash;此 API 可讓您使用較低的層級的通訊路徑： 小裝載傳送單向不需要的掌上型和到穿戴應用程式之間的同步處理。
此 API 的主要進入點是`WearableClass.MessageApi`。
如需有關此 API 的詳細資訊，請參閱 Android[傳送和接收訊息](https://developer.android.com/training/wearables/data-layer/messages.html)主題。

若要註冊回呼以接收這些訊息，透過每個 API 接聽程式介面，或者，在衍生自應用程式中實作的服務，您可以選擇`WearableListenerService`。
這項服務將會自動具現化 Android Wear。
[FindMyPhone](https://developer.xamarin.com/samples/monodroid/wear/FindMyPhoneSample/)範例說明如何實作`WearableListenerService`。



### <a name="deployment"></a>部署

每個到穿戴應用程式會使用自己內嵌在主應用程式的 APK 內的 APK 檔案部署。 此封裝將會自動處理在 Xamarin.Android 5.0 和更新版本，但必須手動執行 Xamarin.Android 的版本早於 5.0 版。 
[使用封裝](~/android/wear/deploy-test/packaging.md)說明詳細的部署。 



## <a name="going-further"></a>繼續進行 

熟悉 Android Wear 的最佳方式是建置及測試您的第一個應用程式。 下列清單提供可協助您快速了解建議您先閱讀順序：

1.  [設定與安裝](~/android/wear/get-started/installation.md)提供安裝和設定您的開發環境，用於建置 Xamarin.Android Wear 應用程式的詳細的指示。 

2.  您已安裝必要的套件，並設定模擬器或裝置之後，請參閱[大家好，Wear](~/android/wear/get-started/hello-wear.md)按 [的逐步指示說明如何建立小型的 Android Wear 專案該控制代碼] 按鈕，顯示按一下在 Wear 裝置上的計數器。 

3.  [部署和測試](~/android/wear/deploy-test/index.md)提供更詳細的資訊設定，並部署至模擬器和裝置，包括如何將您的應用程式部署至在 Wear 裝置透過藍牙的指示。

4.  [使用螢幕大小](~/android/wear/screen-sizes.md)說明如何預覽並最佳化您的使用者介面，如需各種可用的螢幕大小在 Wear 裝置上。 

5.  [使用封裝](~/android/wear/deploy-test/packaging.md)描述手動封裝 Wear 應用程式的 Google Play 上發佈的步驟。

您已建立您的第一個穿戴式應用程式之後，您可能要嘗試建置適用於 Android Wear 的自訂錶面。 
[建立錶面](~/android/wear/platform/creating-a-watchface.md)提供數位監看式臉部服務，後面接著更多的類比樣式 watch 錶面，額外的功能來增強它的程式碼開發已移除的逐步指示與範例程式碼。 



## <a name="android-wear-20"></a>Android Wear 2.0

Android Wear 2.0 導入了各種新功能和功能，例如*複雜性*，弧形版面配置、 導覽和動作抽屜，並展開的通知。 此外，Wear 2.0 可讓您建置與掌上型應用程式分開運作的獨立應用程式。 新*手腕筆勢*功能可讓您的應用程式的只用互動。 下列各節會反白顯示這些功能，並提供連結可幫助您開始使用您的應用程式中使用它們。



### <a name="install-wear-20-packages"></a>安裝 Wear 2.0 套件

若要建置使用 Xamarin.Android Wear 2.0 應用程式，您必須新增**Xamarin.Android.Wear v2.0**封裝至您的專案 (按一下**瀏覽 索引標籤**):

[![Xamarin.Android.Wear v2.0](intro-to-wear-images/wear-nuget-2.0-sml.png "安裝 Xamarin.Android.Wear v2.0 NuGet")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

這個 NuGet 封裝包含 Android 支援到穿戴和穿戴式相容性的程式庫的繫結。

除了**Xamarin.Android.Wear**，我們建議您安裝**Xamarin.GooglePlayServices.Wearable** NuGet: 

[![Xamarin.GooglePlayServices.Wearable](intro-to-wear-images/gpsw-nuget-sml.png "安裝 Xamarin.GooglePlayServices.Wearable NuGet")](intro-to-wear-images/gpsw-nuget.png#lightbox)


### <a name="key-features-of-wear-20"></a>Wear 2.0 的主要功能

Android Wear 2.0 是 Android Wear 空前的更新，因為其初始啟動在 2014 年。 下列各節反白顯示的 Android Wear 2.0 時，重要的功能，並會提供連結將協助您開始在您的應用程式中使用這些新功能。 


#### <a name="complications"></a>複雜功能

*複雜功能*是小型的監看式，您可以查看一目瞭然無須撥動 watch 錶面朝 widget。 複雜功能是類似於桌面樣式儀表板 widget;它們會顯示資訊，例如天氣、 電池壽命、 行事曆事件和符合應用程式統計資料： 

![複雜功能範例](intro-to-wear-images/complications.png "複雜範例")

如需詳細資訊的複雜性，請參閱 Android[監看式臉部複雜性](https://developer.android.com/wear/preview/features/complications.html)主題。 



#### <a name="navigation-and-action-drawers"></a>瀏覽和動作抽屜 

在 Wear 2.0 包含兩個新的抽屜。 *瀏覽 drawer*，出現在畫面頂端的可讓使用者 （如左下方所示），應用程式檢視之間巡覽。 *動作的隱藏式選單*，出現在螢幕底部 （如右邊所示），可讓使用者選擇的動作清單。 

![瀏覽和動作抽屜](intro-to-wear-images/drawers.png "瀏覽和動作抽屜")

如需有關這些兩個新的互動式抽屜的詳細資訊，請參閱 Android[穿戴式導覽和動作](https://developer.android.com/wear/preview/features/ui-nav-actions.html)主題。 



#### <a name="curved-layouts"></a>曲線的版面配置 

Wear 2.0 導入 round Wear 裝置上顯示曲線的版面配置的新功能。 具體來說，新`WearableRecyclerView`類別最適合用於在捨入的顯示器上顯示垂直的項目清單： 

![曲線的版面配置範例](intro-to-wear-images/curved-layout.png "弧形的版面配置範例")

`WearableRecyclerView` 擴充`RecyclerView`類別，以支援曲線的版面配置和循環的捲動軌跡。 如需詳細資訊，請參閱 Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) API 文件。 



#### <a name="standalone-apps"></a>獨立應用程式 

Android Wear 2.0 應用程式能夠與掌上型應用程式無關。 這表示，比方說，智慧型手錶可以繼續提供完整的功能，即使隨附掌上型裝置已關閉或遠到穿戴裝置。 如需這項功能的詳細資訊，請參閱 Android[獨立應用程式](https://developer.android.com/wear/preview/features/standalone-apps.html)主題。



#### <a name="wrist-gestures"></a>手腕筆勢 

手腕筆勢可讓使用者與您的應用程式互動，而不需使用觸控式螢幕&ndash;單一手的應用程式可以回應使用者。 支援兩個手腕筆勢： 

-   筆觸手腕出
-   中的筆觸手腕

如需詳細資訊，請參閱 Android[手腕筆勢](https://developer.android.com/wear/preview/features/gestures.html)主題。 


有許多的多個 Wear 2.0 功能，例如內嵌動作、 智慧型回覆、 遠端輸入、 展開的通知和通知的新橋接模式。 如需有關新 Wear 2.0 功能的詳細資訊，請參閱 Android [API 概觀](https://developer.android.com/wear/preview/api-overview.html)。 



## <a name="devices"></a>裝置

以下是可以執行 Android 穿戴式裝置的一些範例：

* [Motorola 360](https://moto360.motorola.com/)
* [LG G 監看式](http://www.lg.com/us/smart-watches/lg-W100-g-watch)
* [LG G 監看式 R](http://www.lg.com/us/smartwatch/g-watch-r)
* [Samsung 齒輪即時](http://www.samsung.com/global/microsite/gear/gearlive_design.html)
* [Sony SmartWatch 3](http://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
* [ASUS ZenWatch](http://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)



## <a name="further-reading"></a>進一步閱讀

請參閱 Google 的 Android Wear 的文件：

* [關於 Android Wear](http://www.android.com/wear/)
* [Android Wear 應用程式設計](https://developer.android.com/design/wear/index.html)
* [android.support.wearable 程式庫 ](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
* [Android Wear 2.0](https://developer.android.com/wear/preview/index.html)



## <a name="summary"></a>總結

本簡介提供 Android Wear 的概觀。 它概述 Android Wear 的基本功能，以及包含 Android Wear 2.0 中引進的功能的概觀。 它提供可協助開發人員開始使用 Xamarin.Android Wear 開發的基本讀取的連結，它會列出目前市場上 Android 穿戴式裝置的一些範例。


## <a name="related-links"></a>相關連結

- [安裝和設定](~/android/wear/get-started/installation.md)
- [快速入門](~/android/wear/get-started/index.md)
