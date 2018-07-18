---
title: Android 損耗簡介
description: Google Android 穿問世，您不再僅限於手機和平板電腦來開發很棒的 Android 應用程式時。 Xamarin.Android 的支援 Android 有可讓您能夠在手腕上執行 C# 程式碼 ！ 本簡介提供 Android 戴上的基本概觀、 說明其重要功能，並提供 Android 穿 2.0 中提供的功能的概觀。 它會列出一些常見的 Android 穿裝置，並提供基本的 Google Android 穿文件，如需進一步閱讀的連結。
ms.prod: xamarin
ms.assetid: EAEF99F0-8FBE-47E4-8644-E7244CFAF464
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 0ab166bb71c23d456cb70d35a2794717110642fd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30772090"
---
# <a name="introduction-to-android-wear"></a>Android 損耗簡介

_Google Android 穿問世，您不再僅限於手機和平板電腦來開發很棒的 Android 應用程式時。Xamarin.Android 的支援 Android 有可讓您能夠在手腕上執行 C# 程式碼 ！本簡介提供 Android 戴上的基本概觀、 說明其重要功能，並提供 Android 穿 2.0 中提供的功能的概觀。它會列出一些常見的 Android 穿裝置，並提供基本的 Google Android 穿文件，如需進一步閱讀的連結。_


## <a name="overview"></a>總覽

Android 損耗執行各種不同的裝置，包括第一代 Motorola 360、 LG 的 G 監看式及 Samsung 齒輪即時。 第二個層代，包括 Sony 的 SmartWatch 3，也已釋放額外的功能，包括內建的 GPS 和離線音樂的播放。 針對 Android 穿 2.0 Google 已組合使用 LG 的兩個新的監看： LG 監看式運動和 LG 監看式樣式。

![Android 穿 2.0 裝置](intro-to-wear-images/hero-image.png "範例 Android 穿 2.0 裝置")

Xamarin.Android 5.0 及更新版本支援 Android 戴上透過我們的 Android 4.4W (應用程式開發介面 20) 支援，並新增其他的 NuGet 封裝損耗特定 UI 控制項。 Xamarin.Android 5.0 及更新版本也包含封裝損耗應用程式的功能。 NuGet 封裝也可供 Android 穿 2.0 稍後在本指南所述。


## <a name="android-wear-basics"></a>Android 損耗基本概念

Android 損耗有不同的 Android 掌上型應用程式的使用者介面典範。 第一波的損耗應用程式都是設計來擴充隨附掌上型應用程式中某些方法，但 Android 損耗 2.0 開頭、 損耗應用程式可以使用的獨立。 當您部署損耗應用程式時，它被封裝與掌上型附屬應用程式。 由於大部分穿掌上型附屬應用程式取決於應用程式，它們需要某種方式來與掌上型應用程式溝通。 下列各節描述這些使用案例，並概述必要的 Android 戴上功能。 



### <a name="usage-scenarios"></a>使用案例

Android 戴上的第一個版本被著重於擴充目前的掌上型應用程式，以增強通知及手持式應用程式和穿戴應用程式之間同步處理資料。 因此，這些案例會相當容易實作。


#### <a name="wearable-notifications"></a>穿戴通知

