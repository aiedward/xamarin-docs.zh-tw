---
title: 以 Xamarin.Forms 應用程式為主題
description: Xamarin： Forms 應用程式支援主題，方法是為每個主題建立 ResourceDictionary，然後載入具有 DynamicResource 標記延伸的資源。
ms.prod: xamarin
ms.assetId: BF92AEDD-EF23-4D08-A972-B089066E75F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2020
ms.openlocfilehash: 5988437b40ac875b8b59f9af0f25d4b5c60ded97
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517135"
---
# <a name="theming-a-xamarinforms-application"></a>主題為 Xamarin 應用程式

## <a name="theme-an-application"></a>[主題應用程式](theming.md)

藉由[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)為每個主題建立，然後載入具有`DynamicResource`標記延伸的資源，可以在 Xamarin 應用程式中執行主題。

## <a name="respond-to-system-theme-changes"></a>[回應系統主題變更](system-theme-changes.md)

裝置通常包含淺色和深色的主題，分別代表一組可在作業系統層級上設定的廣泛外觀喜好設定。 應用程式應該遵守這些系統主題，並在系統主題變更時立即回應。
