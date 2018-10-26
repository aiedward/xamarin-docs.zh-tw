---
title: ViewPager
description: ViewPager 是可讓您實作手勢導覽的佈局管理員。 手勢導覽允許用戶向左和向右滑動以逐步瀏覽資料頁面。 本指南說明如何實作 gestural 導覽使用 ViewPager，而片段。 它也會說明如何新增使用 PagerTitleStrip 和 PagerTabStrip 頁面指標。
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: bb9795eb1e77a48b01556c553ae19613d6ab6de6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115935"
---
# <a name="viewpager"></a>ViewPager

_ViewPager 是可讓您實作手勢導覽的佈局管理員。手勢導覽允許用戶向左和向右滑動以逐步瀏覽資料頁面。本指南說明如何實作 gestural 導覽使用 ViewPager，而片段。它也會說明如何新增使用 PagerTitleStrip 和 PagerTabStrip 頁面指標。_

 
## <a name="overview"></a>總覽

應用程式開發中的常見案例是需要使用者提供 gestural 巡覽同層級檢視。 這種方法，使用者會 swipes 左邊或右邊的內容 （例如，在安裝精靈或投影片放映） 的存取頁面。 您可以使用來建立這些撥動檢視`ViewPager`小工具，用於[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。 `ViewPager`是配置 widget 的多個子檢視所組成，其中每一個子系檢視構成中配置的頁面： 

[![水平的撥動範例的螢幕擷取畫面的 TreePager 應用程式](images/01-intro-sml.png)](images/01-intro.png#lightbox)

通常`ViewPager`用於搭配[片段](https://developer.xamarin.com/guides/android/platform_features/fragments/); 不過，有一些您可能要使用的情況下`ViewPager`而不需要增加的複雜度`Fragment`s。

`ViewPager` 使用配接器模式來提供要顯示的檢視。 這裡使用的配接器是在概念上類似於使用[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash;提供的實作`PagerAdapter`產生的頁面，`ViewPager`向使用者顯示。 所顯示的頁面`ViewPager`可以是`View`s 或`Fragment`s。 當`View`會顯示，配接器的子類別 Android 的`PagerAdapter`基底類別。 如果`Fragment`會顯示，配接器的子類別 Android 的`FragmentPagerAdapter`。 Android 支援程式庫也包含`FragmentPagerAdapter`(的子類別`PagerAdapter`) 來協助進行的連線詳細資料`Fragment`s 資料。 

本指南將示範這兩種方法： 

-   在[使用檢視的 Viewpager](~/android/user-interface/controls/view-pager/viewpager-and-views.md)，則[TreePager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/)應用程式開發為示範如何使用`ViewPager`顯示樹狀目錄 （落葉和長青型樹狀結構的映像庫） 的檢視。 
    `PagerTabStrip`  和`PagerTitleStrip`用來顯示頁面巡覽協助的標題。

-   在[含片段的 Viewpager](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md)，稍微複雜[FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager/)應用程式開發為示範如何使用`ViewPager`使用`Fragment`以建置會呈現為數學問題的應用程式快閃記憶卡並回應使用者輸入。 


## <a name="requirements"></a>需求

若要使用`ViewPager`在您的應用程式專案，您必須安裝[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)封裝。 如需有關如何安裝 NuGet 套件的詳細資訊，請參閱 <<c0> [ 逐步解說： 在您的專案中包含 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。 

 
## <a name="architecture"></a>架構

三個元件用來實作 gestural 導覽`ViewPager`:

-   ViewPager
-   配接器
-   頁面巡覽區指標

以下摘述每個元件。



### <a name="viewpager"></a>ViewPager

`ViewPager` 會顯示集合的配置管理員`View`一次一個 s。 其工作是偵測使用者的撥動手勢，並瀏覽至適當的一個或下一個檢視。 例如，以下螢幕擷取畫面示範`ViewPager`進行從一個映像轉換到下一步 以回應使用者筆勢： 

[![顯示檢視之間的轉換的特寫的 TreePager 應用程式](images/02-transition-sml.png)](images/02-transition.png#lightbox)


### <a name="adapter"></a>配接器

`ViewPager` 提取資料的來源*配接器*。 配接器的工作是建立`View`所顯示的 s `ViewPager`，視需要提供它們。 下圖說明這個概念&ndash;配接器會建立，並於其中填入`View`s，並提供他們`ViewPager`。 作為`ViewPager`偵測到使用者的撥動手勢，它會要求提供適當的介面卡`View`顯示： 

[![說明 配接器如何連接映像和名稱至 ViewPager 圖表](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

在此範例中，每個`View`之前它會傳遞至建構的樹狀目錄中的映像與樹狀目錄名稱`ViewPager`。 



### <a name="pager-indicator"></a>頁面巡覽區指標

`ViewPager` 可用來顯示大型資料集 （例如，映像庫可能包含數百個映像）。 若要協助使用者瀏覽大型資料集，`ViewPager`通常會伴隨*呼叫器指標*所顯示的字串。 字串可能是映像標題、 標題或只是目前的檢視資料集內的位置。 

有兩個檢視，可產生適用於您的瀏覽資訊：`PagerTabStrip`並`PagerTitleStrip.`每個在頂端顯示的字串`ViewPager`，和每個提取資料的來源`ViewPager`的配接器，因此它一律會保持與同步目前顯示`View`。 它們之間的差異在於`PagerTabStrip`包括 「 目前 」 的字串時的視覺指標`PagerTitleStrip`發生不 （這些螢幕擷取畫面所示）： 

[![使用 PagerTitleStrip 和 PagerTabStrip TreePager 應用程式的螢幕擷取畫面](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

本指南示範如何 immplement `ViewPager`，配接器和指示器應用程式元件，並將它們以支援 gestural 導覽。 



## <a name="related-links"></a>相關連結

- [TreePager （範例）](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
- [FlashCardPager （範例）](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
