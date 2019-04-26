---
title: BouncingGame 詳細資料
description: 本文件提供逐步解說建立 BouncingGame，搭配 cocossharp 使用 Tiled 建置簡單彈跳球遊戲。
ms.prod: xamarin
ms.assetid: AC9FD56F-6E4A-40DA-8168-45A761D869FD
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: 9fd6c9108695f58bd69a1c4aa307ca2e4be6dede
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357463"
---
# <a name="bouncinggame-details"></a>BouncingGame 詳細資料

> [!TIP]
> BouncingGame 的程式碼可在[Xamarin 範例](https://developer.xamarin.com/samples/mobile/BouncingGame/)。 若要最，請依照本指南，請下載並探索程式碼，如本指南參考它。

本逐步解說示範如何實作使用 CocosSharp 簡單彈跳球遊戲。 

 - 解壓縮遊戲的內容
 - 常見的 CocosSharp 視覺項目
 - 若要加入視覺項目 `GameLayer`
 - 實作每個畫面格邏輯

我們已完成的遊戲看起來像這樣：

![BouncingGame，完成](bouncing-game-images/image1.png "BouncingGame，完成")

## <a name="unzipping-game-content"></a>解壓縮遊戲的內容

遊戲開發人員通常會使用這個詞彙*內容*參考通常由 visual 演出者、 遊戲的設計工具或音訊的設計工具建立非程式碼檔案。 常見的內容類型包含用來顯示視覺效果、 播放音效，或控制人工智慧 (AI) 行為的檔案。 遊戲開發小組的觀點來看，從非程式設計人員通常會建立內容。 我們的遊戲會包含兩種類型的內容：

 - 若要定義的球及檔板的顯示方式的 png 檔案。
 - 單一 xnb 檔案，以定義 （詳細討論當我們加入下列的分數顯示） 分數顯示所使用的字型

這裡使用此內容可在[內容 zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)。 我們需要這些檔案，下載並解壓縮至我們將在此逐步解說稍後存取的位置。

## <a name="common-cocossharp-visual-elements"></a>常見的 CocosSharp 視覺項目

CocosSharp 提供數個用來顯示視覺效果的類別。 某些項目會直接顯示，而其他適用於組織。 在遊戲中，我們將使用下列：

 - `CCNode` – CocosSharp 中的所有視覺物件基底類別。 `CCNode`類別包含`AddChild`函式可用來建構的父子式階層，也稱為*視覺化樹狀結構*或是*場景圖形*。 下面所述的所有類別都繼承自`CCNode`。
 - `CCScene` – 所有 CocosSharp 的遊戲的視覺化樹狀結構的根目錄。 所有視覺項目必須是與視覺化樹狀結構的一部分`CCScene`根目錄，或者將不會顯示。
 - `CCLayer` – 視覺效果容器物件，例如`CCSprite`。 如同名稱所暗示，`CCLayer`類別用來控制如何視覺化項目層彼此的上方。
 - `CCSprite` – 顯示映像或映像的一部分。 `CCSprite` 執行個體可以定位，調整大小，並提供幾個視覺效果。
 - `CCLabel` – 在畫面上顯示的字串。 所使用的字型`CCLabel`xnb 檔案中定義。 我們將討論 xnbs 詳細說明如下。

若要了解如何使用不同類型的我們會考慮單一`CCSprite`。 視覺項目必須新增至`CCLayer`，且必須是視覺化樹狀結構`CCScene`其根目錄。 因此，單一父子式關係`CCSprite`會是`CCScene`  >  `CCLayer`  >  `CCSprite`。

## <a name="adding-visual-elements-to-gamelayer"></a>將視覺元素新增至 GameLayer

### <a name="adding-the-paddle-sprite"></a>新增檔板 sprite

一開始我們會設定該遊戲的背景，以黑色，以及將單一`CCSprite`呈現在螢幕上。 修改`GameLayer`類別來新增`CCSprite`，如下所示：

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

上述程式碼會建立單一`CCSprite`並將它加入為子系`GameLayer`。 `CCSprite`建構函式可讓我們來定義要做為字串的影像檔。 我們的程式碼會指示來尋找檔案，稱為 CocosSharp `paddle` （延伸模組會省略，將於稍後在本指南中稍後討論）。 CocosSharp 會尋找任何檔案名稱`paddle`的根內容的資料夾中 (即**內容**) 新增至任何資料夾以及`gameView.ContentManager.SearchPaths`（如上一節中討論）。

我們將檔案直接加入根**內容**適用於 iOS 和 Android 的資料夾。 若要這樣做，請以滑鼠右鍵按一下或 Control + 按一下**內容**資料夾中的 iOS 專案，然後選取**新增** > **新增檔案...** 我們解壓縮內容稍早瀏覽並選取**paddle.png**。 如果系統詢問有關如何將檔案加入資料夾，最好還是選擇**複製**選項：

![新增檔案到資料夾 對話方塊](bouncing-game-images/image2.png "到資料夾] 對話方塊的 [新增檔案")

接下來，我們會將檔案加入 Android 專案。 以滑鼠右鍵按一下或 Control + 按一下 [內容] 資料夾 (位於**資產**Android 專案的資料夾)，然後選取**新增** > **新增檔案...**.此時，瀏覽至 iOS 專案**內容**資料夾。 當詢問有關如何新增檔案時，請選取**將連結加入**選項：

![新增檔案到資料夾 對話方塊](bouncing-game-images/addalink.png "將檔案新增到資料夾 對話方塊")

我們將討論檔案必須加入下列這兩個專案的原因。 每個專案**內容**資料夾現在包含**paddle.png**檔案：

![內容資料夾的內容](bouncing-game-images/image3.png "內容資料夾的內容")

如果我們執行遊戲就會看到`CCSprite`繪製：

![檔板，螢幕上繪製](bouncing-game-images/image4.png "檔板，繪製在螢幕上")

### <a name="file-details"></a>檔案詳細資料

到目前為止我們已將單一檔案加入專案，但程序相當簡單。 我們只需新增**paddle.png**的檔案**內容**資料夾和程式碼中參考它。 讓我們花點時間查看使用 CocosSharp 中的檔案時的一些考量。

#### <a name="capitalization"></a>大小寫

請注意，檔案名稱和我們在程式碼中用來存取檔案的字串都小寫。 這是因為某些平台 （例如 Windows 桌面和 iOS 模擬器） 會區分大小寫，而其他平台 （例如 Android 和 iOS 裝置） 會區分大小寫。 我們會將小寫的所有檔案用於本教學課程的其餘部分，以便在檔案和程式碼仍能為跨平台越好。

#### <a name="extensions"></a>延伸模組

建立 Sprite 的建構函式參考檔板檔案時，不包含".png"延伸模組。 處理 CocosSharp 專案相同的資產類型的檔案副檔名為可能不同平台之間，通常會省略的檔案副檔名。 例如，音訊檔案可能取決於平台的.aiff、.mp3 或.wma 檔案格式。 拿掉擴充功能可讓相同的程式碼，無論檔案副檔名的所有平台上運作。

#### <a name="content-in-platform-specific-projects"></a>平台專屬專案中的內容

不同於大部分的程式碼檔案可以是 PCL 中，內容檔案必須新增至每個平台專屬專案中。 CocosSharp 會需要這兩個原因：

1. 每個平台有不同**建置動作**。 已新增至 iOS 專案的內容應該使用**BundleResource**建置動作。 已新增至 Android 專案的內容應該使用**AndroidAsset**建置動作。
2. 某些平台需要平台專屬檔案格式。 例如，字型.xnb 檔案之間的不同 iOS 和 Android，如稍後在本逐步解說稍後所示。

如果檔案格式並無不同平台 （例如.png) 之間，每個平台可以使用相同的檔案，其中一個或多個平台可能會連結相同的位置中的檔案。

