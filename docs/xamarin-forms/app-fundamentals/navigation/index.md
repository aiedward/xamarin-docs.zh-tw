---
title: Xamarin.Forms 導覽
description: 本指南說明如何在 Xamarin.Forms 應用程式執行導覽。 Xamarin.Forms 會根據使用的頁面類型，提供多種不同的網頁導覽體驗。
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 682e3bd0ac4cdd651203496dd28586db2cef3165
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "66835269"
---
# <a name="xamarinforms-navigation"></a>Xamarin.Forms 導覽

_Xamarin.Forms 會根據使用的頁面類型，提供多種不同的網頁導覽體驗。_

![](images/page-types.png "Xamarin.Forms Page Types")

或者，Xamarin.Forms Shell 應用程式使用以 URI 為基礎的瀏覽體驗，該體驗不會強制執行一組瀏覽階層集合。 如需詳細資訊，請參閱 [Xamarin.Forms Shell 導覽](~/xamarin-forms/app-fundamentals/shell/navigation.md)。

## <a name="hierarchical-navigation"></a>[階層式導覽](hierarchical.md)

該[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)類提供分層導航體驗,用戶可以根據需要向前和向後導航頁面。 該類實現導航作為[`Page`](xref:Xamarin.Forms.Page)最後一個入出(LIFO)物件堆疊。

## <a name="tabbedpage"></a>[TabbedPage](tabbed-page.md)

Xamarin.Forms[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)由選項卡清單和更大的詳細資訊區域組成,每個選項卡將內容載入到詳細資訊區域中。

## <a name="carouselpage"></a>[CarouselPage](carousel-page.md)

Xamarin.Forms[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)是一個頁面,用戶可以從一邊滑動到一邊流覽內容頁面,如圖庫。

## <a name="masterdetailpage"></a>[MasterDetailPage](master-detail-page.md)

Xamarin.Forms[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)是管理兩頁相關資訊的頁面, 即顯示項目的母版頁和顯示母版頁上專案詳細資訊的詳細資訊頁。

## <a name="modal-pages"></a>[強制回應頁面](modal.md)

Xamarin.Forms 也支援強制回應頁面。 強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。
