---
title: BouncingGame 詳細資料
description: 本逐步解說示範如何實作簡單彈跳使用 CocosSharp 的球遊戲。
ms.topic: article
ms.prod: xamarin
ms.assetid: AC9FD56F-6E4A-40DA-8168-45A761D869FD
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/29/2018
ms.openlocfilehash: fdf5d28637de839b63f4c7c71c7f9413daeb8c29
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="bouncinggame-details"></a>BouncingGame 詳細資料

> [!TIP]
> BouncingGame 的程式碼位於[Xamarin 範例](https://developer.xamarin.com/samples/mobile/BouncingGame/)。 若要最依照本指南，下載並瀏覽程式碼，如本指南參考它。

本逐步解說示範如何實作簡單彈跳使用 CocosSharp 的球遊戲。 

 - 解壓縮遊戲的內容
 - 常見的 CocosSharp 視覺項目
 - 若要加入視覺化元素 `GameLayer`
 - 實作每個框架邏輯

我們已完成的遊戲看起來像這樣：

![BouncingGame，完成](bouncing-game-images/image1.png "BouncingGame，完成")

## <a name="unzipping-game-content"></a>解壓縮遊戲的內容

遊戲開發人員通常會使用這個詞彙*內容*參照到這通常由 visual 演出者、 遊戲的設計工具或音訊的設計工具建立的非程式碼檔案。 常見的內容類型包括用來顯示視覺效果、 播放聲音，或控制行為人工地智慧 (AI) 檔案。 遊戲開發小組的觀點來看，從非程式設計人員通常會建立內容。 我們遊戲包含兩種類型的內容：

 - 若要定義 球 與 paddle png 檔案。
 - 單一 xnb 檔案，以定義分數顯示 （更詳細討論當我們將加入下列的分數顯示） 所使用的字型

這裡使用此內容位於[內容 zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)。 我們需要下載並解壓縮至的位置，我們將稍後在本逐步解說中存取這些檔案。

## <a name="common-cocossharp-visual-elements"></a>常見的 CocosSharp 視覺項目

CocosSharp 提供數個類別用來顯示視覺效果。 某些項目會直接顯示，而其他適用於組織。 遊戲中，我們將使用下列：

 - `CCNode` – 所有的視覺物件中 CocosSharp 基底類別。 `CCNode`類別包含`AddChild`函式可以用來建構的父子式階層時，也稱為*視覺化樹狀結構*或*場景圖形*。 如下所述的所有類別都繼承自`CCNode`。
 - `CCScene` – 所有 CocosSharp 遊戲的視覺化樹狀結構的根目錄。 所有視覺項目必須是以視覺化樹狀結構的一部分`CCScene`根目錄中，將不會顯示。
 - `CCLayer` -視覺效果容器物件，例如`CCSprite`。 正如其名，`CCLayer`類別用來控制之上彼此的方式視覺化項目層級。
 - `CCSprite` – 顯示影像的一部分。 `CCSprite` 執行個體可以放置，調整大小，並提供數種視覺效果。
 - `CCLabel` – 在螢幕上顯示的字串。 所使用的字型`CCLabel`xnb 檔案中定義。 我們將探討以下更詳細的 xnbs。

若要了解如何使用不同的型別我們會考慮單一`CCSprite`。 視覺項目必須新增至`CCLayer`，且必須是視覺化樹狀結構`CCScene`其根目錄。 因此，單一的父子式關聯性`CCSprite`會`CCScene`  >  `CCLayer`  >  `CCSprite`。

## <a name="adding-visual-elements-to-gamelayer"></a>將視覺項目加入至 GameLayer

### <a name="adding-the-paddle-sprite"></a>加入 paddle 精靈

我們一開始將會以黑色，以及將單一遊戲的背景`CCSprite`呈現在螢幕上。 修改`GameLayer`類別加入`CCSprite`，如下所示：

```csharp
using System;
using System.Collections.Generic;
using CocosSharp;
namespace BouncingGame
{
    public class GameLayer : CCLayer
    {
        CCSprite paddleSprite;
        public GameLayer () : base(CCColor4B.Black)
        {
            // "paddle" refers to the paddle.png image
            paddleSprite = new CCSprite ("paddle");
            paddleSprite.PositionX = 100;
            paddleSprite.PositionY = 100;
            AddChild (paddleSprite);
        }
        protected override void AddedToScene ()
        {
            base.AddedToScene ();
            // Use the bounds to layout the positioning of the drawable assets
            CCRect bounds = VisibleBoundsWorldspace;
            // Register for touch events
            var touchListener = new CCEventListenerTouchAllAtOnce ();
            touchListener.OnTouchesEnded = OnTouchesEnded;
            AddEventListener (touchListener, this);
        }
        void OnTouchesEnded (List<CCTouch> touches, CCEvent touchEvent)
        {
            if (touches.Count > 0)
            {
                // Perform touch handling here
            }
        }
    }
}
```

上述程式碼會建立單一`CCSprite`並將它加入做為子系`GameLayer`。 `CCSprite`建構函式可讓我們來定義要做為字串使用的影像檔。 我們的程式碼會告知尋找檔案，稱為 CocosSharp `paddle` （擴充功能會省略，我們將稍後在本指南中討論）。 CocosSharp 會尋找任何檔案名稱`paddle`的根內容的資料夾中 (也就是**內容**) 以及加入至任何資料夾`gameView.ContentManager.SearchPaths`（如上一節中討論）。

我們會將檔案新增至根直接**內容**適用於 iOS 和 Android 的資料夾。 若要這樣做，請以滑鼠右鍵按一下或控制項按一下**內容**中 iOS 專案，然後選取資料夾**新增** > **加入檔案...**其中我們稍早解壓縮內容瀏覽並選取**paddle.png**。 如果系統詢問如何將檔案加入資料夾，我們應該選取**複製**選項：

![新增檔案到資料夾 對話方塊](bouncing-game-images/image2.png "檔案新增到資料夾 對話方塊")

接下來，我們會將檔案新增至 Android 專案。 以滑鼠右鍵按一下或控制項按一下內容的資料夾上 (這是在**資產**Android 專案的資料夾) 並選擇 選擇**新增** > **加入檔案...**.此時，請瀏覽到 iOS 專案的**內容**資料夾。 當系統詢問關於如何將檔案加入，請選取**將連結加入**選項：

![新增檔案到資料夾 對話方塊](bouncing-game-images/addalink.png "將檔案新增到資料夾 對話方塊")

我們將討論必須加入下列這兩個專案檔案的原因。 每個專案**內容**資料夾現在會包含**paddle.png**檔案：

![內容資料夾的內容](bouncing-game-images/image3.png "內容資料夾的內容")

當我們執行遊戲我們會看到`CCSprite`繪製：

![繪製在螢幕上的 paddle](bouncing-game-images/image4.png "paddle，繪製在螢幕上")

### <a name="file-details"></a>檔案詳細資料

到目前為止我們已將單一檔案加入專案，但程序相當簡單。 我們只是加入**paddle.png**檔案**內容**資料夾及程式碼中參考它。 讓我們花點時間查看使用 CocosSharp 中的檔案時的一些考量。

#### <a name="capitalization"></a>大小寫

請注意，檔案名稱和我們在程式碼中用來存取檔案的字串都是小寫。 這是因為某些平台 （例如 Windows 桌面和 iOS 模擬器） 是不區分大小寫，而其他平台 （例如 Android 和 iOS 裝置） 會區分大小寫。 我們將所有小寫檔案使用本教學課程的其餘部分，讓檔案和程式碼保持為跨平台越好。

#### <a name="extensions"></a>延伸模組

參考 paddle 檔案時，建立精靈的建構函式不包含".png 」 延伸模組。 處理相同的資產類型的副檔名為 CocosSharp 專案可能會不同平台時，通常會省略檔案的副檔名。 例如，音訊檔案可能.aiff、.mp3 或.wma 取決於平台的檔案格式。 離開關閉擴充功能可讓相同的程式碼，不論副檔名的所有平台上運作。

#### <a name="content-in-platform-specific-projects"></a>平台專屬專案中的內容

不同於大部分的程式碼檔案可以位於 PCL，內容檔案必須新增至每個平台特定專案。 CocosSharp 需要這兩個原因：

1. 每個平台而有不同**建置動作**。 加入 iOS 專案的內容應該使用**BundleResource**建置動作。 加入至 Android 專案的內容應該使用**AndroidAsset**建置動作。
2. 某些平台都需要平台專屬檔案格式。 例如，字型.xnb 檔案之間差異 iOS 和 Android，我們會看到稍後在本逐步解說。

如果平台 （例如.png) 檔案格式會相同，每個平台可以使用同一個檔案，其中一個或多個平台可能連結來自於相同位置的檔案。

