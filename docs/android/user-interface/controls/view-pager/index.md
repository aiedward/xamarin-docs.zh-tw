---
title: ViewPager
description: ViewPager 是可讓您實作 gestural 巡覽配置管理員。 左邊和右邊來逐步執行的資料頁，gestural 瀏覽可讓使用者撥動。 本指南說明如何實作 gestural 巡覽與 ViewPager，逾時或無片段。 它也會說明如何新增使用 PagerTitleStrip 和 PagerTabStrip 頁面指標。
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: bd687175048bb6a19dde21e66619667511a76796
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="viewpager"></a>ViewPager

_ViewPager 是可讓您實作 gestural 巡覽配置管理員。左邊和右邊來逐步執行的資料頁，gestural 瀏覽可讓使用者撥動。本指南說明如何實作 gestural 巡覽與 ViewPager，逾時或無片段。它也會說明如何新增使用 PagerTitleStrip 和 PagerTabStrip 頁面指標。_

 
## <a name="overview"></a>總覽

應用程式開發中常見的案例是需要為使用者提供 gestural 同層級檢視之間導覽。 這種方法，使用者會 swipes 左邊或右邊的內容 （例如，在安裝精靈或投影片放映） 頁。 您可以使用來建立這些撥動檢視`ViewPager` widget 中，用於[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。 `ViewPager`是配置 widget 的多個子檢視組成，其中每一個子檢視構成中配置的頁面： 

[![以水平撥動範例螢幕擷取畫面的 TreePager 應用程式](images/01-intro-sml.png)](images/01-intro.png#lightbox)

一般而言，`ViewPager`用於搭配[片段](https://developer.xamarin.com/guides/android/platform_features/fragments/); 不過，有些情況下，您可能想要使用`ViewPager`不增加的複雜度`Fragment`s。

`ViewPager` 為提供檢視以顯示使用配接器模式。 這裡使用的配接器在概念上類似所使用的[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash;您提供的實作`PagerAdapter`產生頁面的`ViewPager`向使用者顯示。 所顯示的頁面`ViewPager`可以`View`s 或`Fragment`s。 當`View`會顯示，配接器的子類別 Android 的`PagerAdapter`基底類別。 如果`Fragment`會顯示，配接器的子類別 Android 的`FragmentPagerAdapter`。 Android 支援程式庫也包含`FragmentPagerAdapter`(的子類別`PagerAdapter`) 連接的詳細資訊與協助`Fragment`s 資料。 

本指南示範這兩種方法： 

-   在[與檢視 Viewpager](~/android/user-interface/controls/view-pager/viewpager-and-views.md)、 [TreePager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/)開發應用程式示範如何使用`ViewPager`顯示的樹狀目錄 （落葉和長青樹狀結構的映像庫） 的檢視。 
    `PagerTabStrip`  和`PagerTitleStrip`用來顯示頁面導覽說明的項目。

-   在[片段 Viewpager](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md)，稍微複雜[FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/)開發應用程式示範如何使用`ViewPager`與`Fragment`來建置會呈現為數學問題的應用程式快閃記憶卡，並回應使用者輸入。 


## <a name="requirements"></a>需求

若要使用`ViewPager`在應用程式專案，您必須安裝[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)封裝。 如需安裝 NuGet 封裝的詳細資訊，請參閱[逐步解說： 在您的專案包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。 

 
## <a name="architecture"></a>架構

三個元件用來實作 gestural 導覽`ViewPager`:

-   ViewPager
-   配接器
-   頁面巡覽區標記

以下摘述每個元件。



### <a name="viewpager"></a>ViewPager

`ViewPager` 顯示集合是配置管理員`View`一次一個 s。 偵測使用者撥動手勢，並瀏覽至適當的一個或下一個檢視是其工作。 例如，下列螢幕擷取畫面示範`ViewPager`以回應使用者軌跡進行到下一個影像中的轉換： 

[![顯示檢視之間的轉換的 TreePager 經過應用程式](images/02-transition-sml.png)](images/02-transition.png#lightbox)


### <a name="adapter"></a>配接器

`ViewPager` 它會從提取資料*配接器*。 配接器的工作是建立`View`所顯示的 s `ViewPager`，讓他們能夠視需要。 下圖說明此概念&ndash;配接器會建立並於其中填入`View`s，並提供他們`ViewPager`。 做為`ViewPager`偵測到使用者的撥動手勢，它會要求配接器，以提供適當`View`顯示： 

[![圖表說明配接器如何連接影像和名稱至 ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

在此特定範例中，每個`View`傳送至之前從樹狀目錄影像，以及樹狀目錄名稱所建構`ViewPager`。 



### <a name="pager-indicator"></a>頁面巡覽區標記

`ViewPager` 可用於顯示大型資料集 （例如，映像庫可能包含數百個映像）。 若要協助使用者巡覽大型資料集，`ViewPager`通常會伴隨*頁面巡覽區標記*所顯示的字串。 字串可能會是映像標題、 標題或只是目前的檢視資料集內的位置。 

有兩種檢視可能會產生適用於您的瀏覽資訊：`PagerTabStrip`和`PagerTitleStrip.`每個頂端的顯示字串`ViewPager`，每個提取資料的來源和`ViewPager`的配接器，讓它永遠與保持同步目前顯示`View`。 它們之間的差異在於`PagerTabStrip`包含 「 目前 」 的字串時的視覺指標`PagerTitleStrip`發生不 （這些螢幕擷取畫面所示）： 

[![PagerTitleStrip 與 PagerTabStrip TreePager 應用程式的螢幕擷取畫面](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

本指南示範如何 immplement `ViewPager`，配接器和指標的應用程式元件，並整合，以支援 gestural 瀏覽。 



## <a name="related-links"></a>相關連結

- [TreePager （範例）](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
- [FlashCardPager (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
