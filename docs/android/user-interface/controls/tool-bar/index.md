---
title: 工具列
description: 工具列會提供更大的彈性比預設動作列中的動作列元件： 它可放在任何應用程式中，可以變更其大小，而且它可以使用不同的應用程式的佈景主題色彩配置。 此外，每個應用程式畫面可以有多個工具列。
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 2c9de4058fdaee53671e65f49ad95c3af5e127d6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082824"
---
# <a name="toolbar"></a>工具列

_工具列會提供更大的彈性比預設動作列中的動作列元件： 它可放在任何應用程式中，可以變更其大小，而且它可以使用不同的應用程式的佈景主題色彩配置。此外，每個應用程式畫面可以有多個工具列。_

 
## <a name="overview"></a>總覽

任何 Android 活動的一個重要的設計項目是*動作列*。 動作列是用來瀏覽、 搜尋、 功能表和商標 Android 應用程式的 UI 元件。 在 Android 5.0 Lollipop，動作列之前的 Android 版本 (也稱為*應用程式列*) 已提供這項功能的建議的元件。 

`Toolbar`小工具 （Android 5.0 Lollipop 中引進） 可以視為動作列介面的概括&ndash;它要取代動作列。 `Toolbar`可用於應用程式版面配置的任何位置，而且比動作列更多自訂。 下列螢幕擷取畫面說明自訂`Toolbar`本指南中建立的範例： 

[![範例螢幕擷取畫面的工具列與編輯、 儲存和溢位功能表項目](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

有一些重要差異`Toolbar`和動作列： 

-   A`Toolbar`可以放在使用者介面中的任何地方。

-   可以在同一畫面上顯示多個工具列。

-   如果片段，每個片段都可以有它自己`Toolbar`。 

-   A`Toolbar`可以設定成跨越只有部分螢幕的寬度。 

-   因為`Toolbar`未繫結至活動的視窗 decor 的色彩配置，它可以有視覺上有所不同的色彩配置。 

-   不同的動作列中，於`Toolbar`不包含左側的圖示。 在右邊其功能表使用較少的空間。 

-   `Toolbar`高度會調整。 

-   其他檢視可以包含在內`Toolbar`。 

A`Toolbar`可以包含一或多個下列項目： 

-   瀏覽按鈕

-   加上品牌的標誌影像

-   標題和副標題

-   自訂檢視

-   動作功能表

-   溢位功能表

Google[材料設計指導方針](https://material.google.com/)建議利用這些元素，以提供不同的外觀 （而非依賴應用程式圖示和標題） 的應用程式。 

本指南涵蓋最常使用`Toolbar`案例：

-   取代具有活動的預設動作列`Toolbar`。 

-   新增第二個`Toolbar`到活動中。

-   使用**Android 支援程式庫 v7 AppCompat**程式庫 (稱為*AppCompat*本指南的其餘部分) 來部署`Toolbar`舊版 Android 上。 

 
 
## <a name="requirements"></a>需求

`Toolbar` Android 5.0 Lollipop (API 21) 和更新版本，則會是可用。 當目標 Android 版本早於 Android 5.0 時，使用[Android 支援程式庫 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)，以提供回溯相容`Toolbar`支援 NuGet 套件中。 
[工具列相容性](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md)說明如何使用此程式庫。 




## <a name="related-links"></a>相關連結

- [棒棒糖符號工具列 （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [AppCompat 工具列 （範例）](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
