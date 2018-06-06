---
title: CocosSharp 2D 遊戲引擎
description: 提供使用 CocosSharp 遊戲開發各種文章此文件連結。 連結的內容描述範例應用程式、 繪圖、 動畫和更多。
ms.prod: xamarin
ms.assetid: 5E72869D-3541-408B-AB64-D34C777AFB79
author: charlespetzold
ms.author: chape
ms.date: 03/29/2018
ms.openlocfilehash: 34d22b6e9d8c671e38fbadab0edafa2f57c566e7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783242"
---
# <a name="cocossharp-2d-game-engine"></a>CocosSharp 2D 遊戲引擎

_CocosSharp 是用於建置 2D 遊戲使用 C# 和 F # 程式庫。它是.NET 連接埠的熱門 Cocos2D 引擎。_

## <a name="introduction-to-cocossharp"></a>CocosSharp 簡介

CocosSharp 2D 遊戲引擎提供技術來進行跨平台遊戲。 如需支援的平台的完整清單，請參閱[CocosSharp GitHub 上的 wiki](https://github.com/mono/CocosSharp/wiki)。
這些指南使用 C# 程式碼範例，雖然 CocosSharp 以及使用 F # 完全正常運作。

所提供的核心 CocosSharp [MonoGame framework](http://www.monogame.net/)、 其本身是跨平台、 硬體加速應用程式開發介面匯入資產提供圖形、 音訊、 遊戲狀態管理、 輸入和內容的管線。
CocosSharp 是適合 2D 遊戲有效率的抽象層。
此外，較大的遊戲可以執行其核心程式庫以外自己最佳化遊戲越來越複雜。 換句話說，CocosSharp 提供混合便於使用及效能，讓開發人員快速上手不限制遊戲的大小或複雜度。

這個實際操作的影片示範如何建立簡單的跨平台 CocosSharp 遊戲：

> [!Video https://channel9.msdn.com/Shows/Visual-Studio-Toolbox/Developing-Cross-platform-2D-Games-in-C-and-CocosSharp/player]

## <a name="bouncinggamegraphics-gamescocossharpbouncing-gamemd"></a>[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)

![BouncingGame](images/bouncing-game.png "BouncingGame")

本指南說明 BouncingGame，包括如何使用遊戲的內容，用來建置遊戲、 加入遊戲邏輯和多個不同的視覺元素。

## <a name="fruity-falls-gamegraphics-gamescocossharpfruity-fallsmd"></a>[Fruity 落遊戲](~/graphics-games/cocossharp/fruity-falls.md)

![Fruity 落遊戲螢幕擷取畫面](images/fruity-falls.png "Fruity 落遊戲螢幕擷取畫面")

本指南說明 Fruity 落遊戲時，涵蓋常見 CocosSharp 和遊戲開發概念，例如物理、 內容管理、 遊戲的狀態和遊戲的設計。  

## <a name="coin-time-gamegraphics-gamescocossharpcointimemd"></a>[參考時間遊戲](~/graphics-games/cocossharp/cointime.md)

![錢幣式時間遊戲螢幕擷取畫面](images/cointime.png "錢幣時間遊戲螢幕擷取畫面")

參考時間是完整的 platformer 適用於 iOS 和 Android 遊戲。 遊戲的目標是收集所有層級中錢幣，然後避免敵人和障礙到達結束媒體櫃門。

## <a name="drawing-geometry-with-ccdrawnodegraphics-gamescocossharpccdrawnodemd"></a>[繪製與 CCDrawNode 幾何](~/graphics-games/cocossharp/ccdrawnode.md)

![以 CCDrawNode 繪製圖形](images/ccdrawnode.png "CCDrawNode 以繪製的圖形")

CCDrawNode 提供繪圖基本的物件，例如線條、 圓形和三角形的方法。

## <a name="animating-with-ccactiongraphics-gamescocossharpccactionmd"></a>[使用 CCAction 建立動畫](~/graphics-games/cocossharp/ccaction.md)

![CCAction 動畫](images/ccaction.png "CCAction 動畫")

`CCAction` 是可用來建立動畫 CocosSharp 物件的基底類別。 本指南涵蓋了內建`CCAction`實作一般工作，例如位置、 縮放和旋轉。 它也會查看如何建立自訂實作繼承自`CCAction`。

## <a name="using-tiled-with-cocossharpgraphics-gamescocossharptiledmd"></a>[搭配 CocosSharp 使用 Tiled](~/graphics-games/cocossharp/tiled.md)

![遊戲中的層級](images/tiled.png "遊戲中的層級")

並排顯示是一個功能強大、 有彈性，並建立垂直及等磚的完整應用程式將對應的遊戲。 CocosSharp 提供內建整合磚塊的原生檔案格式。

## <a name="entities-in-cocossharpgraphics-gamescocossharpentitiesmd"></a>[CocosSharp 中的實體](~/graphics-games/cocossharp/entities.md)

![從遊戲太空船](images/entities.png "太空船遊戲")

實體模式是強大的方式來組織的遊戲程式碼。 它有助於改善可讀性，讓程式碼更易於維護，並利用內建的父/子功能。

## <a name="handling-multiple-resolutions-in-cocossharpgraphics-gamescocossharpresolutionsmd"></a>[處理 CocosSharp 在多種解析度](~/graphics-games/cocossharp/resolutions.md)

![表示螢幕解析度方格](images/resolutions.png "方格，代表螢幕解析度")

本指南示範如何使用 CocosSharp 開發會正確顯示不同解析度的裝置的遊戲。

## <a name="cocossharp-content-pipelinegraphics-gamescocossharpcontent-pipelineindexmd"></a>[CocosSharp 內容管線](~/graphics-games/cocossharp/content-pipeline/index.md)

![XNB](images/content-pipeline.png "XNB")

內容的管線常用在遊戲開發來最佳化內容，並設定其格式，可以載入特定的硬體上或具有特定遊戲開發架構。

## <a name="improving-frame-rate-with-ccspritesheetgraphics-gamescocossharpccspritesheetmd"></a>[提高畫面播放速率與 CCSpriteSheet](~/graphics-games/cocossharp/ccspritesheet.md)

![樹狀結構中的從 CCSpriteSheet](images/ccspritesheet.png "CCSpriteSheet 從樹狀結構")

`CCSpriteSheet` 提供合併，並使用一種材質中的多個映像檔案的功能。 減少紋理計數可以改善遊戲的載入次數與畫面播放速率。

## <a name="texture-caching-using-cctexturecachegraphics-gamescocossharptexture-cachemd"></a>[使用 CCTextureCache 紋理快取](~/graphics-games/cocossharp/texture-cache.md)

![如何 CocosSharp 快取映像的表示法](images/texture-cache.png "CocosSharp 如何快取映像的表示法")

CocosSharp 的`CCTextureCache`類別會提供標準方式組織，快取，並卸載內容。 

## <a name="2d-math-with-cocossharpgraphics-gamescocossharpmathmd"></a>[與 CocosSharp 2D 數學運算](~/graphics-games/cocossharp/math.md)

![旋轉影像](images/math.png "旋轉影像")

本指南涵蓋 2D 數學開發遊戲。 它使用 CocosSharp 示範如何執行一般的遊戲開發工作，並說明這些工作背後的數學運算。

## <a name="performance-and-visual-effects-with-ccrendertexturegraphics-gamescocossharpccrendertexturemd"></a>[效能和使用 CCRenderTexture 的視覺效果](~/graphics-games/cocossharp/ccrendertexture.md)

![從遊戲精靈](images/ccrendertexture.png "遊戲精靈")

`CCRenderTexture`類別提供的功能來呈現為單一材質的多個 CocosSharp 物件。 一旦建立，`CCRenderTexture`可以使用執行個體，才能有效呈現圖形，以及實作視覺效果。
