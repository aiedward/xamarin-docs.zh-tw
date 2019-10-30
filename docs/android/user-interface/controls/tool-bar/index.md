---
title: ToolBar
description: 工具列是一個動作列元件，提供比預設動作列更多的彈性：它可以放在應用程式中的任何位置，也可以變更其大小，也可以使用不同于應用程式主題的色彩配置。 此外，每個應用程式畫面都可以有多個工具列。
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 3f4a9393d8ef6ef54ba3dba29f1109080f1e321a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029096"
---
# <a name="toolbar"></a>ToolBar

_工具列是一個動作列元件，提供比預設動作列更多的彈性：它可以放在應用程式中的任何位置，也可以變更其大小，也可以使用不同于應用程式主題的色彩配置。此外，每個應用程式畫面都可以有多個工具列。_

## <a name="overview"></a>總覽

任何 Android 活動的主要設計項目都是*動作*列。 動作列是在 Android 應用程式中用於導覽、搜尋、功能表和品牌化的 UI 元件。 在 android 5.0 棒的 Android 版本之前，動作列（也稱為*應用程式行*）是提供這項功能的建議元件。 

您可以將 `Toolbar` widget （在 Android 5.0 棒糖中引進）視為動作列介面的一般化，&ndash; 它是用來取代動作列。 `Toolbar` 可以在應用程式版面配置中的任何位置使用，而且比動作列更容易自訂。 下列螢幕擷取畫面說明本指南中所建立的自訂 `Toolbar` 範例： 

[![具有 [編輯]、[儲存] 和 [溢位] 功能表項目之工具列的範例螢幕擷取畫面](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

`Toolbar` 和動作列之間有一些重要的差異： 

- `Toolbar` 可以放在使用者介面中的任何位置。

- 可以在同一個畫面上顯示多個工具列。

- 如果使用片段，每個片段都可以有自己的 `Toolbar`。 

- `Toolbar` 可以設定為只跨越螢幕的部分寬度。 

- 因為 `Toolbar` 不會系結至使用中視窗 décor 的色彩配置，所以它可以有一個視覺上相異的色彩配置。 

- 不同于動作列，`Toolbar` 不會在左側包含圖示。 它在右側的功能表使用較少的空間。 

- `Toolbar` 高度是可調整的。 

- 其他視圖可以包含在 `Toolbar`內。 

`Toolbar` 可以包含下列一個或多個元素： 

- 瀏覽按鈕

- 品牌標誌影像

- 標題和副標題

- 自訂視圖

- 動作功能表

- 溢位功能表

Google 的[材質設計指導方針](https://material.google.com/)建議您使用這些元素，讓應用程式具有不同的外觀（而不是只依賴應用程式圖示和標題）。 

本指南涵蓋最常使用的 `Toolbar` 案例：

- 將活動的預設動作列取代為 `Toolbar`。 

- 將第二個 `Toolbar` 加入至活動。

- 使用**Android 支援程式庫 V7 AppCompat**程式庫（在本指南的其餘部分稱為「 *AppCompat* 」），在舊版 Android 上部署 `Toolbar`。 

## <a name="requirements"></a>需求

`Toolbar` 適用于 Android 5.0 棒糖（API 21）和更新版本。 以 android 5.0 之前的 Android 版本為目標時，請使用[Android 支援程式庫 V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)，它會在 NuGet 套件中提供回溯相容的 `Toolbar` 支援。 
[工具列相容性](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md)說明如何使用此程式庫。 

## <a name="related-links"></a>相關連結

- [棒糖工具列（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具列（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
