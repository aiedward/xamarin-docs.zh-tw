---
title: Xamarin.Forms Shell 簡介
description: Xamarin.Forms Shell 會提供大部分應用程式所需的基本功能，包括常見的導覽使用者體驗、以 URI 為基礎的導覽配置，以及整合式搜尋處理常式。
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 215bf467c6f00e45d3e00e10438b01d238050504
ms.sourcegitcommit: f2942b518f51317acbb263be5bc0c91e66239f50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590320"
---
# <a name="no-locxamarinforms-shell-introduction"></a>Xamarin.Forms Shell 簡介

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell 會提供大部分行動應用程式所需的基本功能，藉此降低行動應用程式開發的複雜度，包括：

- 說明應用程式視覺階層的單一位置。
- 常見的導覽使用者體驗。
- 允許導覽至應用程式中任何頁面的 URI 式導覽配置。
- 整合式搜尋處理常式。

此外，Shell 應用程式的優點是轉譯速度更快，且記憶體使用量更少。

> [!IMPORTANT]
> 現有的應用程式可採用 Shell，立即受益於導覽、效能與擴充性等改進功能。

## <a name="platform-support"></a>平台支援

Xamarin.Forms Shell 已在 iOS 和 Android 上完整提供，但通用 Windows 平臺 (UWP) 只部分可用。 此外，Shell 目前在 UWP 上為實驗性，而且只能透過在呼叫 `Forms.Init` 之前將下列程式碼行新增至 UWP 專案中的 `App` 類別來使用：

```csharp
global::Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

如需 UWP 上 Shell 狀態的詳細資訊，請參閱 github.com 上的[ Xamarin.Forms Shell 專案面板](https://github.com/xamarin/Xamarin.Forms/projects/54)。

## <a name="shell-navigation-experience"></a>Shell 導覽體驗

Shell 會根據飛出視窗和索引標籤，提供固定的導覽體驗。 Shell 應用程式中的最上層導覽是飛出視窗或底部索引標籤列，具體取決於應用程式的導覽要求。 下列範例示範最上層導覽為飛出視窗的應用程式：

[![iOS 與 Android 上的 Shell 飛出視窗螢幕擷取畫面](introduction-images/flyout.png "Shell 飛出視窗")](introduction-images/flyout-large.png#lightbox "Shell 飛出視窗")

選取飛出視窗項目會產生代表要選取並顯示之項目的底部索引標籤：

[![iOS 與 Android 上的 Shell 底部索引標籤螢幕擷取畫面](introduction-images/monkeys.png "Shell 底部索引標籤")](introduction-images/monkeys-large.png#lightbox "Shell 底部索引標籤")

> [!NOTE]
> 未開啟飛出視窗時，底部的索引標籤列可視為應用程式中導覽的最上層。

每個索引標籤都會顯示 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。 不過，如果底部索引標籤包含多個頁面，則可透過頂端索引標籤列導覽頁面：

[![iOS 與 Android 上的 Shell 頂端索引標籤螢幕擷取畫面](introduction-images/cats.png "Shell 頂端索引標籤")](introduction-images/cats-large.png#lightbox "Shell 頂端索引標籤")

在每個索引標籤中，您 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 可以流覽其他物件：

[![iOS 與 Android 上的 Shell 頁面巡覽螢幕擷取畫面](introduction-images/cat-details.png "Shell 應用程式導覽")](introduction-images/cat-details-large.png#lightbox "Shell 應用程式導覽")

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
