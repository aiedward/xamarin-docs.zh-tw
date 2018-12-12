---
title: Xamarin.Forms 導覽
description: 本指南說明如何在 Xamarin.Forms 應用程式執行導覽。 Xamarin.Forms 會根據使用的頁面類型，提供多種不同的網頁導覽體驗。
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 202f044ebd7dd5b110b94d2aa60eeb7151150607
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994724"
---
# <a name="xamarinforms-navigation"></a>Xamarin.Forms 導覽

_Xamarin.Forms 會根據使用的頁面類型，提供多種不同的網頁導覽體驗。_

![](images/page-types.png "Xamarin.Forms 頁面類型")

## <a name="hierarchical-navigationhierarchicalmd"></a>[階層式導覽](hierarchical.md)

[`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 類別提供的階層式導覽體驗讓使用者能夠視需要，向前及向後導覽頁面。 此類別會實作一堆後進先出 (LIFO) 的 [`Page`](xref:Xamarin.Forms.Page) 物件導覽。

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 由索引標籤和較大的詳細資料區域清單所組成，每個索引標籤會將內容載入至詳細資料區域中。

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

Xamarin.Forms [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 讓使用者像使用資源庫般，能從一邊撥動到另一邊的頁面，以便導覽內容頁面。

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

Xamarin.Forms [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 是可管理兩個相關資訊頁面的頁面，包含會顯示項目的主版頁面，以及詳細資料頁面，顯示的是主版頁面上的項目相關詳細資料。

## <a name="modal-pagesmodalmd"></a>[強制回應頁面](modal.md)

Xamarin.Forms 也支援強制回應頁面。 強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。

## <a name="displaying-pop-upspop-upsmd"></a>[顯示快顯視窗](pop-ups.md)

Xamarin.Forms 提供兩個類似快顯視窗的使用者介面元素：警示和動作工作表。 這些介面元素可用於詢問使用者簡單的問題，並引導使用者完成工作。
