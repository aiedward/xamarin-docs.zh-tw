---
title: CocosSharp 的 2D 遊戲引擎
description: 使用 CocosSharp 的遊戲開發有關的各種文件到這個文件連結。 連結的內容會說明範例應用程式、 繪圖、 動畫等等。
ms.prod: xamarin
ms.assetid: 5E72869D-3541-408B-AB64-D34C777AFB79
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: add73360ea98d8c516e413f0cc0264f68c58d79d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61024391"
---
# <a name="cocossharp-2d-game-engine"></a>CocosSharp 的 2D 遊戲引擎

_CocosSharp 是用於建置使用的 2D 遊戲的程式庫C#和F#。它是受歡迎的 Cocos2D 引擎移植至.NET。_

## <a name="introduction-to-cocossharp"></a>CocosSharp 簡介

CocosSharp 的 2D 遊戲引擎提供技術的製作跨平台遊戲。 如需支援的平台的完整清單[CocosSharp GitHub 上的 wiki](https://github.com/mono/CocosSharp/wiki)。
這些指南使用C#如需程式碼範例，雖然 CocosSharp 都可以正常使用F#以及。

所提供的核心 CocosSharp [MonoGame framework](http://www.monogame.net/)本身是跨平台、 硬體加速 API 的匯入資產提供圖形、 音訊、 遊戲狀態管理、 輸入和內容的管線。
CocosSharp 是適合的 2D 遊戲的有效率的抽象層。
此外，較大的遊戲可以執行自己的最佳化其核心程式庫以外，如遊戲越來越複雜。 換句話說，CocosSharp 提供了便於使用及效能，讓開發人員能夠快速上手不會限制遊戲的規模或複雜度。

這個實際操作影片會示範如何建立簡單的跨平台 CocosSharp 遊戲：

> [!Video https://channel9.msdn.com/Shows/Visual-Studio-Toolbox/Developing-Cross-platform-2D-Games-in-C-and-CocosSharp/player]

## <a name="bouncinggamegraphics-gamescocossharpbouncing-gamemd"></a>[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)

![BouncingGame](images/bouncing-game.png "BouncingGame")

本指南說明 BouncingGame，包括如何使用遊戲的內容，用來建置遊戲、 加入遊戲邏輯和更多功能的各種視覺化元素。

## <a name="fruity-falls-gamegraphics-gamescocossharpfruity-fallsmd"></a>[Fruity Falls 遊戲](~/graphics-games/cocossharp/fruity-falls.md)

![Fruity Falls 遊戲螢幕擷取畫面](images/fruity-falls.png "Fruity Falls 遊戲螢幕擷取畫面")

本指南說明 Fruity 便在 2007 年遊戲時，涵蓋常見 CocosSharp 和遊戲開發概念，例如物理、 內容管理、 遊戲狀態和遊戲設計。  

## <a name="coin-time-gamegraphics-gamescocossharpcointimemd"></a>[Coin 時間遊戲](~/graphics-games/cocossharp/cointime.md)

![Coin 時間遊戲螢幕擷取畫面](images/cointime.png "錢幣來比喻時間遊戲螢幕擷取畫面")

錢幣來比喻時適用於 iOS 和 Android 遊戲完整平台遊戲。 遊戲的目標是收集所有層級中銅板和則同時避免敵人和障礙達到結束的門。

## <a name="drawing-geometry-with-ccdrawnodegraphics-gamescocossharpccdrawnodemd"></a>[繪製使用 ccdrawnode 建立幾何圖形](~/graphics-games/cocossharp/ccdrawnode.md)

![使用 ccdrawnode 建立繪製的形狀](images/ccdrawnode.png "使用 ccdrawnode 建立繪製的圖形")

Ccdrawnode 建立提供繪圖基本的物件，例如線條、 圓形和三角形的方法。

## <a name="animating-with-ccactiongraphics-gamescocossharpccactionmd"></a>[使用 CCAction 建立動畫](~/graphics-games/cocossharp/ccaction.md)

![Ccaction 建立動畫的動畫](images/ccaction.png "ccaction 建立動畫的動畫")

`CCAction` 是可用來以動畫顯示 CocosSharp 物件的基底類別。 本指南涵蓋內建`CCAction`常見工作，例如位置、 縮放和旋轉的實作。 也會介紹如何建立自訂的實作，藉由繼承自`CCAction`。

## <a name="using-tiled-with-cocossharpgraphics-gamescocossharptiledmd"></a>[搭配 CocosSharp 使用 Tiled](~/graphics-games/cocossharp/tiled.md)

![在遊戲中的層級](images/tiled.png "遊戲中的層級")

並排顯示是一個功能強大、 有彈性，並建立且互不影響等的圖格的完整應用程式對應的遊戲。 CocosSharp 提供內建整合圖案並排的原生檔案格式。

## <a name="entities-in-cocossharpgraphics-gamescocossharpentitiesmd"></a>[CocosSharp 中的實體](~/graphics-games/cocossharp/entities.md)

![從遊戲的太空船](images/entities.png "的太空船，從遊戲")

實體的模式是強大的方式來組織遊戲的程式碼。 它可改善可讀性，讓您更輕鬆地維護的程式碼，並利用內建的父/子功能。

## <a name="handling-multiple-resolutions-in-cocossharpgraphics-gamescocossharpresolutionsmd"></a>[處理 CocosSharp 中的多個解決方法](~/graphics-games/cocossharp/resolutions.md)

![表示螢幕解析度的方格](images/resolutions.png "方格，代表螢幕解析度")

本指南說明如何搭配 cocossharp 使用 Tiled 開發會正確顯示不同解析度的裝置的遊戲。

## <a name="cocossharp-content-pipelinegraphics-gamescocossharpcontent-pipelineindexmd"></a>[CocosSharp 內容管線](~/graphics-games/cocossharp/content-pipeline/index.md)

![XNB](images/content-pipeline.png "XNB")

遊戲開發人員通常使用內容管線來最佳化內容並將其格式化，使它可以在特定硬體上或特定的遊戲開發架構與載入。

## <a name="improving-frame-rate-with-ccspritesheetgraphics-gamescocossharpccspritesheetmd"></a>[改善 CCSpriteSheet 的畫面播放速率](~/graphics-games/cocossharp/ccspritesheet.md)

![樹狀結構中的從 CCSpriteSheet](images/ccspritesheet.png "CCSpriteSheet 從樹狀結構")

`CCSpriteSheet` 提供結合，並使用一種材質中的許多映像檔的功能。 減少紋理計數可以改善遊戲的載入時間和畫面播放速率。

## <a name="texture-caching-using-cctexturecachegraphics-gamescocossharptexture-cachemd"></a>[使用 CCTextureCache 紋理快取](~/graphics-games/cocossharp/texture-cache.md)

![CocosSharp 如何快取映像的表示法](images/texture-cache.png "CocosSharp 如何快取映像的表示法")

CocosSharp 的`CCTextureCache`類別提供組織、 快取，並卸載內容的標準方法。 

## <a name="2d-math-with-cocossharpgraphics-gamescocossharpmathmd"></a>[使用 CocosSharp 的 2D 數學運算](~/graphics-games/cocossharp/math.md)

![旋轉影像](images/math.png "旋轉影像")

本指南涵蓋開發遊戲的 2D 數學運算。 它使用 CocosSharp 示範如何執行一般的遊戲開發工作，並說明這些工作背後的數學運算。

## <a name="performance-and-visual-effects-with-ccrendertexturegraphics-gamescocossharpccrendertexturemd"></a>[效能和使用 CCRenderTexture 的視覺效果](~/graphics-games/cocossharp/ccrendertexture.md)

![從遊戲 sprite](images/ccrendertexture.png "原件，從遊戲")

`CCRenderTexture`類別會提供用於呈現單一材質的多個 CocosSharp 物件的功能。 建立後，`CCRenderTexture`可以用於執行個體，才能有效呈現圖形，並且實作視覺效果。
