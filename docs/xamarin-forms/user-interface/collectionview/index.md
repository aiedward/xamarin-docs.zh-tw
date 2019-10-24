---
title: Xamarin. Forms CollectionView
description: CollectionView 是彈性且高效能的觀點，可讓您使用不同的版面配置規格來呈現資料清單。
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 8050d952556ce0b55a7ce72bc5f25de903fee6e5
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697005"
---
# <a name="xamarinforms-collectionview"></a>Xamarin. Forms CollectionView

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

[@No__t_1](xref:Xamarin.Forms.CollectionView)是一種彈性且高效能的觀點，可讓您使用不同的版面配置規格來呈現資料清單。

## <a name="datapopulate-datamd"></a>[Data](populate-data.md)

[@No__t_1](xref:Xamarin.Forms.CollectionView)會藉由將其[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)屬性設定為任何會執行 `IEnumerable` 的集合來填入資料。 將 [ [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) ] 屬性設定為 [ [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)]，即可定義清單中每個專案的外觀。

## <a name="layoutlayoutmd"></a>[版面配置](layout.md)

根據預設， [`CollectionView`](xref:Xamarin.Forms.CollectionView)會在垂直清單中顯示其專案。 不過，您可以指定垂直和水準清單和格線。

## <a name="selectionselectionmd"></a>[選取範圍](selection.md)

預設會停用[`CollectionView`](xref:Xamarin.Forms.CollectionView)選取專案。 不過，可以啟用單一和多重選取。

## <a name="empty-viewsemptyviewmd"></a>[空白的視圖](emptyview.md)

在[`CollectionView`](xref:Xamarin.Forms.CollectionView)中，您可以指定空的視圖，以在沒有資料可供顯示時提供意見反應給使用者。 空白的視圖可以是字串、視圖或多個視圖。

## <a name="scrollingscrollingmd"></a>[捲動](scrolling.md)

當使用者撥動起始捲軸時，可以控制捲軸的結束位置，以便完全顯示專案。 此外， [`CollectionView`](xref:Xamarin.Forms.CollectionView)會定義兩個[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法，以程式設計方式將專案滾動到 view。 其中一個多載會將指定索引處的專案滾動到 view，而另一個則會將指定的專案滾動到 view。

## <a name="groupinggroupingmd"></a>[群組](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以將其 `IsGrouped` 屬性設定為 `true`，以顯示正確群組的資料。
