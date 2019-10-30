---
title: 索引標籤式版面配置
description: Android 中索引標籤式版面配置的總覽
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/08/2017
ms.openlocfilehash: 4ca4200d0f9036ed76e20e3a1840303e7bb3b7e3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028782"
---
# <a name="tabbed-layouts"></a>索引標籤式版面配置

## <a name="overview"></a>總覽

索引標籤是行動應用程式中的熱門使用者介面模式，因為其簡易性和可用性。 它們提供一致且簡單的方法，在應用程式中的各種畫面之間流覽。 Android 有數個適用于索引標籤式介面的 API： 

- **ActionBar** &ndash; 這是在 Android 3.0 （API 層級11）中引進的一組新 API 的一部分，其目標是要提供一致的導覽和視圖切換介面。 其已使用[Android 支援程式庫 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)回到 android 2.2 （API 層級8）。 

- **PagerTabStrip** &ndash; 表示 `ViewPager`的目前、下一頁和上一頁。 `ViewPager` 僅透過[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)提供。
     如需 `PagerTabStrip`的詳細資訊，請參閱[ViewPager](~/android/user-interface/controls/view-pager/index.md)。

- **工具列**&ndash; `Toolbar` 是較新且更有彈性的動作列元件，會取代 `ActionBar`。 `Toolbar` 適用于 Android 5.0 或更新版本，也適用于較舊版本的 Android （透過[Android 支援程式庫 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet 套件）。 
    `Toolbar` 目前是在 Android 應用程式中使用的建議動作列元件。
    如需詳細資訊，請參閱[工具列](~/android/user-interface/controls/tool-bar/index.md)。 

## <a name="related-links"></a>相關連結

- [材質設計-](https://material.io/guidelines/components/tabs.html)索引標籤- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [Android 支援程式庫 v7 AppCompat NuGet 套件](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [v7 appcompat 程式庫](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
