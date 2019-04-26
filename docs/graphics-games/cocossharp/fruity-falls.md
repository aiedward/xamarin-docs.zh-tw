---
title: Fruity Falls 遊戲詳細資料
description: 本指南會檢閱 Fruity 便在 2007 年遊戲時，涵蓋常見 CocosSharp 和遊戲開發概念，例如物理、 內容管理、 遊戲狀態和遊戲設計。
ms.prod: xamarin
ms.assetid: A5664930-F9F0-4A08-965D-26EF266FED24
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 959f5eb149ad375d686b17a85eb3d3b8fbdf3659
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61160025"
---
# <a name="fruity-falls-game-details"></a>Fruity Falls 遊戲詳細資料

_本指南會檢閱 Fruity 便在 2007 年遊戲時，涵蓋常見 CocosSharp 和遊戲開發概念，例如物理、 內容管理、 遊戲狀態和遊戲設計。_

Fruity Falls 是簡單、 物理條件為基礎的遊戲，玩家在其中彩色的貯體，來獲得點數到排序紅色與黃色的水果。 遊戲的目標是盡可能取得多個點，而不讓水果拖放到錯誤的分類收納，結束遊戲。

![](fruity-falls-images/image1.png "遊戲的目標是要盡可能取得多個點，而不讓水果拖放到錯誤的分類收納，結束遊戲")

Fruity Falls 擴充所介紹的概念[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)新增下列內容：

 - 中的 Png 格式的內容
 - 進階的物理
 - 遊戲狀態 （場景之間的轉換）
 - 若要微調的遊戲的係數，透過單一類別中包含的變數的能力
 - 內建的視覺化偵錯支援
 - 程式碼組織使用遊戲的實體
 - 著重於有趣和重新執行值的遊戲設計

雖然[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)Fruity 便著重於介紹 CocosSharp 的核心概念，示範如何將其全部整合到完成遊戲的產品。 因為本指南參考 BouncingGame，讀取器應該先熟悉自行向[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)之前閱讀這份指南。

本指南涵蓋的實作和 Fruity 便在 2007 年的設計，提供深入解析，可以協助您建立自己的遊戲。 其中涵蓋下列主題：