支援 Android 戴上最簡單的方式是本質的利用共用通知掌上型與穿戴裝置之間。 使用支援 v4 通知應用程式開發介面和`WearableExtender`類別 (用於[Xamarin Android 支援程式庫](https://www.nuget.org/packages/Xamarin.Android.Support.v4/))，您可以點選不同的平台，例如收件匣樣式卡的原生功能或語音輸入。 [RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/)範例提供示範如何將穿 Android 裝置傳送的通知清單的範例程式碼。 



#### <a name="companion-applications"></a>附屬應用程式

另一個策略是建立的完整應用程式，穿戴的裝置上執行的原生組附屬掌上型應用程式。 這種方法的一個好範例就是[測驗](https://developer.xamarin.com/samples/monodroid/wear/Quiz/)範例應用程式，示範如何建立一項測驗掌上型裝置上執行，並詢問測驗問題穿戴裝置上。 



### <a name="user-interface"></a>使用者介面

損耗的主瀏覽模式是一系列垂直排列的卡片。 每個這些卡片可以有關聯的同一個資料列層級時的動作。 `GridViewPager`類別會提供這項功能，而且符合配接器概念與相同`ListView`。 通常關聯`GridViewPager`與`FragmentGridPagerAdaptor`(或`GridPagerAdaptor`)，可讓您代表做為每個資料列和資料行儲存格`Fragment`: 

[![戴上瀏覽](intro-to-wear-images/2d-picker-sml.png "戴上瀏覽")](intro-to-wear-images/2d-picker.png#lightbox)

也有可讓 big 所組成的動作按鈕使用不同色彩圓形其下方的小型的描述文字 （如上面所示）。  [GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/)範例示範如何使用`GridViewPager`和`GridPagerAdapter`損耗應用程式中。

Android 穿 2.0 會將瀏覽抽屜、 動作抽屜和內嵌動作按鈕加入至損耗使用者介面。 如需 Android 穿 2.0 使用者介面項目相關資訊，請參閱 Android[剖析](https://www.google.com/design/spec-wear/system-overview/anatomy.html)主題。 



### <a name="communications"></a>通訊

Android 損耗提供兩個不同的通訊以便穿戴應用程式和附屬掌上型應用程式之間通訊的應用程式開發介面： 

**資料 API** &ndash;此 API 是類似於穿戴裝置與掌上型裝置之間的同步處理的資料存放區。 Android 會負責之間傳播變更穿戴和掌上型時最好是將執行這項操作。 當可穿戴式超出範圍時，它排入佇列的稍後時間同步處理。 此 API 的主要進入點是`WearableClass.DataApi`。 如需有關此 API 的詳細資訊，請參閱 Android[同步資料項目](https://developer.android.com/training/wearables/data-layer/data-items.html)主題。 

**訊息 API** &ndash;此 API 可讓您使用較低的層級的通訊路徑： 小裝載會傳送單向不進行同步處理之間的掌上型和穿戴應用程式。
此 API 的主要進入點是`WearableClass.MessageApi`。
如需有關此 API 的詳細資訊，請參閱 Android[傳送和接收訊息](https://developer.android.com/training/wearables/data-layer/messages.html)主題。

若要註冊回呼來接收這些訊息透過每一個應用程式開發介面的接聽程式介面，或者，衍生自您應用程式中實作的服務，您可以選擇`WearableListenerService`。
此服務將會自動具現化 Android 戴上。
[FindMyPhone](https://developer.xamarin.com/samples/monodroid/wear/FindMyPhoneSample/)範例說明如何實作`WearableListenerService`。



### <a name="deployment"></a>部署

每個穿戴應用程式會部署與它自己的 APK 檔內嵌於主應用程式 APK。 此封裝 Xamarin.Android 5.0 版和更新版本，會自動處理，但必須手動執行版本 Xamarin.Android 之前 5.0 版。 
[使用封裝](~/android/wear/deploy-test/packaging.md)說明詳細的部署。 



## <a name="going-further"></a>繼續進行 

熟悉 Android 戴上的最佳方式是建置和測試您的第一個應用程式。 下列清單提供可協助您快速了解建議您先閱讀順序：

1.  [安裝程式 & 安裝](~/android/wear/get-started/installation.md)提供安裝和設定您的開發環境建置 Xamarin.Android 損耗應用程式的詳細的指示。 

2.  您已安裝必要的封裝，並設定在模擬器或裝置之後，請參閱[Hello，穿](~/android/wear/get-started/hello-wear.md)如需逐步指示，說明如何建立小型的 Android 穿專案該控制代碼 按鈕按一下並顯示按一下損耗裝置上的計數器。 

3.  [部署和測試](~/android/wear/deploy-test/index.md)提供更詳細的資訊設定及部署至模擬器和裝置，包括如何將您的應用程式部署至損耗藍芽裝置的指示。

4.  [使用的螢幕大小](~/android/wear/screen-sizes.md)解釋如何預覽並最佳化您的使用者介面，損耗裝置上的各種可用的螢幕大小。 

5.  [使用封裝](~/android/wear/deploy-test/packaging.md)描述手動封裝損耗 Google Play 上的發佈的應用程式的步驟。

建立您的第一個損耗應用程式之後，您可能要嘗試建置自訂 watch 錶面 Android 戴上。 
[建立 Watch 錶面](~/android/wear/platform/creating-a-watchface.md)提供關閉數位監看式朝服務，後面接著增強到額外的功能與類比樣式 watch 錶面的更多程式碼開發已移除的逐步指示和範例程式碼。 



## <a name="android-wear-20"></a>Android Wear 2.0

Android 穿 2.0 導入了各種不同的新特色與功能，例如*複雜性*，彎曲的版面配置、 導覽和動作抽屜，並展開的通知。 此外，有 2.0 可讓您建置工作與掌上型應用程式分開的獨立應用程式。 新*腕帶筆勢*功能可讓您的應用程式的只用互動。 下列章節會反白顯示這些功能，並提供連結可幫助您開始在您的應用程式中使用它們。



### <a name="install-wear-20-packages"></a>安裝有 2.0 封裝

若要建置的 Xamarin.Android 穿 2.0 應用程式，您必須新增**Xamarin.Android.Wear v2.0**封裝至您的專案 (按一下**瀏覽索引標籤**):

[![Xamarin.Android.Wear v2.0](intro-to-wear-images/wear-nuget-2.0-sml.png "安裝 Xamarin.Android.Wear v2.0 NuGet")](intro-to-wear-images/wear-nuget-2.0.png#lightbox)

此 NuGet 封裝包含 Android 支援穿戴和有相容的程式庫的繫結。

除了**Xamarin.Android.Wear**，我們建議您安裝**Xamarin.GooglePlayServices.Wearable** NuGet: 

[![Xamarin.GooglePlayServices.Wearable](intro-to-wear-images/gpsw-nuget-sml.png "安裝 Xamarin.GooglePlayServices.Wearable NuGet")](intro-to-wear-images/gpsw-nuget.png#lightbox)


### <a name="key-features-of-wear-20"></a>主要功能有 2.0

Android 穿 2.0 是最大更新 Android 穿自初始 2014年中。 下列各節反白顯示的 Android 穿 2.0 時，重要的功能，並提供連結，可協助您開始在您的應用程式中使用這些新功能。 


#### <a name="complications"></a>複雜性

*複雜性*是小型的監看式，您可以快速查看不必撥動 watch 錶面朝 widget。 複雜功能是類似於桌面樣式儀表板 widget。它們會顯示資訊，例如天氣、 電池壽命、 行事曆事件和適用性應用程式統計資料： 

![複雜範例](intro-to-wear-images/complications.png "複雜範例")

如需詳細資訊的複雜性，請參閱 Android[監看式朝複雜性](https://developer.android.com/wear/preview/features/complications.html)主題。 



#### <a name="navigation-and-action-drawers"></a>瀏覽和動作抽屜 

有 2.0 包含兩個新的抽屜。 *瀏覽抽屜*，出現在螢幕頂端，可讓使用者 （如所示，左下方），應用程式檢視之間巡覽。 *動作抽屜*，出現在螢幕底部 （如下所示右邊），可讓使用者選擇的動作清單。 

![瀏覽和動作抽屜](intro-to-wear-images/drawers.png "導覽和動作抽屜")

如需這些兩個新的互動式抽屜的詳細資訊，請參閱 Android[戴上瀏覽和動作](https://developer.android.com/wear/preview/features/ui-nav-actions.html)主題。 



#### <a name="curved-layouts"></a>有個小曲形版面配置 

損耗 2.0 導入了新功能有個小曲形版面配置顯示圓形損耗裝置上。 具體來說，新`WearableRecyclerView`類別最適合用於顯示圓形的顯示畫面上垂直的項目清單： 

![曲線版面配置範例](intro-to-wear-images/curved-layout.png "彎曲的版面配置範例")

`WearableRecyclerView` 擴充`RecyclerView`類別，以支援曲線的配置和循環捲動的筆勢。 如需詳細資訊，請參閱 Android [WearableRecyclerView](https://developer.android.com/reference/android/support/wearable/view/WearableRecyclerView.html) API 文件。 



#### <a name="standalone-apps"></a>獨立應用程式 

Android 穿 2.0 應用程式可與掌上型應用程式無關。 這表示，例如，智慧監看式可以繼續提供完整功能，即使附屬掌上型裝置已關閉或遠穿戴裝置。 如需有關這項功能的詳細資訊，請參閱 Android[獨立應用程式](https://developer.android.com/wear/preview/features/standalone-apps.html)主題。



#### <a name="wrist-gestures"></a>腕帶筆勢 

腕帶筆勢可讓使用者與您的應用程式互動，而不使用觸控式螢幕&ndash;使用者能回應應用程式與單一手的形狀。 支援兩個腕帶手勢： 

-   筆觸腕帶出
-   筆觸腕帶中

如需詳細資訊，請參閱 Android[腕帶筆勢](https://developer.android.com/wear/preview/features/gestures.html)主題。 


有許多的多個有 2.0 功能，例如內嵌動作、 智慧型回覆、 遠端輸入、 展開的通知和新的橋接模式，通知。 如需新穿 2.0 功能的詳細資訊，請參閱 Android [API 概觀](https://developer.android.com/wear/preview/api-overview.html)。 



## <a name="devices"></a>裝置

以下是可以執行 Android 戴上的裝置的一些範例：

* [Motorola 360](https://moto360.motorola.com/)
* [LG G 監看式](http://www.lg.com/us/smart-watches/lg-W100-g-watch)
* [LG G Watch R](http://www.lg.com/us/smartwatch/g-watch-r)
* [Samsung 齒輪即時](http://www.samsung.com/global/microsite/gear/gearlive_design.html)
* [是使用 Sony SmartWatch 3](http://www.sonymobile.com/global-en/products/smartwear/smartwatch-3-swr50/)
* [ASUS ZenWatch](http://www.asus.com/us/Phones/ASUS_ZenWatch_WI500Q/)



## <a name="further-reading"></a>進一步閱讀

請參閱 Google Android 穿文件：

* [有關 Android 損耗](http://www.android.com/wear/)
* [Android 損耗應用程式設計](https://developer.android.com/design/wear/index.html)
* [android.support.wearable 程式庫 ](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
* [Android 損耗 2.0](https://developer.android.com/wear/preview/index.html)



## <a name="summary"></a>總結

本簡介提供 Android 穿的概觀。 它所述的 Android 戴上的基本功能，且包含 Android 穿 2.0 中引進的功能的概觀。 它提供連結可協助開發人員 Xamarin.Android 損耗開發入門的基本讀取，而且它會列出目前市場上有 Android 裝置的一些範例。


## <a name="related-links"></a>相關連結

- [安裝和設定](~/android/wear/get-started/installation.md)
- [快速入門](~/android/wear/get-started/index.md)
