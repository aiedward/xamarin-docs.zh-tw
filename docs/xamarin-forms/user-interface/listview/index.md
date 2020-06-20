---
title: Xamarin.FormsListView
description: 本指南介紹 Xamarin.Forms ListView，可以用來在互動式清單中呈現資料。
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a1ff8dd5c8a8a4051cea8ce4b288c42bdbaa8d31
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139902"
---
# <a name="xamarinforms-listview"></a>Xamarin.FormsListView

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)

[`ListView`](xref:Xamarin.Forms.ListView)是用來呈現資料清單的視圖，特別是需要滾動的長清單。

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)這是使用不同的版面配置規格來呈現資料清單的視圖。 它的目的是要提供更具彈性且更高效能的替代方案 [`ListView`](xref:Xamarin.Forms.ListView) 。 如需詳細資訊，請參閱[ Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)。

## <a name="use-cases"></a>使用案例

`ListView`在您要顯示可滾動資料清單的任何情況下，都可以使用控制項。 `ListView`類別支援內容動作和資料系結。

`ListView`控制項不應該與 [`TableView`](~/xamarin-forms/user-interface/tableview.md) 控制項混淆。 `TableView`每當您有選項或資料的非系結清單時，控制項就是較好的選項，因為它允許在 XAML 中指定預先定義的選項。 例如，iOS 設定應用程式（大部分預先定義的選項組）較適合使用而不是 `TableView` `ListView` 。

`ListView`類別不支援在 XAML 中定義清單專案，您必須搭配使用 `ItemsSource` 屬性或資料系結， `ItemTemplate` 以定義清單中的專案。

`ListView`最適合用於由單一資料類型組成的集合。 這項需求是因為清單中的每個資料列都只能使用一種資料格類型。 `TableView`控制項可以支援多個資料格類型，因此當您需要顯示多個資料類型時，這是較好的選項。

如需將資料系結至實例的詳細資訊 `ListView` ，請參閱[ListView 資料來源](~/xamarin-forms/user-interface/listview/data-and-databinding.md)。

## <a name="components"></a>單元

`ListView`控制項有許多元件可用來執行每個平臺的原生功能。 這些元件會在下列各節中定義。

### <a name="headers-and-footers"></a>[頁首和頁尾](customizing-list-appearance.md#headers-and-footers)

頁首和頁尾元件會顯示在清單的開頭和結尾，與清單的資料分開。 頁首和頁尾可以系結至 ListView 資料來源中的個別資料來源。

### <a name="groups"></a>[群組](customizing-list-appearance.md#grouping)

中的資料 `ListView` 可以分組以方便流覽。 群組通常是資料系結的。 下列螢幕擷取畫面顯示 `ListView` 具有群組資料的：

[![「ListView 中的群組資料」](images/grouping-depth-cropped.png)](images/grouping-depth.png#lightbox "ListView 中的群組資料")

### <a name="cells"></a>[資料格](customizing-cell-appearance.md)

中的資料項目 `ListView` 稱為資料格。 每個資料格都會對應到一個資料列。 有內建的儲存格可供選擇，您也可以定義自己的自訂資料格。 內建和自訂資料格都可以在 XAML 或程式碼中使用/定義。

- [內建資料格](customizing-cell-appearance.md#built-in-cells)（例如 `TextCell` 和 `ImageCell` ）會對應至原生控制項，而且效能特別高。
  - 會 [`TextCell`](customizing-cell-appearance.md#textcell) 顯示文字字串，選擇性地包含詳細資料文字。 詳細文字會轉譯為較小字型中具有輔色的第二行。
  - 會 [`ImageCell`](customizing-cell-appearance.md#imagecell) 顯示含有文字的影像。 以影像顯示 `TextCell` 在左側。
- [自訂資料格](customizing-cell-appearance.md#custom-cells)可用來呈現複雜的資料。 例如，您可以使用自訂資料格來呈現包含專輯和演出者的歌曲清單。

下列螢幕擷取畫面顯示 `ListView` 具有 ImageCell 專案的：

[![「ImageCell ListView 中的專案」](images/image-cell-default-cropped.png)](images/image-cell-default.png#lightbox "ListView 中的 ImageCell 專案")

若要深入瞭解自訂中的資料格 `ListView` ，請參閱[自訂 ListView 儲存格外觀](customizing-cell-appearance.md)。

## <a name="functionality"></a>功能

`ListView`類別支援多種互動樣式。

- [[提取至](interactivity.md#pull-to-refresh)重新整理] 可讓使用者向下提取以重新整理 `ListView` 內容。
- [內容動作](interactivity.md#context-actions)可讓開發人員指定個別清單專案的自訂動作。 例如，您可以在 iOS 上執行輕觸動作，或在 Android 上進行長點查看動作。
- [選取](interactivity.md#selection-and-taps)可讓開發人員將功能附加至清單專案上的選取範圍和 deselection 事件。

下列螢幕擷取畫面顯示 `ListView` 具有內容動作的：

[![「ListView 中的內容動作」](images/context-default-cropped.png)](images/context-default.png#lightbox "ListView 中的內容動作")

若要深入瞭解的互動性功能 `ListView` ，請參閱[使用 ListView & 互動的動作](interactivity.md)。

## <a name="related-links"></a>相關連結

- [使用 ListView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistview)
- [雙向系結（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
- [內建資料格（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [自訂資料格（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [群組（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [自訂轉譯器視圖（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative/)
- [ListView 互動性（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
