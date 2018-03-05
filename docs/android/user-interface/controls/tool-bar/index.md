---
title: "工具列"
description: "工具列會提供更大的彈性比預設動作列的動作列元件： 它可以放在中的應用程式，可以變更其大小，而且它可以使用不同的應用程式佈景主題色彩配置。 此外，每個應用程式畫面上可以有多個工具列。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/22/2018
ms.openlocfilehash: cf2211a572d45b7c29018d00f36cb8408484483f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="toolbar"></a>工具列

_工具列會提供更大的彈性比預設動作列的動作列元件： 它可以放在中的應用程式，可以變更其大小，而且它可以使用不同的應用程式佈景主題色彩配置。此外，每個應用程式畫面上可以有多個工具列。_


<a name="overview" />
 
## <a name="overview"></a>總覽

任何的 Android 活動的關鍵設計項目是*動作列*。 在動作列是用來瀏覽、 搜尋、 功能表和商標 Android 應用程式的 UI 元件。 在 Android 5.0 棒棒糖符號，動作列之前的 Android 版本 (也稱為*應用程式列*) 是建議的元件，提供此功能。 

`Toolbar` Widget （Android 5.0 棒棒糖符號中引進） 可以視為動作列介面的一般化&ndash;它用來取代動作列。 `Toolbar`可以用於應用程式版面配置的任何位置，而且它可更自訂比動作列。 下列螢幕擷取畫面說明自訂`Toolbar`本指南中所建立的範例： 

[![使用 [編輯]，工具列的範例螢幕擷取畫面儲存和溢位功能表項目](images/01-toolbar-sml.png)](images/01-toolbar.png)

有某些重要差異`Toolbar`和動作列： 

-   A`Toolbar`可以放在使用者介面中的任何地方。

-   可以在相同的螢幕上顯示多個工具列。

-   如果使用片段，每個片段都可以有它自己`Toolbar`。 

-   A`Toolbar`可以設定成跨越只有部分螢幕的寬度。 

-   因為`Toolbar`未繫結至活動的視窗裝潢的色彩配置，它可以包含以視覺化方式不同的色彩配置。 

-   不同於在動作列`Toolbar`不包含左側的圖示。 在右邊其功能表使用較少的空間。 

-   `Toolbar`高度會調整。 

-   可以包含其他檢視，在內部`Toolbar`。 

A`Toolbar`可包含一或多個下列項目： 

-   瀏覽按鈕

-   加上品牌的標誌影像

-   標題和副標題

-   自訂檢視

-   [動作] 功能表

-   溢位功能表

Google[材料設計指導方針](https://material.google.com/)建議利用這些元素，以提供不同的外觀 （而非完全仰賴應用程式圖示和標題） 的應用程式。 

本指南涵蓋最常用的`Toolbar`案例：

-   取代具有活動的預設動作列`Toolbar`。 

-   新增第二個`Toolbar`到活動中。

-   使用**Android 支援程式庫 v7 AppCompat**程式庫 (稱為*AppCompat*本指南的其餘部分) 來部署`Toolbar`較早版本的 Android 上。 

 
<a name="requirements" />
 
## <a name="requirements"></a>需求

`Toolbar` 可在 Android 5.0 棒棒糖符號 (API 21) 及更新版本。 當目標為 Android 版本早於 Android 5.0 時，使用[Android 支援的程式庫 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)，以提供回溯相容`Toolbar`NuGet 封裝中支援。 
[工具列相容性](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md)說明如何使用此程式庫。 




## <a name="related-links"></a>相關連結

- [棒棒糖符號工具列 （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [AppCompat 工具列 （範例）](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
