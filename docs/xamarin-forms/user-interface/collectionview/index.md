---
title: Xamarin. Forms CollectionView
description: CollectionView 是彈性且高效能的觀點, 可讓您使用不同的版面配置規格來呈現資料清單。
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: b3841ed1287c980212ce37078f38f4984393c414
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888599"
---
# <a name="xamarinforms-collectionview"></a>Xamarin. Forms CollectionView

![](~/media/shared/preview.png "此 API 目前是發行前版本")

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)是彈性且高效能的觀點, 可讓您使用不同的版面配置規格來呈現資料清單。

## <a name="datapopulate-datamd"></a>[Data](populate-data.md)

會[`CollectionView`](xref:Xamarin.Forms.CollectionView)藉由[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)將資料的屬性設定`IEnumerable`為任何可執行檔集合, 來填入資料。 您可以藉由將[`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)屬性設定[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)為, 來定義清單中每個專案的外觀。

## <a name="layoutlayoutmd"></a>[版面配置](layout.md)

根據預設, [`CollectionView`](xref:Xamarin.Forms.CollectionView)會在垂直清單中顯示其專案。 不過, 您可以指定垂直和水準清單和格線。

## <a name="selectionselectionmd"></a>[選取範圍](selection.md)

預設會停[`CollectionView`](xref:Xamarin.Forms.CollectionView)用選取範圍。 不過, 可以啟用單一和多重選取。

## <a name="empty-viewsemptyviewmd"></a>[空白的視圖](emptyview.md)

在[`CollectionView`](xref:Xamarin.Forms.CollectionView)中, 您可以指定空的視圖, 以在沒有資料可供顯示時提供意見反應給使用者。 空白的視圖可以是字串、視圖或多個視圖。

## <a name="scrollingscrollingmd"></a>[捲動](scrolling.md)

當使用者撥動起始捲軸時, 可以控制捲軸的結束位置, 以便完全顯示專案。 此外, [`CollectionView`](xref:Xamarin.Forms.CollectionView)也會定義[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)兩個方法, 以程式設計方式將專案滾動到 view。 其中一個多載會將指定索引處的專案滾動到 view, 而另一個則會將指定的專案滾動到 view。

## <a name="groupinggroupingmd"></a>[群組](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以將`IsGrouped`屬性設定為, 以`true`顯示正確群組的資料。