## <a name="orientation"></a>方向

就像任何其他應用程式、 CocosSharp 應用程式可以執行以直向或橫向的方向。 我們將調整遊戲僅限直向模式中執行。 首先，我們將會變更解決方案中的程式碼來處理縱向外觀比例的遊戲。 若要這樣做，請修改`width`和`height`值`LoadGame`方法中的`ViewController`在 iOS 上的類別和`MainActivity`在 Android 上的類別：

```csharp
void LoadGame (object sender, EventArgs e)
{
    CCGameView gameView = sender as CCGameView;

    if (gameView != null)
    {
        var contentSearchPaths = new List<string> () { "Fonts", "Sounds" };
        CCSizeI viewSize = gameView.ViewSize;

        int width = 768;
        int height = 1027;
    // ...
```

接下來，我們必須先修改在直向模式中執行的每個平台特定專案。

### <a name="ios-orientation"></a>iOS 方向

若要修改的 iOS 專案的方向，請選取**Info.plist**檔案**BouncingGame.iOS**專案，然後變更**iPhone/iPod 部署資訊**和**iPad 部署資訊**使其只包含直式方向：

![列印方向 選項](bouncing-game-images/image5.png "列印方向 選項")

### <a name="android-orientation"></a>Android 的方向

若要修改的 Android 專案的方向，請開啟 Weatherapp 檔案 BouncingGame.Android 專案中。 修改活動屬性定義，因此它會指定只為縱向方向，如下所示：

