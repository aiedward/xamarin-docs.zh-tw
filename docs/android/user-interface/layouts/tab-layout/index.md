---
title: 索引標籤式的配置
description: 在 Android 中的索引標籤式配置的概觀
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/08/2017
ms.openlocfilehash: 53ed5f91583d43839e96388194aea8c0d6ac5315
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
ms.locfileid: "34149256"
---
# <a name="tabbed-layouts"></a>索引標籤式的配置


## <a name="overview"></a>總覽

由於其簡易性和可用性會在行動裝置應用程式中的熱門使用者介面樣式索引標籤。 提供一致且簡單的方式，來瀏覽應用程式中的各種螢幕之間。 Android 有數個應用程式開發介面的索引標籤式介面： 

-   **項**&ndash;這是一組新 API 的 Android 3.0 （API 層級 11） 中以提供一致的目標導入的瀏覽和檢視切換介面。 它有回移植到 Android 2.2 (API level 8) 與[Android 支援程式庫 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)。 

-   **PagerTabStrip** &ndash;表示的目前下, 一步] 和 [上一個頁面`ViewPager`。 `ViewPager` 使用僅透過[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。
     如需有關`PagerTabStrip`，請參閱[ViewPager](~/android/user-interface/controls/view-pager/index.md)。

-   **工具列** &ndash; `Toolbar`是一種較新且更具彈性的動作列元件，以取代`ActionBar`。 `Toolbar` 可以在 Android 5.0 棒棒糖符號或更新版本，它也是適用於較舊版本的 Android 透過[Android 支援程式庫 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet 封裝。 
    `Toolbar` 目前是在 Android 應用程式中使用的建議的動作列元件。
    如需詳細資訊，請參閱[工具列](~/android/user-interface/controls/tool-bar/index.md)。 



## <a name="related-links"></a>相關連結

- [索引標籤的材料設計-](https://material.io/guidelines/components/tabs.html)- [項](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Android 支援程式庫 v7 AppCompat NuGet 封裝](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [v7 appcompat 程式庫](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
