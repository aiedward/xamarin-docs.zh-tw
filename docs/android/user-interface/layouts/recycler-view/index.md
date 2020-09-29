---
title: RecyclerView
description: RecyclerView 是用於顯示集合的視圖群組;它是專為較舊的視圖群組（例如 ListView 和 GridView）提供更有彈性的取代。  本指南說明如何在 Xamarin. Android 應用程式中使用和自訂 RecyclerView。
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/03/2018
ms.openlocfilehash: a972f6686103dc9b3a1317d15985cbf3dc78bd82
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457054"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView 是用於顯示集合的視圖群組;它是專為較舊的視圖群組（例如 ListView 和 GridView）提供更有彈性的取代。 本指南說明如何在 Xamarin. Android 應用程式中使用和自訂 RecyclerView。_

## <a name="recyclerview"></a>RecyclerView

許多應用程式都需要顯示相同類型的集合 (例如訊息、連絡人、影像或歌曲) ;此集合通常太大而無法放入畫面上，因此集合會顯示在可順暢地滾動集合中所有專案的小型視窗中。
`RecyclerView` 是一個 Android widget，可顯示清單或方格中的專案集合，讓使用者可以滾動收集。 以下是範例應用程式的螢幕擷取畫面，用 `RecyclerView` 來顯示垂直捲動清單中的電子郵件收件匣內容：

[![使用 RecyclerView 列出收件匣訊息的範例應用程式](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` 提供兩種引人注目的功能：

- 它有彈性的架構，可讓您藉由插入慣用的元件來修改其行為。

- 很有效率地使用大型集合，因為它會重複使用專案視圖，而且需要使用 *view 持有* 者來快取視圖參考。

本指南說明如何 `RecyclerView` 在 Xamarin android 應用程式中使用，它會說明如何將 `RecyclerView` 套件新增至您的 Xamarin. android 專案，並說明如何 `RecyclerView` 在一般應用程式中使用函數。 提供實際的程式碼範例，示範如何整合 `RecyclerView` 至您的應用程式、如何執行專案視圖的點擊，以及如何 `RecyclerView` 在其基礎資料變更時重新整理。 本指南假設您已熟悉 Xamarin. Android 開發。

### <a name="requirements"></a>需求

雖然 `RecyclerView` 通常與 Android 5.0 棒的相關聯，但它是以支援程式庫的形式提供，其適用于以 &ndash; `RecyclerView` API 層級7為目標的應用程式 (Android 2.1) 和更新版本。 以下是 `RecyclerView` 在 Xamarin 架構應用程式中使用的必要條件：

- **Xamarin.Android**您 &ndash; 必須安裝 Visual Studio 或 Visual Studio for Mac，才能安裝並設定 Xamarin. Android xamarin. android 4.20 或更新版本。

- 您的應用程式專案必須包含 **v7. RecyclerView** 套件。 如需安裝 NuGet 套件的詳細資訊，請參閱 [逐步解說：在您的專案中包含 NuGet](/visualstudio/mac/nuget-walkthrough)。

### <a name="overview"></a>概觀

`RecyclerView` 可以視為 Android 中的和 widget 的取代 `ListView` `GridView` 。 如同前身，它的 `RecyclerView` 設計是在小視窗中顯示大型資料集，但 `RecyclerView` 會提供更多配置選項，且更適合用來顯示大型集合。 如果您熟悉 `ListView` ，與之間有幾個重要的差異 `ListView` `RecyclerView` ：

- `RecyclerView` 要使用的程式碼稍微複雜一點：您必須撰寫更多的程式碼，以便與搭配使用 `RecyclerView` `ListView` 。

- `RecyclerView` 未提供預先定義的介面卡;您必須執行可存取資料來源的介面卡程式碼。 不過，Android 包含數個可搭配和使用的預先定義的介面卡 `ListView` `GridView` 。

- `RecyclerView` 當使用者按一下專案時，不會提供專案點擊事件;相反地，專案按一下事件是由 helper 類別處理。 相反地，會 `ListView` 提供專案 click 事件。

- `RecyclerView` 藉由回收 views 和強制執行視圖器模式來增強效能，以消除不必要的版面配置資源查閱。 在中，您可以選擇使用「視圖持有人」模式 `ListView` 。

- `RecyclerView` 是以模組化設計為基礎，可讓您更輕鬆地進行自訂。 例如，您可以插入不同的配置原則，而不需要對應用程式進行重大的程式碼變更。
    相反地， `ListView` 在結構中是相對的。

- `RecyclerView` 包含專案加入和移除的內建動畫。 `ListView` 動畫需要一些額外的工作，才能讓應用程式開發人員參與。

### <a name="sections"></a>章節

#### <a name="recyclerview-parts-and-functionality"></a>[RecyclerView 元件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

本主題說明如何將 `Adapter` 、 `LayoutManager` 和做 `ViewHolder` 為協助程式類別一起運作，以支援 `RecyclerView` 。
它提供每個 helper 類別的概要說明，並說明如何在應用程式中使用它們。

#### <a name="a-basic-recyclerview-example"></a>[基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

本主題是以 [RecyclerView 元件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) 中提供的資訊為基礎，提供各種元素如何實行， `RecyclerView` 以建立真實世界的相片流覽應用程式的實際程式碼範例。

#### <a name="extending-the-recyclerview-example"></a>[擴充 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

本主題將額外的程式碼新增至 [基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) 中所顯示的範例應用程式，以示範如何 `RecyclerView` 在基礎資料來源變更時處理專案點按事件和更新。

### <a name="summary"></a>摘要

本指南引進了 Android `RecyclerView` widget; 其中說明了如何將 `RecyclerView` 支援程式庫新增至 Xamarin. Android 專案、如何 `RecyclerView` 回收、如何強制執行視圖器模式，以提升效率，以及構成如何 `RecyclerView` 共同作業來顯示集合的各種協助程式類別。 它提供的範例程式碼示範如何 `RecyclerView` 整合至應用程式中，並說明如何藉 `RecyclerView` 由插入不同的版面建構管理員來量身打造版面配置原則，並說明如何處理專案點擊事件，以及通知 `RecyclerView` 資料來源變更。

如需的詳細資訊 `RecyclerView` ，請參閱 [RecyclerView 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)。

## <a name="related-links"></a>相關連結

- [RecyclerViewer (範例) ](/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [棒棒的簡介](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)