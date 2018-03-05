---
title: "自訂轉譯器"
description: "使用允許保留每個平台適當的外觀及操作 Xamarin.Forms 應用程式的目標平台的原生控制項轉譯 Xamarin.Forms 使用者介面。 自訂轉譯器可讓開發人員覆寫以自訂外觀和行為 Xamarin.Forms 控制項，在每個平台上的這個程序。"
ms.topic: article
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: a92da0320addf1569c25ed05873aa11a198b1daa
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="custom-renderers"></a>自訂轉譯器

_使用允許保留每個平台適當的外觀及操作 Xamarin.Forms 應用程式的目標平台的原生控制項轉譯 Xamarin.Forms 使用者介面。自訂轉譯器可讓開發人員覆寫以自訂外觀和行為 Xamarin.Forms 控制項，在每個平台上的這個程序。_

## <a name="introduction-to-custom-renderersintroductionmd"></a>[自訂轉譯器簡介](introduction.md)

自訂轉譯器會提供強大的方法用於自訂的外觀和 Xamarin.Forms 控制項的行為。 它們可以用於小型的樣式變更或複雜的平台專屬版面配置和自訂行為。 這篇文章提供自訂的轉譯器的簡介，並且摘要說明建立自訂轉譯器的程序。

## <a name="renderer-base-classes-and-native-controlsrenderersmd"></a>[轉譯器的基底類別和原生控制項](renderers.md)

Xamarin.Forms 中的每個控制項都有隨附的轉譯器，每個平台建立原生控制項的執行個體。 本文列出的轉譯器和原生控制項類別可實作每個 Xamarin.Forms 頁面、 版面配置、 檢視和資料格。

## <a name="customizing-an-entryentrymd"></a>[自訂項目](entry.md)

Xamarin.Forms [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項可讓單行編輯的文字。 本文示範如何建立自訂轉譯器`Entry`控制項，讓開發人員覆寫預設原生呈現使用他們自己平台專屬的自訂。

## <a name="customizing-a-contentpagecontentpagemd"></a>[自訂 ContentPage](contentpage.md)

A [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)是視覺化的項目會顯示在單一檢視，而且會佔用大部分的螢幕。 本文示範如何建立自訂轉譯器`ContentPage`頁面上，讓開發人員覆寫預設原生呈現使用他們自己平台專屬的自訂。

## <a name="customizing-a-mapmapindexmd"></a>[自訂地圖](map/index.md)

Xamarin.Forms.Maps 提供抽象的跨平台，來顯示使用者使用的每個平台上，服務 Api 用於提供快速且熟悉的對應會遇到的原生對應的對應。 本主題示範如何建立自訂轉譯器的`Map`控制項，讓開發人員覆寫預設原生呈現使用他們自己平台專屬的自訂。

## <a name="customizing-a-listviewlistviewmd"></a>[自訂 ListView](listview.md)

Xamarin.Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)是以垂直清單顯示的資料集合的檢視。 本文示範如何建立自訂轉譯器會封裝特定平台清單控制項和原生的儲存格的版面配置，允許更充分掌控原生清單控制效能。

## <a name="customizing-a-viewcellviewcellmd"></a>[自訂 ViewCell](viewcell.md)

Xamarin.Forms [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)是可以加入的資料格[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)或[ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/)，其中包含開發人員定義的檢視。 本文示範如何建立自訂轉譯器`ViewCell`Xamarin.Forms 內裝載`ListView`控制項。 這會停止從重複呼叫期間 Xamarin.Forms 配置計算`ListView`捲動。

## <a name="implementing-a-viewviewmd"></a>[實作檢視](view.md)

Xamarin.Forms 自訂使用者介面控制項應衍生自[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)類別，用來放置版面配置和螢幕上的控制項。 本文示範如何建立自訂轉譯器用來顯示預覽視訊資料流裝置的相機中的 Xamarin.Forms 自訂控制項。

## <a name="implementing-a-hybridwebviewhybridwebviewmd"></a>[實作 HybridWebView](hybridwebview.md)

本文示範如何建立自訂轉譯器`HybridWebView`示範如何以加強平台專屬 web 控制項可讓 C# 程式碼叫用從 JavaScript 中的自訂控制項。

## <a name="implementing-a-video-playervideo-playerindexmd"></a>[實作影片播放器](video-player/index.md)

本文將說明如何撰寫實作自訂的轉譯器`VideoPlayer`可以播放從 web、 影片內嵌為應用程式資源或儲存在使用者的裝置上的視訊媒體櫃中的視訊的視訊的控制項。 示範數種技術，包括實作方法和唯讀的可繫結屬性。 


## <a name="related-links"></a>相關連結

- [Effects](~/xamarin-forms/app-fundamentals/effects/index.md)
- [自訂轉譯器 （Xamarin 大學影片）](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
- [自訂轉譯器 （Xamarin 大學影片） 範例](http://bit.ly/xf-customrenderer)
