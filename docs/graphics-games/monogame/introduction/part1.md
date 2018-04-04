---
title: 第 1 – 建立跨平台 MonoGame 部分
description: 本逐步解說示範如何建立新的專案，適用於 iOS 和 Android 使用 MonoGame。 結果是一個 Visual Studio for Mac 方案中使用跨平台共用程式碼專案，以及每個平台的一個專案。 這個專案將會顯示空白的藍色畫面時執行。
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 0cd12f23f8cb269b2a41a08bf641db08e18fb82b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>第 1 – 建立跨平台 MonoGame 部分

_本逐步解說示範如何建立新的專案，適用於 iOS 和 Android 使用 MonoGame。結果是一個 Visual Studio for Mac 方案中使用跨平台共用程式碼專案，以及每個平台的一個專案。這個專案將會顯示空白的藍色畫面時執行。_

MonoGame 可讓您開發跨平台遊戲與大量重複使用程式碼。 本逐步解說將著重於設定包含適用於 iOS 和 Android 專案，以及跨平台程式碼的共用程式碼專案的方案。

當我們完成時，我們將已有適當的結構來執行遊戲更新邏輯的專案，遊戲繪圖邏輯每秒 30 個畫面。 它可以當做基底的專案的任何 MonoGame 專案。 執行時，受測專案看起來像這樣：

![](part1-images/image1.png "執行時，將看起來像這樣的專案")


# <a name="adding-monogame-to-visual-studio-for-mac"></a>將 MonoGame 加入至 Visual Studio 中，for Mac

MonoGame 可新增為增益集至 Visual Studio for mac。 在 Mac 上，選取**Visual Studio for Mac** > **增益集管理員...** . 在 Windows 中，選取 * * 工具 * * >**增益集管理員...** . 選取**圖庫**索引標籤上，依序展開**遊戲開發**類別目錄，然後選取**MonoGame Addin**，然後按一下 **安裝**:

![](part1-images/image2.png "選取 [圖庫] 索引標籤，展開遊戲開發類別選取 MonoGame 增益集，再按一下 [安裝]")

> [!IMPORTANT]
> **請注意**： 如果**遊戲開發**區段不會出現在增益集管理員中，您可以手動下載並安裝最新版本，從這裡： http://www.monogame.net/downloads/。 您可能需要重新啟動 Visual Studio for Mac 的範本才會出現。



安裝之後，MonoGame 範本會出現在 Visual Studio for Mac，我們將會看到下一節。


# <a name="creating-a-new-solution"></a>建立新的方案

在 Visual Studio for Mac 選取**檔案 > 新的方案**。 在**新專案**對話方塊中，按一下 **其他**，捲動到**一般**區段中，選取 * * 通用 MonoGame 行動應用程式 * * 選項，然後按一下 下一步。

![](part1-images/image3.png "在 [新增專案] 對話方塊中，按一下其他，捲動到 [一般] 區段中，選取 [通用 MonoGame 行動應用程式] 選項，然後按一下 [下一步]")

WalkingGame 為專案名稱，然後按一下 建立：

![](part1-images/image4.png "WalkingGame 為專案名稱，然後按一下 建立")

現在將會執行受測專案，就像其他任何 iOS 或 Android 專案。 專案應該執行顯示矢菊花藍背景：

![](part1-images/image5.png "專案應該執行顯示矢菊花藍背景")


# <a name="fixing-android-compile-errors"></a>修正 Android 編譯錯誤

MonoGame 的範本的目前版本包含幾個語法錯誤中的 Android`Activity1.cs`檔案。 若要修正這些問題，取代`OnCreate`具有下列函式：


```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    var g = new Game1();
    SetContentView((View)g.Services.GetService(typeof(View)));
    g.Run();
}
```


# <a name="summary"></a>總結

本逐步解說涵蓋如何建立跨平台 MonoGame 專案使用 Visual Studio for mac。 結果會是空的藍色畫面。 這個專案可以當做起點的任何 iOS 和 Android 的遊戲。

## <a name="related-links"></a>相關連結

- [MonoGame Android NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [MonoGame iOS NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [MonoGame API 文件](http://www.monogame.net/documentation/?page=main)
