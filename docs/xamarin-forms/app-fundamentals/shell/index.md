---
title: Xamarin.Forms Shell
description: 本指南說明如何使用 Xamarin.Forms Shell，其會提供大部分應用程式需要的基本功能，藉此降低 Xamarin.Forms 應用程式的複雜度。
ms.prod: xamarin
ms.assetid: 85B322AA-808F-41B6-953A-5877264AE643
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 7699b39a6df6c64ae9a481d9171f23dc6a8eba57
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65054188"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

![](~/media/shared/preview.png "此 API 目前是發行前版本")

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

Xamarin.Forms Shell 會提供大部分行動應用程式需要的基本功能，藉此降低行動應用程式開發的複雜度。 這包括一般導覽使用者體驗、URI 型導覽配置，以及整合式搜尋處理常式。

## <a name="flyoutflyoutmd"></a>[飛出視窗](flyout.md)

飛出視窗為 Shell 應用程式的根功能表，且可透過圖示或從螢幕側邊撥動來存取。 飛出視窗會由選用標題、飛出視窗項目及選用功能表項目所組成。

## <a name="tabstabsmd"></a>[索引標籤](tabs.md)

飛出視窗之後，Shell 應用程式中的下一個導覽層級是底部的索引標籤列。 當一個索引標籤包含多個頁面時，將可透過頂端索引標籤導覽頁面。

## <a name="navigationnavigationmd"></a>[巡覽](navigation.md)

Shell 應用程式可以利用 URI 型導覽配置，使用路由導覽至應用程式中的任何頁面，而不需要遵循設定的導覽階層。

## <a name="searchsearchmd"></a>[搜尋](search.md)

Shell 應用程式可以使用能夠新增至每個頁面頂端的搜尋方塊所提供的整合式搜尋功能。

## <a name="custom-rendererscustomrenderersmd"></a>[自訂轉譯器](customrenderers.md)

Shell 應用程式可透過各種不同 Shell 類別公開的屬性與方法進行高度自訂。 不過，它也能夠在需要更複雜的平台專用自訂時，建立 Shell 自訂轉譯器。