```csharp
[Activity (
    Label = "BouncingGame.Android",
    AlwaysRetainTaskState = true,
    Icon = "@drawable/icon",
    Theme = "@android:style/Theme.NoTitleBar",
    ScreenOrientation = ScreenOrientation.Portrait,
    LaunchMode = LaunchMode.SingleInstance,
    MainLauncher = true,
    ConfigurationChanges = ConfigChanges.Orientation | ConfigChanges.ScreenSize | ConfigChanges.Keyboard | ConfigChanges.KeyboardHidden)
]
public class MainActivity : Activity
{ 
...
```

## <a name="default-coordinate-system"></a>預設座標系統

我們的程式碼，其會具現化`CCSprite`，設定`PositionX`和`PositionY`到 100 的值。 根據預設，這表示`CCSprite`中心將會位於 100 像素為單位啟動並在畫面左下方的右側。 座標系統可以修改透過附加`CCCamera`至`CCLayer`。 我們將不會使用`CCCamera`此專案中，但需`CCCamera`位於[CocosSharp API 文件](https://developer.xamarin.com/api/type/CocosSharp.CCLayer/)。

上述 「 遊戲 」 的像素，而不是像素為單位的硬體上 100 像素。 這表示，不同的解析度 （例如 iPhone 和 iPad) 的裝置上執行相同的遊戲將導致物件的定位和正確調整大小，相對於實體的螢幕。 具體來說，1024年像素高和 768 像素寬一律可遊戲的可見區域，因為這是解析我們已經指定稍早在`LoadGame`方法。

## <a name="adding-the-ball-sprite"></a>加入球精靈

現在，我們很熟悉使用的基本`CCSprite`，我們會將第二個新增`CCSprite`– 球。 我們會遵循步驟非常類似於我們如何建立 paddle `CCSprite`。 

首先，我們會將新增**ball.png**映像從 iOS 專案的資料夾解壓縮**內容**資料夾。 選取即可**複製**檔案**內容**目錄。 遵循上述要新增連結的相同步驟**ball.png** Android 專案中的檔案。

接下來，建立`CCSprite`對於此球藉由新增成員呼叫`ballSprite`至`GameScene`類別，以及具現化的程式碼`ballSprite`。 當完成`GameLayer`類別看起來像這樣：

```csharp
public class GameLayer : CCLayer
{
    CCSprite paddleSprite;
    CCSprite ballSprite;
    public GameLayer () : base (CCColor4B.Black)
    {
        // "paddle" refers to the paddle.png image
        paddleSprite = new CCSprite ("paddle");
        paddleSprite.PositionX = 100;
        paddleSprite.PositionY = 100;
        AddChild (paddleSprite);
        ballSprite = new CCSprite ("ball");
        ballSprite.PositionX = 320;
        ballSprite.PositionY = 600;
        AddChild (ballSprite);
    }
```

## <a name="adding-the-score-label"></a>新增計分標籤

我們會加入該遊戲的最後一個視覺項目是`CCLabel`將數字的次數顯示使用者已順利跳回球。 `CCLabel`使用螢幕上顯示字串的建構函式中指定的字型。

加入下列程式碼，以建立`CCLabel`執行個體中`GameLayer`。 一旦完成程式碼應該看起來像這樣：

