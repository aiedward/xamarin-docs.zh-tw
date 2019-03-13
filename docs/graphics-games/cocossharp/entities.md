---
title: CocosSharp 中的實體
description: 實體的模式是強大的方式來組織遊戲的程式碼。 它可改善可讀性，讓您更輕鬆地維護的程式碼，並利用內建的父/子功能。
ms.prod: xamarin
ms.assetid: 1D3261CE-AC96-4296-8A53-A76A42B927A8
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 42034261c374183346c8072eb42014f43a4fe22c
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667603"
---
# <a name="entities-in-cocossharp"></a>CocosSharp 中的實體

_實體的模式是強大的方式來組織遊戲的程式碼。它可改善可讀性，讓您更輕鬆地維護的程式碼，並利用內建的父/子功能。_

實體的模式可以改善搭配 cocossharp 使用 Tiled 透過改良的程式碼組織的開發人員的努力。 本逐步解說會示範如何建立兩個實體-在出貨實體與項目符號實體的實際操作範例。 這些實體會是獨立的物件，這表示一次具現化，它們會自動轉譯，並會執行移動邏輯，以適合其類型。 

本指南涵蓋下列主題：

 - 遊戲的實體的簡介
 - 與特定實體類型的一般
 - 專案設定
 - 建立實體類別
 - 加入實體執行個體 `GameLayer`
 - 回應中的實體邏輯 `GameLayer`

已完成的遊戲看起來像這樣：

![](entities-images/image1.png "已完成的遊戲看起來像這樣")


## <a name="introduction-to-game-entities"></a>遊戲的實體的簡介

遊戲的實體會定義物件需要轉譯、 衝突、 物理條件或人工智慧邏輯的類別。 幸運的是，遊戲的程式碼基底中的實體通常會符合在遊戲中的概念物件。 在這個情況下，識別在遊戲中所需的實體可以更輕鬆地完成。 

