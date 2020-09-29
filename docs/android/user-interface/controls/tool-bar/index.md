---
title: 工具列
description: 工具列是動作列元件，可提供比預設動作列更多的彈性：它可以放在應用程式中的任何位置、可以變更其大小，也可以使用與應用程式主題不同的色彩配置。 此外，每個應用程式畫面都可以有多個工具列。
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 85d649ec464b725b5e0b438c650e0c9f32d23a27
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457678"
---
# <a name="toolbar"></a>工具列

_工具列是動作列元件，可提供比預設動作列更多的彈性：它可以放在應用程式中的任何位置、可以變更其大小，也可以使用與應用程式主題不同的色彩配置。此外，每個應用程式畫面都可以有多個工具列。_

## <a name="overview"></a>概觀

任何 Android 活動的主要設計項目都是 *動作*列。 動作列是在 Android 應用程式中用於流覽、搜尋、功能表和商標的 UI 元件。 在 android 5.0 棒糖之前的 Android 版本中，動作列 (也稱為 *應用程式行*) 是提供此功能的建議元件。 

您 `Toolbar` 可以將 Android 5.0 棒棒) 引進的 widget (，視為要取代動作列的動作列介面一般化 &ndash; 。 `Toolbar`可以在應用程式版面配置中的任何地方使用，而且比動作列更容易自訂。 下列螢幕擷取畫面說明 `Toolbar` 本指南中建立的自訂範例： 

[![工具列的範例螢幕擷取畫面，其中包含 [編輯]、[儲存] 和 [溢位] 功能表項目](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

和動作列之間有一些重要的差異 `Toolbar` ： 

- `Toolbar`可以放在使用者介面中的任何位置。

- 您可以在同一個畫面上顯示多個工具列。

- 如果使用片段，每個片段都可以有它自己的片段 `Toolbar` 。 

- `Toolbar`可以設定為只跨越螢幕的部分寬度。 

- 由於未系結 `Toolbar` 至使用中視窗 décor 的色彩配置，因此它可以有以視覺化方式區分的色彩配置。 

- 與動作列不同， `Toolbar` 不會在左邊包含圖示。 它在右側的功能表使用較少的空間。 

- `Toolbar`高度是可調整的。 

- 其他的視圖可以包含在中 `Toolbar` 。 

`Toolbar`可以包含下列一或多個元素： 

- 瀏覽按鈕

- 品牌標誌影像

- 標題與子標題

- 自訂檢視

- 動作功能表

- 溢位功能表

Google 的 [材質設計指導方針](https://material.google.com/) 會建議您利用這些元素來提供應用程式不同的外觀 (而不只依賴應用程式圖示和標題) 。 

本指南涵蓋最常使用的 `Toolbar` 案例：

- 將活動的預設動作列取代為 `Toolbar` 。 

- 將第二個加入 `Toolbar` 至活動。

- 使用 **Android 支援程式庫 V7 AppCompat** 程式庫 (在本指南的其餘部分中稱為 *AppCompat* ，) 在   `Toolbar` 舊版 Android 上進行部署。 

## <a name="requirements"></a>需求

`Toolbar` 適用于 Android 5.0 棒 (API 21) 和更新版本。 以 android 5.0 之前的 Android 版本為目標時，請使用 [Android 支援程式庫 V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)，以提供 `Toolbar` NuGet 套件中回溯相容的支援。 
[工具列相容性](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md) 說明如何使用此程式庫。 

## <a name="related-links"></a>相關連結

- [棒糖工具列 (範例) ](/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具列 (範例) ](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)