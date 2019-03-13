---
title: 索引標籤式版面配置
description: 在 Android 中的索引標籤式版面配置的概觀
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/08/2017
---

# <a name="tabbed-layouts"></a>索引標籤式版面配置


## <a name="overview"></a>總覽

索引標籤會是行動應用程式的廣大的使用者介面模式，因為其簡易性和可用性。 它們提供的應用程式中的各個畫面之間瀏覽的一致且簡單的方法。 Android 提供數個 API 的索引標籤式介面： 

-   **ActionBar** &ndash;這是新的 API 中引進了 Android 3.0 （API 層級 11） 提供一致的目標集的一部分瀏覽和檢視切換的介面。 它已經回移植到 Android 2.2 （API 層級 8），與[Android 支援程式庫 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)。 

-   **PagerTabStrip** &ndash;表示目前下, 一步] 和 [上一個頁面的`ViewPager`。 `ViewPager` 只能透過[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。
     如需詳細資訊`PagerTabStrip`，請參閱 < [ViewPager](~/android/user-interface/controls/view-pager/index.md)。

-   **工具列** &ndash; `Toolbar`是較新且更具彈性的動作列元件，它會取代`ActionBar`。 `Toolbar` 適用於 Android 5.0 Lollipop 或更新版本，同時它也是適用於舊版的 Android 透過[Android 支援程式庫 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet 套件。 
    `Toolbar` 目前建議的動作列元件，以用於 Android 的應用程式。
    如需詳細資訊，請參閱 <<c0> [ 工具列](~/android/user-interface/controls/tool-bar/index.md)。 



## <a name="related-links"></a>相關連結

- [材料設計-索引標籤](https://material.io/guidelines/components/tabs.html)- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [Android 支援程式庫 v7 AppCompat NuGet 封裝](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [v7 appcompat 程式庫](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
