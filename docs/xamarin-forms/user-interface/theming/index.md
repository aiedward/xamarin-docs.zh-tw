---
title: 主題 Xamarin.Forms 應用程式
description: Xamarin.Forms應用程式支援主題，方法是為每個主題建立 ResourceDictionary，然後載入具有 DynamicResource 標記延伸的資源。
ms.prod: xamarin
ms.assetId: BF92AEDD-EF23-4D08-A972-B089066E75F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 80660ae7d3af0fe5948a5ae4ffdb35d2f9c2a40f
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136132"
---
# <a name="theming-a-xamarinforms-application"></a>主題 Xamarin.Forms 應用程式

## <a name="theme-an-application"></a>[主題應用程式](theming.md)

藉 Xamarin.Forms 由建立 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 每個主題的，然後載入具有標記延伸的資源，即可在應用程式中執行主題 `DynamicResource` 。

## <a name="respond-to-system-theme-changes"></a>[回應系統主題變更](system-theme-changes.md)

裝置通常包含淺色和深色的主題，分別代表一組可在作業系統層級上設定的廣泛外觀喜好設定。 應用程式應該遵守這些系統主題，並在系統主題變更時立即回應。
