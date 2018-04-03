---
title: 使用 Xamarin.Forms 中 CocosSharp
description: CocosSharp 可用於進階視覺效果應用程式中加入精確圖形、 影像和文字轉譯
ms.topic: article
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/03/2016
ms.openlocfilehash: 83852b6b2d7324ae6aaf6b1dbf86a6ef7f9ac509
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="using-cocossharp-in-xamarinforms"></a>使用 Xamarin.Forms 中 CocosSharp

_CocosSharp 可用於進階視覺效果應用程式中加入精確圖形、 影像和文字轉譯_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**發展 2016年: Cocos # Xamarin.Forms 中**

## <a name="overview"></a>總覽

CocosSharp 是一種具彈性且功能強大的技術，用於顯示圖形、 讀取具備觸控輸入、 播放音訊及管理內容。 本指南說明如何加入 CocosSharp Xamarin.Forms 應用程式。 它涵蓋下列資訊：

* [什麼是 CocosSharp？](#what)
* [新增 CocosSharp Nuget 封裝](#nuget)
* [逐步解說： 加入 CocosSharp Xamarin.Forms 應用程式](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>什麼是 CocosSharp？

[CocosSharp](~/graphics-games/cocossharp/index.md)是一種開放原始碼遊戲引擎，可在 Xamarin 平台上取得。
CocosSharp 是執行階段有效率的程式庫包括下列功能：

* 影像轉譯使用[CCSprite 類別](https://developer.xamarin.com/api/type/CocosSharp.CCSprite/)
* 使用圖形呈現[CCDrawNode 類別](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
* 每個框架邏輯使用[CCNode.Schedule 方法](https://developer.xamarin.com/api/member/CocosSharp.CCNode.Schedule/p/System.Action%7BSystem.Single%7D/)
* 內容管理 （載入和卸載的資源，例如.png 檔案） 使用[CCTextureCache 類別](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
* 使用動畫[CCAction 類別](https://developer.xamarin.com/api/type/CocosSharp.CCAction/)

CocosSharp 的主要焦點在於簡化建立跨平台 2D 遊戲。不過，它也可以是另一項絕佳 Xamarin 表單應用程式。 由於遊戲通常需要有效率的轉譯和精確地控制視覺效果，CocosSharp 可用來新增非遊戲應用程式功能強大的視覺效果和效果。

Xamarin.Forms 是以原生、 平台專屬的 UI 系統為基礎。 例如， [ `Button`s](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)顯示 iOS 和 Android 上的方式會和可能甚至根據作業系統版本之間的差異。 相反地，CocosSharp 不使用任何平台專屬視覺物件，因此所有的 visual 物件會出現在所有平台上完全相同。 當然，解析及長寬比裝置之間的差異，可能會影響 CocosSharp 其視覺效果的呈現方式。 在本指南稍後將討論這些詳細資料。

更詳細的資訊位於[CocosSharp 區段](~/graphics-games/cocossharp/index.md)。

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>新增 CocosSharp Nuget 封裝

在使用之前 CocosSharp，開發人員需要進行一些 Xamarin.Forms 專案新增項目。
本指南假設在 Xamarin.Forms 專案中的使用 iOS、 Android 和 PCL 專案。
所有的程式碼會寫入 PCL 專案中不過，必須新增程式庫的 iOS 和 Android 專案。

CocosSharp Nuget 封裝包含所有建立 CocosSharp 物件所需的物件。
CocosSharp.Forms nuget 套件會包含`CocosSharpView`用來裝載 CocosSharp Xamarin.Forms 中的類別。
新增**CocosSharp.Forms** NuGet 和**CocosSharp**將會自動加入以及。
若要這樣做，以滑鼠右鍵按一下 PCL<span class="UIItem">封裝</span>資料夾，然後選取<span class="UIItem">新增套件...</span>.輸入搜尋字詞<span class="UIItem">CocosSharp.Forms</span>，選取<span class="UIItem">Xamarin.Forms 的 CocosSharp</span>，然後按一下 <span class="UIItem">加入封裝</span>。

![](cocossharp-images/image1.png "新增套件 對話方塊")

同時**CocosSharp**和**CocosSharp.Forms** NuGet 封裝將會加入至專案：

![](cocossharp-images/image2.png "封裝資料夾")

平台專屬專案中 （例如 iOS 和 Android） 重複上述步驟。

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>逐步解說： 加入 CocosSharp Xamarin.Forms 應用程式

請依照下列步驟將簡單的 CocosSharp 檢視加入至 Xamarin.Forms 應用程式：

1. [建立 Xamarin Forms 頁面](#1)
1. [加入 CocosSharpView](#2)
1. [建立 GameScene](#3)
1. [加入圓形](#4)
1. [與 CocosSharp 互動](#5)

一旦您已成功新增至 Xamarin.Forms 應用程式的 CocosSharp 檢視，請瀏覽[CocosSharp 文件](~/graphics-games/cocossharp/index.md)若要深入了解使用 CocosSharp 建立內容。

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1.建立 Xamarin Forms 頁面

CocosSharp 可以裝載任何 Xamarin.Forms 容器中。 此頁面的這個範例使用一個稱為`HomePage`。 `HomePage` 分割成兩半由`Grid`顯示 Xamarin.Forms 和 CocosSharp 可以將如何轉譯同時在相同頁面上。

首先，設定頁面，使其包含`Grid`和兩個`Button`執行個體：


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

在 iOS、`HomePage`會出現在下圖所示：

![](cocossharp-images/image3.png "首頁的螢幕擷取畫面")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2.加入 CocosSharpView

`CocosSharpView`類別用來將 CocosSharp 內嵌至 Xamarin.Forms 應用程式。 因為`CocosSharpView`繼承自[Xamarin.Forms.View](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)類別，它提供了熟悉的介面配置，而且可以用於版面配置容器內例如[Xamarin.Forms.Grid](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)。 加入新`CocosSharpView`完成專案`CreateTopHalf`方法：


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

`HandleViewCreated`方法有兩個重要的詳細資訊，我們會查看。 第一個是`GameScene`類別，會在下一節中建立。 請務必注意應用程式不會編譯直到`GameScene`建立和`gameScene`執行個體參考已解決。

第二個的重要詳細資料是`DesignResolution`屬性，定義 CocosSharp 物件的遊戲的可見區域。 `DesignResolution`屬性將會在建立之後查看`GameScene`。

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3.建立 GameScene

`GameScene`類別繼承自 CocosSharp 的`CCScene`。 `GameScene` 是其中我們處理單純 CocosSharp 的第一個點。 包含在程式碼`GameScene`是否它位於 Xamarin.Forms 專案內或不會在任何 CocosSharp 應用程式中的函式。

`CCScene`類別是所有 CocosSharp 轉譯的根 visual。 任何可見的 CocosSharp 物件必須包含在`CCScene`。 更具體來說，必須新增視覺物件至`CCLayer`執行個體，以及那些`CCLayer`執行個體必須加入到`CCScene`。

下圖可協助視覺化一般 CocosSharp 階層：

![](cocossharp-images/image4.png "一般 CocosSharp 階層")

只有一個`CCScene`每次最多可處於作用中。 大多數的遊戲使用多個`CCLayer`排序的內容，但我們的應用程式的執行個體僅使用一個。 同樣地，大多數的遊戲使用多個視覺化的物件，但我們只需要在我們的應用程式中。 更詳細討論位於視覺階層 CocosSharp [BouncingGame 逐步解說](~/graphics-games/cocossharp/bouncing-game.md)。

一開始`GameScene`類別將會幾乎空白 – 我們將只建立它以滿足中的參考`HomePage`。 將新類別加入名為您 PCL `GameScene`。 它應該是繼承自`CCScene`類別，如下所示：


```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

既然`GameScene`是定義，我們可以回來`HomePage`和加入欄位：


```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

我們現在可以編譯專案，並加以執行，請參閱 CocosSharp 執行。 我們還沒有加入任何項目我們`GameScene,`因此我們頁面的上半部是黑色 – CocosSharp 場景的預設色彩：

![](cocossharp-images/image5.png "Blank GameScene")

<a name="4" />

### <a name="4-adding-a-circle"></a>4.加入圓形

應用程式目前沒有 CocosSharp 引擎，顯示空的執行個體`CCScene`。 接下來，我們要在其中加入視覺物件： 一個圓形。 `CCDrawNode`類別可用來繪製幾何形狀，各種中所述[CCDrawNode 指南繪製幾何](~/graphics-games/cocossharp/ccdrawnode.md)。

新增圓形我們`GameScene`類別，並將它執行個體化建構函式中的下列程式碼所示：


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

現在，顯示視覺 CocosSharp 物件時，我們可以調查`DesignResolution`屬性。

`DesignResolution`代表 CocosSharp 區域以放置並調整物件大小的高度與寬度。 實際的解析度區域的為測量單位*像素為單位*時`DesignResolution`測量世界*單位*。 下圖顯示檢視，顯示在 iPhone 5 640 x 1136 像素為單位的螢幕解析度的各種組件的解析：

![](cocossharp-images/image7.png "iPhone 5 秒設計解析度")

上圖顯示以黑色文字螢幕外部的像素維度。 單位會顯示在圖表中白色文字的內部。 以下是部分上方顯示的重要詳細資料：

* CocosSharp 顯示的原點位於左下方。 移到右增加的 X 值，並向上移增加的 Y 值。 請注意 Y 值會被反轉相較於其他某些 2D 版面配置引擎，其中 (0，0) 是畫布左上方。
* CocosSharp 的預設行為是保持其檢視的外觀比例。 在方格中的第一個資料列的寬度比高度長的因為 CocosSharp 不填滿的整個寬度其儲存格，點狀的白色矩形所示。 此行為可以變更，如中所述[CocosSharp 中處理多個解決方案指南](~/graphics-games/cocossharp/resolutions.md)。
* 在此範例中，CocosSharp 會維護寬和頁高而不管大小的 100 個單位的顯示區域或其裝置的外觀比例。 這表示程式碼可以假設，X = 100 表示最右側繫結的 CocosSharp 顯示，維持一致的所有裝置上的版面配置。


#### <a name="ccdrawnode-details"></a>CCDrawNode 詳細資料

我們簡單的應用程式會使用`CCDrawNode`繪製圓形的類別。 這個類別可能會對於企業營運應用程式非常有用，因為它提供以向量為基礎的幾何呈現 – Xamarin.Forms 中遺漏的功能。 圓形呈現，除了`CCDrawNode`類別可以用來繪製的矩形、 曲線、 線條和多邊形的自訂。 `CCDrawNode` 也是簡單易用，因為它不需要使用的映像檔案 （例如.png)。 位於 CCDrawNode 的更詳細的討論[CCDrawNode 指南繪製幾何](~/graphics-games/cocossharp/ccdrawnode.md)。

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5.與 CocosSharp 互動

CocosSharp 視覺項目 (例如`CCDrawNode`) 繼承自`CCNode`類別。 `CCNode` 提供可用來將位置相對於其父物件的兩個屬性：`PositionX`和`PositionY`。 此程式碼片段所示，我們的程式碼會將這兩個屬性目前使用的圓圈，中心：


```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

請務必注意 CocosSharp 物件位於所明確的位置值，而非對大部分的 Xamarin.Forms 檢視，會自動放置根據其父版面配置控制項的行為。

我們會將新增程式碼，以讓使用者按一下向左或向右移動圓形的 10 個單位 （不像素為單位，因為 CocosSharp 世界單元空間中繪製圓形） 的兩個按鈕的其中一個。 我們將建立兩個公用的方法中的第一次`GameScene`類別：


```csharp
public void MoveCircleLeft()
{
    circle.PositionX -= 10;
}

public void MoveCircleRight()
{
    circle.PositionX += 10;
}
```

接下來，我們會將處理常式新增至中的兩個按鈕`HomePage`點選回應。 完成時，我們`CreateBottomHalf`方法包含下列程式碼：


```csharp
void CreateBottomHalf(Grid grid)
{
    // We'll use a StackLayout to organize our buttons
    var stackLayout = new StackLayout();

    // The first button will move the circle to the left when it is clicked:
    var moveLeftButton = new Button {
        Text = "Move Circle Left"
    };
    moveLeftButton.Clicked += (sender, e) => gameScene.MoveCircleLeft ();
    stackLayout.Children.Add (moveLeftButton);

    // The second button will move the circle to the right when clicked:
    var moveCircleRight = new Button {
        Text = "Move Circle Right"
    };
    moveCircleRight.Clicked += (sender, e) => gameScene.MoveCircleRight ();
    stackLayout.Children.Add (moveCircleRight);

    // The stack layout will be in the bottom half (row 1):
    grid.Children.Add (stackLayout, 0, 1);
}
```

點選回應 CocosSharp 圓形現在移動。 我們也清楚可以遠向左或向右移動圓形看到 CocosSharp 畫布的界限：

![](cocossharp-images/image8.png "移動圓形 GameScene")

## <a name="summary"></a>總結

本指南示範如何加入現有的 Xamarin.Forms CocosSharp 專案、 如何建立 Xamarin.Forms 與 CocosSharp，之間的互動，並討論各種考量 CocosSharp 中建立的版面配置時。

CocosSharp 遊戲引擎提供了許多功能和深度，所以本指南只觸及 CocosSharp 可以做什麼。 開發人員想要深入了解 CocosSharp 的可以找到許多發行項中的[CocosSharp 區段](~/graphics-games/cocossharp/index.md)。



## <a name="related-links"></a>相關連結

- [CocosSharp 應用程式開發介面](https://developer.xamarin.com/api/root/CocosSharp/)
- [CocosSharpForms （範例）](https://developer.xamarin.com/samples/xamarin-forms/CocosSharpForms/)
