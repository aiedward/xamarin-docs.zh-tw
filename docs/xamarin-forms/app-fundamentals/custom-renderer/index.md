---
title: Xamarin.Forms自訂轉譯器
description: 自訂轉譯器可讓開發人員覆寫每個平臺上的原生控制項轉譯，以自訂控制項的外觀和行為 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/03/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: be281f9b7987a8d23ba6ac93f0771e432f277d45
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138914"
---
# <a name="xamarinforms-custom-renderers"></a>Xamarin.Forms自訂轉譯器

_Xamarin：表單使用者介面是使用目標平臺的原生控制項來轉譯，讓 Xamarin.Forms 應用程式可以為每個平臺保留適當的外觀與風格。自訂轉譯器可讓開發人員覆寫此程式，以自訂 Xamarin.Forms 每個平臺上控制項的外觀和行為。_

## <a name="introduction-to-custom-renderers"></a>[自訂轉譯器簡介](introduction.md)

自訂轉譯器提供了一種強大的方法，可自訂控制項的外觀和行為 Xamarin.Forms 。 自訂轉譯器可用於小型樣式變更或複雜的平台特定版面配置，以及行為自訂。 本文簡介如何自訂轉譯器，並概述建立自訂轉譯器的程序。

## <a name="renderer-base-classes-and-native-controls"></a>[轉譯器基類和原生控制項](renderers.md)

每 Xamarin.Forms 個控制項都有一個適用于每個平臺的轉譯器，可建立原生控制項的實例。 本文列出的轉譯器和原生控制項類別會執行每個 Xamarin.Forms 頁面、版面配置、視圖和儲存格。

## <a name="customizing-an-entry"></a>[自訂 Entry](entry.md)

Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) 控制項可讓您編輯一行文字。 本文示範如何建立 `Entry` 控制項的自訂轉譯器，讓開發人員以自己的平台特定自訂來覆寫預設原生轉譯。

## <a name="customizing-a-contentpage"></a>[自訂 ContentPage](contentpage.md)

[`ContentPage`](xref:Xamarin.Forms.ContentPage)是視覺化元素，會顯示單一視圖並佔用大部分的畫面。 本文示範如何建立 `ContentPage` 頁面的自訂轉譯器，讓開發人員以自己的平台特定自訂來覆寫預設原生轉譯。

## <a name="customizing-a-map-pin"></a>[自訂地圖釘選](map-pin.md)

Xamarin.Forms.對應提供跨平臺抽象概念，用於顯示在每個平臺上使用原生地圖 Api 的地圖，為使用者提供快速且熟悉的地圖體驗。 本主題示範如何建立控制項的自訂轉譯器 `Map` ，讓開發人員能夠以自己的平臺特定自訂來覆寫預設原生轉譯。

## <a name="customizing-a-listview"></a>[自訂 ListView](listview.md)

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 是將資料集合顯示為垂直清單的視圖。 本文示範如何建立自訂轉譯器，其會封裝平台特定清單控制項和原生資料格配置，讓您對原生清單控制效能擁有更多掌控權。

## <a name="customizing-a-viewcell"></a>[自訂 ViewCell](viewcell.md)

Xamarin.Forms [`ViewCell`](xref:Xamarin.Forms.ViewCell) 是可以加入至或的資料格 [`ListView`](xref:Xamarin.Forms.ListView) [`TableView`](xref:Xamarin.Forms.TableView) ，其中包含開發人員定義的視圖。 本文示範如何為裝載 `ViewCell` 在控制項內的建立自訂轉譯器 Xamarin.Forms `ListView` 。 這會停止在 Xamarin.Forms 滾動期間重複呼叫版面配置計算 `ListView` 。

## <a name="customizing-a-webview"></a>[自訂 WebView](hybridwebview.md)

Xamarin.Forms [`WebView`](xref:Xamarin.Forms.WebView) 是在您的應用程式中顯示 WEB 和 HTML 內容的視圖。 本文說明如何建立自訂轉譯器，以擴充 `WebView` 以允許從 JavaScript 叫用 c # 程式碼。

## <a name="implementing-a-view"></a>[實作檢視](view.md)

Xamarin.Forms自訂使用者介面控制項應該衍生自 [`View`](xref:Xamarin.Forms.View) 類別，用來在螢幕上放置版面配置和控制項。 本文示範如何建立自訂控制項的自訂轉譯器 Xamarin.Forms ，以用來從裝置相機顯示預覽影片串流。

## <a name="implementing-a-video-player"></a>[執行影片播放機](video-player/index.md)

本文說明如何編寫轉譯器以實作 `VideoPlayer` 控制項，此控制項可從 Web、內嵌為應用程式資源的影片、儲存於使用者裝置上影片庫中的影片，來播放影片。 已示範數種技術，其中包括實作方法和唯讀的可繫結屬性。
