---
title: Xamarin.Forms CollectionView
description: CollectionView 是富彈性且高效能的檢視，來呈現資料使用不同的版面配置規格的清單。
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 73e68f29c61661019cfc56f8caa26c6a0b1ce4ba
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65971003"
---
# <a name="xamarinforms-collectionview"></a>Xamarin.Forms CollectionView

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)是一種有彈性且高效能的檢視，來呈現資料使用不同的版面配置規格的清單。

## <a name="datapopulate-datamd"></a>[Data](populate-data.md)

A [ `CollectionView` ](xref:Xamarin.Forms.CollectionView)藉由設定資料填入其[ `ItemsSource` ](xref:Xamarin.Forms.ItemsView.ItemsSource)屬性來實作任何集合`IEnumerable`。 設定也可以定義在清單中每個項目的外觀[ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView.ItemTemplate)屬性設[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。

## <a name="layoutlayoutmd"></a>[版面配置](layout.md)

根據預設， [ `CollectionView` ](xref:Xamarin.Forms.CollectionView)會以垂直清單顯示其項目。 不過，可以指定垂直和水平清單與格線。

## <a name="selectionselectionmd"></a>[選取範圍](selection.md)

根據預設， [ `CollectionView` ](xref:Xamarin.Forms.CollectionView)選取項目已停用。 不過，您可以啟用單一和多重選取項目。

## <a name="empty-viewsemptyviewmd"></a>[空的檢視](emptyview.md)

在  [ `CollectionView` ](xref:Xamarin.Forms.CollectionView)，可以指定空的檢視，可將提供意見反應給使用者，當沒有資料可供顯示。 字串、 一個檢視或多個檢視，可以是空的檢視。

## <a name="scrollingscrollingmd"></a>[捲動](scrolling.md)

當起始捲使用者 swipes 時，就可以控制捲軸的結束位置，完整顯示的項目。 颾魤 ㄛ [ `CollectionView` ](xref:Xamarin.Forms.CollectionView)定義兩個[ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法，以程式設計方式捲動至檢視的 項目。 另將指定的項目捲動至檢視時，其中一個多載捲動到檢視中，指定索引處的項目。
