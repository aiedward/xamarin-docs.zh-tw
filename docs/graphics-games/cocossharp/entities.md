---
title: CocosSharp 中的實體
description: 實體模式是強大的方式來組織的遊戲程式碼。 它有助於改善可讀性，讓程式碼更易於維護，並利用內建的父/子功能。
ms.topic: article
ms.prod: xamarin
ms.assetid: 1D3261CE-AC96-4296-8A53-A76A42B927A8
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: bb4af0f76f6b266cad4eb969d987a346b7396aa9
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="entities-in-cocossharp"></a>CocosSharp 中的實體

_實體模式是強大的方式來組織的遊戲程式碼。它有助於改善可讀性，讓程式碼更易於維護，並利用內建的父/子功能。_

實體模式可以改善 CocosSharp 與透過改良的程式碼組織的開發人員的努力。 本逐步解說會示範如何建立兩個實體 – 出貨實體和項目符號的實際操作範例。 這些實體會各自獨立的物件，這表示一旦具現化，它們將自動轉譯，並會執行移動邏輯，視其型別。 

本指南涵蓋下列主題：

 - 遊戲實體簡介
 - 一般與特定實體類型
 - 專案設定
 - 建立實體類別
 - 加入至實體執行個體 `GameLayer`
 - 實體邏輯中對回應 `GameLayer`

完成的遊戲看起來像這樣：

![](entities-images/image1.png "完成的遊戲看起來像這樣")


## <a name="introduction-to-game-entities"></a>遊戲實體簡介

遊戲的實體是類別，定義物件需要呈現、 衝突、 物理或人工地智慧邏輯。 幸運的是，出現在遊戲的程式碼基底實體通常符合在遊戲概念性的物件。 在這個情況下，找出需要在遊戲中的實體可以更輕鬆地完成。 