```csharp
public class GameLayer : CCLayer
{
    CCSprite paddleSprite;
    CCSprite ballSprite;
    CCLabel scoreLabel;
    public GameLayer () : base (CCColor4B.Black)
    {
        // "paddle" refers to the paddle.png image
        paddleSprite = new CCSprite ("paddle");
        paddleSprite.PositionX = 100;
        paddleSprite.PositionY = 100;
        AddChild (paddleSprite);
        ballSprite = new CCSprite ("ball");
        ballSprite.PositionX = 320;
        ballSprite.PositionY = 600;
        AddChild (ballSprite);
        scoreLabel = new CCLabel ("Score: 0", "Arial", 20, CCLabelFormat.SystemFont);
        scoreLabel.PositionX = 50;
        scoreLabel.PositionY = 1000;
        scoreLabel.AnchorPoint = CCPoint.AnchorUpperLeft;
        AddChild (scoreLabel);
    }
    // ...
```

請注意，使用 scoreLabel`AnchorPoint`的`CCPoint.AnchorUpperLeft`，這表示`PositionX`和`PositionY`值會定義其左上角位置。 這可讓我們位置`scoreLabel`相對於左上方的螢幕，而不考慮標籤的維度。

## <a name="implementing-every-frame-logic"></a>實作每個框架邏輯

目前為止，遊戲提供靜態場景。 我們將加入邏輯，以控制場景中的物件移動加入程式碼，就會更新物件在高頻率的位置。 在此情況下，此程式碼會執行每秒-也稱為 sixty 六十次*框架*秒 （除非硬體無法處理這頻繁地更新）。 具體來說，我們將持續加入邏輯，以讓球落在一對 paddle，若要移動 paddle 根據輸入，並更新播放程式的分數，每次球萬一 paddle 跳躍。

`Schedule`方法，由提供`CCNode`類別，可讓我們將每個框架邏輯加入至遊戲。 我們會將程式碼之後新增`// New code`GameLayer 建構函式：

```csharp
public GameLayer () : base (CCColor4B.Black)
{
    // "paddle" refers to the paddle.png image
    paddleSprite = new CCSprite ("paddle");
    paddleSprite.PositionX = 100;
    paddleSprite.PositionY = 100;
    AddChild (paddleSprite);
    ballSprite = new CCSprite ("ball");
    ballSprite.PositionX = 320;
    ballSprite.PositionY = 600;
    AddChild (ballSprite);
        scoreLabel = new CCLabel ("Score: 0", "arial", 22, CCLabelFormat.SpriteFont);
    scoreLabel.PositionX = 50;
    scoreLabel.PositionY = 1000;
    scoreLabel.AnchorPoint = CCPoint.AnchorUpperLeft;
    AddChild (scoreLabel);
    // New code:
    Schedule (RunGameLogic);
}
```

現在建立`RunGameLogic`方法中的`GameLayer`類別，包含所有的每個框架邏輯：

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
}
```

浮點數參數定義多少時間的畫面格是以秒為單位。 我們將使用此值實作球的移動時。

### <a name="making-the-ball-fall"></a>進行切換球

我們可以讓落在任一個手動實作的重力程式碼或使用內建 Box2D 功能中 CocosSharp 球。 使用 CocosSharp 遊戲 Box2D 物理模擬引擎。 它是非常強大且更有效率，但需要撰寫安裝程式的程式碼。 由於物理模擬是相當簡單，此處它將會手動實作。

若要實作重力我們需要存放區目前的 X 和 Y 速度球。 我們會將兩個成員加入`GameLayer`類別：

```csharp
float ballXVelocity;
float ballYVelocity;
// How much to modify the ball's y velocity per second:
const float gravity = 140;
```

接下來，我們可以實作中的下降邏輯`RunGameLogic`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
}
```

### <a name="moving-the-paddle-with-touch-input"></a>移動觸控輸入 paddle

既然下降球，我們會加入水平移動 paddle 使用`CCEventListenerTouchAllAtOnce`物件。 這個物件提供的輸入相關的事件數目。 在此情況下，我們想要收到通知，如果任何觸控點移動，以便我們可以調整 paddle 的位置。 `GameLayer`已具現化`CCEventListenerTouchAllAtOnce`，因此我們只需要指定`OnTouchesMoved`委派。 若要這樣做，請修改 AddedToScene 方法，如下所示：

