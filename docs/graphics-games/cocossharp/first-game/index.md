---
title: "與 CocosSharp 遊戲應用程式開發簡介"
description: "此多部分的逐步解說將示範如何建立簡單的 2D 遊戲使用 CocosSharp。 它涵蓋了常見遊戲的程式設計概念，例如圖形、 輸入和物理。"
ms.topic: article
ms.prod: xamarin
ms.assetid: BCA99A61-A48D-4207-9A35-4C62F10C9AA5
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 5ab6f68aed791dd21516d663367ac5435e92d6cc
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="introduction-to-game-development-with-cocossharp"></a>與 CocosSharp 遊戲應用程式開發簡介

_此多部分的逐步解說將示範如何建立簡單的 2D 遊戲使用 CocosSharp。它涵蓋了常見遊戲的程式設計概念，例如圖形、 輸入和物理。_

CocosSharp 2D 遊戲引擎提供技術來進行跨平台遊戲。 如需支援的平台的完整清單，請參閱[CocosSharp GitHub 上的 wiki](https://github.com/mono/CocosSharp/wiki)。 本教學課程會使用 C# 程式碼範例，雖然 CocosSharp 以及使用 F # 完全正常運作。

所提供的核心 CocosSharp [MonoGame framework](http://www.monogame.net/)、 其本身是跨平台、 硬體加速應用程式開發介面匯入資產提供圖形、 音訊、 遊戲狀態管理、 輸入和內容的管線。 CocosSharp 是適合 2D 遊戲有效率的抽象層。 此外，較大的遊戲可以執行其核心程式庫以外自己最佳化遊戲越來越複雜。 換句話說，CocosSharp 提供混合便於使用及效能，讓開發人員快速上手不限制遊戲的大小或複雜度。

這項逐步解說重點空專案所設定的第一個區段。  第二個部分涵蓋所有我們遊戲的邏輯寫入。 

本逐步解說結束我們將建立簡單的遊戲，玩家的目標是要投影片 paddle 水平擴展至嘗試防止球下降不在畫面上的位置。 每個彈回提高一個點 media player 的分數。

![](images/image1.png "每個彈回會增加一個點的 media player 的分數")

# <a name="walkthrough-parts"></a>逐步解說的部分

* [組件 1 – 建立 CocosSharp 專案](~/graphics-games/cocossharp/first-game/part1.md)
* [第 2 – 實作 BouncingGame 部分](~/graphics-games/cocossharp/first-game/part2.md)

## <a name="related-links"></a>相關連結

- [遊戲的內容 （範例）](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
- [已完成的專案 （範例）](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [需由 NuGet CocosSharp PCL](http://www.nuget.org/packages/CocosSharp.PCL.Shared/)
- [CocosSharp API 文件](https://developer.xamarin.com/api/namespace/CocosSharp/)