例如，空間佈景主題[向上遊戲豬羊顆](http://en.wikipedia.org/wiki/Shoot_%27em_up)可能包含下列項目：

 - `PlayerShip`
 - `EnemyShip`
 - `PlayerBullet`
 - `EnemyBullet`
 - `CollectableItem`
 - `HUD`
 - `Environment`

這些實體會在遊戲中，他們自己類別，並每個執行個體需要少量或沒有安裝程式之外具現化。


## <a name="general-vs-specific-entity-types"></a>一般與特定實體類型

使用實體系統的遊戲開發人員所面臨的第一個問題是多少一般化其實體。 特定的實作會定義類別之每種實體，即使它們的幾項特性不同。 更一般的系統會將個實體群組合併成一個類別，並允許可自訂的執行個體。

例如，我們可以想像空間遊戲定義下列類別：

 - `PlayerDogfighter`
 - `PlayerBomber`
 - `EnemyMissileShip`
 - `EnemyLaserShip`

更通用的方法是建立播放程式隨附的單一類別和船，無法進行設定以支援不同的出貨類型的單一類別。 自訂可能包含要載入，哪種類型的項目符號實體以建立時疑難排解，移動係數的映像，以及隨附敵人 AI 邏輯。 在此情況下的實體清單可能會降低為：

 - `PlayerShip`
 - `EnemyShip`

當然，您可以進一步藉由使用控制移動的每個執行個體自訂一般化這些實體型別。 敵人的出貨的執行個體可能會執行 AI 邏輯時，會從輸入讀取 Player 出貨的執行個體。 這表示實體，無法一般化甚至進一步成單一類別：

 - `Ship`

一般化可以繼續進一步 – 遊戲可能用於所有實體的單一基底類別。 這個單一類別，可能呼叫`GameEntity`會是用於整個遊戲中的每個實體執行個體的類別，包括實體未隨附等項目符號及可收集之項目 (電源 ups)。

此系統的大部分一般通常稱為*元件系統*。 在這類系統中，遊戲實體可以具有個別的元件，例如物理 AI，或呈現元件加入至自訂行為和外觀。 純元件為基礎的系統啟用最大的彈性，並可避免使用複雜的繼承鏈結例如繼承所造成的問題。 如同其他一般化，有效元件系統很難進行設定，而且可以減少程式碼的表達。

使用一般化的層級取決於許多考量，包括：

 - 遊戲大小 – 建立特定的類別，而較大的遊戲可能很難管理含有大量類別可以負擔較小的遊戲。
 - 資料導向的開發工作 – 依賴資料 （映像、 3D 模型和資料檔案，例如 JSON 或 XML） 的遊戲可能受益於具有一般化實體類型和設定資料為基礎的細節。 這是要加入新的內容，在開發期間或之後已發行的遊戲的遊戲特別匯入。
 - 遊戲引擎模式 – 某些遊戲引擎強其他人可讓開發人員可以決定如何組織實體鼓勵系統元件的使用方式。 CocosSharp 不需要元件系統的使用量，因此開發人員可自行實作任何類型的實體。 

為了簡單起見，我們會使用特定類別為基礎的方法與單一的出貨和項目符號實體本教學課程。


## <a name="project-setup"></a>專案設定

我們開始實作我們實體之前，我們需要建立專案。 我們將使用 CocosSharp 專案範本來簡化建立專案。 [請檢查這篇文章](http://forums.xamarin.com/discussion/26822/cocossharp-project-templates-for-xamarin-studio)建立 CocosSharp 專案從 Visual Studio for Mac 範本的資訊。 本指南的其餘部分將會使用專案名稱**EntityProject**。

一旦受測專案會建立我們會隨即制定我們遊戲的解決方式，在 480 x 320 執行。 若要這樣做，請呼叫`CCScene.SetDefaultDesignResolution`中`GameAppDelegate.ApplicationDidFinishLaunching`方法，如下所示：


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...

    // New code for resolution setting:
    CCScene.SetDefaultDesignResolution(480, 320, CCSceneResolutionPolicy.ShowAll);
    
    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);
    mainWindow.RunWithScene (scene);
} 
```

如需有關處理 CocosSharp 解決方式的詳細資訊，請參閱我們[上 CocosSharp 中處理多個解決方案指南](~/graphics-games/cocossharp/resolutions.md)。


## <a name="adding-content-to-the-project"></a>將內容加入至專案

一旦建立專案之後，我們會將所包含的檔案加入[此內容的 zip 檔案](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)。 若要這樣做，請下載 zip 檔案，並將它解壓縮。 新增這兩個**ship.png**和**bullet.png**至**內容**資料夾。 **內容**資料夾就會在內**資產**資料夾在 Android 上，並會在 iOS 上專案的根目錄。 一旦加入，我們應該會看到這兩個檔案中的**內容**資料夾：

![](entities-images/image2.png "一旦加入，這兩個檔案應該是內容的資料夾中")


## <a name="creating-the-ship-entity"></a>建立出貨實體

`Ship`類別將為我們遊戲的第一個實體。 若要加入`Ship`類別中，先建立一個稱為資料夾**實體**根層級的專案。 加入新的類別中**實體**資料夾稱為`Ship`:

![](entities-images/image3.png "加入新的類別稱為 「 出貨實體資料夾中")

我們要進行的第一個變更我們`Ship`類別是要讓它繼承自`CCNode`類別。 `CCNode` 可做為通用 CocosSharp 類別的基底類別喜歡`CCSprite`和`CCLayer`，並為我們提供下列功能：

 - `Position` 移動螢幕上的出貨的屬性。
 - `Children` 新增屬性 `CCSprite.`
 - `Parent` 屬性，可用來附加`Ship`其他執行個體`CCNodes`。 我們將不會使用這項功能在本教學課程。較大的遊戲通常會充分利用實體附加至其他`CCNodes`。 
 - `AddEventListener` 回應輸入移出貨的方法。
 - `Schedule` 疑難排解項目符號的方法。

我們也可以加入`CCSprite`讓您可以看到我們的出貨，螢幕上的執行個體：


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Ship : CCNode
    {
        CCSprite sprite;

        public Ship () : base()
        {
            sprite = new CCSprite ("ship.png");
            // Center the Sprite in this entity to simplify
            // centering the Ship when it is instantiated
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);
        }
    }
}
```

接下來，我們會將新增到出貨我們`GameLayer`以查看顯示在我們的遊戲：


```csharp
public class GameLayer : CCLayer
{
    Ship ship;

    public GameLayer ()
    {
        ship = new Ship ();
        ship.PositionX = 240;
        ship.PositionY = 50;
        this.AddChild (ship);
    } 
    ...
```

當我們執行我們遊戲我們現在會看到我們的出貨實體：

![](entities-images/image4.png "當執行遊戲時，會顯示出貨實體")


### <a name="why-inherit-from-ccnode-instead-of-ccsprite"></a>為什麼會繼承而不是 CCSprite CCNode 嗎？

此時我們`Ship`類別是簡單的包裝函式，如`CCSprite`執行個體。 因為`CCSprite`也會繼承`CCNode`，我們無法擁有直接繼承自`CCSprite`，其會降低中的程式碼`Ship.cs`。 此外，直接從繼承`CCSprite`減少記憶體內物件的數目，並透過讓較小的相依性樹狀結構來提高效能。

儘管有這些優點，我們會繼承自`CCNode`來隱藏某些資料`CCSprite`從每個執行個體的屬性。 例如，`Texture`屬性不能修改外部`Ship`類別，並繼承自`CCNode`可讓我們來隱藏此屬性。 我們實體的 public 成員成為遊戲的大小和其他開發人員會加入至小組尤其重要。


