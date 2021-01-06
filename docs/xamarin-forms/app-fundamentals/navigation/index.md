---
title: Xamarin.Forms 導航
description: 本指南說明如何在應用程式中執行導覽 Xamarin.Forms 。 Xamarin.Forms 提供許多不同的頁面導覽體驗，視使用的頁面類型而定。
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d976048b15c1fc545e1fbdc6c911e3cb4542d4f2
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939091"
---
# <a name="no-locxamarinforms-navigation"></a>Xamarin.Forms 導航

_Xamarin.Forms 提供許多不同的頁面導覽體驗，視使用的頁面類型而定。_

![：：：非 loc (Xamarin. Forms) ：：：頁面類型](images/page-types.png)

或者， Xamarin.Forms Shell 應用程式使用以 URI 為基礎的流覽體驗，而不會強制執行設定的導覽階層。 如需詳細資訊，請參閱[ Xamarin.Forms Shell 導覽](~/xamarin-forms/app-fundamentals/shell/navigation.md)。

## <a name="hierarchical-navigation"></a>[階層式導覽](hierarchical.md)

此 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 類別提供階層式導覽體驗，讓使用者能夠視需要，向前及向後流覽頁面。 類別會將導覽實作為後進先出 (LIFO) [`Page`](xref:Xamarin.Forms.Page) 物件堆疊。

## <a name="tabbedpage"></a>[TabbedPage](tabbed-page.md)

是由索引標籤 Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 清單和較大的詳細資料區域所組成，每個索引標籤會將內容載入至詳細資料區域。

## <a name="carouselpage"></a>[CarouselPage](carousel-page.md)

Xamarin.Forms [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 是使用者可以從側邊滑到一邊以流覽內容頁面的頁面，例如資源庫。

## <a name="flyoutpage"></a>[FlyoutPage](flyoutpage.md)

Xamarin.Forms [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) 是管理兩個相關資訊頁面的頁面–顯示專案的飛出視窗頁面，以及顯示 [飛出視窗] 頁面上專案相關詳細資料的詳細資料頁面。

## <a name="modal-pages"></a>[強制回應頁面](modal.md)

Xamarin.Forms 也提供模式頁面的支援。 強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。
