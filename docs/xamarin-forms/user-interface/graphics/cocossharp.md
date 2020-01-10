---
title: 在 Xamarin 中使用 CocosSharp
description: CocosSharp 可以用來將精確的圖形、影像和文字轉譯新增至應用程式，以進行先進的視覺效果
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2016
ms.openlocfilehash: d13237f270fe01e2a91b69a60c3109843db713bf
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728005"
---
# <a name="using-cocossharp-in-xamarinforms"></a>在 Xamarin 中使用 CocosSharp

[![下載範例](~/media/shared/download.png) 下載範例](h https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)

_CocosSharp 可以用來將精確的圖形、影像和文字轉譯新增至應用程式，以進行先進的視覺效果_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**演進2016： Xamarin 中的科科 # #**

## <a name="overview"></a>概觀

CocosSharp 是一種彈性且功能強大的技術，可用於顯示圖形、讀取觸控輸入、播放音訊及管理內容。 本指南說明如何將 CocosSharp 新增至 Xamarin. Forms 應用程式。 其中涵蓋下列各項：

- [什麼是 CocosSharp？](#what)
- [新增 CocosSharp NuGet 套件](#nuget)
- [逐步解說：將 CocosSharp 新增至 Xamarin. Forms 應用程式](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>什麼是 CocosSharp？

[CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md)是 Xamarin 平臺上提供的開放原始碼遊戲引擎。
CocosSharp 是執行時間有效率的程式庫，其中包含下列功能：

- 使用 `CCSprite` 類別呈現影像
- 使用 `CCDrawNode` 類別的圖形呈現
- 使用 `CCNode.Schedule` 類別的每個畫面格邏輯
- 使用 `CCTextureCache` 的內容管理（載入和卸載資源，例如 .png 檔案）
- 使用 `CCAction` 類別的動畫

CocosSharp 的主要重點在於簡化跨平臺2D 遊戲的建立;不過，它也可以是 Xamarin 表單應用程式的絕佳新增。 因為遊戲通常需要有效率的轉譯和精確控制視覺效果，所以 CocosSharp 可以用來在非遊戲應用程式中新增強大的視覺效果和效果。

Xamarin 是以原生平臺特定的 UI 系統為基礎。 例如， [`Button`](xref:Xamarin.Forms.Button)會在 IOS 和 Android 上以不同的方式出現，甚至可能因作業系統版本而有所不同。 相反地，CocosSharp 不會使用任何平臺特定的視覺物件，因此所有的視覺物件在所有平臺上都是相同的。 當然，裝置之間的解析度和外觀比例會有所不同，而這可能會影響 CocosSharp 呈現其視覺效果的方式。 本指南稍後會討論這些詳細資料。

您可以在[CocosSharp 一節](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md)中找到更詳細的資訊。

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>新增 CocosSharp NuGet 套件

在使用 CocosSharp 之前，開發人員必須對其 Xamarin. Forms 專案進行一些新增。
本指南假設有一個包含 iOS、Android 和 .NET Standard 程式庫專案的 Xamarin. Forms 專案。
所有程式碼都會寫入 .NET Standard 程式庫專案中;不過，您必須將程式庫新增至 iOS 和 Android 專案。

CocosSharp NuGet 套件包含建立 CocosSharp 物件所需的所有物件。
CocosSharp NuGet 套件包含 `CocosSharpView` 類別，用來裝載 Xamarin 中的 CocosSharp。
新增**CocosSharp。** NuGet 和**CocosSharp**也會自動新增。
若要這麼做，請以滑鼠右鍵按一下 .NET Standard 程式庫專案中的 [**套件**] 資料夾，然後選取 [**新增套件**]。輸入搜尋詞彙**CocosSharp**，**針對 [Xamarin**] 選取 [CocosSharp]，然後按一下 [**新增套件**]。

![](cocossharp-images/image1.png "Add Packages Dialog")

**CocosSharp**和**CocosSharp**都會將 NuGet 套件新增至專案：

![](cocossharp-images/image2.png "Packages Folder")

針對平臺特定專案（例如 iOS 和 Android）重複上述步驟。

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>逐步解說：將 CocosSharp 新增至 Xamarin. Forms 應用程式

請遵循下列步驟，將簡單的 CocosSharp 視圖新增至 Xamarin. Forms 應用程式：

1. [建立 Xamarin 表單頁面](#1)
1. [新增 CocosSharpView](#2)
1. [建立 GameScene](#3)
1. [新增圓形](#4)
1. [與 CocosSharp 互動](#5)

當您成功地將 CocosSharp 視圖新增至 Xamarin 應用程式之後，請造訪[CocosSharp 檔](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md)，以深入瞭解如何使用 CocosSharp 建立內容。

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. 建立 Xamarin Forms 頁面

CocosSharp 可以裝載于任何 Xamarin. Forms 容器中。 此頁面的這個範例會使用稱為 `HomePage`的頁面。 `HomePage` 是以半 `Grid` 分割，以顯示如何在相同頁面上同時轉譯 Xamarin. Forms 和 CocosSharp。

首先，設定頁面，使其包含 `Grid` 和兩個 `Button` 實例：

```csharp
public class HomePage : ContentPage
{
public HomePage ()
    {
        // This is the top-level grid, which will split our page in half
        var grid = new Grid ();
        this.Content = grid;
        grid.RowDefinitions = new RowDefinitionCollection {
            // Each half will be the same size:
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
        };
        CreateTopHalf (grid);
        CreateBottomHalf (grid);
    }
    void CreateTopHalf(Grid grid)
    {
        // We'll be adding our CocosSharpView here:
    }
    void CreateBottomHalf(Grid grid)
    {
        // We'll use a StackLayout to organize our buttons
        var stackLayout = new StackLayout();
        // The first button will move the circle to the left when it is clicked:
        var moveLeftButton = new Button {
            Text = "Move Circle Left"
        };
        stackLayout.Children.Add (moveLeftButton);

        // The second button will move the circle to the right when clicked:
        var moveCircleRight = new Button {
            Text = "Move Circle Right"
        };
        stackLayout.Children.Add (moveCircleRight);
        // The stack layout will be in the bottom half (row 1):

        grid.Children.Add (stackLayout, 0, 1);
    }
}
```

在 iOS 上，`HomePage` 會顯示如下圖所示：

![](cocossharp-images/image3.png "HomePage Screenshot")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2. 加入 CocosSharpView

`CocosSharpView` 類別是用來將 CocosSharp 內嵌至 Xamarin. Forms 應用程式。 由於 `CocosSharpView` 繼承自 node.js[類別，](xref:Xamarin.Forms.View)因此它提供了熟悉的版面配置介面，而且可以在版面配置容器中使用，例如 [Xamarin.[表單方格](xref:Xamarin.Forms.Grid)]。 藉由完成 `CreateTopHalf` 方法，將新的 `CocosSharpView` 新增至專案：

```csharp
void CreateTopHalf(Grid grid)
{
    // This hosts our game view.
    var gameView = new CocosSharpView () {
        // Notice it has the same properties as other XamarinForms Views
        HorizontalOptions = LayoutOptions.FillAndExpand,
        VerticalOptions = LayoutOptions.FillAndExpand,
        // This gets called after CocosSharp starts up:
        ViewCreated = HandleViewCreated
    };
    // We'll add it to the top half (row 0)
    grid.Children.Add (gameView, 0, 0);
}
```

CocosSharp 初始化不是立即的，因此，請在 `CocosSharpView` 完成建立時，為其註冊事件。 請在 `HandleViewCreated` 方法中執行此動作：

```csharp
void HandleViewCreated (object sender, EventArgs e)
{
    var gameView = sender as CCGameView;
    if (gameView != null)
    {
        // This sets the game "world" resolution to 100x100:
        gameView.DesignResolution = new CCSizeI (100, 100);
        // GameScene is the root of the CocosSharp rendering hierarchy:
        gameScene = new GameScene (gameView);
        // Starts CocosSharp:
        gameView.RunWithScene (gameScene);
    }
}
```

`HandleViewCreated` 方法有兩個我們要查看的重要詳細資料。 第一個是 `GameScene` 類別，將在下一節中建立。 請務必注意，在建立 `GameScene` 並解析 `gameScene` 實例參考之前，應用程式將不會編譯。

第二個重要的詳細資料是 `DesignResolution` 屬性，它會定義遊戲 CocosSharp 物件的可見區域。 建立 `GameScene`之後，將會查看 `DesignResolution` 屬性。

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. 建立 GameScene

`GameScene` 類別繼承自 CocosSharp 的 `CCScene`。 `GameScene` 是我們純粹處理 CocosSharp 的第一個重點。 包含在 `GameScene` 中的程式碼將會在任何 CocosSharp 應用程式中運作，不論其是否裝載于 Xamarin. Forms 專案中。

`CCScene` 類別是所有 CocosSharp 轉譯的視覺化根目錄。 任何可見的 CocosSharp 物件都必須包含在 `CCScene`中。 更具體來說，必須將視覺物件加入 `CCLayer` 實例中，而且這些 `CCLayer` 實例必須加入至 `CCScene`。

下圖可協助您將一般 CocosSharp 階層視覺化：

![](cocossharp-images/image4.png "Typical CocosSharp Hierarchy")

一次只能有一個作用中的 `CCScene`。 大部分的遊戲會使用多個 `CCLayer` 實例來排序內容，但我們的應用程式只會使用一個。 同樣地，大部分的遊戲都使用多個視覺物件，但我們的應用程式中只會有一個。 您可以在[BouncingGame 逐步](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/bouncing-game.md)解說中找到有關 CocosSharp 視覺化階層的詳細討論。

一開始，`GameScene` 類別幾乎是空的，我們只會建立它以滿足 `HomePage`中的參考。 將新類別新增至名為 `GameScene`的 .NET Standard 程式庫專案。 它應該繼承自 `CCScene` 類別，如下所示：

```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

現在已定義 `GameScene`，我們可以返回 `HomePage` 並新增欄位：

```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

我們現在可以編譯專案並加以執行，以查看 CocosSharp 正在執行。 我們尚未在 `GameScene,` 中新增任何內容，因此頁面上半部是黑色– CocosSharp 場景的預設色彩：

![](cocossharp-images/image5.png "Blank GameScene")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. 加入圓形

應用程式目前有執行中的 CocosSharp 引擎實例，並顯示空的 `CCScene`。 接下來，我們要加入一個視覺物件：一個圓形。 `CCDrawNode` 類別可以用來繪製各種幾何形狀，如[使用 CCDrawNode 的繪圖幾何](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md)中所述。

將圓形加入我們的 `GameScene` 類別，並在此函式中將它具現化，如下列程式碼所示：

```csharp
public class GameScene : CCScene
{
    CCDrawNode circle;
    public GameScene (CCGameView gameView) : base(gameView)
    {
        var layer = new CCLayer ();
        this.AddLayer (layer);
        circle = new CCDrawNode ();
        layer.AddChild (circle);
        circle.DrawCircle (
            // The center to use when drawing the circle,
            // relative to the CCDrawNode:
            new CCPoint (0, 0),
            radius:15,
            color:CCColor4B.White);
        circle.PositionX = 20;
        circle.PositionY = 50;
    }
}
```

執行應用程式現在會在 CocosSharp 顯示區域的左側顯示一個圓圈：

![](cocossharp-images/image6.png "Circle in GameScene")

#### <a name="understanding-designresolution"></a>瞭解 DesignResolution

現在已顯示 visual CocosSharp 物件，我們可以調查 `DesignResolution` 屬性。

`DesignResolution` 代表用來放置和調整物件大小之 CocosSharp 區域的寬度和高度。 區域的實際解析是以*圖元*為單位測量，而 `DesignResolution` 則是以世界*單位*測量。 下圖顯示在 iPhone 5 上以640x1136 圖元的螢幕解析度顯示的各種視圖部分的解決方式：

![](cocossharp-images/image7.png "iPhone 5s Design Resolution")

上圖會以黑色文字顯示畫面外部的圖元尺寸。 單位會以白色文字顯示在圖表的內部。 以下是上面顯示的一些重要詳細資料：

- CocosSharp 顯示的來源位於左下方。 向右移動會增加 X 值，而向上移動則會增加 Y 值。 請注意，Y 值與一些其他2D 版面配置引擎相反，其中（0，0）是畫布的左上角。
- CocosSharp 的預設行為是維持其觀點的外觀比例。 因為方格中的第一個資料列寬度大於高度，所以 CocosSharp 不會填滿其資料格的整個寬度，如虛線白色矩形所示。 可以變更此行為，如在[CocosSharp 中處理多個解決方法](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/resolutions.md)中所述。
- 在此範例中，不論裝置的大小或外觀比例為何，CocosSharp 都會維持100單位寬度和高度的顯示區域。 這表示程式碼可以假設 X = 100 代表 CocosSharp 顯示的最右側系結，在所有裝置上保持版面配置一致。

#### <a name="ccdrawnode-details"></a>CCDrawNode 詳細資料

我們的簡單應用程式會使用 `CCDrawNode` 類別來繪製圓形。 這個類別對於商務應用程式非常有用，因為它提供以向量為基礎的 geometry 轉譯– Xamarin 中遺漏的功能。 除了圓形外，`CCDrawNode` 類別也可以用來繪製矩形、曲線、線條和自訂多邊形。 `CCDrawNode` 也很容易使用，因為它不需要使用影像檔案（例如 .png）。 如需 CCDrawNode 的更詳細討論，請參閱[使用 CCDrawNode 的繪圖幾何指南](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md)。

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. 與 CocosSharp 互動

CocosSharp 視覺元素（例如 `CCDrawNode`）會繼承自 `CCNode` 類別。 `CCNode` 提供兩個屬性，可用來放置相對於其父系的物件： `PositionX` 和 `PositionY`。 我們的程式碼目前會使用這兩個屬性來定位圓形的中心，如下列程式碼片段所示：

```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

請務必注意，CocosSharp 物件是以明確的位置值定位，而不是大部分的 Xamarin。表單檢視，會根據其父系版面配置控制項的行為自動定位。

我們會加入程式碼，讓使用者按一下兩個按鈕的其中之一，將圓形向左或向右移動10個單位（而不是圖元），因為圓形會在 CocosSharp 的世界單位空間中繪製。 首先，我們將在 `GameScene` 類別中建立兩個公用方法：

```csharp
public void MoveCircleLeft()
{
    circle.PositionX -= 10;
}

public void MoveCircleRight()
{
    circle.PositionX += 10;
}
```

接下來，我們會將處理常式新增至 `HomePage` 中的兩個按鈕，以回應按下的動作。 完成後，我們的 `CreateBottomHalf` 方法會包含下列程式碼：

```csharp
void CreateBottomHalf(Grid grid)
{
    // We'll use a StackLayout to organize our buttons
    var stackLayout = new StackLayout();

    // The first button will move the circle to the left when it is clicked:
    var moveLeftButton = new Button {
        Text = "Move Circle Left"
    };
    moveLeftButton.Clicked += (sender, e) => gameScene.MoveCircleLeft ();
    stackLayout.Children.Add (moveLeftButton);

    // The second button will move the circle to the right when clicked:
    var moveCircleRight = new Button {
        Text = "Move Circle Right"
    };
    moveCircleRight.Clicked += (sender, e) => gameScene.MoveCircleRight ();
    stackLayout.Children.Add (moveCircleRight);

    // The stack layout will be in the bottom half (row 1):
    grid.Children.Add (stackLayout, 0, 1);
}
```

CocosSharp 圓形現在會移動以回應按下的動作。 我們也可以清楚地看到 CocosSharp 畫布的界限，其方式是將圓形向左或向右移動夠遠：

![](cocossharp-images/image8.png "GameScene with Moving Circle")

## <a name="summary"></a>摘要

本指南說明如何將 CocosSharp 新增至現有的 Xamarin 專案、如何建立 Xamarin 和 CocosSharp 之間的互動，以及討論在 CocosSharp 中建立配置時的各種考慮。

CocosSharp 遊戲引擎提供許多功能和深度，因此本指南只會將 CocosSharp 所能執行的動作呈現在表面上。 有興趣閱讀更多 CocosSharp 的開發人員，可以在[CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/)封存中找到許多文章。

## <a name="related-links"></a>相關連結

- [CocosSharpForms （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)
