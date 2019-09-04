---
title: ViewPager
description: ViewPager 是可讓您實作手勢導覽的佈局管理員。 手勢導覽允許用戶向左和向右滑動以逐步瀏覽資料頁面。 本指南說明如何使用 ViewPager 來執行具有和不含片段的 gestural 導覽。 它也會說明如何使用 PagerTitleStrip 和 PagerTabStrip 加入頁面指標。
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: eca0f82fd967c28bffc8f20bcc9e2ec6bb3ba737
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227698"
---
# <a name="viewpager"></a>ViewPager

_ViewPager 是可讓您實作手勢導覽的佈局管理員。手勢導覽允許用戶向左和向右滑動以逐步瀏覽資料頁面。本指南說明如何使用 ViewPager 來執行具有和不含片段的 gestural 導覽。它也會說明如何使用 PagerTitleStrip 和 PagerTabStrip 加入頁面指標。_

 
## <a name="overview"></a>總覽

應用程式開發的常見案例是需要為使用者提供同級視圖之間的 gestural 流覽。 在這種方法中, 使用者撥動向左或向右鍵存取內容頁面 (例如, 在安裝程式或投影片放映中)。 您可以使用 [ `ViewPager` [Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)] 中提供的 widget 來建立這些輕刷視圖。 `ViewPager`是由多個子視圖組成的版面配置 widget, 其中每個子視圖會構成版面配置中的頁面: 

[![具有水準滑動的 TreePager 應用程式螢幕擷取畫面範例](images/01-intro-sml.png)](images/01-intro.png#lightbox)

一般而言, `ViewPager`會與[片段](~/android/platform/fragments/index.md)搭配使用; 不過, 在某些情況下, 您可能會想要使用`ViewPager` , 而不會增加`Fragment`s 的複雜性。

`ViewPager`會使用介面卡模式來提供要顯示的視圖。 此處使用的介面卡在概念上類似于[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash;所使用, 您會`PagerAdapter`提供的執行, 以產生向`ViewPager`使用者顯示的頁面。 所顯示`ViewPager`的頁面可以是`View`s 或`Fragment`s。 當`View`顯示時, 介面卡會將 Android 的`PagerAdapter`基類子類別化。 如果`Fragment`顯示了, 介面卡會將 Android 的`FragmentPagerAdapter`子類別化。 Android 支援程式庫也包含`FragmentPagerAdapter` (的子`PagerAdapter`類別), 可協助您深入瞭解如何`Fragment`連接到資料。 

本指南會示範這兩種方法: 

- 在[具有 Views 的 Viewpager](~/android/user-interface/controls/view-pager/viewpager-and-views.md)中, 會開發[TreePager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager)應用程式, 以示範`ViewPager`如何使用來顯示樹狀目錄的視圖 (落葉和長時間樹狀結構的影像資源庫)。 
    `PagerTabStrip`和`PagerTitleStrip`可用來顯示可協助進行頁面導覽的標題。

- 在[含有片段的 Viewpager](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md)中, 會開發稍微複雜的[FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)應用程式, 以示範`ViewPager`如何`Fragment`使用搭配來建立應用程式, 以將數學問題呈現為快閃卡並回應使用者輸入。 


## <a name="requirements"></a>需求

若要`ViewPager`在您的應用程式專案中使用, 您必須安裝[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)封裝。 如需安裝 NuGet 套件的詳細資訊, [請參閱逐步解說:在您的專案](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)中包含 NuGet。 

 
## <a name="architecture"></a>架構

使用`ViewPager`下列三個元件來執行 gestural 導覽:

- ViewPager
- 配接器
- 分頁指標

以下摘要說明每個元件。



### <a name="viewpager"></a>ViewPager

`ViewPager`是一個版面建構管理員, 一次顯示`View`一個集合。 其工作是偵測使用者的滑動手勢, 並適當地流覽至下一個或上一個視圖。 例如, 下列螢幕擷取畫面示範`ViewPager`如何在回應使用者手勢時, 從一個影像轉換成下一個影像: 

[![顯示 Views 之間轉換的 TreePager 應用程式特寫](images/02-transition-sml.png)](images/02-transition.png#lightbox)


### <a name="adapter"></a>配接器

`ViewPager`從*介面卡*提取其資料。 介面卡的工作是建立所顯示`View` `ViewPager`的, 並視需要提供它們。 下圖說明介面卡建立&ndash;和填入`View`的`ViewPager`概念, 並將其提供給。 當偵測到使用者的刷手勢時, 它會要求介面卡提供適當`View`的來顯示: `ViewPager` 

[![說明介面卡如何將影像和名稱連接至 ViewPager 的圖表](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

在此特定範例中, `View`每個都是從樹狀結構和樹狀結構名稱所組成, 然後才`ViewPager`會傳遞至。 



### <a name="pager-indicator"></a>分頁指標

`ViewPager`可以用來顯示大型資料集 (例如, 映射庫可能包含數百個影像)。 為了協助使用者流覽大型資料集, `ViewPager`通常會伴隨一個顯示字串的*呼機指示器*。 字串可能是影像標題、標題, 或只是目前視圖在資料集內的位置。 

有兩種可為您產生此導覽資訊的視圖: `PagerTabStrip`和`PagerTitleStrip.`各會在頂端`ViewPager`顯示一個字串`ViewPager`, 而且每個都會從的介面卡提取其資料, 使其永遠保持同步與目前-顯示`View`。 兩者之間的差異在於包含`PagerTabStrip` 「目前」字串的視覺指標, 而`PagerTitleStrip`不是 (如下列螢幕擷取畫面所示): 

[![具有 PagerTitleStrip 和 PagerTabStrip 的 TreePager 應用程式螢幕擷取畫面](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

本指南示範如何 immplement `ViewPager`、介面卡和指標應用程式元件, 並將其整合以支援 gestural 導覽。 



## <a name="related-links"></a>相關連結

- [TreePager (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager)
- [FlashCardPager (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
