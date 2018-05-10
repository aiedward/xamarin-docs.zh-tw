---
title: Fruity 落遊戲的詳細資訊
description: 本指南會檢閱 Fruity 落遊戲時，涵蓋常見 CocosSharp 和遊戲開發概念，例如物理、 內容管理、 遊戲的狀態和遊戲的設計。
ms.prod: xamarin
ms.assetid: A5664930-F9F0-4A08-965D-26EF266FED24
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 19b57d41fdf8aa4f8a749cf4979755161a0e7e51
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="fruity-falls-game-details"></a>Fruity 落遊戲的詳細資訊

_本指南會檢閱 Fruity 落遊戲時，涵蓋常見 CocosSharp 和遊戲開發概念，例如物理、 內容管理、 遊戲的狀態和遊戲的設計。_

Fruity 落是簡單、 物理為基礎的遊戲，播放程式在其中為彩色賺點的值區排序紅色與黃色水果。 遊戲的目標是要盡可能贏得分數，不讓水果拖放到錯誤的分類收納，結束遊戲。

![](fruity-falls-images/image1.png "遊戲的目標是要盡可能贏得分數，不讓水果拖放到錯誤的分類收納，結束遊戲")

Fruity 落擴充所介紹的概念[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)中加入下列：

 - 內容 Png 格式
 - 進階的物理
 - 遊戲的狀態 （場景之間的轉換）
 - 若要微調的遊戲的係數，透過單一類別中包含的變數的能力
 - 內建的視覺化偵錯支援
 - 程式碼的組織使用遊戲的實體
 - 遊戲的設計重點在於有趣和重新執行的值

雖然[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)Fruity 落著重於介紹核心 CocosSharp 概念，示範如何一起放到遊戲成品。 因為本指南會參考 BouncingGame，讀取器應該先瞭解自行向[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)再閱讀本指南。

本指南涵蓋的實作和 Fruity 便在 2008 年的設計，提供可以協助您建立自己的遊戲的見解。 它涵蓋下列主題：


