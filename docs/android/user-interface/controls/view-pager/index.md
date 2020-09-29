---
title: ViewPager
description: ViewPager 是一種版面建構管理員，可讓您執行手勢導覽。 手勢流覽可讓使用者向左和向右滑動，以逐步執行資料頁面。 本指南說明如何搭配使用 ViewPager 和不含片段來執行手勢導覽。 此外，也會說明如何使用 PagerTitleStrip 和 PagerTabStrip 來新增頁面指示器。
ms.prod: xamarin
ms.assetid: D42896C0-DE7C-4818-B171-CB2D5E5DD46A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 427ace2043f966b617a258b5f50fa42f943e707e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457649"
---
# <a name="viewpager"></a>ViewPager

_ViewPager 是一種版面建構管理員，可讓您執行手勢導覽。手勢流覽可讓使用者向左和向右滑動，以逐步執行資料頁面。本指南說明如何搭配使用 ViewPager 和不含片段來執行手勢導覽。此外，也會說明如何使用 PagerTitleStrip 和 PagerTabStrip 來新增頁面指示器。_

## <a name="overview"></a>概觀

應用程式開發的常見案例是，必須為使用者提供同級視圖之間的手勢導覽。 在此方法中，使用者會撥動存取內容頁面的左邊或右邊 (例如，在安裝程式或投影片) 中。 您可以使用 `ViewPager` widget （可在 [Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)中取得）來建立這些滑動視圖。 `ViewPager`是由多個子視圖組成的版面配置 widget，每個子視圖都在配置中構成頁面： 

[![具有水準刷圖範例的 TreePager 應用程式螢幕擷取畫面](images/01-intro-sml.png)](images/01-intro.png#lightbox)

一般而言， `ViewPager` 會與 [片段](~/android/platform/fragments/index.md)一起使用; 不過，在某些情況下，您可能會想要使用， `ViewPager` 而不會增加複雜度 `Fragment` 。

`ViewPager` 使用介面卡模式來提供要顯示的視圖。 此處使用的介面卡在概念上類似于 [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) &ndash; 您提供的執行， `PagerAdapter` 以產生向使用者顯示的頁面 `ViewPager` 。 顯示的頁面 `ViewPager` 可以是 `View` 或 `Fragment` s。 當 `View` 顯示時，介面卡子類別會分類 Android 的 `PagerAdapter` 基類。 如果 `Fragment` 顯示，則介面卡子類別是 Android 的子類別 `FragmentPagerAdapter` 。 Android 支援程式庫也包含 `FragmentPagerAdapter` () 的子類別 `PagerAdapter` ，以協助您瞭解將連接 `Fragment` 到資料的詳細資料。 

本指南將示範這兩種方法： 

- 在 [Viewpager With Views](~/android/user-interface/controls/view-pager/viewpager-and-views.md)時，會開發 [TreePager](/samples/xamarin/monodroid-samples/userinterface-treepager) 應用程式，以示範如何使用 `ViewPager` 來顯示樹狀目錄的視圖， (位於落葉和長時間樹的影像資源庫) 。 
    `PagerTabStrip`  和 `PagerTitleStrip` 可用來顯示有助於進行頁面導覽的標題。

- 在 [具有片段的 Viewpager](~/android/user-interface/controls/view-pager/viewpager-and-fragments.md)中，會開發稍微複雜的 [FlashCardPager](/samples/xamarin/monodroid-samples/userinterface-flashcardpager) 應用程式，以示範如何使用搭配 `ViewPager` `Fragment` 來建立應用程式，以將數學問題呈現為快閃卡片並回應使用者輸入。 

## <a name="requirements"></a>需求

若要 `ViewPager` 在您的應用程式專案中使用，您必須安裝 [Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) 套件。 如需安裝 NuGet 套件的詳細資訊，請參閱 [逐步解說：在您的專案中包含 NuGet](/visualstudio/mac/nuget-walkthrough)。 

## <a name="architecture"></a>架構

有三個元件可用於使用下列程式來執行手勢導覽 `ViewPager` ：

- ViewPager
- 配接器
- 呼機指標

以下摘要列出每個元件。

### <a name="viewpager"></a>ViewPager

`ViewPager` 是一次顯示一個集合的版面建構管理員 `View` 。 其工作是偵測使用者的滑動手勢，並適當地流覽至下一個或上一個視圖。 例如，下列螢幕擷取畫面示範如何在 `ViewPager` 回應使用者手勢時，將一個影像轉換為下一個影像： 

[![顯示 Views 之間轉換的 TreePager 應用程式特寫](images/02-transition-sml.png)](images/02-transition.png#lightbox)

### <a name="adapter"></a>配接器

`ViewPager` 從 *介面卡*提取其資料。 介面卡的工作是建立所 `View` 顯示的 `ViewPager` ，視需要提供它們。 下圖說明 &ndash; 介面卡建立並填入的概念， `View` 並將其提供給 `ViewPager` 。 當 `ViewPager` 偵測到使用者的滑動手勢時，它會要求介面卡提供適當的 `View` 顯示： 

[![說明介面卡如何將影像和名稱連接至 ViewPager 的圖表](images/03-adapter-sml.png)](images/03-adapter.png#lightbox)

在這個特定的範例中，每個 `View` 都是從樹狀結構和樹狀目錄名稱傳遞至 `ViewPager` 。 

### <a name="pager-indicator"></a>呼機指標

`ViewPager` 可以用來顯示大型資料集 (例如，影像庫可能包含數百個影像) 。 為了協助使用者流覽大型資料集， `ViewPager` 通常會伴隨顯示字串的 *呼機指標* 。 字串可能是影像標題、標題，或只是目前視圖在資料集內的位置。 

有兩個可為您產生此流覽資訊的視圖： `PagerTabStrip` `PagerTitleStrip.` 每個都在頂端顯示一個字串 `ViewPager` ，每個都從的介面卡提取其資料， `ViewPager` 使其永遠保持與目前顯示的同步 `View` 。 它們之間的差異在於 `PagerTabStrip` 包含 "current" 字串的視覺指標，但 `PagerTitleStrip` 不 (如下列螢幕擷取畫面所示) ： 

[![使用 PagerTitleStrip 和 PagerTabStrip 的 TreePager 應用程式螢幕擷取畫面](images/04-comparison-sml.png)](images/04-comparison.png#lightbox)

本指南示範如何 immplement `ViewPager` 、介面卡和指標應用程式元件，並將其整合以支援手勢導覽。 

## <a name="related-links"></a>相關連結

- [TreePager (範例) ](/samples/xamarin/monodroid-samples/userinterface-treepager)
- [FlashCardPager (範例) ](/samples/xamarin/monodroid-samples/userinterface-flashcardpager)