---
title: Xamarin.Forms 自訂轉譯器
description: 自訂轉譯器可讓開發人員覆寫每個平台上原生控制項的轉譯，以自訂 Xamarin.Forms 控制項的外觀和行為。
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
---

# <a name="xamarinforms-custom-renderers"></a>Xamarin.Forms 自訂轉譯器

_Xamarin.Forms 使用者介面使用目標平台的原生控制項來轉譯，讓 Xamarin.Forms 應用程式保留每個平台適當的外觀及操作。自訂轉譯器可讓開發人員覆寫此程序，以自訂每個平台上 Xamarin.Forms 控制項的外觀和行為。_

## <a name="introduction-to-custom-renderersintroductionmd"></a>[自訂轉譯器簡介](introduction.md)

自訂轉譯器提供自訂 Xamarin.Forms 控制項外觀和行為的有力方法。 自訂轉譯器可用於小型樣式變更或複雜的平台特定版面配置，以及行為自訂。 本文簡介如何自訂轉譯器，並概述建立自訂轉譯器的程序。

## <a name="renderer-base-classes-and-native-controlsrenderersmd"></a>[轉譯器基底類別和原生控制項](renderers.md)

每個 Xamarin.Forms 控制項都伴有每個平台的轉譯器，這些平台可建立原生控制項的執行個體。 本文列出可實作每個 Xamarin.Forms 頁面、版面配置、檢視和資料格的轉譯器和原生控制項類別。

## <a name="customizing-an-entryentrymd"></a>[自訂項目](entry.md)

Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) 控制項允許單行文字的編輯。 本文示範如何建立 `Entry` 控制項的自訂轉譯器，讓開發人員以自己的平台特定自訂來覆寫預設原生轉譯。

## <a name="customizing-a-contentpagecontentpagemd"></a>[自訂 ContentPage](contentpage.md)

[`ContentPage`](xref:Xamarin.Forms.ContentPage) 是可見的元素，會顯示單一檢視，並佔用螢幕的大部分空間。 本文示範如何建立 `ContentPage` 頁面的自訂轉譯器，讓開發人員以自己的平台特定自訂來覆寫預設原生轉譯。

## <a name="customizing-a-mapmapindexmd"></a>[自訂地圖](map/index.md)

Xamarin.Forms.Maps 提供用於顯示地圖的跨平台抽象概念，這些地圖在每個平台上使用原生地圖 API，可為使用者提供快速且熟悉的地圖體驗。 本主題示範如何建立 `Map` 控制項的自訂轉譯器，讓開發人員以自己的平台特定自訂來覆寫預設原生轉譯。

## <a name="customizing-a-listviewlistviewmd"></a>[自訂 ListView](listview.md)

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 是將資料集合顯示為垂直清單的檢視。 本文示範如何建立自訂轉譯器，其會封裝平台特定清單控制項和原生資料格版面配置，讓您對原生清單控制效能擁有更多掌控權。

## <a name="customizing-a-viewcellviewcellmd"></a>[自訂 ViewCell](viewcell.md)

Xamarin.Forms [`ViewCell`](xref:Xamarin.Forms.ViewCell) 是可以新增至 [`ListView`](xref:Xamarin.Forms.ListView) 或 [`TableView`](xref:Xamarin.Forms.TableView) 的資料格，包含開發人員定義的檢視。 本文示範如何建立 `ViewCell` 的自訂轉譯器，裝載於 Xamarin.Forms `ListView` 控制項內。 這會停止在 `ListView` 捲動期間重複呼叫 Xamarin.Forms 版面配置計算。

## <a name="implementing-a-viewviewmd"></a>[實作檢視](view.md)

Xamarin.Forms 自訂使用者介面控制項應該衍生自用來在螢幕上放置配置和控制項的 [`View`](xref:Xamarin.Forms.View) 類別。 本文示範如何建立 Xamarin.Forms 自訂控制項的自訂轉譯器，用以從裝置相機顯示預覽視訊資料流。

## <a name="implementing-a-hybridwebviewhybridwebviewmd"></a>[實作 HybridWebView](hybridwebview.md)

本文示範如何建立 `HybridWebView` 自訂控制項的自訂轉譯器，該自訂控制項會示範如何強化平台特定的 Web 控制項，以允許從 JavaScript 叫用 C# 程式碼。

## <a name="implementing-a-video-playervideo-playerindexmd"></a>[實作影片播放程式](video-player/index.md)

本文說明如何編寫轉譯器以實作 `VideoPlayer` 控制項，此控制項可從 Web、內嵌為應用程式資源的影片、儲存於使用者裝置上影片庫中的影片，來播放影片。 已示範數種技術，其中包括實作方法和唯讀的可繫結屬性。


## <a name="related-links"></a>相關連結

- [Effects](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Custom Renderers (Xamarin University Video)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/) (自訂轉譯器 (Xamarin University 影片))