## <a name="adding-input-to-the-ship"></a>正在將出貨的輸入

既然我們的出貨會顯示在螢幕上我們將加入的輸入。 我們的方法將會類似於描述中所採取的方法[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)，不過，我們將會放置在移動的程式碼`Ship`類別，而不是在包含`CCLayer`或`CCScene`。

將程式碼加入`Ship`才能將它移至使用者觸控螢幕的任一處：


```csharp
public class Ship : CCNode
{
    CCSprite sprite;

    CCEventListenerTouchAllAtOnce touchListener;

    public Ship () : base()
    {
        sprite = new CCSprite ("ship.png");
        // Center the Sprite in this entity to simplify
        // centering the Ship on screen
        sprite.AnchorPoint = CCPoint.AnchorMiddle;
        this.AddChild(sprite);

        touchListener = new CCEventListenerTouchAllAtOnce();
        touchListener.OnTouchesMoved = HandleInput;
        AddEventListener(touchListener, this);

    }

    private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
    {
        if(touches.Count > 0)
        {
            CCTouch firstTouch = touches[0];

            this.PositionX = firstTouch.Location.X;
            this.PositionY = firstTouch.Location.Y;
        } 
    }
} 
```

許多攻擊來向上遊戲實作最大的速度，模擬傳統的控制站型移動。 話雖如此，我們只會實作立即移至保留較短的程式碼。


## <a name="creating-the-bullet-entity"></a>建立項目符號實體

在簡單遊戲中的第二個實體是以顯示項目符號的實體。 就像`Ship`實體，`Bullet`實體會包含`CCSprite`，讓它出現在螢幕上。 移動邏輯不同，因為它不相依於移動; 的使用者輸入相反地，`Bullet`執行個體將會使用速度屬性以直線移動。

首先我們會將新增新的類別檔案，以便我們**實體**資料夾，並為它**項目符號**:

![](entities-images/image5.png "將新的類別檔案加入至實體資料夾，並呼叫它的項目符號")

