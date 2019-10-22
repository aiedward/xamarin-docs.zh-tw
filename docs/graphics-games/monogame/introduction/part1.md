---
title: 第1部分–建立跨平臺 MonoGame
description: 本逐步解說示範如何使用 MonoGame 來建立適用于 iOS 和 Android 的新專案。 結果是具有跨平臺共用程式碼專案的 Visual Studio for Mac 解決方案，以及每個平臺的一個專案。 此專案會在執行時顯示空的藍色畫面。
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: d72c428bb4b8c88365180c5c3c50b107eed2b21d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "68978449"
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>第1部分–建立跨平臺 MonoGame

_本逐步解說示範如何使用 MonoGame 來建立適用于 iOS 和 Android 的新專案。結果是具有跨平臺共用程式碼專案的 Visual Studio for Mac 解決方案，以及每個平臺的一個專案。此專案會在執行時顯示空的藍色畫面。_

MonoGame 可讓您在大量程式碼重複使用的情況中，開發跨平臺遊戲。 本逐步解說將著重于設定包含 iOS 和 Android 專案的方案，以及跨平臺程式碼的共用程式碼專案。

完成時，專案具有適當的結構，可在每秒30個畫面上執行遊戲更新邏輯和遊戲繪製邏輯。 它可用來做為任何 MonoGame 專案的基底專案。 執行時，專案看起來會像這樣：

![空白藍色畫面](part1-images/image1.png)

## <a name="adding-monogame-to-visual-studio"></a>將 MonoGame 新增至 Visual Studio

> [!IMPORTANT]
> MonoGame 預設不會安裝在 Visual Studio 2019 或 Visual Studio for Mac 中。
>
> 您應該從 http://www.monogame.net/downloads/ 手動下載並安裝最新版本，然後執行安裝程式。 您可能需要重新開機 Visual Studio，範本才會出現。
>
> [**遊戲開發**] 區段應該會出現在 [**增益集管理員**] 中。

若要啟用 Visual Studio for Mac 的 MonoGame 增益集，請選取 [ **Visual Studio for Mac**  > **增益集管理員**]。 針對 Windows 上的 Visual Studio 2019，請選取 [**工具**]  >  [**增益集管理員**]。選取 [主機**庫**] 索引標籤，展開 [**遊戲開發**] 類別並選取 [ **MonoGame 載入**宏]，然後按一下 [**安裝**]：

![Visual Studio for Mac 延伸模組資源庫選取 MonoGame](part1-images/image2.png)

安裝之後，MonoGame 範本會出現在 Visual Studio for Mac 中，如我們在下一節中所見。

## <a name="creating-a-new-solution"></a>建立新的解決方案

在 Visual Studio for Mac 選取 [檔案 **> 新增方案**]。 在 [**新增專案**] 對話方塊中，按一下 [**其他**]，並流覽至 [**一般**] 區段，選取 [**通用 MonoGame 行動應用程式**] 選項，然後按 [下一步]。

![建立 MonoGame 應用程式的 [新增專案] 對話方塊](part1-images/image3.png)

將專案命名為 WalkingGame，然後按一下 [建立]：

![選取名稱和位置的 [新增專案] 對話方塊](part1-images/image4.png)

現在，我們的專案會如同任何其他 iOS 或 Android 專案般執行。 專案應該會執行顯示矢菊花藍色背景：

![空白藍色應用程式背景](part1-images/image5.png)

## <a name="fixing-android-compile-errors"></a>修正 Android 編譯錯誤

目前版本的 MonoGame 範本在 Android 的 `Activity1.cs` 檔案中包含幾個語法錯誤。 若要修正這些問題，請將 `OnCreate` 函式取代為下列內容：

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

本逐步解說涵蓋如何使用 Visual Studio for Mac 建立跨平臺的 MonoGame 專案。 這是一個空的藍色畫面。 此專案可用來做為任何 iOS 和 Android 遊戲的起點。

## <a name="related-links"></a>相關連結

- [MonoGame Android NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [MonoGame iOS NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [MonoGame API 檔](http://www.monogame.net/documentation/?page=main)
