---
title: Xamarin.Forms 自訂轉譯器
description: 自訂轉譯器可讓開發人員覆寫每個平台上原生控制項的轉譯，以自訂 Xamarin.Forms 控制項的外觀和行為。
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/03/2019
ms.openlocfilehash: b87e713f89951d03408fa559bcf6e02cdae65e28
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "74824238"
---
# <a name="xamarinforms-custom-renderers"></a>Xamarin.Forms 自訂轉譯器

_Xamarin.Forms 使用者介面使用目標平臺的本機控制件呈現,允許 Xamarin.Forms 應用程式為每個平臺保留適當的外觀。自定義呈現器允許開發人員重寫此過程,以自定義每個平臺上 Xamarin.Forms 控制件的外觀和行為。_

## <a name="introduction-to-custom-renderers"></a>[自訂轉譯器簡介](introduction.md)

自訂轉譯器提供自訂 Xamarin.Forms 控制項外觀和行為的有力方法。 自訂轉譯器可用於小型樣式變更或複雜的平台特定版面配置，以及行為自訂。 本文簡介如何自訂轉譯器，並概述建立自訂轉譯器的程序。

## <a name="renderer-base-classes-and-native-controls"></a>[轉譯器基本類別和本機控制項](renderers.md)

每個 Xamarin.Forms 控制項都具有每個平台的轉譯器，這些轉譯器可建立原生控制項的執行個體。 本文列出可實作每個 Xamarin.Forms 頁面、配置、檢視和資料格的轉譯器和原生控制項類別。

## <a name="customizing-an-entry"></a>[自訂項目](entry.md)

Xamarin.Forms[`Entry`](xref:Xamarin.Forms.Entry)控制件允許編輯一行文本。 本文示範如何建立 `Entry` 控制項的自訂轉譯器，讓開發人員以自己的平台特定自訂來覆寫預設原生轉譯。

## <a name="customizing-a-contentpage"></a>[自訂 ContentPage](contentpage.md)

A[`ContentPage`](xref:Xamarin.Forms.ContentPage)是顯示單個視圖並佔據大部分螢幕的可視元素。 本文示範如何建立 `ContentPage` 頁面的自訂轉譯器，讓開發人員以自己的平台特定自訂來覆寫預設原生轉譯。

## <a name="customizing-a-map"></a>[自訂地圖](map/index.md)

Xamarin.Forms.Maps 提供用於顯示地圖的跨平台抽象概念，這些地圖在每個平台上使用原生地圖 API，可為使用者提供快速且熟悉的地圖體驗。 本主題示範如何建立 `Map` 控制項的自訂轉譯器，讓開發人員以自己的平台特定自訂來覆寫預設原生轉譯。

## <a name="customizing-a-listview"></a>[自訂 ListView](listview.md)

Xamarin.Forms[`ListView`](xref:Xamarin.Forms.ListView)是一個檢視,它將數據集合顯示為垂直清單。 本文示範如何建立自訂轉譯器，其會封裝平台特定清單控制項和原生資料格配置，讓您對原生清單控制效能擁有更多掌控權。

## <a name="customizing-a-viewcell"></a>[自訂 ViewCell](viewcell.md)

Xamarin.Forms[`ViewCell`](xref:Xamarin.Forms.ViewCell)是儲存格,可以新增到 引入[`ListView`](xref:Xamarin.Forms.ListView)開發人員[`TableView`](xref:Xamarin.Forms.TableView)定義的檢視的或 。 本文示範如何建立 `ViewCell` 的自訂轉譯器，裝載於 Xamarin.Forms `ListView` 控制項內。 這會停止在 `ListView` 捲動期間重複呼叫 Xamarin.Forms 配置計算。

## <a name="customizing-a-webview"></a>[自訂 WebView](hybridwebview.md)

Xamarin.Forms[`WebView`](xref:Xamarin.Forms.WebView)是一個在應用中顯示 Web 和 HTML 內容的檢視。 本文介紹如何創建自定義呈現器,該呈現器擴展`WebView`以允許從 JavaScript 調用 C# 代碼。

## <a name="implementing-a-view"></a>[實作檢視](view.md)

Xamarin.Forms 自定義使用者介面控件應派生[`View`](xref:Xamarin.Forms.View)自 類,該類用於將佈局和控制項放在螢幕上。 本文示範如何建立 Xamarin.Forms 自訂控制項的自訂轉譯器，用以從裝置相機顯示預覽視訊資料流。

## <a name="implementing-a-video-player"></a>[實現影片播放器](video-player/index.md)

本文說明如何編寫轉譯器以實作 `VideoPlayer` 控制項，此控制項可從 Web、內嵌為應用程式資源的影片、儲存於使用者裝置上影片庫中的影片，來播放影片。 已示範數種技術，其中包括實作方法和唯讀的可繫結屬性。