```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();
    // Use the bounds to layout the positioning of the drawable assets
    CCRect bounds = VisibleBoundsWorldspace;
    // Register for touch events
    var touchListener = new CCEventListenerTouchAllAtOnce ();
    touchListener.OnTouchesEnded = OnTouchesEnded;
    // new code:
    touchListener.OnTouchesMoved = HandleTouchesMoved;
    AddEventListener (touchListener, this);
}
```

接下來，我們會實作`HandleTouchesMoved`:

```csharp
void HandleTouchesMoved (System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    // we only care about the first touch:
    var locationOnScreen = touches [0].Location;
    paddleSprite.PositionX = locationOnScreen.X;
}
```

### <a name="implementing-ball-collision"></a>實作球衝突

如果我們會發現球落透過 paddle 現在，我們會執行遊戲。 我們會實作*衝突*（邏輯來回應重疊遊戲物件） 中的每個框架的程式碼。 移動物件變更放置每個畫面格，因為衝突檢查通常是也執行每個畫面格。 我們將也會加入速度在 X 軸上球撞上加入遊戲的一些挑戰 paddle，但這表示我們必須將球移動超過螢幕的邊緣。 我們會執行這項`RunGameLogic`程式碼之後套用到速度`ballSprite`之後`// New Code`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
    // New Code:
    // Check if the two CCSprites overlap...
    bool doesBallOverlapPaddle = ballSprite.BoundingBoxTransformedToParent.IntersectsRect(
        paddleSprite.BoundingBoxTransformedToParent);
    // ... and if the ball is moving downward.
    bool isMovingDownward = ballYVelocity < 0;
    if (doesBallOverlapPaddle && isMovingDownward)
    {
        // First let's invert the velocity:
        ballYVelocity *= -1;
        // Then let's assign a random value to the ball's x velocity:
        const float minXVelocity = -300;
        const float maxXVelocity = 300;
        ballXVelocity = CCRandom.GetRandomFloat (minXVelocity, maxXVelocity);
    }
    // First let’s get the ball position:   
    float ballRight = ballSprite.BoundingBoxTransformedToParent.MaxX;
    float ballLeft = ballSprite.BoundingBoxTransformedToParent.MinX;
    // Then let’s get the screen edges
    float screenRight = VisibleBoundsWorldspace.MaxX;
    float screenLeft = VisibleBoundsWorldspace.MinX;
    // Check if the ball is either too far to the right or left:    
    bool shouldReflectXVelocity = 
        (ballRight > screenRight && ballXVelocity > 0) ||
        (ballLeft < screenLeft && ballXVelocity < 0);
    if (shouldReflectXVelocity)
    {
        ballXVelocity *= -1;
    }
}
```

## <a name="adding-scoring"></a>新增計分

現在已可播放的遊戲，最後一個步驟是將計分的邏輯。 首先，我們會將分數成員新增至名為 GameLayer 類別`score`:

```csharp
int score;
```

我們將使用此變數，來追蹤玩家的分數，並顯示它使用`scoreLabel`。 做這加入下列程式碼中的 if 陳述式內`RunGameLogic`球和 paddle 重疊：

```csharp
// ...
if (doesBallOverlapPaddle && isMovingDownward)
{
    // First let's invert the velocity:
    ballYVelocity *= -1;
    // Then let's assign a random to the ball's x velocity:
    const float minXVelocity = -300;
    const float maxXVelocity = 300;
    ballXVelocity = CCRandom.GetRandomFloat (minXVelocity, maxXVelocity);
    // New code:
    score++;
    scoreLabel.Text = "Score: " + score;
}
// ...
```

現在我們可以執行遊戲，並查看遊戲會顯示遞增球撞 paddle 開的分數：

![已完成的遊戲時，遞增的分數](bouncing-game-images/image1.png "完成的遊戲時，遞增的分數")

## <a name="summary"></a>總結

本逐步解說會呈現圖形、 物理，與使用 CocosSharp 的輸入建立跨平台遊戲。 它是 CocosSharp 遊戲開發入門的第一個步驟。 我們探討一些最常見的類別中 CocosSharp、 如何建構視覺化樹狀結構，所以呈現物件正常運作，以及如何實作每個框架遊戲邏輯。

本逐步解說涵蓋一小部分的解 CocosSharp 遊戲引擎提供的功能。 資訊及其他 CocosSharp 主題逐步解說，請參閱[CocosSharp 指南的其餘部分](~/graphics-games/cocossharp/index.md)。

## <a name="related-links"></a>相關的連結

- [已完成的遊戲 （範例）](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [遊戲的內容 （範例）](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
