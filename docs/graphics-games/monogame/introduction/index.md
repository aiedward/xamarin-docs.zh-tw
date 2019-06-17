---
title: 使用 MonoGame 的遊戲開發簡介
description: 此多部分的逐步解說會示範如何建立簡單的 2D 應用程式，以使用 MonoGame。  它涵蓋了常見的遊戲程式設計的概念，例如圖形、 輸入、 遊戲實體和物理條件。
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 4ab98d59bc74672f9531f4dbd3c33a6270582612
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386260"
---
# <a name="introduction-to-game-development-with-monogame"></a>使用 MonoGame 的遊戲開發簡介

_此多部分的逐步解說會示範如何建立簡單的 2D 應用程式，以使用 MonoGame。它涵蓋了常見的遊戲程式設計的概念，例如圖形、 輸入、 遊戲實體和物理條件。_

這篇文章說明 MonoGame API 技術來進行跨平台遊戲。 如需的平台的完整清單，請參閱 < [MonoGame 網站](http://www.monogame.net/)。 本教學課程會使用C#如需程式碼範例，雖然 MonoGame 都可以正常使用F#以及。

MonoGame 跨平台，硬體加速 API 的匯入資產提供圖形、 音訊、 遊戲狀態管理、 輸入和內容的管線。 不同於大多數的遊戲引擎，MonoGame 不提供或強制任何模式或專案的結構。  雖然這表示開發人員可自行隨心所欲地組織其程式碼，這也表示，在第一次啟動新的專案時，需要的安裝程式的程式碼。

本逐步解說的第一個區段，著重於設定的空白專案。 最後一節涵蓋撰寫所有的遊戲邏輯和內容 – 最這會是跨平台。

本逐步解說結束時，我們將建立一個簡單的遊戲，讓播放程式可以控制動畫的字元，觸控輸入。  雖然這不是技術上一個完整的遊戲 （因為它沒有贏了還是輸條件），它會示範許多遊戲開發概念，並可用來當做基礎的許多類型的遊戲。 

下圖顯示本逐步解說的結果：

![範例遊戲的字元，下列滑鼠的動畫](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogame 和 XNA

MonoGame 文件庫被要模仿 Microsoft XNA 語法和功能的文件庫。  MonoGame 的所有物件都存在 Microsoft.Xna 命名空間 – 可讓大部分的 XNA 程式碼，以用於未修改 MonoGame 之下。 

Xna 開發人員將已經很熟悉 MonoGame 的語法，並尋找其他有關使用使用 MonoGame 的開發人員將能夠參考現有的線上 XNA 逐步解說、 API 文件，以及討論。


## <a name="walkthrough-parts"></a>逐步解說中的組件

- [第 1 部分-建立跨平台 MonoGame 專案](~/graphics-games/monogame/introduction/part1.md)
- [第 2 部分-實作 WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>相關連結

- [WalkingGame MonoGame 專案 （範例）](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
- [XNB 字型 iOS](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [XNB 字型 Android](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [在 NuGet 上的 MonoGame Android](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [在 NuGet 上的 MonoGame iOS](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [MonoGame API 文件](http://www.monogame.net/documentation/?page=main)