## <a name="orientation"></a>方向

就像任何其他應用程式，CocosSharp 應用程式可以執行在直向或橫向方向。 我們會調整在僅限直向模式中執行遊戲。 首先，我們將變更解析中的程式碼來處理直向外觀比例的遊戲。 若要這樣做，請修改`width`並`height`中的值`LoadGame`中的方法`ViewController`在 iOS 上的類別和`MainActivity`在 Android 上的類別：

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

接下來，我們必須修改每個平台特定專案，以在直向模式中執行。

### <a name="ios-orientation"></a>iOS 方向

若要修改 iOS 專案的方向，請選取**Info.plist**中的檔案**BouncingGame.iOS**專案，然後變更**iPhone/iPod 部署資訊**和**iPad 部署資訊**使其只包含 直向方向：

![列印方向 選項](bouncing-game-images/image5.png "方向選項")

### <a name="android-orientation"></a>Android 的方向

若要修改 Android 專案的方向，開啟 MainActivity.cs 檔案 BouncingGame.Android 專案中。 修改活動屬性定義，因此它會指定只為縱向方向，如下所示：

```csharp
[Activity (
    Label = "BouncingGame.Android",
    AlwaysRetainTaskState = true,
    Icon = "@drawable/icon",
    Theme = "@android:style/Theme.NoTitleBar",
    ScreenOrientation = ScreenOrientation.Portrait,
    LaunchMode = LaunchMode.SingleInstance,
    MainLauncher = true,
    ConfigurationChanges = ConfigChanges.Orientation | ConfigChanges.ScreenSize | ConfigChanges.Keyboard | ConfigChanges.KeyboardHidden)
]
public class MainActivity : Activity
{ 
...
```

