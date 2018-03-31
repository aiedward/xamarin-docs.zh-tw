---
title: 建立多重平台 CocosSharp 專案
description: 本逐步解說示範如何建立新的多平台 CocosSharp 方案。 本逐步解說的結果是 Mac 的解決方案包括三個專案的 Visual Studio： 一個可攜式類別庫專案，一個特定的 Android 專案和一個特定的 iOS 專案。 產生的專案將會顯示空白的黑色畫面時執行。
ms.topic: article
ms.prod: xamarin
ms.assetid: 37C97693-B0A8-4064-97B6-A6FAB5BA4FB7
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 2906035ce9bd44d111b89ccfe7443896775315b7
ms.sourcegitcommit: 7b88081a979381094c771421253d8a388b2afc16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2018
---
# <a name="creating-a-multi-platform-cocossharp-project"></a>建立多重平台 CocosSharp 專案

_本逐步解說示範如何建立新的多平台 CocosSharp 方案。本逐步解說的結果是 Mac 的解決方案包括三個專案的 Visual Studio： 一個可攜式類別庫專案，一個特定的 Android 專案和一個特定的 iOS 專案。產生的專案將會顯示空白的黑色畫面時執行。_

CocosSharp 2D 遊戲引擎可讓程式碼和跨多個平台共用的內容。 本逐步解說示範如何建立可支援 iOS 和 Android 開發專案。 具體來說，本逐步解說涵蓋下列主題：

 - 安裝 CocosSharp
 - 建立新的方案
 - `LoadGame` 方法

# <a name="installing-cocossharp"></a>安裝 CocosSharp

首先，我們會加入 CocosSharp Visual Studio for mac。 如果在 Mac 上執行，請選取**Visual Studio for Mac** > **增益集管理員...** . 如果執行於 Windows 上，選取**工具** > **增益集管理員...** . 按一下**圖庫**索引標籤上，依序展開**CocosSharp 項目**，選取**CocosSharp 專案範本**，最後按一下**安裝...** .

![CocosSharp 增益集](part1-images/xamarinstudioaddinsmac.png "")

# <a name="creating-a-new-solution"></a>建立新的解決方案

既然已安裝 CocosSharp，我們將建立方案。 在 Visual Studio for Mac 選取**檔案** > **新增** > **方案...**.選取**應用程式**選項在**跨平台**區段中，選取**CocosSharp 空專案**，然後按一下 **下一步**:

![](part1-images/image1.png "選取 [跨平台] 區段下的應用程式選項選取 CocosSharp 空的專案，然後按 下一步")

輸入名稱**BouncingGame**如**專案名稱**，然後按一下 **建立**:

![](part1-images/image2.png "為專案名稱，輸入 BouncingGame 的名稱，然後按一下 [建立]")

一旦建立專案和 Visual Studio for Mac，我們可以編譯並執行以檢視灰色背景： 

![](part1-images/image3.png "一旦專案已建立和 Visual Studio for Mac，編譯及執行此程式碼檢視灰色背景")


# <a name="loadgame-method"></a>LoadGame 方法

預設 CocosSharp 專案包含設定 iOS 和 Android 的特定類別`CCGameView`，用來啟動 CocosSharp。 `CCGameView`以特定平台的方式建立執行個體： iOS 專案會建立`CCGameView`中`Main.storyboard`檔案，而會建立 Android`CCGameView`中`Main.axml`檔案。 每個平台使用 CCGameView 執行個體中的`LoadGame`方法用於執行一些基本的安裝程式。 雖然我們不會修改這個程式碼，讓我們看看一些重要詳細資料：

 - 程式碼集`gameView.DesignResolution`為 1024 x 768。 這標準化定位跨裝置，不論目前的裝置比例、 實際的解析度或列印方向。 
 - 程式碼會加入幾個搜尋路徑。 搜尋路徑也允許載入沒有目錄前置詞的內容。 例如，因為`"Sounds"`路徑加入搜尋路徑，然後在目錄中的檔案為`"Content/Sounds/mysound.xnb"`可能只是以載入`"mysound.xnb"`。 搜尋路徑如下`using`陳述式中程式碼 – 可降低程式碼，但它們也可能導致模稜兩可。
 - 程式碼會建構`GameLayer`執行個體。 `GameLayer` 是`CCLayer`-繼承的類別，我們將會加入所有我們遊戲的邏輯。 較大的遊戲可能需要多個`CCLayer`執行個體或偶數倍數`CCScene`執行個體 (其中可以包含多個`CCLayer`執行個體)，但我們黏貼至單一`CCLayer`此遊戲。

#  <a name="summary"></a>總結

本逐步解說涵蓋如何建立跨平台 CocosSharp 專案使用 Visual Studio for mac。 結果會是空白畫面，可以作為起始點的任何遊戲專案。