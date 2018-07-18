---
title: Xamarin.Forms 導覽
description: 本指南說明如何在 Xamarin.Forms 應用程式執行導覽。 Xamarin.Forms 提供許多不同的網頁瀏覽體驗，所使用的頁面類型而定。
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 202f044ebd7dd5b110b94d2aa60eeb7151150607
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994724"
---
# <a name="xamarinforms-navigation"></a>Xamarin.Forms 導覽

_Xamarin.Forms 提供許多不同的網頁瀏覽體驗，所使用的頁面類型而定。_

![](images/page-types.png "Xamarin.Forms 頁面類型")

## <a name="hierarchical-navigationhierarchicalmd"></a>[階層式導覽](hierarchical.md)

[`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 類別提供的階層式導覽體驗讓使用者能夠視需要，向前及向後導覽頁面。 此類別會實作一堆後進先出 (LIFO) 的 [`Page`](xref:Xamarin.Forms.Page) 物件導覽。

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

Xamarin.Forms [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)清單所組成的索引標籤和較大的 [詳細資料] 區域中，使用內容載入詳細資料區域的每個索引標籤。

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

Xamarin.Forms [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)是使用者可以向側邊撥動可巡覽的內容，例如資源庫頁面的頁面。

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

Xamarin.Forms [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)是管理兩個頁面的相關資訊-顯示項目、 主版頁面和主版頁面顯示項目的相關詳細資料的詳細資料頁面的頁面。

## <a name="modal-pagesmodalmd"></a>[強制回應頁面](modal.md)

Xamarin.Forms 也有提供支援強制回應頁面。 強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。

## <a name="displaying-pop-upspop-upsmd"></a>[顯示快顯視窗](pop-ups.md)

Xamarin.Forms 提供兩個快顯註冊類似使用者介面項目： 警示和動作的工作表。 詢問使用者簡單的問題，並引導使用者完成工作，可以使用這些介面項目。
