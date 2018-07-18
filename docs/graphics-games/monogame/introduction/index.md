---
title: 與 MonoGame 遊戲應用程式開發簡介
description: 此多部分的逐步解說將示範如何建立簡單的 2D 應用程式使用 MonoGame。  它涵蓋了常見的遊戲程式設計概念，例如圖形中，輸入，遊戲實體和物理。
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 46cc3a7e3bb6c58e04626c9d2cc9437c16ba19f5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
ms.locfileid: "33920802"
---
# <a name="introduction-to-game-development-with-monogame"></a>與 MonoGame 遊戲應用程式開發簡介

_此多部分的逐步解說將示範如何建立簡單的 2D 應用程式使用 MonoGame。它涵蓋了常見的遊戲程式設計概念，例如圖形中，輸入，遊戲實體和物理。_

本文說明 MonoGame API 技術來進行跨平台遊戲。 如需完整的平台清單，請參閱[MonoGame 網站](http://www.monogame.net/)。 本教學課程會使用 C# 程式碼範例，雖然 MonoGame 以及使用 F # 完全正常運作。

MonoGame 跨平台，硬體加速應用程式開發介面匯入資產提供圖形、 音訊、 遊戲狀態管理、 輸入和內容的管線。 不像大多數的遊戲引擎 MonoGame 不會提供或強制執行任何模式或專案的結構。  雖然這表示開發人員可以自由地組織程式碼，這也表示第一次啟動新的專案時需要的位元的安裝程式碼。

本逐步解說的第一個區段會著重於設定的空專案。 最後一節涵蓋撰寫所有我們遊戲邏輯和內容 – 最都將會是跨平台。

本逐步解說結束時，我們將建立簡單的遊戲，播放程式可以控制觸控輸入動畫的字元的位置。  雖然這不是技術上完整遊戲 （因為它沒有 win 或遺失條件），它會示範許多遊戲開發概念，並可用來當做基礎的許多類型的遊戲。 

下圖顯示這個逐步解說的結果：

![下列滑鼠範例遊戲字元的動畫](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogame 和 XNA

MonoGame 程式庫會模擬 Microsoft XNA 語法和功能的文件庫。  所有 MonoGame 物件都存在 Microsoft.Xna 命名空間之下，– 允許用於未修改的 MonoGame 大部分 XNA 程式碼。 

XNA 熟悉的開發人員會熟悉 MonoGame 的語法，並尋找 MonoGame 搭配使用的其他資訊的開發人員可以參考現有的線上 XNA 逐步解說中，API 文件及討論。


## <a name="walkthrough-parts"></a>逐步解說的部分

- [組件 1-建立跨平台 MonoGame 專案](~/graphics-games/monogame/introduction/part1.md)
- [第 2 – 實作 WalkingGame 部分](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>相關連結

- [WalkingGame MonoGame 專案 （範例）](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
- [XNB 字型 iOS](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [XNB 字型 Android](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [需由 NuGet MonoGame Android](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [需由 NuGet MonoGame iOS](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [MonoGame API 文件](http://www.monogame.net/documentation/?page=main)
