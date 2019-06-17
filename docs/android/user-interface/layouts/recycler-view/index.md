---
title: RecyclerView
description: RecyclerView 顯示集合，是檢視群組它被設計為更有彈性取代較舊的檢視群組，例如 ListView 和 GridView。  本指南說明如何使用和自訂 RecyclerView Xamarin.Android 應用程式中。
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/03/2018
ms.openlocfilehash: 1332a7ef5b8e5bb2f1178582bcf058123f1e177c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61308752"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView 顯示集合，是檢視群組它被設計為更有彈性取代較舊的檢視群組，例如 ListView 和 GridView。本指南說明如何使用和自訂 RecyclerView Xamarin.Android 應用程式中。_

## <a name="recyclerview"></a>RecyclerView

許多應用程式需要顯示 （例如郵件、 連絡人、 影像或歌曲）; 相同類型的集合通常，這個集合是太大，在畫面上，讓集合會顯示在小的視窗，可以順暢地捲動瀏覽集合中的所有項目。
`RecyclerView` 是 Android 的 widget，顯示在清單或方格中，讓使用者能夠捲動瀏覽集合中的項目集合。 以下是使用的範例應用程式的螢幕擷取畫面`RecyclerView`來顯示垂直捲動清單中的電子郵件收件匣內容：

[![列出收件匣訊息使用 RecyclerView 範例應用程式](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` 提供兩個吸引人的功能：

-  它有彈性的架構，可讓您插入您慣用的元件來修改其行為。

-  這是有效地使用大型集合，因為它會重複使用項目檢視，而且需要使用*檢視持有者*以快取檢視參考。

本指南說明如何使用`RecyclerView`在 Xamarin.Android 應用程式; 它會說明如何新增`RecyclerView`套件，以您的 Xamarin.Android 專案和其描述如何`RecyclerView`一般應用程式中的函式。 真正的程式碼範例可說明如何整合`RecyclerView`到您的應用程式、 如何實作項目檢視，請按一下，以及如何重新整理`RecyclerView`其基礎資料變更時。 本指南假設您已熟悉 Xamarin.Android 開發。


### <a name="requirements"></a>需求

雖然`RecyclerView`是通常 Android 5.0 Lollipop 及建立關聯，它提供做為支援程式庫&ndash;`RecyclerView`搭配應用程式的目標 API 層級 7 (Android 2.1) 和更新版本。 下列，才能使用`RecyclerView`以 Xamarin 為基礎的應用程式中：

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更新版本必須安裝並設定與 Visual Studio 或 Visual Studio for mac。

-  您的應用程式專案必須包含**Xamarin.Android.Support.v7.RecyclerView**封裝。 如需有關如何安裝 NuGet 套件的詳細資訊，請參閱[逐步解說：在專案中包含 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。


### <a name="overview"></a>總覽

`RecyclerView` 可以視為取代`ListView`和`GridView`在 Android 中的小工具。 如同它的前身`RecyclerView`可以用來顯示大型資料集，在小的視窗中，但`RecyclerView`提供更多的版面配置選項，並進一步最適合用於顯示大型集合。 如果您熟悉`ListView`，有數個重要差異`ListView`和`RecyclerView`:

-   `RecyclerView` 使用稍微比較複雜： 您必須撰寫更多的程式碼，以使用`RecyclerView`相較於`ListView`。

-   `RecyclerView` 不會提供預先定義的介面卡;您必須實作存取您的資料來源的配接器程式碼。 不過，Android 會包含數個預先定義的配接器可搭配`ListView`和`GridView`。

-   `RecyclerView` 不提供的項目按一下事件，當使用者點選項目;相反地，協助程式類別會處理項目按一下事件。 相較之下，`ListView`提供項目按一下事件。

-   `RecyclerView` 回收檢視和強制檢視持有者模式中，這樣會消除不必要的版面配置的資源查閱，可以提升效能。 使用檢視持有者模式中是選擇性`ListView`。

-   `RecyclerView` 取決於模組化的設計，可讓您更容易自訂。 比方說，您可以插入不同的版面配置原則，而不需要重大的程式碼變更您的應用程式。
    相較之下，`ListView`是相當龐大的結構中。

-   `RecyclerView` 包含項目新增和移除內建的動畫。 `ListView` 動畫會需要一些額外的工作執行應用程式開發人員。


### <a name="sections"></a>章節

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[RecyclerView 組件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

本主題說明如何`Adapter`， `LayoutManager`，並`ViewHolder`合作協助程式類別，以支援為`RecyclerView`。
它提供的每個協助程式類別的高階概觀，並說明您如何使用這些應用程式中。

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[基本的 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

本主題中提供的資訊是根據[RecyclerView 組件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)藉由提供的真正的程式碼範例的各種`RecyclerView`實作項目可以建置真實世界相片瀏覽應用程式。

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[延伸 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

本主題會將額外的程式碼加入至範例應用程式中呈現[基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)示範如何處理項目按一下事件，並更新`RecyclerView`當基礎資料來源變更。


### <a name="summary"></a>總結

本指南介紹 Android`RecyclerView`小工具; 不僅說明如何新增`RecyclerView`如何支援 Xamarin.Android 專案的程式庫`RecyclerView`回收檢視，它會強制執行的持有者的檢視模式，為了提高效率，以及如何各種helper 類別，其構成`RecyclerView`顯示集合共同作業。 它提供範例程式碼，示範如何`RecyclerView`整合到應用程式，它會說明如何量身訂作`RecyclerView`的配置原則，藉由在不同的版面配置管理員中將說明如何處理項目按一下 [事件]，並通知`RecyclerView`的資料來源的變更。

如需詳細資訊`RecyclerView`，請參閱 < [RecyclerView 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)。


## <a name="related-links"></a>相關連結

- [RecyclerViewer （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [棒棒糖符號的簡介](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