## <a name="default-coordinate-system"></a>預設座標系統

我們的程式碼，這會具現化`CCSprite`，設定`PositionX`和`PositionY`到 100 的值。 根據預設，這表示，`CCSprite`中心將會放置在 100 像素，向上和向右，從畫面的左下方。 座標系統可以修改附加`CCCamera`至`CCLayer`。 我們不會使用`CCCamera`在此專案中，但更多有關`CCCamera`篇[CocosSharp API 文件](https://developer.xamarin.com/api/type/CocosSharp.CCLayer/)。

上述 「 遊戲 」 的像素，而不是像素為單位的硬體上 100 像素。 這表示，不同的解析度 （例如 iPhone 與 iPad) 的裝置上執行相同的遊戲會導致正在定位，並正確相對於實體螢幕大小的物件。 具體來說，遊戲的可見區域一律為 1024年像素高和 768 像素寬，因為這是解析我們已經指定稍早在`LoadGame`方法。

## <a name="adding-the-ball-sprite"></a>加入球 sprite

現在，我們已熟悉使用的基本概念`CCSprite`，我們將新增第二個`CCSprite`– 球。 我們將遵循步驟非常類似於我們如何建立檔板`CCSprite`。 

首先，我們將新增**ball.png**解壓縮的資料夾到 iOS 專案的映像**內容**資料夾。 選取即可**複製**的檔案**內容**目錄。 遵循相同的步驟加入的連結，如上所述**ball.png** Android 專案中的檔案。

接下來，建立`CCSprite`藉由將成員加入此球名`ballSprite`要`GameScene`類別，以及具現化的程式碼`ballSprite`。 當完成`GameLayer`類別看起來像這樣：

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

## <a name="adding-the-score-label"></a>新增評分標籤

我們將新增至遊戲的最後一個視覺項目是`CCLabel`的次數將數字顯示使用者已成功將球彈。 `CCLabel`螢幕上顯示字串時，用以建構函式中指定的字型。

新增下列程式碼，以建立`CCLabel`執行個體中`GameLayer`。 一旦完成程式碼應該看起來像這樣：

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

請注意，在使用 scoreLabel`AnchorPoint`的`CCPoint.AnchorUpperLeft`，這表示`PositionX`和`PositionY`值會定義其左上角位置。 這可讓我們位置`scoreLabel`相對於左上角的螢幕，而不考慮標籤的維度。

## <a name="implementing-every-frame-logic"></a>實作每個畫面格邏輯

到目前為止，遊戲提供靜態的場景。 我們會新增邏輯來控制將更新的高頻率的物件位置的程式碼加入的場景中物件的移動。 在此情況下，程式碼會執行 60 次每秒鐘-也稱為六十*框架*每秒 （除非硬體無法處理經常更新此）。 具體來說，我們會新增邏輯，以讓球落，並針對翹翹板，來移動檔板根據輸入，並更新玩家的分數，每次球襲檔板退件。

