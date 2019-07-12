---
title: 在 Xamarin.Forms 中使用 CocosSharp
description: CocosSharp 可用來加入應用程式的進階視覺效果中的精確的圖形、 影像和文字轉譯
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2016
ms.openlocfilehash: c493b43edbfa04aafba56c4b47d7c89b455de423
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830476"
---
# <a name="using-cocossharp-in-xamarinforms"></a>在 Xamarin.Forms 中使用 CocosSharp

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/CocosSharpForms/)

_CocosSharp 可用來加入應用程式的進階視覺效果中的精確的圖形、 影像和文字轉譯_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Evolve 2016：Cocos # 在 Xamarin.Forms 中**

## <a name="overview"></a>總覽

CocosSharp 是一種有彈性且功能強大的技術，用於顯示圖形、 讀取具備觸控輸入、 播放音效，與管理內容。 本指南說明如何將 CocosSharp 新增至 Xamarin.Forms 應用程式。 它涵蓋下列資訊：

* [CocosSharp 是什麼？](#what)
* [新增 CocosSharp Nuget 套件](#nuget)
* [逐步解說：將 CocosSharp 新增至 Xamarin.Forms 應用程式](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>CocosSharp 是什麼？

[CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md)是一個開放原始碼遊戲引擎，可在 Xamarin 平台上取得。
CocosSharp 是執行階段有效的程式庫包括下列功能：

* 影像轉譯使用`CCSprite`類別
* 使用圖形轉譯`CCDrawNode`類別
* 每個畫面格邏輯使用`CCNode.Schedule`類別
* 使用內容管理 （載入和卸載的資源，例如.png 檔案） `CCTextureCache`
* 使用動畫`CCAction`類別

CocosSharp 的主要焦點在於簡化跨平台的 2D 遊戲; 建立不過，它也可以是 Xamarin 表單應用程式增添絕佳的工具。 遊戲通常需要有效的轉譯和精確地控制視覺效果，因為 CocosSharp 可用來將強大的視覺效果和效果新增至非遊戲應用程式。

Xamarin.Forms 是以原生、 平台特定編碼 UI 系統為基礎。 例如， [ `Button`s](xref:Xamarin.Forms.Button)以不同的方式出現在 iOS 和 Android 上，甚至可能會因作業系統版本。 相較之下，CocosSharp 不使用任何平台專屬視覺物件，因此所有視覺物件會出現在所有平台上完全相同。 當然，解析和外觀比例不同的裝置，而這可能會影響 CocosSharp 其視覺效果的轉譯方式。 本指南稍後將討論這些詳細資料。

更詳細的資訊可在[CocosSharp 區段](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md)。

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>新增 CocosSharp Nuget 套件

之前使用 CocosSharp，開發人員必須進行了一些附加功能其 Xamarin.Forms 專案。
本指南假設 iOS、 Android 和.NET Standard 的 Xamarin.Forms 專案程式庫專案。
所有的程式碼會以.NET Standard 程式庫專案中;不過，程式庫必須新增至 iOS 和 Android 專案。

CocosSharp Nuget 套件包含所有建立 CocosSharp 物件所需的物件。
CocosSharp.Forms nuget 套件包含`CocosSharpView`類別，用來主控 CocosSharp 在 Xamarin.Forms 中。
新增**CocosSharp.Forms** NuGet 並**CocosSharp**將會自動加入以及。
若要這樣做，以滑鼠右鍵按一下<span class="UIItem">封裝</span>資料夾中，.NET Standard 程式庫專案，然後選取<span class="UIItem">新增套件...</span>.輸入搜尋字詞<span class="UIItem">CocosSharp.Forms</span>，選取<span class="UIItem">適用於 Xamarin.Forms CocosSharp</span>，然後按一下<span class="UIItem">加入封裝</span>。

![](cocossharp-images/image1.png "新增套件 對話方塊")

兩者**CocosSharp**並**CocosSharp.Forms** NuGet 封裝會加入至專案：

![](cocossharp-images/image2.png "Packages 資料夾")

（例如 iOS 和 Android） 平台專屬專案重複上述步驟。

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>逐步解說：將 CocosSharp 新增至 Xamarin.Forms 應用程式

請遵循下列步驟來新增至 Xamarin.Forms 應用程式的簡單的 CocosSharp 檢視：

1. [建立 Xamarin Forms 頁面](#1)
1. [新增 CocosSharpView](#2)
1. [建立 GameScene](#3)
1. [新增圓形](#4)
1. [搭配 cocossharp 使用 Tiled 互動](#5)

一旦您已成功將 Xamarin.Forms 應用程式的 CocosSharp 檢視加入，請瀏覽[CocosSharp 文件](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md)來了解如何建立搭配 cocossharp 使用 Tiled 的內容。

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1.建立 Xamarin Forms 頁面

CocosSharp 可以裝載在 Xamarin.Forms 中的任何容器。 此頁面的這個範例使用一個稱為`HomePage`。 `HomePage` 分割成兩半的`Grid`顯示如何 Xamarin.Forms 以及 CocosSharp 可以呈現同時在相同頁面上。

首先，設定頁面，使其包含`Grid`並將兩個`Button`執行個體：


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

在 iOS 上，`HomePage`隨即顯示在下圖所示：

![](cocossharp-images/image3.png "首頁的螢幕擷取畫面")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2.新增 CocosSharpView

`CocosSharpView`類別用來將 CocosSharp 內嵌至 Xamarin.Forms 應用程式。 由於`CocosSharpView`繼承自[Xamarin.Forms.View](xref:Xamarin.Forms.View)類別，它提供熟悉的介面進行配置，而且它可用版面配置容器內這類[Xamarin.Forms.Grid](xref:Xamarin.Forms.Grid)。 加入新`CocosSharpView`藉由完成專案`CreateTopHalf`方法：


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

CocosSharp 初始化不會立即，因此當登錄一則事件`CocosSharpView`已完成建立。 這樣在`HandleViewCreated`方法：


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

`HandleViewCreated`方法有兩個重要的詳細資訊，我們將查看。 第一個是`GameScene`類別，將會建立下一節。 請務必注意應用程式之前不會編譯`GameScene`建立和`gameScene`執行個體的參考會解析。

第二個重要的詳細資料是`DesignResolution`屬性，定義該遊戲的 CocosSharp 物件的可見區域。 `DesignResolution`屬性會在建立之後查看`GameScene`。

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3.建立 GameScene

`GameScene`類別繼承自 CocosSharp 的`CCScene`。 `GameScene` 是，我們會處理純粹 CocosSharp 的第一個點。 中所包含的程式碼`GameScene`是否它位於 Xamarin.Forms 專案內或不會在任何 CocosSharp 應用程式中的函式。

`CCScene`類別是所有 CocosSharp 呈現的根 visual。 任何可見的 CocosSharp 物件必須包含在`CCScene`。 更具體來說，視覺物件必須新增至`CCLayer`執行個體，以及那些`CCLayer`執行個體必須加入至`CCScene`。

下圖可協助您以視覺化方式檢視一般 CocosSharp 階層：

![](cocossharp-images/image4.png "典型的 CocosSharp 階層")

只有一個`CCScene`可同時處於作用中。 大多數的遊戲使用多個`CCLayer`排序內容，但我們的應用程式的執行個體僅使用一個。 同樣地，大多數遊戲使用多個視覺物件，但我們只需要在我們的應用程式中。 更詳細討論視覺階層可在 CocosSharp [BouncingGame 逐步解說](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/bouncing-game.md)。

最初`GameScene`類別會幾乎是空白 – 我們只是要建立它以滿足中的參考`HomePage`。 名為.NET Standard 程式庫專案中加入新的類別`GameScene`。 它應該繼承自`CCScene`類別，如下所示：


```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

既然`GameScene`是定義，我們可以返回`HomePage`和加入欄位：


```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

我們現在可以編譯專案，並執行它以查看 CocosSharp 執行。 我們還沒有新增任何我們`GameScene,`因此我們頁面的上半部是黑色 – CocosSharp 場景的預設色彩：

![](cocossharp-images/image5.png "空白 GameScene")

<a name="4" />

### <a name="4-adding-a-circle"></a>4.新增圓形

應用程式目前有顯示空的 CocosSharp 引擎的執行個體`CCScene`。 接下來，我們要在其中加入視覺物件： 一個圓形。 `CCDrawNode`類別可以用來繪製各種不同的幾何圖形中, 所述[繪製 Geometry ccdrawnode 建立指南](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md)。

新增一個圓形來我們`GameScene`類別，並加以具現化建構函式中的下列程式碼所示：


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

現在執行應用程式會顯示圓形 CocosSharp 顯示區域左邊：

![](cocossharp-images/image6.png "GameScene 的圓形")


#### <a name="understanding-designresolution"></a>了解 DesignResolution

現在，顯示視覺的 CocosSharp 物件時，我們可以調查`DesignResolution`屬性。

`DesignResolution`代表 CocosSharp 區域，以放置並調整物件大小的高度與寬度。 區域的實際的解決方案以單位*像素為單位*雖然`DesignResolution`測量世界*單位*。 下圖顯示檢視為 iphone 5 以上 640 x 1136 像素螢幕解析度上所顯示的各個部分的解決方式：

![](cocossharp-images/image7.png "iPhone 5 秒設計解析")

上圖顯示像素尺寸外部的黑色文字中的畫面。 單位會顯示在內部的白色文字中的圖表。 以下是一些上方顯示的重要詳細資料：

* CocosSharp 顯示的原點位於左下方。 向右移動會增加 X 值，並向上移增加的 Y 值。 請注意，要反轉的 Y 值相較於其他一些 2D 版面配置引擎，其中 (0，0) 是畫布的左上方。
* CocosSharp 的預設行為是要維持其檢視的外觀比例。 由於在方格中的第一個資料列寬度比高度長，CocosSharp 不填滿的整個寬度其儲存格，虛線的白色矩形所示。 此行為可以變更，如中所述[處理 CocosSharp 中的多個解決方法指南](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/resolutions.md)。
* 在此範例中，寬度與高度，無論規模大小的 100 個單位的顯示區域或其裝置的外觀比例，將會維持 CocosSharp。 這表示程式碼可以假設，X = 100 表示最右側的繫結 CocosSharp 的顯示，請在所有裝置上一致的保留配置。

#### <a name="ccdrawnode-details"></a>Ccdrawnode 建立詳細資料

我們的簡單應用程式會使用`CCDrawNode`類別用來繪製圓形。 這個類別可以是非常適合商務應用程式，因為它提供以向量為基礎的幾何轉譯-Xamarin.Forms 中遺漏的功能。 圓形，除了`CCDrawNode`類別可以用來繪製矩形、 曲線、 線條和自訂的多邊形。 `CCDrawNode` 也是簡單易用，因為它不需要使用的映像檔案 （例如.png)。 中可以找到 ccdrawnode 建立的更詳細的討論[ccdrawnode 建立指南繪製 Geometry](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md)。

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5.搭配 cocossharp 使用 Tiled 互動

CocosSharp 視覺項目 (例如`CCDrawNode`) 會繼承`CCNode`類別。 `CCNode` 提供可用來放置相對於其父物件的兩個屬性：`PositionX`和`PositionY`。 此程式碼片段所示，我們的程式碼會將這兩個屬性目前使用的圓圈，中心：


```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

請務必注意 CocosSharp 物件位於所明確的位置值，而不是大部分的 Xamarin.Forms 檢視，會自動放置依據其父系版面配置控制項的行為。

我們將新增程式碼，讓使用者可以按一下向左或向右移動圓形的 10 個單位 （不像素為單位，因為圓形繪製 CocosSharp 全局單位空間中） 的兩個按鈕的其中一個。 我們將建立兩個公用方法的第一次`GameScene`類別：


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

接下來，我們會在這裡將處理常式中的兩個按鈕加入`HomePage`回應按下。 完成時，我們`CreateBottomHalf`方法包含下列程式碼：


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

CocosSharp 圓形現在移動中的按選動作的回應。 我們也清楚 CocosSharp 畫布的界限所見到足以將圓形移到左邊或右邊：

![](cocossharp-images/image8.png "將圓形移 GameScene")

## <a name="summary"></a>總結

本指南說明如何將 CocosSharp 新增至現有的 Xamarin.Forms 專案、 如何建立 Xamarin.Forms 與 CocosSharp，之間的互動，並建立 CocosSharp 中的版面配置時，討論的各種考量。

CocosSharp 的遊戲引擎會提供許多功能和深度，因此本指南只觸及了 CocosSharp 可以做什麼。 開發人員想要深入了解 CocosSharp 中的可以找到許多文章[CocosSharp 封存](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/)。

## <a name="related-links"></a>相關連結

- [CocosSharpForms （範例）](https://developer.xamarin.com/samples/xamarin-forms/CocosSharpForms/)
