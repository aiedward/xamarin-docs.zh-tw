---
title: 內容管線簡介
description: 內容管線是應用程式或部分的應用程式，用來將檔案轉換成可由遊戲專案載入的格式。 MonoGame Content Pipeline 會轉換為 CocosSharp 和 MonoGame 專案檔案的特定內容的管線實作。
ms.prod: xamarin
ms.assetid: 40628B5F-FAF7-4FA7-A929-6C3FEA83F8EC
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 712c430fb6309ba0f5c3e573267c59e422de8ad2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61029514"
---
# <a name="introduction-to-content-pipelines"></a>內容管線簡介

_內容管線是應用程式或部分的應用程式，用來將檔案轉換成可由遊戲專案載入的格式。MonoGame Content Pipeline 會轉換為 CocosSharp 和 MonoGame 專案檔案的特定內容的管線實作。_

這篇文章提供概念性的了解的內容管線，主要著重*MonoGame Content Pipeline*，這是搭配 CocosSharp 和 MonoGame 的內容管線實作。


## <a name="what-is-a-content-pipeline"></a>什麼是內容的管線？

詞彙*內容管線*是將檔案從一種格式轉換為另一個處理序一般詞彙。 *輸入*content pipeline 的通常是撰寫工具，例如 Photoshop 影像檔所輸出的檔案。 建立 content pipeline*輸出*直接遊戲專案要載入的格式檔案。 通常的輸出檔案最適合用於快速載入，並降低磁碟大小。

內容管線有可能是命令列可執行檔、 專用的 gui 應用程式或外掛程式內嵌在 Visual Studio 這類的另一個應用程式。 內容管線通常執行遊戲執行之前。 如果 Visual Studio 等某些應用程式相關聯內容的管線，它可能會執行編譯時間期間。 如果內容的管線是一個獨立應用程式，則它可能會執行時明確地告訴使用者的 若要這樣做。 負責將轉換的特定輸入的檔案的邏輯的應用程式 (例如 **.png**) 相關聯的輸出檔案指*產生器*。 

我們可以視覺化的路徑，檔案會接收來自放編寫，以載入在執行階段，如下所示：

![](introduction-images/image1.png "檔案會接收來自放編寫，以在執行階段載入的路徑會在此圖中視覺化")

## <a name="why-use-a-content-pipeline"></a>為何要使用內容管線？

內容管線導入額外的步驟之間撰寫的應用程式和遊戲時，它可以增加編譯時間，並將複雜性加入至開發程序。 儘管這些考量，內容的管線會介紹遊戲開發的一些優點：


### <a name="converting-to-a-format-understood-by-the-game"></a>將轉換成格式理解的遊戲

CocosSharp 和 MonoGame 提供載入各種類型的內容; 方法不過，必須正確地格式化內容之前載入。 大多數類型的內容需要某種類型的轉換之前載入。 例如，在 音效 **.wav**格式必須轉換成 **.xnb**因為 CocosSharp 和 MonoGame 不支援載入要在執行階段載入的檔案 **.wav**檔案格式。


### <a name="converting-to-a-format-native-to-the-hardware"></a>將轉換成硬體的原生格式

不同的硬體可能以不同方式處理內容，在執行階段。 比方說，CocosSharp 的遊戲可以載入映像檔案建立時`CCSprite`執行個體。 雖然相同的程式碼可以用來載入 iOS 和 Android 上的檔案中，每個平台會以不同的方式儲存載入的檔案。 如此一來，MonoGame Content Pipeline 格式紋理 **.xnb**以不同的方式視目標平台的檔案。


### <a name="reducing-size-on-disk"></a>減少在磁碟上的大小 

內容管線可以用來移除資訊，這是在撰寫時很有用，但不是需要在執行階段。 原始檔案 （輸入） 可以儲存所有的資訊可協助維護現有的內容，透過內容創作者，但可以保留整體遊戲檔案小精簡將輸出檔案。 這項考量是特別適合行動的遊戲，會下載，而在安裝媒體上散發。


### <a name="reducing-load-time"></a>減少載入時間

遊戲可能需要修改的內容，以改善執行階段效能，以改善視覺效果，或加入新功能。 例如許多的 3D 遊戲計算光源一次，然後轉譯複雜的場景時，使用這項計算的結果。 因為執行這些計算，正在載入內容可以費不貲建置遊戲時改為可執行的計算。 計算結果可以包含在內容中，啟用要載入速度比所可能的內容。 


## <a name="xnb-file-extension"></a>xnb 檔案延伸模組

**.Xnb**副檔名是 Monogame 內容管線所輸出的所有檔案的副檔名。 這會比對 Microsoft XNA Content Pipeline 所輸出的檔案的副檔名。

**.Xnb**不論原始的檔案類型會使用延伸模組。 也就是說，影像檔 (**.png**)，音訊檔案 (**.wav**)，和任何自訂檔案類型將所有輸出作為 **.xnb**檔案時傳遞內容的管線。 因為擴充功能無法用來區別不同的檔案格式再 CocosSharp 和 MonoGame 方法載入 **.xnb**檔案不會希望擴充功能載入檔案時。

使用 Monogame 管線工具涵蓋就可以建立 CocosSharp 和 MonoGame.xnb 檔案[在這個逐步解說](~/graphics-games/cocossharp/content-pipeline/walkthrough.md)。


## <a name="summary"></a>總結

這篇文章提供概觀及優點的內容管線在一般情況下，以及 MonoGame Content Pipeline 的簡介。

## <a name="related-links"></a>相關連結

- [MonoGame 管線文件](http://www.monogame.net/documentation/?page=Pipeline)