- [GameController 類別](#gamecontroller-class)
- [遊戲的實體](#game-entities)
- [水果圖形](#fruit-graphics)
- [物理](#physics)
- [內容的遊戲](#game-content)
- [GameCoefficients](#gamecoefficients)


## <a name="gamecontroller-class"></a>GameController 類別

Fruity 落 PCL 專案包含`GameController`類別負責具現化遊戲和場景之間移動。 這個類別的 iOS 和 Android 專案來消除重複的程式碼。

內含的程式碼`Initialize`中的程式碼的方法大致`Initialize`方法中的不變的 CocosSharp 範本，但包含一些修改。

根據預設，`GameView.ContentManager.SearchPaths`取決於裝置解析度。 Fruity 便如下所述在更多詳細資料，使用相同的內容，不論裝置解析度，因此`Initialize`方法會加入`Images`路徑 （與任何子資料夾） `SearchPaths`:


```csharp
contentSearchPaths.Add ("Images");
```

新的 CocosSharp 範本包含類別，繼承自`CCLayer`，意味著遊戲的視覺效果和邏輯應該加入至這個類別。 相反地，Fruity 便會使用多個`CCLayer`至控制項的執行個體繪製順序。 這些`CCLayer`內所包含的執行個體`GameView`類別，繼承自`CCScene`。 Fruity Falls 也包含多個場景，第一個要`TitleScene`。 因此，`Initialize`方法具現化`TitleScene`執行個體傳遞至`RunWithScene`:


```csharp
var scene = new TitleScene (GameView);
GameView.Director.RunWithScene (scene);
```

Fruity 便在 2007 年的內容已建立為低解析度的像素藝術，邊線。 `GameView.DesignResolution`使遊戲才 384 單位的寬度和 512 安裝設定：


```csharp
// We use a lower-resolution display to get a pixellated appearance
int width = 384;
int height = 512;
GameView.DesignResolution = new CCSizeI (width, height); 
```

最後，`GameController`類別提供靜態方法，可以呼叫任何`CCGameScene`轉換為不同`CCScene`。 這個方法用於之間移動`TitleScene`而`GameScene`。


## <a name="game-entities"></a>遊戲的實體

Fruity Falls 會利用實體模式來執行大部分的遊戲的物件。 此模式的詳細的說明可在[CocosSharp 中的實體引導](~/graphics-games/cocossharp/entities.md)。

可以找到的實體實作的遊戲物件，在 [實體] 資料夾中**FruityFalls.Common**專案：

![](fruity-falls-images/image2.png "FruityFalls.Common 專案中的 [實體] 資料夾")

實體是繼承自物件`CCNode`，而且可能會有視覺效果、 衝突和每個畫面格的行為。

`Fruit`物件都代表一個典型的 CocosSharp 實體： 包含視覺物件、 衝突，以及每個畫面格邏輯。 其建構函式會負責初始化果實：


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

`CreateFruitGraphic`方法會建立`CCSprite`執行個體，並將它加入至`Fruit`。 `IsAntialiased`屬性設為 false 會讓遊戲不美觀的外觀。 此值設定為 false，所有`CCSprite`和`CCLabel`整個遊戲的執行個體：


```csharp
private void CreateFruitGraphic()
{
    graphic = new CCSprite ("cherry.png");
    graphic.IsAntialiased = false;
    this.AddChild (graphic);
}
```

每當接觸到 media player`Fruit`執行個體與`Paddle`，該水果的點值增加一。 這提供額外的挑戰有經驗的玩家以有效利用多加一分的水果。 使用顯示的水果的點值`extraPointsLabel`執行個體。

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

Fruity Falls 包含兩種不同的水果 – 櫻桃和 lemons。 `CreateFruitGraphic`指派可以透過變更預設視覺效果，但水果視覺效果`FruitColor`屬性，而呼叫`UpdateGraphics`:


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

第一個 if 陳述式中`UpdateGraphics`更新偵錯圖形中，用來以視覺化方式檢視衝突區域。 這些視覺效果會關閉，才能進行時，最終版本的遊戲，但可以在偵錯物理的開發期間會保留。 第二個組件變更`graphic.Texture`屬性，藉由呼叫`CCTextureCache.SharedTextureCache.AddImage`。 這個方法會提供存取材質依檔案名稱。 這個方法的詳細資訊可在[紋理快取指南](~/graphics-games/cocossharp/texture-cache.md)。

`extraPointsLabel`色彩調整，以保留對比水果映像，並將其`PositionY`值會調整至中心`CCLabel`水果上`CCSprite`:

![](fruity-falls-images/image3.png "ExtraPointsLabel 色彩會調整成保留水果映像，相較，而且其 PositionY 值會調整為中心上的水果 CCSprite CCLabel")

![](fruity-falls-images/image4.png "ExtraPointsLabel 色彩會調整成保留水果映像，相較，而且其 PositionY 值會調整為中心上的水果 CCSprite CCLabel")


### <a name="collision"></a>衝突

Fruity Falls 會實作自訂衝突解決方案，使用 [幾何] 資料夾中的物件：

![](fruity-falls-images/image5.png "Fruity Falls 會實作自訂衝突解決方案，使用 [幾何] 資料夾中的物件")

使用其中一個實作 Fruity 便在 2007 年中的衝突`Circle`或`Polygon`物件時，通常會新增為子系實體這兩種類型的其中一個。 例如，`Fruit`物件具有`Circle`稱為`Collision`用來在它初始化其`CreateCollision`方法：


```csharp
private void CreateCollision()
{
    Collision = new Circle ();
    Collision.Radius = GameCoefficients.FruitRadius;
    this.AddChild (Collision);
}
```

請注意，`Circle`類別繼承自`CCNode`類別，因此可以做為子系中新增至我們的遊戲實體：


```csharp
public class Circle : CCNode
{
    ...
}
```

`Polygon` 建立大致`Circle`建立，如中所示`Paddle`類別：


```csharp
private void CreateCollision()
{
    Polygon = Polygon.CreateRectangle(width, height);
    this.AddChild (Polygon);
}
```

衝突的邏輯涵蓋[本指南稍後的](#collision)。


## <a name="physics"></a>物理

Fruity 落在物理條件，可區分為兩類： 移動和衝突。 


### <a name="movement-using-velocity-and-acceleration"></a>使用速度和加速移動

使用 Fruity Falls`Velocity`並`Acceleration`值，以控制其實體，類似於移動[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)。 實體在一個名為方法中實作其移動邏輯`Activity`，其會呼叫一次每個畫面。 比方說，我們可以看到的實作中移動`Fruit`類別的`Activity`方法：

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
`Fruit`物件都是唯一在拖曳實作 – 相對於速度成果的速度會變慢的值是移動。 拖曳此實作會提供*終端機的速度*，這是最快速度狀況的水果執行個體。 拖曳也會減慢水果，會讓遊戲很容易用來播放的水平移動。

`Paddle`物件也適用於`Velocity`在其`Activity`方法，但其`Velocity`會使用計算`desiredLocation`值：


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

一般而言，遊戲使用`Velocity`無法直接控制其物件執行的位置設定的實體位置，至少不會在初始化之後。 而不是直接設定`Paddle`位置`Paddle.HandleInput`方法指派`desiredLocation`值：

```csharp
public void HandleInput(CCPoint touchPoint)
{
    desiredLocation = touchPoint;
}
```

### <a name="collision"></a>衝突

Fruity Falls 這類實作半實際成果和其他 collidable 物件之間的衝突`Paddle`和`GameScene.Splitter`。 若要協助偵錯衝突，Fruity 便衝突區域可變成可見變更`GameCoefficients.ShowDebugInfo`在`GameCoefficients.cs`檔案：


```csharp
public static class GameCoefficients
{
    ... 
    public const bool ShowCollisionAreas = true;
    ...
}
```

此值設定為`true`讓衝突區域呈現：  

![](fruity-falls-images/image6.png "此值設定為 true 可讓衝突區域的轉譯")

衝突的邏輯開始`GameScene.Activity`方法：


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

`PerformCollision` 處理所有衝突`Fruit`與其他物件的執行個體： 


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

`FruitVsBorders` 衝突會衝突，而不是依賴不同的類別中包含的邏輯執行自己的邏輯。 不是完全實心的成果與畫面的框線之間的衝突 – 就可能會發送藉由仔細檔板移動的畫面邊緣的水果，就會發生這項差異。 水果會彈離開螢幕叫用時使用檔板，但是如果播放程式緩時變推送水果會移動超過的邊緣，並在螢幕：


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

`FruitVsBins`方法負責檢查任何水果已破合而為一的兩個分類收納組。如果是的話，播放程式點數 （如果水果/bin 色彩相符項目），或遊戲結束 （如果不相符的色彩）：


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

與翹翹板，以及與分隔 （分隔兩個分類收納的區域） 的水果的水果衝突均仰賴`FruitPolygonCollision`方法。 這個方法有三個部分：

1. 測試是否物件碰撞在一起
1. 移動物件 （只 Fruity 落在水果），使它們不再重疊
1. 若要模擬的退回，下列程式碼示範在前面製作的點調整物件 （只 Fruity 落在水果） 的速度：


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

Fruity Falls 衝突回應只是單面 – 僅成果的速度和位置會進行調整。 值得注意的是其他遊戲，可能會影響這兩個物件相關，例如字元，將 boulder 或損毀的兩部車推入彼此衝突。

中所包含的衝突邏輯背後的數學`Polygon`和`CollisionResponse`類別已超出本指南的範圍，但做為撰寫，這些方法可以用於許多類型的遊戲。 多邊形和`CollisionResponse`類別甚至支援非矩形和凸面多邊形，因此這段程式碼可以用於許多類型的遊戲。

 


## <a name="game-content"></a>內容的遊戲

Fruity 落在封面立即會區別與 BouncingGame 的遊戲。 類似遊戲設計時，播放程式會立即看到有不同的兩個遊戲的外觀。 玩家通常會決定是否要玩遊戲，其視覺效果。 因此，它是非常重要的是，開發人員在挹注資源進行視覺上吸引人遊戲。

Fruity 便在 2007 年的藝術被經由下列目標：

 - 一致的佈景主題
 - 著重於只是一個字元 – Xamarin monkey
 - 若要建立放寬、 愉悅明亮的色彩體驗
 - 因此遊戲物件可以輕易地以視覺化方式追蹤背景和前景之間比較
 - 能夠建立簡單的視覺效果，而不需要耗用大量資源的動畫


### <a name="content-location"></a>內容位置

Fruity Falls 會包含其所有內容在 Android 專案中的 [影像] 資料夾中：

![](fruity-falls-images/image7.png "Fruity Falls 會將其所有內容納入 Android 專案中的 [影像] 資料夾")

這些相同的檔案的連結的 iOS 專案，以避免重複，並且是檔案的變更會影響這兩個專案：

![](fruity-falls-images/image8.png "這些相同的檔案的連結的 iOS 專案，以避免重複，並且是檔案的變更會影響這兩個專案")

值得注意的是，內容不會包含在**Ld**或是**Hd**屬於預設 CocosSharp 範本的資料夾。 **Ld**並**Hd**資料夾要用於提供兩組內容 – 其中一個較低解析度的裝置，例如電話及更高解析度裝置，例如平板電腦的遊戲。 Fruity 便封面刻意建立的不美觀美觀，這樣它就不需要提供內容的不同螢幕大小。 因此， **Ld**並**Hd**資料夾已完全移除從專案。


### <a name="gamescene-layering"></a>GameScene 分層

本指南中，稍早所述`GameScene`會負責所有遊戲物件具現化、 定位和間的物件 （例如衝突） 的邏輯。 所有的物件會新增至其中的四個`CCLayer`執行個體：


```csharp
CCLayer backgroundLayer;
CCLayer gameplayLayer;
CCLayer foregroundLayer;
CCLayer hudLayer;
```

這些四個層級建立在`CreateLayers`方法。 請注意，它們會新增至`GameScene`後至前順序：


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

一旦建立圖層，所有的視覺物件加入至圖層上使用`AddChild`方法，如中所示`CreateBackground`方法：


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

`Vine`實體唯一使用的內容，它不會影響上的遊戲。 它組成釐清問題`CCSprite`執行個體，藉此確定藤蔓永遠會到達畫面頂端選取的試驗和錯誤號碼：


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

第一個`CCSprite`位於使與其下邊緣符合藤蔓的位置。 這可以藉由設為 AnchorPoint `new CCPoint(.5f, 0)`。 `AnchorPoint`屬性使用*正規化座標*的範圍介於 0 和 1 不論大小為何`CCSprite`:

![](fruity-falls-images/image9.png "AnchorPoint 屬性使用標準化的座標的範圍介於 0 和 1 不論 CCSprite 的大小")

使用位於後續藤蔓 sprite`graphic.ContentSize.Height`傳回影像的高度，單位為像素的值。 下圖顯示藤蔓圖形如何向上磚：

![](fruity-falls-images/image10.png "下圖顯示藤蔓圖形如何向上磚")

因為的原點`Vine`實體位於底部的 藤蔓，則放置它很簡單，如所示`Paddle.CreateVines`方法：


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

中的執行個體藤蔓`Paddle`實體也有感興趣的旋轉行為。 由於`Paddle`整體的實體會根據使用者輸入 （這本指南涵蓋詳細說明如下），旋轉`Vine`執行個體也會受到此循環。 不過，旋轉`Vine`執行個體連同`Paddle`會產生非預期的視覺效果，如下列動畫中所示：

![](fruity-falls-images/image11.gif "不過，旋轉藤蔓執行個體，以及檔板產生非預期的視覺效果，在下列動畫中所示")

為了消弭輕忽`Paddle`旋轉`leftVine`並`rightVine`會循環使用執行個體檔板，如中所示的反方向`Paddle.Activity`方法：


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

請注意旋轉一小部分會加回至透過藤蔓`vineAngle`係數。 這個值可以變更來調整 vines 多少旋轉。


## <a name="gamecoefficients"></a>GameCoefficients

每個良好的遊戲是個反覆項目，產品，因此 Fruity 便包含類別，稱為`GameCoefficients`這會控制播放遊戲的方式。 這個類別包含易懂控制項物理學，版面配置，繁衍，和評分的遊戲中使用變數。

比方說，水果物理條件是由下列變數進行控制：


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

顧名思義，這些變數可用來調整速度的水果落，如何水平移動隨著時間而檔板 bounciness 減慢。

（例如 XML) 的程式碼或資料檔案中儲存的遊戲係數可以是團隊遊戲的設計人員也不是程式設計人員特別有用。

`GameCoefficients`類別也會包含在偵錯資訊，例如繁衍資訊或衝突的區域時開啟的值：


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

本指南探索 Fruity 便在 2007 年遊戲。 它涵蓋包括內容、 物理條件，以及遊戲的狀態管理的概念。

## <a name="related-links"></a>相關連結

- [CocosSharp API 文件](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [已完成的專案 （範例）](https://developer.xamarin.com/samples/mobile/FruityFalls/)
