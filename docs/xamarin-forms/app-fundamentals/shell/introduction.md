---
title: Xamarin.Forms Shell 簡介
description: Xamarin.Forms Shell 提供大部分應用程式需要的基本功能，包括常見的導覽使用者體驗、URI 式導覽配置，以及整合式搜尋處理常式。
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: 3e63a580bbdb1c220d44b100725cdc8ce387b405
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696524"
---
# <a name="xamarinforms-shell-introduction"></a>Xamarin.Forms Shell 簡介

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell 會提供大部分行動應用程式需要的基本功能，藉此降低行動應用程式開發的複雜度，這些基本功能包括：

- 說明應用程式視覺階層的單一位置。
- 常見的導覽使用者體驗。
- 允許導覽至應用程式中任何頁面的 URI 式導覽配置。
- 整合式搜尋處理常式。

此外，Shell 應用程式的優點是轉譯速度更快，且記憶體使用量更少。

> [!IMPORTANT]
> 現有的應用程式可以從流覽、效能和擴充性的改善，立即採用 Shell 和優點。

## <a name="platform-support"></a>平台支援

在 iOS 和 Android 上，已完全提供 Forms Shell，但只有部分可在通用 Windows 平臺（UWP）上使用。 此外，在 UWP 上，Shell 目前為實驗性，而且只能透過將下列程式程式碼新增至 UWP 專案中的 `App` 類別，然後再呼叫 `Forms.Init`：

```csharp
global::Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

## <a name="shell-navigation-experience"></a>Shell 導覽體驗

Shell 會根據飛出視窗和索引標籤，提供固定的導覽體驗。 Shell 應用程式中的最上層導覽是飛出視窗或底部索引標籤列，具體取決於應用程式的導覽要求。 下列範例示範最上層導覽為飛出視窗的應用程式：

[![IOS 和 Android 上 Shell 飛出視窗的螢幕擷取畫面](introduction-images/flyout.png "Shell 飛出視窗")](introduction-images/flyout-large.png#lightbox "Shell 飛出視窗")

選取飛出視窗項目會產生代表要選取並顯示之項目的底部索引標籤：

[![在 iOS 和 Android 上 Shell 底端索引標籤的螢幕擷取畫面](introduction-images/monkeys.png "Shell 底端索引標籤")](introduction-images/monkeys-large.png#lightbox "Shell 底端索引標籤")

> [!NOTE]
> 未開啟飛出視窗時，底部的索引標籤列可視為應用程式中導覽的最上層。

每個索引標籤都會顯示一個 [`ContentPage`](xref:Xamarin.Forms.ContentPage)。 不過，如果底部索引標籤包含多個頁面，則可透過頂端索引標籤列導覽頁面：

[![在 iOS 和 Android 上 Shell 頂端索引標籤的螢幕擷取畫面](introduction-images/cats.png "Shell 頂端索引標籤")](introduction-images/cats-large.png#lightbox "Shell 頂端索引標籤")

在每個索引標籤中，都可以導覽至其他 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件：

[![Shell 頁面流覽在 iOS 和 Android 上的螢幕擷取畫面](introduction-images/cat-details.png "Shell 應用程式導覽")](introduction-images/cat-details-large.png#lightbox "Shell 應用程式導覽")

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
