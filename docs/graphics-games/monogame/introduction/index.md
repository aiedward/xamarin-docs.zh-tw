---
title: 使用 MonoGame 進行遊戲開發的簡介
description: 這個多部分的逐步解說會示範如何使用 MonoGame 建立簡單的2D 應用程式。  其中涵蓋常見的遊戲程式設計概念, 例如圖形、輸入、遊戲實體和物理。
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 47ed7fc1b4485864646a17940aceed395a4a8983
ms.sourcegitcommit: f255aa286bd52e8a80ffa620c2e93c97f069f8ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68680915"
---
# <a name="introduction-to-game-development-with-monogame"></a>使用 MonoGame 進行遊戲開發的簡介

_這個多部分的逐步解說會示範如何使用 MonoGame 建立簡單的2D 應用程式。其中涵蓋常見的遊戲程式設計概念, 例如圖形、輸入、遊戲實體和物理。_

本文說明用於製作跨平臺遊戲的 MonoGame API 技術。 如需完整的平臺清單, 請參閱[MonoGame 網站](http://www.monogame.net/)。 本教學課程將C#使用來執行程式碼範例, 雖然 MonoGame 也F#能夠完全運作。

MonoGame 是跨平臺的硬體加速 API, 提供圖形、音訊、遊戲狀態管理、輸入, 以及用於匯入資產的內容管線。 不同于大部分的遊戲引擎, MonoGame 不會提供或強加任何模式或專案結構。  雖然這表示開發人員可以自由地組織其程式碼, 但這也表示當第一次啟動新專案時, 需要一些安裝程式碼。

本逐步解說的第一節著重于設定空的專案。 最後一節涵蓋撰寫所有遊戲邏輯和內容, 其中大部分都是跨平臺。

在本逐步解說結束時, 我們將建立一個簡單的遊戲, 讓播放程式可以透過觸控輸入來控制動畫字元。  雖然這在技術上並不是一項完整的遊戲 (因為它沒有任何獲勝或遺失的情況), 但它會示範許多遊戲開發概念, 並可做為許多遊戲類型的基礎。 

以下顯示此逐步解說的結果:

![遵循滑鼠後的範例遊戲字元動畫](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogame 和

MonoGame 程式庫的目的是要以語法和功能來模擬 Microsoft 的「主要」程式庫。  所有的 MonoGame 物件都存在於 MonoGame 中, 可讓大部分的未修改的程式碼都能在中使用。 

熟悉的應用程式開發人員將已熟悉 MonoGame 的語法, 而開發人員若要取得使用 MonoGame 的其他資訊, 就能夠參考現有的線上的執行集區逐步解說、API 檔和討論。


## <a name="walkthrough-parts"></a>逐步解說部分

- [第1部分–建立跨平臺 MonoGame 專案](~/graphics-games/monogame/introduction/part1.md)
- [第2部分–執行 WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>相關連結

- [WalkingGame MonoGame 專案 (範例)](https://docs.microsoft.com/samples/xamarin/mobile-samples/walkinggamemg/)
- [XNB 字型 iOS](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [XNB 字型 Android](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [在 NuGet 上 MonoGame Android](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [在 NuGet 上 MonoGame iOS](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [MonoGame API 檔](http://www.monogame.net/documentation/?page=main)