- [GameController 類別](#gamecontroller-class)
- [遊戲的實體](#game-entities)
- [水果圖形](#fruit-graphics)
- [物理](#physics)
- [遊戲的內容](#game-content)
- [GameCoefficients](#gamecoefficients)


## <a name="gamecontroller-class"></a>GameController 類別

Fruity 落 PCL 專案包含`GameController`類別，即負責具現化遊戲及場景之間移動。 這個類別可由 iOS 和 Android 專案來消除重複的程式碼。

中包含的程式碼`Initialize`方法是類似的程式碼中`Initialize`方法中未變更的 CocosSharp 範本，但它包含修改的次數。

根據預設，`GameView.ContentManager.SearchPaths`裝置的解析度而定。 Fruity 落在更多詳細資料，如下所述，使用相同的內容，不論裝置解析度，因此`Initialize`方法會將`Images`路徑 （具有任何子資料夾） `SearchPaths`:


```csharp
contentSearchPaths.Add ("Images");
```

新的 CocosSharp 範本包含類別，繼承自`CCLayer`，隱含的遊戲的視覺效果和邏輯應加入這個類別。 相反地，Fruity 便會使用多個`CCLayer`至控制項的執行個體繪製順序。 這些`CCLayer`內所包含的執行個體`GameView`類別，繼承自`CCScene`。 Fruity 落也包含多個場景，第一個被`TitleScene`。 因此，`Initialize`方法具現化`TitleScene`執行個體傳遞給`RunWithScene`:


```csharp
var scene = new TitleScene (GameView);
GameView.Director.RunWithScene (scene);
```

邊線 Fruity 便在 2008 年的內容建立為低解析度像素美工圖案。 `GameView.DesignResolution`使遊戲才 384 單位的寬度和高度 512 設定：


```csharp
// We use a lower-resolution display to get a pixellated appearance
int width = 384;
int height = 512;
GameView.DesignResolution = new CCSizeI (width, height); 
```

最後，`GameController`類別提供靜態方法，您可以呼叫任何`CCGameScene`轉換至不同`CCScene`。 這個方法用來之間移動`TitleScene`和`GameScene`。


## <a name="game-entities"></a>遊戲的實體

Fruity 便會使用實體模式大部分遊戲的物件。 此模式的詳細的說明位於[CocosSharp 中的實體引導](~/graphics-games/cocossharp/entities.md)。

可以找到的實體實作的遊戲物件，在實體資料夾**FruityFalls.Common**專案：

![](fruity-falls-images/image2.png "FruityFalls.Common 專案中的 [實體] 資料夾")

實體是繼承自物件`CCNode`，而且可能會有視覺效果、 衝突，並每隔框架行為。

`Fruit`物件都代表一般的 CocosSharp 實體： 包含視覺物件、 衝突，以及每個框架邏輯。 其建構函式可以負責初始化水果：


```csharp
public Fruit ()
{
    CreateFruitGraphic ();
    if (GameCoefficients.ShowCollisionAreas)
    {
        CreateDebugGraphic ();
    }
    CreateCollision ();
    CreateExtraPointsLabel ();
    Acceleration.Y = GameCoefficients.FruitGravity;
}
```


### <a name="fruit-graphics"></a>水果圖形

`CreateFruitGraphic`方法會建立`CCSprite`執行個體，並將它加入至`Fruit`。 `IsAntialiased`屬性設為 false 會讓遊戲不太美觀的外觀。 此值設為 false，所有`CCSprite`和`CCLabel`整個遊戲的執行個體：


```csharp
private void CreateFruitGraphic()
{
    graphic = new CCSprite ("cherry.png");
    graphic.IsAntialiased = false;
    this.AddChild (graphic);
}
```

當播放程式碰觸`Fruit`執行個體，其`Paddle`，該水果的點值都會增加一個。 如此可將額外的挑戰經驗豐富的玩家龐大的額外點數的水果。 使用顯示的水果的點值`extraPointsLabel`執行個體。

`CreateExtraPointsLabel`方法會建立`CCLabel`執行個體，並將它加入至`Fruit`:


```csharp
private void CreateExtraPointsLabel()
{
    extraPointsLabel = new CCLabel("", "Arial", 12, CCLabelFormat.SystemFont);
    extraPointsLabel.IsAntialiased = false;
    extraPointsLabel.Color = CCColor3B.Black;
    this.AddChild(extraPointsLabel);
}
```

Fruity 便包括兩種不同的水果 – 櫻桃和 lemons。 `CreateFruitGraphic`指派可以透過變更預設視覺效果，但水果視覺效果`FruitColor`屬性，依序呼叫`UpdateGraphics`:


```csharp
private void UpdateGraphics()
{
if (GameCoefficients.ShowCollisionAreas)
    {
        debugGrahic.Clear ();
        const float borderWidth = 4;
        debugGrahic.DrawSolidCircle (
            CCPoint.Zero,
            GameCoefficients.FruitRadius,
            CCColor4B.Black);
        debugGrahic.DrawSolidCircle (
            CCPoint.Zero,
            GameCoefficients.FruitRadius - borderWidth,
            fruitColor.ToCCColor ());
    }
    if (this.FruitColor == FruitColor.Yellow)
    {
        graphic.Texture = CCTextureCache.SharedTextureCache.AddImage ("lemon.png");
        extraPointsLabel.Color = CCColor3B.Black;
        extraPointsLabel.PositionY = 0;
    }
    else
    {
        graphic.Texture = CCTextureCache.SharedTextureCache.AddImage ("cherry.png");
        extraPointsLabel.Color = CCColor3B.White;
        extraPointsLabel.PositionY = -8;
    }
}
```

第一個 if 陳述式中`UpdateGraphics`更新偵錯圖形中，用來以視覺化方式檢視衝突區域。 遊戲的最終版本會但可以在偵錯物理開發期間會保留之前，會關閉這些視覺效果。 第二個組件變更`graphic.Texture`屬性，藉由呼叫`CCTextureCache.SharedTextureCache.AddImage`。 這個方法會提供存取紋理依檔案名稱。 這個方法的詳細資訊位於[紋理快取指南](~/graphics-games/cocossharp/texture-cache.md)。

`extraPointsLabel`色彩會調整為保留對比與水果映像，並將其`PositionY`值調整至中心`CCLabel`上水果`CCSprite`:

![](fruity-falls-images/image3.png "ExtraPointsLabel 色彩會調整為保留對比與水果映像和其 PositionY 值會調整為中心的水果 CCSprite CCLabel")

![](fruity-falls-images/image4.png "ExtraPointsLabel 色彩會調整為保留對比與水果映像和其 PositionY 值會調整為中心的水果 CCSprite CCLabel")


### <a name="collision"></a>衝突

Fruity 落實作自訂衝突解決方案，使用 [幾何] 資料夾中的物件：

![](fruity-falls-images/image5.png "Fruity 落實作使用物件的幾何資料夾中的自訂衝突解決方案")

衝突 Fruity 落在實作使用`Circle`或`Polygon`物件時，通常會新增為實體的子系這兩種類型的其中一個。 例如，`Fruit`物件具有`Circle`呼叫`Collision`它在初始化其`CreateCollision`方法：


```csharp
private void CreateCollision()
{
    Collision = new Circle ();
    Collision.Radius = GameCoefficients.FruitRadius;
    this.AddChild (Collision);
}
```

請注意，`Circle`類別繼承自`CCNode`類別，因此可以做為子系中新增至我們遊戲的實體：


```csharp
public class Circle : CCNode
{
    ...
}
```

`Polygon` 建立的功能類似於`Circle`建立，如中所示`Paddle`類別：


```csharp
private void CreateCollision()
{
    Polygon = Polygon.CreateRectangle(width, height);
    this.AddChild (Polygon);
}
```

涵蓋的衝突邏輯[本指南稍後](#collision)。


## <a name="physics"></a>物理

Fruity 落在物理可以分為兩類： 移動和衝突。 


### <a name="movement-using-velocity-and-acceleration"></a>使用速度和加速移動

Fruity 便會使用`Velocity`和`Acceleration`值以控制其實體，類似於移動[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)。 實體在名為的方法中實作其移動邏輯`Activity`，這會呼叫一次每個畫面格。 例如，我們可以看到的實作中移動`Fruit`類別`Activity`方法：

```csharp
public void Activity(float frameTimeInSeconds)
{
    timeUntilExtraPointsCanBeAdded -= frameTimeInSeconds;
    
    // linear approximation:
    this.Velocity += Acceleration * frameTimeInSeconds;

    // This is a linear approximation to drag. It's used to
    // keep the object from falling too fast, and eventually
    // to slow its horizontal movement. This makes the game easier
    this.Velocity -= Velocity * GameCoefficients.FruitDrag * frameTimeInSeconds;
    
    this.Position += Velocity * frameTimeInSeconds;
}
```
`Fruit`物件是唯一在實作拖放到-相對於速度有多快水果的速度會變慢的值是移動。 拖放到此實作會提供*終端機的速度*，這是最快速度，可切換的水果執行個體。 拖放到也會減慢成果，可以遊戲稍微更輕鬆地播放的水平移動。

`Paddle`物件也適用於`Velocity`中其`Activity`方法，但其`Velocity`使用計算`desiredLocation`值：


```csharp
public void Activity(float frameTimeInSeconds)
{
    // This code will cause the cursor to lag behind the touch point
    // Increasing this value reduces how far the paddle lags
    // behind the player’s finger. 
    const float velocityCoefficient = 4;
    // Get the velocity from current location and touch location
    Velocity = (desiredLocation - this.Position) * velocityCoefficient;
    this.Position += Velocity * frameTimeInSeconds;
    ...
}
```

一般而言，使用的遊戲`Velocity`來控制其物件執行的位置不是直接的實體位置至少不初始設定之後設定。 而不是直接設定`Paddle`位置`Paddle.HandleInput`方法指派`desiredLocation`值：

```csharp
public void HandleInput(CCPoint touchPoint)
{
    desiredLocation = touchPoint;
}
```

### <a name="collision"></a>衝突

Fruity 便在 2008 年這類實作半實際水果和其他 collidable 物件之間的衝突`Paddle`和`GameScene.Splitter`。 若要協助偵錯衝突，Fruity 落衝突區域可變成可見藉由變更`GameCoefficients.ShowDebugInfo`中`GameCoefficients.cs`檔案：


```csharp
public static class GameCoefficients
{
    ... 
    public const bool ShowCollisionAreas = true;
    ...
}
```

此值設定為`true`啟用衝突區域呈現：  

![](fruity-falls-images/image6.png "此值設定為 true 可讓衝突區域的轉譯")

衝突的邏輯開始在`GameScene.Activity`方法：


```csharp
private void Activity(float frameTimeInSeconds)
{
    if (hasGameEnded == false)
    {
        paddle.Activity(frameTimeInSeconds);
        foreach (var fruit in fruitList)
        {
            fruit.Activity(frameTimeInSeconds);
        }
        spawner.Activity(frameTimeInSeconds);
        DebugActivity();
        PerformCollision();
    }
}
```

`PerformCollision` 處理正在所有`Fruit`與其他物件的執行個體： 


```csharp
private void PerformCollision()
{
    // reverse for loop since fruit may be destroyed:
    for(int i = fruitList.Count - 1; i > -1; i--)
    {
        var fruit = fruitList[i];
        FruitVsPaddle(fruit);
        FruitPolygonCollision(fruit, splitter.Polygon, CCPoint.Zero);
        FruitVsBorders(fruit);
        FruitVsBins(fruit);
    }
}
```

#### <a name="fruitvsborders"></a>FruitVsBorders

`FruitVsBorders` 衝突會為衝突，而不是在不同的類別中包含的邏輯上，信賴憑證者執行自己的邏輯。 這項差異存在是因為水果和畫面的框線之間的衝突並不是完全實心 – 很可能要關閉螢幕邊緣小心 paddle 移動被推入的水果。 水果會跳躍不叫用時 paddle，與在畫面上，但是如果播放程式緩時變推播通知的水果會移動超過的邊緣，並關閉螢幕：


```csharp
private void FruitVsBorders(Fruit fruit)
{
    if(fruit.PositionX - fruit.Radius < 0 && fruit.Velocity.X < 0)
    {
        fruit.Velocity.X *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
    if(fruit.PositionX + fruit.Radius > gameplayLayer.ContentSize.Width && fruit.Velocity.X > 0)
    {
        fruit.Velocity.X *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
    if(fruit.PositionY + fruit.Radius > gameplayLayer.ContentSize.Height && fruit.Velocity.Y > 0)
    {
        fruit.Velocity.Y *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
}
```

#### <a name="fruitvsbins"></a>FruitVsBins

`FruitVsBins`方法負責檢查任何水果已經落後合而為一的兩個分類收納。如果是的話，播放程式會對點 （如果水果/bin 色彩比對），或遊戲結束 （如果不相符的色彩）：


```csharp
private void FruitVsBins(Fruit fruit)
{
    foreach(var bin in fruitBins)
    {
        if(bin.Polygon.CollideAgainst(fruit.Collision))
        {
            if(bin.FruitColor == fruit.FruitColor)
            {
                // award points:
                score += 1 + fruit.ExtraPointValue;
                scoreText.Score = score;
                Destroy(fruit);
            }
            else
            {
                EndGame();
            }
            break;
        }
    }
}
```

#### <a name="fruitvspaddle-and-fruitpolygoncollision"></a>FruitVsPaddle 和 FruitPolygonCollision

對 paddle 及與分隔 （分隔兩個分類收納的區域） 的水果的水果衝突同時依賴`FruitPolygonCollision`方法。 這個方法有三個部分：

1. 測試是否會發生衝突的物件
1. 移動物件 （只 Fruity 落在水果），讓它們不再重疊
1. 調整物件 （只 Fruity 落在水果） 的速度來模擬的彈回，下列程式碼示範上述提出的幾點：


```csharp
private static bool FruitPolygonCollision(Fruit fruit, Polygon polygon, CCPoint polygonVelocity)
{
    // Test whether the fruit collides
    bool didCollide = polygon.CollideAgainst(fruit.Collision);
    if (didCollide)
    {
        var circle = fruit.Collision;
        // Get the separation vector to reposition the fruit so it doesn't overlap the polygon
        var separation = CollisionResponse.GetSeparationVector(circle, polygon);
        fruit.Position += separation;
        // Adjust the fruit's Velocity to make it bounce:
        var normal = separation;
        normal.Normalize();
        fruit.Velocity = CollisionResponse.ApplyBounce(
            fruit.Velocity, 
            polygonVelocity, 
            normal, 
            GameCoefficients.FruitCollisionElasticity);
    }
    return didCollide;
}
```

Fruity 落衝突回應是單面 – 只水果的速度與位置都會予以調整。 它是值得一提，其他遊戲可能會有衝突，而這會影響這兩個相關，例如將 boulder 或損毀的兩部車推送到每一個其他字元的物件。

中所包含的衝突邏輯背後的數學`Polygon`和`CollisionResponse`類別已超出本指南的範圍，但做為撰寫，這些方法可用於許多類型的遊戲。 多邊形和`CollisionResponse`類別也支援非矩形和凸面多邊形，因此這段程式碼可以用於許多類型的遊戲。

 


## <a name="game-content"></a>遊戲的內容

Fruity 落在封面立即從 BouncingGame 中區分該遊戲。 類似遊戲設計時，播放程式會立即看到有不同的兩個遊戲的外觀。 玩家正面對決通常會決定是否要由其視覺效果玩遊戲。 因此，它是非常重要的開發人員投資資源進行視覺上吸引人遊戲。

Fruity 落美工圖案已建立具有下列目標：

 - 一致的主題
 - 只是一個字元 – Xamarin 猴子強調
 - 若要建立放寬順暢明亮的色彩體驗
 - 因此可以輕易地以視覺化方式追蹤遊戲物件之間的背景和前景對比
 - 能夠建立簡單的視覺效果，不含資源繁重動畫


### <a name="content-location"></a>內容位置

Fruity 便會包含所有其內容的 Android 專案中的 [映像] 資料夾中：

![](fruity-falls-images/image7.png "Fruity 落包含 Android 專案中的 [映像] 資料夾中所有的內容")

這些相同的檔案的連結的 iOS 專案，以避免重複，並且是檔案的變更會影響這兩個專案：

![](fruity-falls-images/image8.png "這些相同的檔案的連結中的 iOS 專案，以避免重複，並且是檔案的變更會影響這兩個專案")

它是值得注意的內容未包含在**Ld**或**Hd**資料夾，也就是預設 CocosSharp 範本的一部分。 **Ld**和**Hd**資料夾要用於遊戲提供兩個集合的內容，針對較低解析度的裝置，例如電話及更高解析度裝置，例如平板電腦的其中一個。 Fruity 落封面刻意建立不太美觀美觀的因此不需要提供內容的不同螢幕大小。 因此， **Ld**和**Hd**資料夾已完全移除專案。


### <a name="gamescene-layering"></a>GameScene 分層

如稍早在本指南中所述`GameScene`會負責所有遊戲物件具現化、 定位和間的物件 （例如衝突） 的邏輯。 所有物件都會都新增至其中的四個`CCLayer`執行個體：


```csharp
CCLayer backgroundLayer;
CCLayer gameplayLayer;
CCLayer foregroundLayer;
CCLayer hudLayer;
```

這些四個層級建立在`CreateLayers`方法。 請注意，就會加入至此`GameScene`後至前順序：


```csharp
private void CreateLayers()
{
    backgroundLayer = new CCLayer();
    this.AddLayer(backgroundLayer);
    gameplayLayer = new CCLayer();
    this.AddLayer(gameplayLayer);
    foregroundLayer = new CCLayer();
    this.AddLayer(foregroundLayer);
    hudLayer = new CCLayer();
    this.AddLayer(hudLayer);
}
```

一旦建立圖層，所有的 visual 物件加入至圖層使用`AddChild`方法中所示`CreateBackground`方法：


```csharp
private void CreateBackground()
{
    var background = new CCSprite("background.png");
    background.AnchorPoint = new CCPoint(0, 0);
    background.IsAntialiased = false;
    backgroundLayer.AddChild(background);
}
```


### <a name="vine-entity"></a>藤蔓實體

`Vine`實體唯一用於內容 – 它遊戲上沒有任何影響。 它組成 20`CCSprite`執行個體，並確定藤蔓永遠會到達畫面頂端選取試驗的數字：


```csharp
public Vine ()
{
    const int numberOfVinesToAdd = 20;
    for (int i = 0; i < numberOfVinesToAdd; i++)
    {
        var graphic = new CCSprite ("vine.png");
        graphic.AnchorPoint = new CCPoint(.5f, 0);
        graphic.PositionY = i * graphic.ContentSize.Height;
        this.AddChild (graphic);
    }
}
```

第一個`CCSprite`位於使與其下邊緣符合藤蔓的位置。 這可以藉由設定 AnchorPoint `new CCPoint(.5f, 0)`。 `AnchorPoint`屬性使用*正規化座標*哪些範圍介於 0 和 1 的大小為何`CCSprite`:

![](fruity-falls-images/image9.png "AnchorPoint 屬性會使用標準化的座標的範圍介於 0 和 1，而不管 CCSprite 的大小")

使用位於後續藤蔓小`graphic.ContentSize.Height`傳回影像的高度，單位為像素的值。 下圖顯示藤蔓圖形如何向上磚：

![](fruity-falls-images/image10.png "這個圖表會顯示藤蔓圖形如何向上磚")

因為來源的`Vine`實體位於底部藤蔓，則放置它很簡單，如下所示`Paddle.CreateVines`方法：


```csharp
private void CreateVines()
{
    // Increasing this value moves the vines closer to the 
    // center of the paddle.
    const int vineDistanceFromEdge = 4;
    leftVine = new Vine ();
    var leftEdge = -width / 2.0f;
    leftVine.PositionX = leftEdge + vineDistanceFromEdge;
    this.AddChild (leftVine);
    rightVine = new Vine ();
    var rightEdge = width / 2.0f;
    rightVine.PositionX = rightEdge - vineDistanceFromEdge;
    this.AddChild (rightVine);
}
```

執行個體藤蔓`Paddle`實體也有有趣旋轉行為。 因為`Paddle`player 輸入 （本指南涵蓋的下方的詳細資料），根據旋轉整個實體`Vine`執行個體也會受到這種旋轉。 不過，旋轉`Vine`執行個體連同`Paddle`會產生非預期的視覺效果中下列動畫所示：

![](fruity-falls-images/image11.gif "不過，旋轉以及 Paddle 藤蔓執行個體產生非預期的視覺效果中下列動畫所示")

可以抵制`Paddle`旋轉`leftVine`和`rightVine`會循環使用執行個體中所示 paddle 的反方向`Paddle.Activity`方法：


```csharp
public void Activity(float frameTimeInSeconds)
{
    ...
    // This value adds a slight amount of rotation
    // to the vine. Having a small amount of tilt looks nice.
    float vineAngle = this.Velocity.X / 100.0f;
    leftVine.Rotation = -this.Rotation + vineAngle;
    rightVine.Rotation = -this.Rotation + vineAngle;
}
```

請注意少量旋轉加回透過藤蔓`vineAngle`係數。 若要調整 vines 多少旋轉，可以變更此值。


## <a name="gamecoefficients"></a>GameCoefficients

每個很好的遊戲是個反覆項目，產品，使 Fruity 落包含類別，稱為`GameCoefficients`這會控制播放遊戲的方式。 這個類別包含整個控制項物理，版面配置，繁衍 （spawn），和計分遊戲用易懂變數。

例如，水果物理受到下列變數：


```csharp
public static class GameCoefficients
{
    ...
    
    // The strength of the gravity. Making this a 
    // smaller (bigger negative) number will make the
    // fruit fall faster. A larger (smaller negative) number
    // will make the fruit more floaty.
    public const float FruitGravity = -60;
    // The impact of "air drag" on the fruit, which gives
    // the fruit terminal velocity (max falling speed) and slows
    // the fruit's horizontal movement (makes the game easier)
    public const float FruitDrag = .1f;
    // Controls fruit collision bouncyness. A value of 1
    // means no momentum is lost. A value of 0 means all
    // momentum is lost. Values greater than 1 create a spring-like effect
    public const float FruitCollisionElasticity = .5f;
    
    ...
}
```

依照名稱提示，這些變數可用來調整速度有多快水果便在 2007 年，如何水平移動一段時間，以及 paddle bounciness 慢。

（例如 XML) 的程式碼或資料檔案中儲存的遊戲係數可以與遊戲的設計工具也不是程式設計人員團隊特別有用。

`GameCoefficients`類別還包括開啟偵錯資訊，例如繁衍 （spawn） 資訊或衝突範圍的值：


```csharp
public static class GameCoefficients
{
    ...
    // This controls whether debug information is displayed on screen.
    public const bool ShowDebugInfo = false;
    public const bool ShowCollisionAreas = false;
    ...
}
```


## <a name="conclusion"></a>結論

本指南中，瀏覽 Fruity 落遊戲。 它涵蓋概念包括內容、 物理和遊戲的狀態管理。

## <a name="related-links"></a>相關的連結

- [CocosSharp API 文件](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [已完成的專案 （範例）](https://developer.xamarin.com/samples/mobile/FruityFalls/)