例如，空間配置其佈景主題[限定是恨死它們讓精益求精](https://en.wikipedia.org/wiki/Shoot_%27em_up)可能包含下列實體：

 - `PlayerShip`
 - `EnemyShip`
 - `PlayerBullet`
 - `EnemyBullet`
 - `CollectableItem`
 - `HUD`
 - `Environment`

這些實體將可在自己的類別，在遊戲中，以及每個執行個體時，幾乎沒有任何安裝程式之外具現化。


## <a name="general-vs-specific-entity-types"></a>與特定實體類型的一般

其中一個使用的是實體系統的遊戲開發人員所面臨的第一個問題是多少一般化其實體。 特定的實作會定義類別的每一種實體，即使它們的一些特性不同。 更一般的系統會將實體的群組合併成一個類別，並允許可自訂的執行個體。

比方說，我們可以想像空間遊戲定義下列類別：

 - `PlayerDogfighter`
 - `PlayerBomber`
 - `EnemyMissileShip`
 - `EnemyLaserShip`

若要建立播放器隨附的單一類別和敵人隨附，可以設定成支援不同的出貨類型的單一類別，會更一般化的方法。 自訂可能會包含要載入，哪種類型的項目符號實體射擊、 移動係數時要建立的映像，且附隨的敵人的 AI 邏輯。 在此情況下可能會減少實體清單為：

 - `PlayerShip`
 - `EnemyShip`

當然，您可以進一步藉由使用控制移動的每個執行個體自訂一般化這些實體型別。 敵人的出貨的執行個體可能會執行人工智慧邏輯時，就會從輸入讀取播放程式出貨的執行個體。 這表示，實體無法一般化，進一步為單一類別：

 - `Ship`

一般化可以繼續進一步 – 遊戲可能用於所有實體的單一基底類別。 這個單一類別，可以呼叫`GameEntity`會用於整個遊戲中的每個實體執行個體的類別，包括實體不是出貨等項目符號及可回收的項目 （增強物品）。

這大部分一般的系統通常稱為*元件系統*。 在這類系統中，遊戲的實體可以有個別的元件，例如物理學，AI，或者呈現元件加入至自訂行為和外觀。 純元件為基礎的系統啟用最大的彈性，並可以避免使用複雜的繼承鏈結例如繼承所造成的問題。 如同其他歸納，有效元件系統可能很難設定，而且可以減少的表達力的程式碼。

使用一般化的層級取決於許多考量，包括：

 - 遊戲大小 – 建立特定類別，而較大的遊戲可能很難管理且含有大量類別可以負擔較小的遊戲。
 - 資料驅動型開發-依賴資料 （影像、 3D 模型和資料檔案，例如 JSON 或 XML） 的遊戲可能受益於有一般化的實體類型，以及設定資料為基礎的細節。 這是特別針對預期會加入新的內容，在開發期間或之後已發行的遊戲的遊戲的匯入。
 - 遊戲引擎模式 – 某些遊戲引擎強烈元件系統的使用方式而有些則可讓開發人員決定組織實體的方式。 CocosSharp 不需要是元件的系統，使用的方式，讓開發人員可自行實作任何類型的實體。 

為了簡單起見，我們將使用特定類別為基礎的方法與單一的出貨和項目符號實體在本教學課程。


## <a name="project-setup"></a>專案設定

我們開始實作實體之前，我們需要建立專案。 我們將使用 CocosSharp 專案範本來簡化建立專案。 [請檢查這篇文章](http://forums.xamarin.com/discussion/26822/cocossharp-project-templates-for-xamarin-studio)建立 CocosSharp 專案從 Visual Studio for Mac 範本的資訊。 本指南的其餘部分將會使用專案名稱**EntityProject**。

我們的專案建立之後，我們會設定在 480 x 320 執行我們的遊戲的解析度。 若要這樣做，請呼叫`CCScene.SetDefaultDesignResolution`在`GameAppDelegate.ApplicationDidFinishLaunching`方法，如下所示：


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...

    // New code for resolution setting:
    CCScene.SetDefaultDesignResolution(480, 320, CCSceneResolutionPolicy.ShowAll);
    
    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);
    mainWindow.RunWithScene (scene);
} 
```

如需有關處理 CocosSharp 解決方式的詳細資訊，請參閱我們[處理 CocosSharp 中的多個解決方法指南](~/graphics-games/cocossharp/resolutions.md)。


## <a name="adding-content-to-the-project"></a>將內容新增至專案

一旦建立專案之後，我們將新增中包含的檔案[此內容的 zip 檔案](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)。 若要這樣做，請下載 zip 檔案，並將它解壓縮。 新增兩者**ship.png**並**bullet.png**來**內容**資料夾。 **內容**資料夾將會是內部**資產**資料夾在 Android 上，而且會在 iOS 上的專案的根目錄。 新增之後，我們應該會看到這兩個檔案中的**內容**資料夾：

![](entities-images/image2.png "一旦加入，這兩個檔案應該位於內容的資料夾")


## <a name="creating-the-ship-entity"></a>建立出貨實體

`Ship`類別將會是我們遊戲的第一個實體。 若要新增`Ship`類別中，先建立名為的資料夾**實體**專案的根層級。 加入新的類別中**實體**稱為資料夾`Ship`:

![](entities-images/image3.png "加入新的類別稱為 「 出貨的實體資料夾中")

我們要進行的第一個變更我們`Ship`類別是要讓它繼承自`CCNode`類別。 `CCNode` 可做為通用 CocosSharp 類別的基底類別喜歡`CCSprite`和`CCLayer`，並且為我們提供下列功能：

 - `Position` 移動太空船在螢幕上的屬性。
 - `Children` 新增屬性 `CCSprite.`
 - `Parent` 屬性，可用來附加`Ship`其他執行個體`CCNodes`。 我們不會在本教學課程中，使用這項功能較大的遊戲通常會充分利用將實體附加至其他`CCNodes`。 
 - `AddEventListener` 針對回應輸入移出貨的方法。
 - `Schedule` 疑難排解項目符號的方法。

我們也會新增`CCSprite`執行個體，以便在畫面上，可以看到我們的出貨：


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Ship : CCNode
    {
        CCSprite sprite;

        public Ship () : base()
        {
            sprite = new CCSprite ("ship.png");
            // Center the Sprite in this entity to simplify
            // centering the Ship when it is instantiated
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);
        }
    }
}
```

接下來，我們將新增到出貨我們`GameLayer`看見它顯示在我們的遊戲：


```csharp
public class GameLayer : CCLayer
{
    Ship ship;

    public GameLayer ()
    {
        ship = new Ship ();
        ship.PositionX = 240;
        ship.PositionY = 50;
        this.AddChild (ship);
    } 
    ...
```

如果我們執行我們的遊戲我們現在會看到我們的出貨實體：

![](entities-images/image4.png "當執行遊戲時，會顯示出貨實體")


### <a name="why-inherit-from-ccnode-instead-of-ccsprite"></a>為什麼會繼承而不是 CCSprite CCNode 嗎？

此時我們`Ship`類別是簡單的包裝函式，如`CCSprite`執行個體。 由於`CCSprite`也會繼承`CCNode`，我們可以直接從具有繼承`CCSprite`，這會減少中的程式碼`Ship.cs`。 此外，直接從繼承`CCSprite`可減少記憶體中的物件數目，並可改善效能，讓較小的相依性樹狀結構。

