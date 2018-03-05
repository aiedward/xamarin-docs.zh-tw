---
title: "TabHost 配置 索引標籤"
description: "本文將提供的高層級概觀 TabHost，較舊的 API 用來建立索引標籤式的配置 Xamarin.Android 應用程式中。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/25/2017
ms.openlocfilehash: ff61ca0a2bca466da3e33c93a17944915328b70c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="tab-layout-with-tabhost"></a>TabHost 配置 索引標籤

_本文將提供的高層級概觀 TabHost，較舊的 API 用來建立索引標籤式的配置 Xamarin.Android 應用程式中。_

<a name="Overview" />

## <a name="overview"></a>總覽

> [!NOTE]
> **注意：** `TabHost`是舊的 API，可由 Google 已被取代。 開發人員都建置索引標籤式的應用程式使用[項](~/android/user-interface/controls/action-bar.md)。 `ActionBar`用於所有的 Android 版本。 它引進 Android 3.0 （API 層級 11） 和回已移植到 Android 2.2 (API level 8) 和中的 Android 2.3 （API 層級 10） [V7 AppCompat 程式庫](http://developer.android.com/tools/support-library/features.html#v7-appcompat)，這是可透過 Xamarin.Android [XamarinAndroid 支援程式庫-V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)封裝。

`TabHost`是舊版的原始 API 來建立索引標籤式的使用者 interfacesIt 最適合 Xamarin.Android 應用程式必須支援 Android 2.2 和 Android 2.3，但不能使用**ActionBarSherlock**。
下列五個元件是與所有參與`TabHost`API:

-  **TabActivity** &ndash;這是做為容器的特殊的檢視`TabHost`（如下所述）。

-  **TabHost** &ndash;這是索引標籤式 UI 的容器。 它所主控的兩個小孩： 的索引標籤的清單以及`FrameLayout`顯示索引標籤內容。

-  **TabWidget** &ndash;此控制項顯示索引標籤，其中每個索引標籤中的清單`TabHost`。 每個索引標籤中的`TabHost`都由`TabHost.TabSpec`物件。 此物件包含的每個索引標籤的中繼資料。當使用者選取索引標籤上，`TabHost`回應選取範圍顯示適當的索引標籤。

-  **FrameLayout** &ndash;必須要有一個索引標籤式的 UI`FrameLayout`內包含`TabHost`。 它用來顯示內容 索引標籤。

-  **活動或檢視表**&ndash;選取索引標籤時，它會顯示活動或檢視中的`FrameLayout`。

下圖顯示所有這些元件如何一起關聯：

![圖表說明內 TabHost TabWidget 框架配置](tab-host-images/image03.png)

索引標籤內容可能活動或檢視表。 檢視相當輕量且簡單的但可能會導致許多無關的程式碼共同-habitating 活動中。 這會導致不佳的考量和分隔繁雜難以維護的類別。 相較之下，活動需要系統資源，但使用的邏輯封裝在它本身的不同類別的每個索引標籤允許更模組化的方法。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章解釋較舊的高層級元件`TabHost`API for Android，和其所有關聯在一起。



## <a name="related-links"></a>相關連結

- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [TabHost](https://developer.xamarin.com/api/type/Android.Widget.TabHost/)
- [TabHost.TabSpec](https://developer.xamarin.com/api/type/Android.Widget.TabHost+TabSpec/)
- [TabWidget](https://developer.xamarin.com/api/type/Android.Widget.TabWidget/)
- [TabActivity](https://developer.xamarin.com/api/type/Android.App.TabActivity/)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Android 支援程式庫 v7 AppCompat NuGet 封裝](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [v7 appcompat 程式庫](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
