---
title: RecyclerView
description: RecyclerView 是用來顯示集合的視圖群組;其設計目的是更有彈性的取代舊版視圖群組，例如 ListView 和 GridView。  本指南說明如何在 Xamarin Android 應用程式中使用和自訂 RecyclerView。
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/03/2018
ms.openlocfilehash: e6c5f6e19599624899f74b99dcaaae734d098b3a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764199"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView 是用來顯示集合的視圖群組;其設計目的是更有彈性的取代舊版視圖群組，例如 ListView 和 GridView。本指南說明如何在 Xamarin Android 應用程式中使用和自訂 RecyclerView。_

## <a name="recyclerview"></a>RecyclerView

許多應用程式都需要顯示相同類型的集合（例如訊息、連絡人、影像或歌曲）;通常，此集合太大而無法放入螢幕上，因此集合會顯示在小視窗中，可以順暢地流覽集合中的所有專案。
`RecyclerView`是 Android widget，可顯示清單或方格中的專案集合，讓使用者可以在集合中進行滾動。 以下是範例應用程式的螢幕擷取畫面，其使用`RecyclerView`在垂直捲動清單中顯示電子郵件收件匣內容：

[![使用 RecyclerView 列出收件匣訊息的範例應用程式](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView`提供兩種強大的功能：

- 它具有彈性的架構，可讓您藉由插入慣用的元件來修改其行為。

- 大型集合的效率很高，因為它會重複使用專案視圖，而且需要使用*view 持*者來快取視圖參考。

本指南說明如何在 xamarin `RecyclerView` android 應用程式中使用`RecyclerView` ，它會說明如何`RecyclerView`將套件新增至您的 xamarin 專案，並說明一般應用程式中的函式。 我們提供了實際的程式碼範例，示範如何`RecyclerView`將整合到您的應用程式、如何執行專案查看的點擊，以及`RecyclerView`如何在其基礎資料變更時重新整理。 本指南假設您已熟悉 Xamarin. Android 開發。

### <a name="requirements"></a>需求

雖然`RecyclerView`通常與 Android 5.0 棒的相關聯，但它是以支援連結&ndash;庫`RecyclerView`的形式提供，適用于以 API 層級7（Android 2.1）和更新版本為目標的應用程式。 下列是在以 Xamarin 為`RecyclerView`基礎的應用程式中使用的必要條件：

- 您必須安裝並設定 Visual Studio 或 Visual Studio for Mac 的**xamarin android** &ndash; xamarin. android 4.20 或更新版本。

- 您的應用程式專案必須包含**v7. RecyclerView**套件。 如需安裝 NuGet 套件的詳細資訊， [請參閱逐步解說：在您的專案](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)中包含 NuGet。

### <a name="overview"></a>總覽

`RecyclerView`可視為 Android 中`ListView`和`GridView` widget 的取代。 如同其前身， `RecyclerView`設計為在小視窗中顯示大型資料集，但`RecyclerView`提供更多的版面配置選項，並且更適合顯示大型集合。 如果您已熟悉`ListView`，和`RecyclerView`之間`ListView`有幾項重要的差異：

- `RecyclerView`使用起來稍微複雜一點：您必須撰寫比更多的程式碼`RecyclerView`來使用`ListView`。

- `RecyclerView`不提供預先定義的介面卡;您必須執行可存取資料來源的介面卡程式碼。 不過，Android 包含數個可與`ListView`和`GridView`搭配使用的預先定義介面卡。

- `RecyclerView`當使用者按了專案時，不會提供專案按的事件;而是由協助程式類別來處理專案按一下事件。 相反地， `ListView`會提供專案按一下事件。

- `RecyclerView`藉由回收視圖並強制執行視圖持有者模式來增強效能，這會排除不必要的配置資源查閱。 在中`ListView`，使用視圖預留位置模式是選擇性的。

- `RecyclerView`是以模組化設計為基礎，可讓您更輕鬆地進行自訂。 例如，您可以插入不同的版面配置原則，而不需要對應用程式進行重大的程式碼變更。
    相反地， `ListView`在結構中相當整合。

- `RecyclerView`包括新增和移除專案的內建動畫。 `ListView`動畫需要在應用程式開發人員中進行一些額外的工作。

### <a name="sections"></a>章節

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[RecyclerView 元件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

本主題說明`Adapter`、 `ViewHolder`和如何`LayoutManager`搭配協助程式類別一起使用，以支援`RecyclerView`。
它提供每個協助程式類別的高階總覽，並說明如何在您的應用程式中使用它們。

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

本主題以[RecyclerView 元件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)中提供的資訊為基礎，提供實際的程式碼範例， `RecyclerView`說明如何實行各種元素來建立真實世界的相片流覽應用程式。

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[擴充 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

本主題會將額外的程式碼新增至[基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)中所呈現的範例應用程式，以示範如何在基礎`RecyclerView`資料來源變更時處理專案點擊事件和更新。

### <a name="summary"></a>總結

本指南介紹了 Android `RecyclerView` widget，說明如何`RecyclerView`將支援程式庫新增至 Xamarin Android 專案、如何`RecyclerView`回收視圖、如何強制執行視圖預留位置模式，以及各種組成`RecyclerView`共同作業以顯示集合的 helper 類別。 它提供範例程式碼，示範`RecyclerView`如何將整合到應用程式中，它說明了`RecyclerView`如何藉由插入不同的版面建構管理員來量身打造版面配置原則，並說明如何處理專案 click 事件並通知`RecyclerView`的資料來源變更。

如需的詳細`RecyclerView`資訊，請參閱[RecyclerView 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)。

## <a name="related-links"></a>相關連結

- [RecyclerViewer （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [棒糖的簡介](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
