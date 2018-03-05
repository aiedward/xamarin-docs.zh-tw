---
title: RecyclerView
description: "RecyclerView 顯示集合，是檢視群組它被設計為更有彈性取代較舊的檢視群組，例如 ListView 和 GridView。  本指南說明如何使用和自訂 RecyclerView Xamarin.Android 應用程式中。"
ms.topic: article
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/03/2018
ms.openlocfilehash: ec8b3a4655c8e8d9e492c9f7a1807dd64ecc6ae7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView 顯示集合，是檢視群組它被設計為更有彈性取代較舊的檢視群組，例如 ListView 和 GridView。本指南說明如何使用和自訂 RecyclerView Xamarin.Android 應用程式中。_

## <a name="recyclerview"></a>RecyclerView

許多應用程式需要顯示集合的相同類型 （例如訊息、 連絡人、 影像或歌曲）;通常，這個集合是太大，在畫面上，因此可以順暢地捲動瀏覽集合中的所有項目小視窗中顯示集合。
`RecyclerView` 為 Android widget 會顯示為清單或方格中，讓使用者能夠捲動瀏覽集合中項目的集合。 以下是使用的範例應用程式的螢幕擷取畫面`RecyclerView`以顯示垂直捲動清單中的電子郵件收件匣內容：

[ ![使用 RecyclerView 清單收件匣訊息的範例應用程式](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png)

`RecyclerView` 提供兩項重要功能：

-  它有彈性架構，可讓您修改其行為插入您慣用的元件。

-  它是有效率地使用大型集合，因為它會重複使用的項目檢視，並需要使用*檢視持有者*以快取檢視參考。

本指南說明如何使用`RecyclerView`Xamarin.Android 應用程式; 它會說明如何新增`RecyclerView`封裝至您的 Xamarin.Android 專案，並說明如何`RecyclerView`一般應用程式中的函式。 提供真正的程式碼範例來說明如何整合`RecyclerView`至您的應用程式、 如何實作項目檢視，請按一下及如何重新整理`RecyclerView`其基礎資料變更時。 本指南假設您熟悉 Xamarin.Android 開發。


### <a name="requirements"></a>需求

雖然`RecyclerView`是做為支援程式庫通常與 Android 5.0 棒棒糖符號相關聯，提供&ndash;`RecyclerView`搭配應用程式的目標應用程式開發介面層級 (Android 2.1) 7 及更新版本。 必須使用下列`RecyclerView`Xamarin 架構應用程式中：

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更新版本必須安裝並設定 Visual Studio 或 Visual Studio for mac。

-  您的應用程式的專案必須包含**Xamarin.Android.Support.v7.RecyclerView**封裝。 如需安裝 NuGet 封裝的詳細資訊，請參閱[逐步解說： 在您的專案包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。


### <a name="overview"></a>總覽

`RecyclerView` 可視為取代`ListView`和`GridView`Android 中的 widget。 如同它的前身`RecyclerView`設計來顯示大型資料集，在小視窗中，但`RecyclerView`提供更多的版面配置選項和更適合用於顯示大型集合。 如果您已熟悉`ListView`，有數個重要差異`ListView`和`RecyclerView`:

-   `RecyclerView` 會使用稍微複雜： 您必須撰寫更多的程式碼以使用`RecyclerView`相較於`ListView`。

-   `RecyclerView` 不會提供預先定義的介面卡。您必須實作配接器程式碼存取您的資料來源。 不過，Android 包含數個預先定義的介面卡可搭配`ListView`和`GridView`。

-   `RecyclerView` 不提供項目按一下事件，當使用者點選項目。相反地，協助程式類別會處理項目按一下事件。 相反地，`ListView`提供項目按一下事件。

-   `RecyclerView` 回收檢視和強制檢視持有者模式中，以排除不必要的版面配置的資源查閱，可提高效能。 使用的檢視擁有者模式是選擇性的`ListView`。

-   `RecyclerView` 取決於模組化設計，可讓您更輕鬆地自訂。 例如，您可以插入不同的版面配置原則不需要重大的程式碼變更您的應用程式。
    相反地，`ListView`是相當龐大結構中。

-   `RecyclerView` 包含內建動畫項目加入和移除。 `ListView` 動畫需要一些額外的工作，才能執行應用程式開發人員。


### <a name="sections"></a>章節

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[RecyclerView 組件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

本主題說明如何`Adapter`， `LayoutManager`，和`ViewHolder`工作協助程式類別，來支援一起`RecyclerView`。
它提供的每個協助程式類別的高階概觀，並說明您如何使用這些應用程式中。

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[基本的 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

本主題中提供的資訊為基礎[RecyclerView 組件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)藉由提供真正的程式碼範例說明如何在各種`RecyclerView`建置真實世界相片瀏覽應用程式實作項目。

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[擴充 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

本主題將額外的程式碼加入至範例應用程式中呈現[基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)示範如何處理項目按一下事件，並更新`RecyclerView`當基礎資料來源變更。


### <a name="summary"></a>總結

本指南介紹 Android `RecyclerView` widget; 它說明如何新增`RecyclerView`如何支援 Xamarin.Android 專案的程式庫`RecyclerView`回收檢視，它會強制執行檢視持有者模式，為了提高效率，以及如何在各種協助程式類別構成`RecyclerView`共同作業以顯示集合。 它提供範例程式碼，示範如何`RecyclerView`整合到應用程式，它會說明如何修改`RecyclerView`的版面配置原則插入不同的版面配置管理員，並說明如何處理項目按一下 [事件]，並通知`RecyclerView`的資料來源變更。

如需有關`RecyclerView`，請參閱[RecyclerView 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)。


## <a name="related-links"></a>相關連結

- [RecyclerViewer （範例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [棒棒糖符號簡介](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
