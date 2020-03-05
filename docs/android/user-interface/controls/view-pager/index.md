---
title: ViewPager
description: ViewPager 是可讓您執行 gestural 導覽的版面建構管理員。 Gestural 導覽可讓使用者向左和向右滑動以逐步執行資料頁面。 本指南說明如何使用 ViewPager 來執行具有和不含片段的 gestural 導覽。 它也會說明如何使用 PagerTitleStrip 和 PagerTabStrip 加入頁面指標。
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: c7718ef7a02365e9ca09f7491804cbadfa0c9a41
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "78292944"
---
# <a name="viewpager"></a>ViewPager

_ViewPager 是可讓您執行 gestural 導覽的版面建構管理員。Gestural 導覽可讓使用者向左和向右滑動以逐步執行資料頁面。本指南說明如何使用 ViewPager 來執行具有和不含片段的 gestural 導覽。它也會說明如何使用 PagerTitleStrip 和 PagerTabStrip 加入頁面指標。_

## <a name="overview"></a>概觀

應用程式開發的常見案例是需要為使用者提供同級視圖之間的 gestural 流覽。 在這種方法中，使用者撥動向左或向右鍵存取內容頁面（例如，在安裝程式或投影片放映中）。 您可以使用[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)中提供的 `ViewPager` widget 來建立這些輕刷視圖。 `ViewPager` 是由多個子視圖組成的版面配置 widget，其中每個子視圖會構成版面配置中的頁面： 

[使用水準滑動範例 ![TreePager 應用程式的螢幕擷取畫面](images/01-intro-sml.png)](images/01-intro.png#lightbox)

一般來說，`ViewPager` 會與[片段](~/android/platform/fragments/index.md)搭配使用;不過，在某些情況下，您可能會想要使用 `ViewPager`，而不會增加 `Fragment`的複雜性。

`ViewPager` 使用介面卡模式來提供要顯示的視圖。 此處所使用的介面卡概念上類似于[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; 您提供 `PagerAdapter` 的執行，以產生 `ViewPager` 向使用者顯示的頁面。 `ViewPager` 所顯示的頁面可以是 `View`s 或 `Fragment`s。 當顯示 `View`時，介面卡會子類別化 Android 的 `PagerAdapter` 基類。 如果顯示 `Fragment`s，介面卡會將 Android 的 `FragmentPagerAdapter`子類別。 Android 支援程式庫也包含 `FragmentPagerAdapter` （`PagerAdapter`的子類別），可協助您將 `Fragment`的連接到資料的詳細資訊。 

本指南會示範這兩種方法： 

- 在[具有 Views 的 Viewpager](~/android/user-interface/controls/view-pager/viewpager-and-views.md)中，會開發[TreePager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager)應用程式，以示範如何使用 `ViewPager` 顯示樹狀目錄的視圖（落葉和長時間樹狀結構的影像資源庫）。 
    `PagerTabStrip` 和 `PagerTitleStrip` 可用來顯示可協助進行頁面導覽的標題。

- 在[含有片段的 Viewpager](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md)中，會開發稍微複雜的[FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)應用程式，以示範如何使用 `ViewPager` 搭配 `Fragment`來建立應用程式，以將數學問題呈現為快閃卡並回應使用者輸入。 

## <a name="requirements"></a>需求

若要在您的應用程式專案中使用 `ViewPager`，您必須安裝[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)封裝。 如需安裝 NuGet 套件的詳細資訊，請參閱[逐步解說：在您的專案中包含 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。 

## <a name="architecture"></a>架構

有三個元件用於使用 `ViewPager`來執行 gestural 導覽：

- ViewPager
- 介面卡
- 分頁指標

以下摘要說明每個元件。

### <a name="viewpager"></a>ViewPager

`ViewPager` 是一種版面建構管理員，會一次顯示一個 `View`的集合。 其工作是偵測使用者的滑動手勢，並適當地流覽至下一個或上一個視圖。 例如，下列螢幕擷取畫面示範 `ViewPager` 在回應使用者手勢時，從一個影像轉換到下一個影像： 

[顯示 Views 之間轉換的 TreePager 應用程式 ![特寫](images/02-transition-sml.png)](images/02-transition.png#lightbox)

### <a name="adapter"></a>介面卡

`ViewPager` 從*介面卡*提取其資料。 介面卡的工作是建立由 `ViewPager`所顯示的 `View`，並視需要提供這些專案。 下圖說明此概念 &ndash; 介面卡會建立並填入 `View`s，並將其提供給 `ViewPager`。 當 `ViewPager` 偵測到使用者的刷手勢時，它會要求介面卡提供適當的 `View` 來顯示： 

[![圖表，說明介面卡如何將影像和名稱連接至 ViewPager](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

在此特定範例中，每個 `View` 都是從樹狀結構和樹狀結構名稱所組成，然後再傳遞給 `ViewPager`。 

### <a name="pager-indicator"></a>分頁指標

`ViewPager` 可用來顯示大型資料集（例如，映射庫可能包含數百個影像）。 為了協助使用者流覽大型資料集，`ViewPager` 通常會伴隨一個顯示字串的*呼機指示器*。 字串可能是影像標題、標題，或只是目前視圖在資料集內的位置。 

有兩種可為您產生此導覽資訊的視圖： `PagerTabStrip` 和 `PagerTitleStrip.` 會在 `ViewPager`頂端顯示一個字串，而且每個都會從 `ViewPager`的介面卡提取其資料，使其永遠與目前顯示的 `View`保持同步。 兩者之間的差異在於，`PagerTabStrip` 在 `PagerTitleStrip` 不會包含「目前」字串的視覺指示器（如下列螢幕擷取畫面所示）： 

[使用 PagerTitleStrip 和 PagerTabStrip ![TreePager 應用程式的螢幕擷取畫面](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

本指南示範如何 immplement `ViewPager`、介面卡和指標應用程式元件，並將其整合以支援 gestural 導覽。 

## <a name="related-links"></a>相關連結

- [TreePager （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager)
- [FlashCardPager （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
