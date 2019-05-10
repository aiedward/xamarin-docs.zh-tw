---
title: Xamarin.Forms CollectionView
description: CollectionView 是富彈性且高效能的檢視，來呈現資料使用不同的版面配置規格的清單。
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: a6cb6e695a4c19627b183060a7636320f8083ee2
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65048134"
---
# <a name="xamarinforms-collectionview"></a>Xamarin.Forms CollectionView

![](~/media/shared/preview.png "此 API 是目前發行前版本")

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

`CollectionView`是一種有彈性且高效能的檢視，來呈現資料使用不同的版面配置規格的清單。

## <a name="datapopulate-datamd"></a>[Data](populate-data.md)

A`CollectionView`藉由設定資料填入其`ItemsSource`屬性來實作任何集合`IEnumerable`。 設定也可以定義在清單中每個項目的外觀`ItemTemplate`屬性，以[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。

## <a name="layoutlayoutmd"></a>[版面配置](layout.md)

根據預設，`CollectionView`會以垂直清單顯示其項目。 不過，可以指定垂直和水平清單與格線。

## <a name="selectionselectionmd"></a>[選取範圍](selection.md)

根據預設，`CollectionView`選取項目已停用。 不過，您可以啟用單一和多重選取項目。

## <a name="empty-viewsemptyviewmd"></a>[空的檢視](emptyview.md)

在  `CollectionView`，可以指定空的檢視，可將提供意見反應給使用者，當沒有資料可供顯示。 字串、 一個檢視或多個檢視，可以是空的檢視。

## <a name="scrollingscrollingmd"></a>[捲動](scrolling.md)

當起始捲使用者 swipes 時，就可以控制捲軸的結束位置，完整顯示的項目。 颾魤 ㄛ`CollectionView`會定義兩個`ScrollTo`方法，以程式設計方式捲動至檢視的 項目。 另將指定的項目捲動至檢視時，其中一個多載捲動到檢視中，指定索引處的項目。
