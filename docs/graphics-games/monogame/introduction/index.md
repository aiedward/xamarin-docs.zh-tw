---
title: 使用 MonoGame 進行遊戲開發的簡介
description: 這個多部分的逐步解說示範如何使用 MonoGame 建立簡單的2D 應用程式。  其中涵蓋常見的遊戲程式設計概念，例如圖形、輸入、遊戲實體和物理。
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: bdbce0b001d5baf5ef751e092a2083efdf3ca992
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436273"
---
# <a name="introduction-to-game-development-with-monogame"></a>使用 MonoGame 進行遊戲開發的簡介

_這個多部分的逐步解說示範如何使用 MonoGame 建立簡單的2D 應用程式。 其中涵蓋常見的遊戲程式設計概念，例如圖形、輸入、遊戲實體和物理。_

本文說明用來製作跨平臺遊戲的 MonoGame API 技術。 如需完整的平臺清單，請參閱 [MonoGame 網站](http://www.monogame.net/)。 本教學課程將使用 c # 進行程式碼範例，但 MonoGame 也可與 F # 完全搭配運作。

MonoGame 是跨平臺硬體加速 API，提供圖形、音訊、遊戲狀態管理、輸入，以及匯入資產的內容管線。 與大部分的遊戲引擎不同的是，MonoGame 不會提供或強加任何模式或專案結構。  雖然這表示開發人員可以自由地組織自己的程式碼，但這也表示當第一次啟動新專案時，需要一些安裝程式碼。

本逐步解說的第一節著重于設定空白專案。 最後一節會說明如何撰寫所有的遊戲邏輯和內容–大部分都是跨平臺。

在本逐步解說結束時，我們將建立一個簡單的遊戲，讓播放程式可以使用觸控輸入來控制動畫字元。  雖然這在技術上並不是完整的遊戲 (因為不會有任何勝利或遺失的情況) ，它會示範許多遊戲開發概念，並可作為許多類型遊戲的基礎。

以下顯示此逐步解說的結果：

![滑鼠後面的範例遊戲字元動畫](images/image1.gif)

## <a name="monogame-and-xna"></a>MonoGame 和

MonoGame 程式庫的目的是要以語法和功能模仿 Microsoft 的單元程式庫。  所有的 MonoGame 物件都存在於的命名空間下–允許在 MonoGame 中使用大部分的程式碼，而不需要修改。

熟悉您的應用程式的開發人員已經熟悉 MonoGame 的語法，而開發人員在使用 MonoGame 時，也可以參考現有的線上的新操作逐步解說、API 檔和討論。

## <a name="walkthrough-parts"></a>逐步解說元件

- [第1部分-建立跨平臺 MonoGame 專案](~/graphics-games/monogame/introduction/part1.md)
- [第2部分-執行 WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>相關連結

- [WalkingGame MonoGame Project (範例) ](/samples/xamarin/mobile-samples/walkinggamemg/)
- [在 NuGet 上 MonoGame Android](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [在 NuGet 上 MonoGame iOS](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [MonoGame API 檔](http://www.monogame.net/documentation/?page=main)