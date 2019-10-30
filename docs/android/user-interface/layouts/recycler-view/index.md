---
title: RecyclerView
description: RecyclerView 是用來顯示集合的視圖群組;其設計目的是更有彈性的取代舊版視圖群組，例如 ListView 和 GridView。  本指南說明如何在 Xamarin Android 應用程式中使用和自訂 RecyclerView。
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/03/2018
ms.openlocfilehash: bce89be8bec512ac70ca40015521c7d56f3460d3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028816"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView 是用來顯示集合的視圖群組;其設計目的是更有彈性的取代舊版視圖群組，例如 ListView 和 GridView。 本指南說明如何在 Xamarin Android 應用程式中使用和自訂 RecyclerView。_

## <a name="recyclerview"></a>RecyclerView

許多應用程式都需要顯示相同類型的集合（例如訊息、連絡人、影像或歌曲）;通常，此集合太大而無法放入螢幕上，因此集合會顯示在小視窗中，可以順暢地流覽集合中的所有專案。
`RecyclerView` 是 Android widget，可顯示清單或方格中的專案集合，讓使用者可以在集合中進行滾動。 以下是範例應用程式的螢幕擷取畫面，其使用 `RecyclerView` 在垂直捲動清單中顯示電子郵件收件匣內容：

[使用 RecyclerView 來列出收件匣訊息的![範例應用程式](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` 提供兩個引人注目的功能：

- 它具有彈性的架構，可讓您藉由插入慣用的元件來修改其行為。

- 大型集合的效率很高，因為它會重複使用專案視圖，而且需要使用*view 持*者來快取視圖參考。

本指南說明如何在 Xamarin Android 應用程式中使用 `RecyclerView`;它會說明如何將 `RecyclerView` 封裝新增至您的 Xamarin Android 專案，並說明在一般應用程式中 `RecyclerView` 函式的方式。 我們提供了實際的程式碼範例，示範如何將 `RecyclerView` 整合到您的應用程式、如何執行專案查看的點擊，以及如何在其基礎資料變更時重新整理 `RecyclerView`。 本指南假設您已熟悉 Xamarin. Android 開發。

### <a name="requirements"></a>需求

雖然 `RecyclerView` 通常與 Android 5.0 棒的相關聯，但它是以支援程式庫的形式提供 &ndash; `RecyclerView` 適用于以 API 層級7（Android 2.1）和更新版本為目標的應用程式。 在以 Xamarin 為基礎的應用程式中使用 `RecyclerView` 時，需要下列各項：

- **&ndash; xamarin. android 4.20**或更新版本必須安裝，並以 Visual Studio 或 Visual Studio for Mac 設定。

- 您的應用程式專案必須包含**v7. RecyclerView**套件。 如需安裝 NuGet 套件的詳細資訊，請參閱[逐步解說：在您的專案中包含 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。

### <a name="overview"></a>總覽

`RecyclerView` 可以視為 Android 中 `ListView` 和 `GridView` widget 的取代。 如同它的前身，`RecyclerView` 是設計用來在小視窗中顯示大型資料集，但是 `RecyclerView` 提供更多的版面配置選項，並且更適合顯示大型集合。 如果您熟悉 `ListView`，`ListView` 和 `RecyclerView`之間有幾項重要的差異：

- 使用 `RecyclerView` 稍微複雜一點：您必須撰寫更多的程式碼，才能使用 `RecyclerView` 相較于 `ListView`。

- `RecyclerView` 不提供預先定義的介面卡;您必須執行可存取資料來源的介面卡程式碼。 不過，Android 包含數個預先定義的介面卡，可與 `ListView` 和 `GridView`搭配使用。

- 當使用者按一個專案時，`RecyclerView` 不提供專案點擊事件;而是由協助程式類別來處理專案按一下事件。 相較之下，`ListView` 提供了專案按一下事件。

- `RecyclerView` 藉由回收視圖並強制執行視圖持有者模式來增強效能，這會消除不必要的配置資源查閱。 在 `ListView`中，使用視圖預留位置模式是選擇性的。

- `RecyclerView` 是以模組化設計為基礎，可讓您更輕鬆地進行自訂。 例如，您可以插入不同的版面配置原則，而不需要對應用程式進行重大的程式碼變更。
    相反地，`ListView` 在結構中相當整合。

- `RecyclerView` 包括新增和移除專案的內建動畫。 `ListView` 動畫需要在應用程式開發人員中進行一些額外的工作。

### <a name="sections"></a>章節

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[RecyclerView 元件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

本主題說明如何將 `Adapter`、`LayoutManager`和 `ViewHolder` 一起當做協助程式類別一起使用，以支援 `RecyclerView`。
它提供每個協助程式類別的高階總覽，並說明如何在您的應用程式中使用它們。

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

本主題以[RecyclerView 元件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)中提供的資訊為基礎，提供實際的程式碼範例，說明如何實行各種不同的 `RecyclerView` 元素，以建立真實世界的相片流覽應用程式。

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[擴充 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

本主題會將額外的程式碼新增至[基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)中所呈現的範例應用程式，以示範如何在基礎資料來源變更時處理專案點擊事件和更新 `RecyclerView`。

### <a name="summary"></a>總結

本指南介紹了 Android `RecyclerView` widget;文中說明如何將 `RecyclerView` 支援程式庫新增至 Xamarin. Android 專案、`RecyclerView` 如何回收視圖、如何強制執行視圖持有者模式，以及組成 `RecyclerView` 的各種 helper 類別如何共同作業來顯示集合。 它提供範例程式碼，示範如何將 `RecyclerView` 整合到應用程式中，它說明了如何藉由插入不同的版面建構管理員來量身打造 `RecyclerView`的版面配置原則，並說明如何處理專案的 click 事件和通知 `RecyclerView` 的資料來源變更。

如需 `RecyclerView`的詳細資訊，請參閱[RecyclerView 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)。

## <a name="related-links"></a>相關連結

- [RecyclerViewer （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [棒糖的簡介](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
