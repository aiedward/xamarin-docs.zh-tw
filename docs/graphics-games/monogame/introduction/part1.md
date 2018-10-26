---
title: 第 1 部分-建立跨平台 MonoGame
description: 本逐步解說示範如何建立新的專案，適用於 iOS 和 Android 使用 MonoGame。 結果是 Visual Studio for Mac 的跨平台共用程式碼專案，以及每個平台的一個專案的方案。 此專案會顯示空白藍色畫面時執行。
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 82b1408cafedf98a8619e8e039ba00b332f74516
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "33921985"
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>第 1 部分-建立跨平台 MonoGame

_本逐步解說示範如何建立新的專案，適用於 iOS 和 Android 使用 MonoGame。結果是 Visual Studio for Mac 的跨平台共用程式碼專案，以及每個平台的一個專案的方案。此專案會顯示空白藍色畫面時執行。_

MonoGame 可讓您使用大部分的程式碼重複使用的跨平台遊戲開發。 本逐步解說將著重於設定的解決方案，其中包含適用於 iOS 和 Android 專案，以及跨平台程式碼的共用程式碼專案。

當我們完成時，我們會有具有適當的結構，來執行遊戲的更新邏輯的專案，遊戲每秒 30 畫面格繪製邏輯。 它可用來當做基底專案的任何 MonoGame 專案。 執行時，我們的專案看起來像這樣：

![空白的藍色畫面](part1-images/image1.png)

## <a name="adding-monogame-to-visual-studio-for-mac"></a>Visual studio for Mac 新增 MonoGame

MonoGame 可新增為增益集在 Visual studio for mac。 在 Mac 上，選取**Visual Studio for Mac** > **增益集管理員...** . 在 Windows 中，選取 [工具] >**增益集管理員...** . 選取 **資源庫**索引標籤上，展開**遊戲開發**類別目錄，然後選取**MonoGame 增益集**，然後按一下**安裝**:

![Visual Studio for Mac 延伸模組資源庫選取 MonoGame](part1-images/image2.png)

> [!IMPORTANT]
> **附註**： 如果**遊戲開發**區段不會出現在增益集管理員中，您可以手動下載並從這裡安裝最新版本： http://www.monogame.net/downloads/。 您可能需要重新啟動 Visual Studio for Mac 範本才會出現。

安裝之後，MonoGame 範本會出現在 Visual Studio for Mac 中，我們會看到下一節。

## <a name="creating-a-new-solution"></a>建立新的解決方案

在 Visual Studio for Mac 選取**檔案 > 新的方案**。 在**新的專案**] 對話方塊中，按一下**其他**，捲動到 [**一般**區段中，選取 * * 通用 MonoGame 行動應用程式 * * 選項，然後按一下 [下一步]。

![建立 MonoGame 應用程式的新增專案 對話方塊](part1-images/image3.png)

WalkingGame 為專案名稱，然後按一下 建立：

![挑選的名稱和位置的新 [專案] 對話方塊](part1-images/image4.png)

現在我們的專案將會執行，就像任何其他的 iOS 或 Android 專案。 顯示 矢菊花藍背景應該執行專案：

![空白的藍色應用程式的背景](part1-images/image5.png)

## <a name="fixing-android-compile-errors"></a>修正 Android 的編譯錯誤

目前版本的 MonoGame 的範本在 Android 中包含了幾個語法錯誤`Activity1.cs`檔案。 若要修正這些問題，取代`OnCreate`函式取代為下列：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    var g = new Game1();
    SetContentView((View)g.Services.GetService(typeof(View)));
    g.Run();
}
```

## <a name="summary"></a>總結

本逐步解說涵蓋如何建立 Visual Studio for mac 中使用的跨平台 MonoGame 專案 這個結果會是空的藍色螢幕。 此專案可以用做為起點，適用於任何 iOS 和 Android 的遊戲。

## <a name="related-links"></a>相關連結

- [MonoGame Android NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [MonoGame iOS NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [MonoGame API 文件](http://www.monogame.net/documentation/?page=main)
