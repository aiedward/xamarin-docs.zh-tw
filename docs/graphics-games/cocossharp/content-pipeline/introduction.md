---
title: 內容的管線簡介
description: 內容管線是應用程式或組件的應用程式，可用來將檔案轉換成可以載入由遊戲專案的格式。 MonoGame 內容管線是用於轉換 CocosSharp 和 MonoGame 專案檔案的特定內容的管線實作。
ms.prod: xamarin
ms.assetid: 40628B5F-FAF7-4FA7-A929-6C3FEA83F8EC
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: a369c5ba61033eb61c0f188c03b21e08c71784fb
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
ms.locfileid: "33922831"
---
# <a name="introduction-to-content-pipelines"></a>內容的管線簡介

_內容管線是應用程式或組件的應用程式，可用來將檔案轉換成可以載入由遊戲專案的格式。MonoGame 內容管線是用於轉換 CocosSharp 和 MonoGame 專案檔案的特定內容的管線實作。_

本文章提供概念性的了解的內容的管線，主要將焦點放在*MonoGame 內容管線*，這是搭配 CocosSharp 和 MonoGame 內容的管線實作。


## <a name="what-is-a-content-pipeline"></a>什麼是內容的管線？

詞彙*內容的管線*是從一種格式轉換檔案的程序的一般詞彙。 *輸入*內容的管線的通常是輸出的撰寫工具，例如影像檔從 Photoshop 檔案。 內容的管線就會建立*輸出*遊戲專案可以直接載入的格式檔案。 通常輸出檔案適合用於快速載入，並減少磁碟大小。

內容管線有可能是命令列可執行檔、 專用以 GUI 為基礎應用程式或外掛程式內嵌在 Visual Studio 這類的另一個應用程式。 遊戲執行之前，通常執行內容的管線。 如果 Visual Studio 等某些應用程式相關聯內容的管線，它可能會執行編譯階段期間。 如果內容的管線的獨立應用程式，則表示它可能會執行時明確告知使用者的 若要這樣做。 負責將轉換的特定輸入的檔案的邏輯的應用程式 (例如 **.png**) 相關聯的輸出檔案指*產生器*。 

我們可以視覺化檔案會從撰寫，如下所示載入執行階段取得的路徑：

![](introduction-images/image1.png "從撰寫要在執行階段載入取出檔案的路徑會在此圖中視覺化")

## <a name="why-use-a-content-pipeline"></a>為何要使用內容的管線？

內容的管線導入額外的步驟，撰寫的應用程式和遊戲時，這會增加編譯時間，並且將複雜性加入至在開發程序之間。 雖然這些注意事項，內容的管線會介紹遊戲開發有一些優點：


### <a name="converting-to-a-format-understood-by-the-game"></a>轉換的格式瞭解遊戲

CocosSharp 和 MonoGame 提供方法來載入各種類型的內容。不過，必須正確地格式化內容之前載入。 大部分的內容類型需要某種類型的轉換之前載入。 例如，在混音效果 **.wav**格式必須轉換成 **.xnb**檔案是在執行階段載入，因為 CocosSharp 和 MonoGame 不支援載入 **.wav**檔案格式。


### <a name="converting-to-a-format-native-to-the-hardware"></a>轉換的硬體原生格式

不同的硬體可能以不同方式處理內容，在執行階段。 例如，CocosSharp 遊戲可載入映像檔建立時`CCSprite`執行個體。 雖然可以使用相同的程式碼，載入 iOS 和 Android 上的檔案，但每個平台會以不同的方式儲存載入的檔案。 因此，MonoGame 內容管線格式化紋理 **.xnb**以不同的方式視目標平台的檔案。


### <a name="reducing-size-on-disk"></a>減少在磁碟上的大小 

Content 管線可以用來移除資訊，這是在撰寫時很有用，但不是需要在執行階段。 原始 （輸入） 檔案可以儲存所有的資訊可協助維護現有的內容，內容建立者，但輸出檔可能會最小整體遊戲的檔案。 這項考量是對於行動裝置會下載，而分散式安裝媒體上的遊戲特別有用。


### <a name="reducing-load-time"></a>減少載入時間

遊戲可能需要修改的內容，以改善執行階段效能，以增進視覺效果，或是將新功能。 例如許多 3D 遊戲計算光源一次，然後轉譯複雜的場景時使用這項計算的結果。 之後載入內容可能會極為耗費資源時，執行這些計算計算時，可以改為執行建置遊戲。 計算結果可以包含在內容中，啟用要載入速度比原本是可能的內容。 


## <a name="xnb-file-extension"></a>xnb 檔案延伸模組

**.Xnb**副檔名是由 Monogame 內容管線輸出的所有檔案的副檔名。 這符合 Microsoft XNA 內容管線輸出檔案的副檔名。

**.Xnb**延伸模組會使用不論原始的檔案類型。 換句話說，映像檔案 (**.png**)，音訊檔案 (**.wav**)，和任何自訂檔案類型將所有會輸出為 **.xnb**檔案時傳遞內容的管線。 因為擴充功能無法用於區分不同的檔案格式然後 CocosSharp 和 MonoGame 方法載入 **.xnb**檔案不會希望擴充功能載入檔案時。

可以使用包含 Monogame 管線工具建立 CocosSharp 和 MonoGame.xnb 檔案[在本逐步解說](~/graphics-games/cocossharp/content-pipeline/walkthrough.md)。


## <a name="summary"></a>總結

這篇文章提供概觀和內容的管線優點一般情況下，以及 MonoGame 內容管線的簡介。

## <a name="related-links"></a>相關的連結

- [MonoGame 管線文件](http://www.monogame.net/documentation/?page=Pipeline)
