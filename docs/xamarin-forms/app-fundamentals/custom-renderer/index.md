---
title: Xamarin.Forms 自訂轉譯器
description: 自訂轉譯器可讓開發人員覆寫的每個平台，以自訂外觀和行為的 Xamarin.Forms 控制項中的原生控制項呈現。
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: c7ae25688b2f8635a9a89318e0b307e58add7a5a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998738"
---
# <a name="xamarinforms-custom-renderers"></a>Xamarin.Forms 自訂轉譯器

_Xamarin.Forms 的使用者介面會使用目標平台，讓 Xamarin.Forms 應用程式保留適當的外觀及操作，每個平台的原生控制項呈現的。自訂轉譯器可讓開發人員覆寫的外觀和行為的每個平台上的 Xamarin.Forms 控制項來自訂此程序。_

## <a name="introduction-to-custom-renderersintroductionmd"></a>[自訂轉譯器簡介](introduction.md)

自訂轉譯器會提供自訂的外觀和行為的 Xamarin.Forms 控制項的強大方法。 他們可以使用小型的樣式變更或複雜的平台特定版面配置和行為的自訂。 本文章簡介如何自訂轉譯器，並概述的程序建立自訂轉譯器。

## <a name="renderer-base-classes-and-native-controlsrenderersmd"></a>[轉譯器的基底類別和原生控制項](renderers.md)

每個 Xamarin.Forms 控制項已隨附的轉譯器，每個平台建立原生控制項的執行個體。 本文列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms 頁面、 版面配置、 檢視和資料格。

## <a name="customizing-an-entryentrymd"></a>[自訂項目](entry.md)

Xamarin.Forms [ `Entry` ](xref:Xamarin.Forms.Entry)控制項，可讓 「 單行文字編輯。 這篇文章示範如何建立自訂轉譯器`Entry`控制項，讓開發人員覆寫預設原生呈現自己的平台特定自訂。

## <a name="customizing-a-contentpagecontentpagemd"></a>[自訂 ContentPage](contentpage.md)

A [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)是可見的項目，可顯示的單一檢視，並佔用大部分的螢幕。 這篇文章示範如何建立自訂轉譯器`ContentPage`頁面上，讓開發人員覆寫預設原生呈現自己的平台特定自訂。

## <a name="customizing-a-mapmapindexmd"></a>[自訂地圖](map/index.md)

Xamarin.Forms.Maps 提供顯示讓使用者使用原生 Api 上每個平台，來提供快速且熟悉的對應體驗的地圖的地圖的跨平台抽象概念。 本主題示範如何建立自訂轉譯器的`Map`控制項，讓開發人員覆寫預設原生呈現自己的平台特定自訂。

## <a name="customizing-a-listviewlistviewmd"></a>[自訂 ListView](listview.md)

Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView)是以垂直清單顯示的資料集合的檢視。 這篇文章會示範如何建立自訂轉譯器會封裝特定平台清單控制項和原生的儲存格的版面配置，允許更充分掌控原生清單控制效能。

## <a name="customizing-a-viewcellviewcellmd"></a>[自訂 ViewCell](viewcell.md)

Xamarin.Forms [ `ViewCell` ](xref:Xamarin.Forms.ViewCell)是可加入的資料格[ `ListView` ](xref:Xamarin.Forms.ListView)或是[ `TableView` ](xref:Xamarin.Forms.TableView)，其中包含開發人員定義的檢視。 這篇文章示範如何建立自訂轉譯器`ViewCell`Xamarin.Forms 內裝載`ListView`控制項。 如此一來重複呼叫期間 Xamarin.Forms 版面配置計算`ListView`捲動。

## <a name="implementing-a-viewviewmd"></a>[實作檢視](view.md)

Xamarin.Forms 自訂使用者介面控制項應該衍生自[ `View` ](xref:Xamarin.Forms.View)類別，用來放置版面配置和螢幕上的控制項。 這篇文章會示範如何建立 Xamarin.Forms 自訂控制項是用來顯示裝置的相機中的預覽視訊資料流的自訂轉譯器。

## <a name="implementing-a-hybridwebviewhybridwebviewmd"></a>[實作 HybridWebView](hybridwebview.md)

這篇文章示範如何建立自訂轉譯器`HybridWebView`自訂控制項，其示範如何加強平台專屬的 web 控制項，以允許從 JavaScript 的 C# 程式碼來叫用。

## <a name="implementing-a-video-playervideo-playerindexmd"></a>[實作影片播放程式](video-player/index.md)

這篇文章說明如何撰寫實作自訂的轉譯器`VideoPlayer`控制項可以播放從 web，影片內嵌為應用程式資源或使用者的裝置上的影片庫中儲存的影片的影片。 示範數種技術，包括實作的方法和唯讀的可繫結屬性。


## <a name="related-links"></a>相關連結

- [Effects](~/xamarin-forms/app-fundamentals/effects/index.md)
- [自訂轉譯器 （Xamarin University 影片）](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
- [自訂轉譯器 （Xamarin University 影片） 範例](http://bit.ly/xf-customrenderer)
