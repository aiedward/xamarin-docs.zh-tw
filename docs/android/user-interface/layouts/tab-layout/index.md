---
title: "索引標籤式的配置"
description: "在 Android 中的索引標籤式配置的概觀"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/23/2017
ms.openlocfilehash: f8680cde2e5536495f33d571adea9980020a72fa
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="tabbed-layouts"></a>索引標籤式的配置

<a name="Overview" />

## <a name="overview"></a>總覽

由於其簡易性和可用性會在行動裝置應用程式中的熱門使用者介面樣式索引標籤。 提供一致且簡單的方式，來瀏覽應用程式中的各種螢幕之間。 Android 有數個應用程式開發介面的索引標籤式介面： 

-   **TabHost** &ndash;這是原始應用程式開發介面，用於建立索引標籤式的使用者介面。 A`TabHost`小工具加入至版面配置，並且會做為索引標籤式檢視的容器。 這個 API，因為已被取代，不建議使用它的使用。 

-   **項**&ndash;這是一組新 API 的 Android 3.0 （API 層級 11） 中以提供一致的目標導入的瀏覽和檢視切換介面。 它有回移植到 Android 2.2 (API level 8) 與[Android 支援程式庫 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)。 

-   **PagerTabStrip** &ndash;表示的目前下, 一步] 和 [上一個頁面`ViewPager`。 `ViewPager` 使用僅透過[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。
     如需有關`PagerTabStrip`，請參閱[ViewPager](~/android/user-interface/controls/view-pager/index.md)。

-   **工具列** &ndash; `Toolbar`是一種較新且更具彈性的動作列元件，以取代`ActionBar`。 `Toolbar` 可以在 Android 5.0 棒棒糖符號或更新版本，它也是適用於較舊版本的 Android 透過[Android 支援程式庫 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet 封裝。 
    `Toolbar` 目前是在 Android 應用程式中使用的建議的動作列元件。
    如需詳細資訊，請參閱[工具列](~/android/user-interface/controls/tool-bar/index.md)。 


這些 API 以視覺化方式非常不同，而且與彼此不相容。 下列螢幕影像顯示`TabHost`和`ActionBar`-並存： 

![螢幕擷取畫面的 TabHost 左邊和右邊項](images/image01.png)

因為 Android 3.0 （API 層級 11），因為重大 UI 變更存在這些不相容的 API。 是其中一個這些 UI 變更[動作列設計模式](http://www.androidpatterns.com/uap_pattern/action-bar)，模式是要提供讓您輕鬆瀏覽和索引鍵的功能，應用程式中存取。 `ActionBar` API 為了要支援此模式。 

`ActionBar` API 較簡單，而且說是提供較佳使用者體驗。 它有回移植到 Android 2.2，Xamarin.Android 應用程式的慣用的選擇。 

`TabHost`應用程式開發介面相容跨所有版本的 Android，但需要更多心力在使用而且與目前不一致[Android 的 UI 指導方針](http://developer.android.com/design/index.html)。 開發人員不鼓勵使用此 API，並且應該協助 Xamarin.Android 應用程式較新的項。 


<a name="Introducing_ActionBarSherlock" />

## <a name="actionbarsherlock"></a>ActionBarSherlock

項 API 所 Android 2.2，開發人員想項 API 的較新的外觀及操作，但可以使用協力廠商程式庫的 backported 之前[ActionBarSherlock](http://actionbarsherlock.com)。 ActionBarSherlock 是設計來 backport Android 2.x 動作列設計模式的 Android 支援程式庫的擴充功能。 ActionBarSherlock 時執行 Android 3.0 或更高版本，會自動使用原生`ActionBar`Android 所提供的 API。 Android 的舊版本將會使用模擬外觀與風格的較新的自訂實作`ActionBar`應用程式開發介面。 [ActionBarSherlock 元件](https://www.nuget.org/packages/xamstore-XamarinActionBarSherlock/)可讓您更輕鬆地加入 ActionBarSherlock Xamarin.Android 應用程式。 



## <a name="related-links"></a>相關連結

- [TabHost 概觀](tab-host.md)
- [TabHost 逐步解說](~/android/user-interface/layouts/tab-layout/creating-a-tabbed-ui.md)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Android 支援程式庫 v7 AppCompat NuGet 封裝](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [v7 appcompat 程式庫](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
