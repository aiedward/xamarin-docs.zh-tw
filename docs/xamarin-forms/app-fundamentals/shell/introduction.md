---
title: Xamarin.Forms Shell 簡介
description: Xamarin.Forms Shell 會提供大部分應用程式所需的基本功能，包括常見的導覽使用者體驗、以 URI 為基礎的導覽配置，以及整合式搜尋處理常式。
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/15/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1863481dface7e215764ea4673f2d18ece4e2a87
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101757706"
---
# <a name="xamarinforms-shell-introduction"></a>Xamarin.Forms Shell 簡介

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell 會提供大部分行動應用程式所需的基本功能，藉此降低行動應用程式開發的複雜度，包括：

- 說明應用程式視覺階層的單一位置。
- 常見的導覽使用者體驗。
- 允許導覽至應用程式中任何頁面的 URI 式導覽配置。
- 整合式搜尋處理常式。

此外，Shell 應用程式的優點是轉譯速度更快，且記憶體使用量更少。

> [!IMPORTANT]
> 現有的應用程式可採用 Shell，立即受益於導覽、效能與擴充性等改進功能。

## <a name="application-visual-hierarchy"></a>應用程式視覺階層

在 Xamarin.Forms Shell 應用程式中，會在子類別化類別的類別中描述應用程式的視覺化階層 [`Shell`](xref:Xamarin.Forms.Shell) 。 此類別可包含三個主要階層式物件：

1. [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 或 [`TabBar`](xref:Xamarin.Forms.TabBar) 。 `FlyoutItem`代表飛出視窗中的一或多個專案，且應在應用程式的導覽模式需要飛出視窗時使用。 代表底部索引標籤列 `TabBar` ，當應用程式的導覽模式以底部索引標籤開始，且不需要飛出視窗時，就應該使用此選項。
1. [`Tab`](xref:Xamarin.Forms.Tab)，代表分組的內容，可依底部索引標籤導覽。
1. [`ShellContent`](xref:Xamarin.Forms.ShellContent)，代表每個索引標籤的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件。

這些物件不代表任何使用者介面，而是應用程式視覺階層的組織。 Shell 將會採用這些物件，並產生用於內容的導覽使用者介面。

> [!NOTE]
> 在 Shell 應用程式中依需求建立頁面，以回應導覽。

如需詳細資訊，請參閱 [建立 Xamarin.Forms Shell 應用程式](~/xamarin-forms/app-fundamentals/shell/create.md)。

## <a name="navigation-user-experience"></a>流覽使用者體驗

Shell 所提供的導覽體驗是以 flyouts 和索引標籤 Xamarin.Forms 為基礎。 Shell 應用程式中的最上層導覽是飛出視窗或底部索引標籤列，具體取決於應用程式的導覽要求。 下列範例示範最上層導覽為飛出視窗的應用程式：

[![螢幕擷取畫面： iOS 和 Android 上的 Shell 飛出視窗](introduction-images/flyout.png)](introduction-images/flyout-large.png#lightbox)

在此範例中，某些飛出視窗專案會複製為定位字元列專案。 不過，您也可以從飛出視窗中存取專案。 選取飛出視窗項目會產生代表要選取並顯示之項目的底部索引標籤：

[![IOS 和 Android 上 Shell 底部索引標籤的螢幕擷取畫面](introduction-images/cats.png)](introduction-images/cats-large.png#lightbox)

> [!NOTE]
> 未開啟飛出視窗時，底部的索引標籤列可視為應用程式中導覽的最上層。

索引標籤列上的每個索引標籤都會顯示 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。 不過，如果底部索引標籤包含多個頁面，則可透過頂端索引標籤列導覽頁面：

[![IOS 和 Android 上 Shell 頂端索引標籤的螢幕擷取畫面](introduction-images/dogs.png)](introduction-images/dogs-large.png#lightbox)

在每個索引標籤中，您 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 可以流覽其他稱為詳細資料頁面的物件，如下所示：

[![螢幕擷取畫面： iOS 和 Android 上的 Shell 頁面導覽](introduction-images/dogdetails.png)](introduction-images/dogdetails-large.png#lightbox)

Shell 使用以 URI 為基礎的流覽體驗，該體驗會使用路由導覽至應用程式中的任何頁面，而不需要遵循設定的導覽階層。 此外，它還提供向後巡覽的能力，而不需瀏覽導覽堆疊上的所有頁面。 如需詳細資訊，請參閱[ Xamarin.Forms Shell 導覽](~/xamarin-forms/app-fundamentals/shell/navigation.md)。

## <a name="search"></a>搜尋

Xamarin.Forms Shell 包含類別所提供的整合式搜尋功能 [`SearchHandler`](xref:Xamarin.Forms.SearchHandler) 。 您可以藉由在其中加入子類別化物件，將搜尋功能加入至頁面 `SearchHandler` 。 這會導致在頁面頂端加入搜尋方塊。 在 [搜尋] 方塊中輸入資料時，[搜尋建議] 區域會填入資料：

[![螢幕擷取畫面： iOS 和 Android 上的 Shell 搜尋](introduction-images/search.png)](introduction-images/search-large.png#lightbox)

然後，當從搜尋建議區域選取結果時，即可執行自訂邏輯，例如流覽至詳細資料頁面。

如需詳細資訊，請參閱[ Xamarin.Forms Shell 搜尋](~/xamarin-forms/app-fundamentals/shell/search.md)。

## <a name="platform-support"></a>平台支援

Xamarin.Forms Shell 已在 iOS 和 Android 上完整提供，但在通用 Windows 平臺上僅部分可用 (UWP) 。 此外，Shell 目前在 UWP 上為實驗性，而且只能透過在呼叫 `Forms.Init` 之前將下列程式碼行新增至 UWP 專案中的 `App` 類別來使用：

```csharp
global::Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

如需 UWP 上 Shell 狀態的詳細資訊，請參閱 github.com 上的[ Xamarin.Forms Shell 專案面板](https://github.com/xamarin/Xamarin.Forms/projects/54)。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [建立 Xamarin.Forms Shell 應用程式](~/xamarin-forms/app-fundamentals/shell/create.md)
- [Xamarin.Forms Shell 導覽](~/xamarin-forms/app-fundamentals/shell/navigation.md)
- [Xamarin.Forms Shell 搜尋](~/xamarin-forms/app-fundamentals/shell/search.md)