儘管這些權益，我們會繼承自`CCNode`來隱藏某些資料`CCSprite`從每個執行個體的屬性。 例如，`Texture`屬性不能修改之外`Ship`類別，並繼承自`CCNode`可讓我們來隱藏此屬性。 實體的公用成員變成遊戲的大小和其他開發人員會加入至小組特別重要。


## <a name="adding-input-to-the-ship"></a>新增出貨的輸入

現在，我們的出貨是在螢幕上看見我們將新增輸入。 我們的方法將會類似於中採取的方法[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)，不過我們會將移動中的程式碼`Ship`類別，而不是含有`CCLayer`或`CCScene`。

加入程式碼以`Ship`才能將它移至使用者只要觸碰螢幕：


```csharp
public class Ship : CCNode
{
    CCSprite sprite;

    CCEventListenerTouchAllAtOnce touchListener;

    public Ship () : base()
    {
        sprite = new CCSprite ("ship.png");
        // Center the Sprite in this entity to simplify
        // centering the Ship on screen
        sprite.AnchorPoint = CCPoint.AnchorMiddle;
        this.AddChild(sprite);

        touchListener = new CCEventListenerTouchAllAtOnce();
        touchListener.OnTouchesMoved = HandleInput;
        AddEventListener(touchListener, this);

    }

    private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
    {
        if(touches.Count > 0)
        {
            CCTouch firstTouch = touches[0];

            this.PositionX = firstTouch.Location.X;
            this.PositionY = firstTouch.Location.Y;
        } 
    }
} 
```

許多豬羊是恨死它們啟動遊戲實作最高的速度，以模擬傳統控制器為基礎的移動。 話雖如此，我們只會實作讓我們較短的程式碼的立即移動。


## <a name="creating-the-bullet-entity"></a>建立項目符號實體

在我們簡單的遊戲中的第二個實體是顯示項目符號的實體。 就如同`Ship`實體`Bullet`實體會包含`CCSprite`，使其出現在畫面上。 移動邏輯不同，因為它不相依於移動; 的使用者輸入相反地，`Bullet`執行個體將使用速度屬性以直線移動。

首先我們會將新的類別檔案，以新增我們**實體**資料夾，並稱之為**項目符號**:

![](entities-images/image5.png "將新的類別檔案新增至 [實體] 資料夾，並呼叫它的項目符號")