`Schedule`方法，由提供`CCNode`類別中，可讓我們將每個畫面格邏輯新增至遊戲。 我們將新增程式碼之後`// New code`GameLayer 建構函式：

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

現在，建立`RunGameLogic`方法中的`GameLayer`類別，以容納所有的每個畫面格邏輯：

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
}
```

浮點數參數會定義畫面格的時間以秒為單位。 我們將使用此值時實作球的移動。

### <a name="making-the-ball-fall"></a>讓球落

我們可以讓球落在任一個手動實作的重力程式碼或使用 CocosSharp 中的內建的 Box2D 功能。 使用 CocosSharp 的遊戲 Box2D 物理模擬引擎。 它是非常強大且更有效率，但需要撰寫安裝程式的程式碼。 由於物理模擬是相當簡單，以下它將會實作以手動方式。

若要實作重力我們必須儲存目前的 X 和 y 軸速度的球。 我們將新增兩個成員`GameLayer`類別：

```csharp
float ballXVelocity;
float ballYVelocity;
// How much to modify the ball's y velocity per second:
const float gravity = 140;
```

接下來我們可以在此實作中的下跌邏輯`RunGameLogic`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
}
```

### <a name="moving-the-paddle-with-touch-input"></a>移動檔板觸控輸入

現在，球不符合要求，我們會加入水平移動檔板使用`CCEventListenerTouchAllAtOnce`物件。 這個物件提供的輸入相關的事件數目。 在此情況下，我們想要將任何觸控點移動，讓我們可以調整檔板的位置時收到通知。 `GameLayer`已具現化`CCEventListenerTouchAllAtOnce`，因此我們只需要將指派`OnTouchesMoved`委派。 若要這樣做，請修改 AddedToScene 方法，如下所示：

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

接下來，我們將實作`HandleTouchesMoved`:

```csharp
void HandleTouchesMoved (System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    // we only care about the first touch:
    var locationOnScreen = touches [0].Location;
    paddleSprite.PositionX = locationOnScreen.X;
}
```

### <a name="implementing-ball-collision"></a>實作球衝突

如果我們執行遊戲現在我們就會發現球便會略過檔板。 我們將實作*衝突*（邏輯來回應重疊遊戲物件） 中的每個畫面格程式碼。 移動物件的變更會將每個畫面，因為衝突檢查通常是也執行每個畫面。 我們也會新增速度 X 軸上的球撞上翹翹板，以在遊戲中，新增一些挑戰，但這表示我們要讓球移動超過螢幕的邊緣。 我們會執行這項`RunGameLogic`程式碼之後將套用至的速度`ballSprite`之後`// New Code`:

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

## <a name="adding-scoring"></a>新增評分

遊戲已播放，最後一個步驟是新增評分的邏輯。 首先，我們將在這裡將分數成員新增至名為 GameLayer 類別`score`:

```csharp
int score;
```

我們將使用此變數，來追蹤玩家的分數，並顯示它使用`scoreLabel`。 執行這會新增下列程式碼中的 if 陳述式內`RunGameLogic`球及檔板重疊：

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

現在我們可以執行遊戲，看遊戲會顯示一個球彈出開檔板遞增的分數：

![已完成的遊戲，具有遞增分數](bouncing-game-images/image1.png "完成的遊戲，具有遞增分數")

## <a name="summary"></a>總結

本逐步解說會呈現圖形、 物理條件，與使用 CocosSharp 的輸入建立跨平台遊戲。 它是開始使用 CocosSharp 的遊戲開發的第一個步驟。 我們討論過的一些最常見 CocosSharp、 如何建構視覺化樹狀結構，因此物件轉譯正確，及如何實作每個畫面格遊戲邏輯類別。

本逐步解說涵蓋一小部分的 CocosSharp 的遊戲引擎所提供的功能。 資訊和逐步解說，示範 CocosSharp 中的其他主題，請參閱[CocosSharp 指南的其餘部分](~/graphics-games/cocossharp/index.md)。

## <a name="related-links"></a>相關連結

- [已完成的遊戲 （範例）](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [遊戲的內容 （範例）](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