一旦加入我們會修改`Bullet.cs`程式碼，如下所示：


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Bullet : CCNode
    {
        CCSprite sprite;

        public float VelocityX
        {
            get;
            set;
        }

        public float VelocityY
        {
            get;
            set;
        }

        public Bullet () : base()
        {
            sprite = new CCSprite ("bullet.png");
            // Making the Sprite be centered makes
            // positioning easier.
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);

            this.Schedule (ApplyVelocity);
        }

        void ApplyVelocity(float time)
        {
            PositionX += VelocityX * time;
            PositionY += VelocityY * time;
        }
    }
} 
```

除了變更使用的檔案`CCSprite`至`bullet.png`中的程式碼`ApplyVelocity`包含移動邏輯為基礎的兩個係數：`VelocityX`和`VelocityY`。

`Schedule`方法允許加入每個框架呼叫的委派。 在此情況下，我們要新增`ApplyVelocity`方法，這樣我們項目符號會根據其速度值移動。 `Schedule`方法會採用`Action<float>`、 其中的浮點數參數指定的時間量 （以秒為單位） 後的最後一個畫面格，用來實作以時間為基礎的移動。 以來的秒數測量值，則我們的速度值代表移動*每秒的像素*。


## <a name="adding-bullets-to-gamelayer"></a>將項目符號加入至 GameLayer

我們新增任何之前`Bullet`我們遊戲的執行個體將會進行一種容器，特別是`List<Bullet>`。 修改`GameLayer`使其包含的項目符號清單：


```csharp
    public class GameLayer : CCLayer
    {
        Ship ship;
        List<Bullet> bullets;

        public GameLayer ()
        {
            ship = new Ship ();
            ship.PositionX = 240;
            ship.PositionY = 50;
            this.AddChild (ship);

            bullets = new List<Bullet> ();
        }
        ... 
```

接下來，我們將需要擴展`Bullet`清單。 何時建立的邏輯`Bullet`應該包含在`Ship`實體，但`GameLayer`負責儲存的項目符號清單。 我們將使用處理站模式，來允許`Ship`要建立實體`Bullet`執行個體。 此處理站會公開事件，`GameLayer`可以處理。 

若要這樣做，我們將資料夾加入至呼叫受測專案**工廠**，然後再加入新的類別稱為`BulletFactory`:

![](entities-images/image6.png "將資料夾加入呼叫處理站時，專案，然後加入新的類別稱為 BulletFactory")

接下來，我們會實作`BulletFactory`單一類別：


```csharp
using System;

namespace EntityProject
{
    public class BulletFactory
    {
        static Lazy<BulletFactory> self = 
            new Lazy<BulletFactory>(()=>new BulletFactory());

        // simple singleton implementation
        public static BulletFactory Self
        {
            get
            {
                return self.Value;
            }
        }

        public event Action<Bullet> BulletCreated;

        private BulletFactory()
        {

        }

        public Bullet CreateNew()
        {
            Bullet newBullet = new Bullet ();

            if (BulletCreated != null)
            {
                BulletCreated (newBullet);
            }

            return newBullet;
        }
    }
} 
```

`Ship`實體會處理建立`Bullet`執行個體 – 具體來說，它將處理頻率`Bullet`應該 （亦即頻率引發項目符號） 建立執行個體，其位置和其速度。

修改`Ship`實體的建構函式，將新`Schedule`呼叫，並接著實作這個方法，如下所示：


```csharp
...
public Ship () : base()
{
    sprite = new CCSprite ("ship.png");
    // Center the Sprite in this entity to simplify
    // centering the Ship on screen
    sprite.AnchorPoint = CCPoint.AnchorMiddle;
    this.AddChild(sprite);

    touchListener = new CCEventListenerTouchAllAtOnce();
    touchListener.OnTouchesMoved = HandleInput;
    AddEventListener(touchListener, this);

    Schedule (FireBullet, interval: 0.5f);

}

void FireBullet(float unusedValue)
{
    Bullet newBullet = BulletFactory.Self.CreateNew ();
    newBullet.Position = this.Position;
    newBullet.VelocityY = 100;
} 
...
```

最後一個步驟是新處理建立`Bullet`中執行個體`GameLayer`程式碼。 加入事件處理常式`BulletCreated`加入新建立的事件`Bullet`至適當的清單：


```csharp
...
public GameLayer ()
{
    ship = new Ship ();
    ship.PositionX = 240;
    ship.PositionY = 50;
    this.AddChild (ship);

    bullets = new List<Bullet> ();
    BulletFactory.Self.BulletCreated += HandleBulletCreated;
}

void HandleBulletCreated(Bullet newBullet)
{
    AddChild (newBullet);
    bullets.Add (newBullet);
}
... 
```

現在我們可以執行遊戲，請參閱`Ship`疑難排解`Bullet`執行個體：

![](entities-images/image1.png "執行遊戲和出貨疑難排解項目符號的執行個體")


## <a name="why-gamelayer-has-ship-and-bullets-members"></a>為什麼 GameLayer 已出貨和項目符號的成員

我們`GameLayer`類別會定義兩個欄位，以容納我們實體執行個體的參考 (`ship`和`bullets`)，但不進行任何處理。 此外，實體是負責其自己的行為，例如動作和疑難排解。 因此為什麼沒有我們加入`ship`和`bullets`欄位`GameLayer`？

我們加入這些成員的原因是因為完整遊戲的實作中的邏輯需要`GameLayer`不同實體之間的互動。 例如，您可能會進一步要包含的播放程式可以終結開發此遊戲。 這些敵人會包含在`List`中`GameLayer`，和邏輯，以測試是否`Bullet`與衝突的執行個體敵人會執行在`GameLayer`以及。 換句話說，`GameLayer`是根*擁有者*所有實體的執行個體，以及它會負責實體執行個體之間的互動。


## <a name="bullet-destruction-considerations"></a>項目符號解構考量

我們遊戲目前缺少終結的程式碼`Bullet`執行個體。 每個`Bullet`執行個體有邏輯移動在畫面上，但我們還沒有加入任何程式碼來出螢幕外摧毀任何`Bullet`執行個體。

此外，損毀的`Bullet`執行個體可能不屬於`GameLayer`。 例如，而不是被終結時螢幕之外、`Bullet`實體可能有一段時間之後終結本身的邏輯。 在此情況下，`Bullet`需要一個方法來進行通訊，也應該在要終結`GameLayer`，非常類似`Ship`實體傳達給`GameLayer`，新`Bullet`透過建立`BulletFactory`。

最簡單的解決方法是展開的 factory 類別，以支援解構的責任。 則可以接收通知的實體執行個體被損毀，可以處理由其他物件，例如的工廠`GameLayer`從其清單中移除實體執行個體。 

## <a name="summary"></a>總結

本指南示範如何建立 CocosSharp 實體繼承自`CCNode`類別。 這些實體都各自獨立的物件，處理建立他們自己的視覺效果和自訂邏輯。 本指南會指定從所屬根目錄的實體容器 （衝突和其他實體的互動邏輯） 中的程式碼所屬的實體 （移動及建立其他實體） 內的程式碼。

## <a name="related-links"></a>相關的連結

- [CocosSharp API 文件](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [內容壓縮](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)