一旦加入我們要修改`Bullet.cs`程式碼，如下所示：


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Bullet : CCNode
    {
        CCSprite sprite;

        public float VelocityX
        {
            get;
            set;
        }

        public float VelocityY
        {
            get;
            set;
        }

        public Bullet () : base()
        {
            sprite = new CCSprite ("bullet.png");
            // Making the Sprite be centered makes
            // positioning easier.
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);

            this.Schedule (ApplyVelocity);
        }

        void ApplyVelocity(float time)
        {
            PositionX += VelocityX * time;
            PositionY += VelocityY * time;
        }
    }
} 
```

除了變更所使用的檔案`CCSprite`要`bullet.png`中的程式碼`ApplyVelocity`包含移動的邏輯，此作業取決於兩個係數：`VelocityX`和`VelocityY`。

`Schedule`方法可讓新增委派呼叫每個畫面格。 在此情況下，我們要新增`ApplyVelocity`方法，讓我們的項目符號將根據其速度值。 `Schedule`方法會採用`Action<float>`，其中的浮點數參數指定的時間量 （以秒為單位） 自最後一個畫面格，用來實作以時間為基礎的移動。 時間 （秒），測量值，則我們的速度值代表中的移動*像素為單位，每秒*。


## <a name="adding-bullets-to-gamelayer"></a>將項目符號加入至 GameLayer

我們將新增任何之前`Bullet`給我們的遊戲的執行個體，我們將一種容器，特別是`List<Bullet>`。 修改`GameLayer`使其包含的項目符號清單：


```csharp
    public class GameLayer : CCLayer
    {
        Ship ship;
        List<Bullet> bullets;

        public GameLayer ()
        {
            ship = new Ship ();
            ship.PositionX = 240;
            ship.PositionY = 50;
            this.AddChild (ship);

            bullets = new List<Bullet> ();
        }
        ... 
```

接下來我們必須填入`Bullet`清單。 何時建立的邏輯`Bullet`應包含在`Ship`實體，但`GameLayer`負責儲存的項目符號清單。 我們將使用 factory 模式來允許`Ship`要建立實體`Bullet`執行個體。 此處理站會公開事件，`GameLayer`可以處理。 

若要這樣做首先，我們會將資料夾加入我們的專案稱為**Factory**，然後新增新的類別，稱為`BulletFactory`:

![](entities-images/image6.png "將資料夾加入專案呼叫處理站，並再加入新的類別，稱為 BulletFactory")

接下來，我們將實作`BulletFactory`單一類別：


```csharp
using System;

namespace EntityProject
{
    public class BulletFactory
    {
        static Lazy<BulletFactory> self = 
            new Lazy<BulletFactory>(()=>new BulletFactory());

        // simple singleton implementation
        public static BulletFactory Self
        {
            get
            {
                return self.Value;
            }
        }

        public event Action<Bullet> BulletCreated;

        private BulletFactory()
        {

        }

        public Bullet CreateNew()
        {
            Bullet newBullet = new Bullet ();

            if (BulletCreated != null)
            {
                BulletCreated (newBullet);
            }

            return newBullet;
        }
    }
} 
```

`Ship`實體會處理建立`Bullet`執行個體 – 特別是，它會處理頻率`Bullet`應該 （也就是頻率引發的項目符號），建立執行個體，其位置和其速度。

修改`Ship`實體的建構函式，將新`Schedule`呼叫，並接著實作這個方法，如下所示：


```csharp
...
public Ship () : base()
{
    sprite = new CCSprite ("ship.png");
    // Center the Sprite in this entity to simplify
    // centering the Ship on screen
    sprite.AnchorPoint = CCPoint.AnchorMiddle;
    this.AddChild(sprite);

    touchListener = new CCEventListenerTouchAllAtOnce();
    touchListener.OnTouchesMoved = HandleInput;
    AddEventListener(touchListener, this);

    Schedule (FireBullet, interval: 0.5f);

}

void FireBullet(float unusedValue)
{
    Bullet newBullet = BulletFactory.Self.CreateNew ();
    newBullet.Position = this.Position;
    newBullet.VelocityY = 100;
} 
...
```

最後一個步驟是處理建立的新`Bullet`執行個體在`GameLayer`程式碼。 新增事件處理常式`BulletCreated`加入新建立的事件`Bullet`與適當的清單：


```csharp
...
public GameLayer ()
{
    ship = new Ship ();
    ship.PositionX = 240;
    ship.PositionY = 50;
    this.AddChild (ship);

    bullets = new List<Bullet> ();
    BulletFactory.Self.BulletCreated += HandleBulletCreated;
}

void HandleBulletCreated(Bullet newBullet)
{
    AddChild (newBullet);
    bullets.Add (newBullet);
}
... 
```

現在我們可以執行遊戲，並請參閱`Ship`著眼於`Bullet`執行個體：

![](entities-images/image1.png "執行遊戲和出貨將著眼於項目符號的執行個體")


## <a name="why-gamelayer-has-ship-and-bullets-members"></a>為什麼 GameLayer 已出貨和項目符號的成員

我們`GameLayer`類別會定義兩個欄位，以容納我們的實體執行個體的參考 (`ship`和`bullets`)，但不進行任何處理。 此外，實體會負責他們自己的行為，例如移動和疑難排解。 那麼為什麼沒有我們新增`ship`並`bullets`欄位`GameLayer`？

我們已新增這些成員的原因是因為完整遊戲的實作會要求中的邏輯`GameLayer`不同實體之間的互動。 比方說，您可能會進一步包含播放程式就可以終結的敵人開發這個遊戲中。 這些的敵人將會包含在`List`中`GameLayer`，和邏輯，來測試是否`Bullet`與相衝突的執行個體中執行的敵人`GameLayer`以及。 亦即`GameLayer`是根*擁有者*所有實體的執行個體，以及它會負責實體執行個體之間的互動。


## <a name="bullet-destruction-considerations"></a>項目符號解構考量

我們的遊戲目前缺少終結的程式碼`Bullet`執行個體。 每個`Bullet`執行個體的邏輯來移動在畫面上，但我們還沒有加入任何程式碼來終結任何離`Bullet`執行個體。

此外，解構`Bullet`執行個體可能不屬於`GameLayer`。 比方說，而不是被終結時幕外，`Bullet`實體可能有一段時間之後終結本身的邏輯。 在此情況下，`Bullet`需要的方法進行通訊，應該將它終結來`GameLayer`，就跟`Ship`實體傳送到`GameLayer`的新`Bullet`用來建立`BulletFactory`。

最簡單的解決方法是展開的 factory 類別，以支援解構的責任。 然後 factory 就可以收到一個實體執行個體被損毀，這可由其他物件，例如`GameLayer`從其清單中移除的實體執行個體。 

## <a name="summary"></a>總結

本指南說明如何藉由繼承自建立 CocosSharp 實體`CCNode`類別。 這些實體是獨立的物件，處理建立他們自己的視覺效果和自訂邏輯。 本指南會指定從屬於根實體容器 （衝突及其他實體的互動邏輯） 的程式碼所屬的實體 （移動和建立其他實體） 內的程式碼。

## <a name="related-links"></a>相關連結

- [CocosSharp API 文件](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [內容的 zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)
