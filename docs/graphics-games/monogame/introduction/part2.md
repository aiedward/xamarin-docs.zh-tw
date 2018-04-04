---
title: 第 2 – 實作 WalkingGame 部分
description: 本逐步解說示範如何加入遊戲邏輯和空 MonoGame 專案來建立動畫的精靈，以移動的示範，內容觸控輸入。
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 89f5148dacfb229750839e4554199f78c8d15126
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="part-2--implementing-the-walkinggame"></a>第 2 – 實作 WalkingGame 部分

_本逐步解說示範如何加入遊戲邏輯和空 MonoGame 專案來建立動畫的精靈，以移動的示範，內容觸控輸入。_

本逐步解說的上一個部分說明如何建立空的 MonoGame 專案。 我們將建置這些先前的組件上進行簡單的遊戲示範。 本文包含下列章節：

 - 解壓縮我們遊戲的內容
 - MonoGame 類別概觀
 - 轉譯我們第一次精靈
 - 建立 CharacterEntity
 - 加入遊戲 CharacterEntity
 - 建立動畫類別
 - 將第一次的動畫加入至 CharacterEntity
 - 加入移動的字元
 - 比對的動作和動畫


# <a name="unzipping-our-game-content"></a>解壓縮我們遊戲的內容

我們可以開始撰寫程式碼之前，我們會想要解壓縮我們遊戲*內容*。 遊戲開發人員通常會使用這個詞彙*內容*參照到這通常由 visual 演出者、 遊戲的設計工具或音訊的設計工具建立的非程式碼檔案。 常見的內容類型包括用來顯示視覺效果、 播放聲音，或控制行為人工地智慧 (AI) 檔案。 從遊戲開發非程式設計人員通常會建立小組的檢視方塊的內容。

這裡使用的內容可以找到[github 上](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)。 我們需要這些檔案下載到我們將稍後在本逐步解說中存取的位置。


# <a name="monogame-class-overview"></a>MonoGame 類別概觀

首先，我們將探討使用的基本呈現 MonoGame 類別：

 - `SpriteBatch` – 用來繪製到螢幕的 2D 圖形。 *依照*是用來在螢幕上顯示影像的 2D 視覺項目。 `SpriteBatch`物件可以繪製單一精靈之間的一次其`Begin`和`End`方法或多個小可以群組在一起，或*批次*。
 - `Texture2D` – 表示在執行階段的映像物件。 `Texture2D` 雖然它們也可以建立以動態方式在執行階段執行個體通常會建立從檔案格式，例如.png 或.bmp。 `Texture2D` 執行個體可用，以呈現時`SpriteBatch`執行個體。
 - `Vector2` – 表示 2D 的通常用來定位視覺物件的座標系統中的位置。 也包含 MonoGame`Vector3`和`Vector4`但我們只會使用`Vector2`在本逐步解說。
 - `Rectangle` – 四邊區域位置、 寬度和高度。 我們將使用此定義的哪些部份我們`Texture2D`當我們開始處理動畫呈現。

我們也應注意的 MonoGame 就不會維護 microsoft – 儘管其命名空間。 `Microsoft.Xna`命名空間用於 MonoGame 中更輕鬆地將現有的 XNA 專案移轉至 MonoGame。


# <a name="rendering-our-first-sprite"></a>轉譯我們第一次精靈

接下來我們將會顯示如何執行在 MonoGame 的 2D 轉譯畫面繪製單一精靈。


## <a name="creating-a-texture2d"></a>建立 Texture2D

我們必須建立`Texture2D`轉譯我們精靈時所要使用的執行個體。 最終包含在名為資料夾中所有遊戲內容**內容，**平台專屬專案中。 MonoGame 共用專案不能包含的內容，內容必須使用平台專屬的建置動作。 CocosSharp 開發人員會覺得熟悉概念內容的資料夾，位於 CocosSharp 和 MonoGame 專案中的相同位置。 中的 iOS 專案，並在 Android 專案的 Assets 資料夾內，就可以找到的內容資料夾。

若要加入我們遊戲的內容，以滑鼠右鍵按一下**內容**資料夾，然後選取**新增 > 新增檔案...**瀏覽至 content.zip 檔案已解壓縮的位置，然後選取**charactersheet.png**檔案。 如果系統詢問如何將檔案加入資料夾，我們應該選取**複製**選項：

![](part2-images/image1.png "如果系統詢問如何將檔案加入資料夾，選取 [複製] 選項")

內容的資料夾現在會包含 charactersheet.png 檔案：

![](part2-images/image2.png "內容的資料夾現在會包含 charactersheet.png 檔案")

接下來，我們要在其中加入程式碼載入 charactersheet.png 檔案並建立`Texture2D`。 若要執行此開啟`Game1.cs`檔案，然後加入下列欄位 Game1.cs 類別：


```csharp
Texture2D characterSheetTexture;
```

接下來，我們將建立`characterSheetTexture`中`LoadContent`方法。 之前的任何修改`LoadContent`方法看起來像這樣：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```
我們應該會注意到預設專案是否已經產生`spriteBatch`為我們的執行個體。 我們將使用此更新版本，但我們不會新增任何額外的程式碼，以準備`spriteBatch`供使用。 相反地，我們`spriteSheetTexture`需要初始設定，讓我們將會初始化之後`spriteBatch`建立：


```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

現在，我們已經`SpriteBatch`執行個體和`Texture2D`我們可以加入我們轉譯程式碼的執行個體`Game1.Draw`方法：


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

現在執行遊戲示範顯示紋理從 charactersheet.png 建立單一精靈：

![](part2-images/image3.png "現在執行遊戲示範顯示紋理從 charactersheet.png 建立單一精靈")


#  <a name="creating-the-characterentity"></a>建立 CharacterEntity

到目前為止我們已加入程式碼，以呈現單一精靈畫面。不過，如果我們開發完整的遊戲，而不建立任何其他類別，我們會執行到程式碼組織的問題。


## <a name="what-is-an-entity"></a>什麼是實體？

組織的遊戲程式碼的常見模式為建立新的類別，每個遊戲的*實體*型別。 遊戲開發中的實體是物件可包含的某些 （並非所有需要的） 的下列特性：

 - 例如精靈、 文字或 3D 模型的視覺項目
 - 物理或每個框架行為，例如單位偵查設定路徑或輸入回應的播放程式字元
 - 您可以建立及終結以動態方式，例如開機出現和所收集的播放程式

實體組織系統可能會很複雜，以及許多遊戲引擎提供可協助管理實體的類別。 我們將會實作非常簡單的實體系統，因此您很值得注意的完整的遊戲，通常需要開發人員的組件上的多個組織。


## <a name="defining-the-characterentity"></a>定義 CharacterEntity

我們的實體，我們將稱之為`CharacterEntity`，將具有下列特性：

 - 載入自己的能力 `Texture2D`
 - 能夠呈現其本身，包括其中包含呼叫的方法，來更新走動畫
 - `X `與 Y 屬性，以控制字元的位置。
 - 功能更新本身 – 具體而言，讀取值觸控螢幕和適當地調整位置。

若要加入`CharacterEntity`我們遊戲、 以滑鼠右鍵按一下或控制項按一下**WalkingGame**專案，然後選取**新增 > 新的檔案...**.選取**空類別**選項並將新檔案**CharacterEntity**，然後按一下 **新增**。

首先我們會將新增的能力`CharacterEntity`載入`Texture2D`以及繪製本身。 我們將會修改新加入`CharacterEntity.cs`檔案，如下所示：


```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;


namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

上述程式碼會加入的位置、 轉譯和載入至內容責任`CharacterEntity`。 讓我們花一點時間來指出在上述程式碼中所採取的一些考量。


## <a name="why-are-x-and-y-floats"></a>為什麼是 X 和 Y 浮點數嗎？

新遊戲的程式設計人員可能會懷疑為何`float`類型正與`int`或`double`。 原因是 32 位元值是最常見的低階轉譯程式碼中的定位。 Double 類型會佔用很少會需要位置物件的有效位數的 64 位元。 32 位元差異可能看起來是無意義，而許多現今的遊戲包括圖形需要處理數以萬計的位置值 （30 或每秒 60 次），每個畫面格。 必須移動剪下的記憶體數量，透過圖形一半管線可以對遊戲的效能有重大影響。

`int`資料類型可以是適當的單位的度量來定位，但它可以位於實體的方式加以限制。 例如，使用整數值更難實作的支援中的縮放或 3D 相機的遊戲平滑移動 (這由允許`SpriteBatch`)。

最後，我們會看到將我們的字元，在螢幕上的邏輯將會使用遊戲的計時值進行。 乘以速度多少時間超過指定的框架中的結果很少會為整數，因此我們必須使用`float`如`X`和`Y`。


## <a name="why-is-charactersheettexture-static"></a>為何 characterSheetTexture 靜態嗎？

`characterSheetTexture` `Texture2D`執行個體是 charactersheet.png 檔案的執行階段表示法。 換句話說，它包含每個像素的色彩值從來源擷取**charactersheet.png**檔案。 如果要建立兩個我們遊戲`CharacterEntity`執行個體，則每個需要存取資訊從 charactersheet.png。 在此情況下我們不想要產生效能成本，載入 charactersheet.png 兩次，也不會想要有儲存在 ram 中重複的紋理記憶體。 使用`static`欄位可讓我們共用相同`Texture2D`所有`CharacterEntity`執行個體。

使用`static`成員內容的執行階段表示是最簡單的解決方案，並不會處理時將移到另一個層級從較大的遊戲，例如卸載內容中所遇到的問題。 更多高階的方案，已超出本逐步解說的範圍，包括使用 MonoGame 的內容的管線，或建立自訂內容的管理系統。


## <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>為何 SpriteBatch 傳遞做為引數 Instead of 具現化的實體？

`Draw`方法實作會採用上述`SpriteBatch`引數，但相反地，`characterSheetTexture`來具現化`CharacterEntity`。

這是最有效率的轉譯時，可能因為相同`SpriteBatch`所有使用執行個體`Draw`呼叫，和當所有`Draw`呼叫所進行的單一組之間`Begin`和`End`呼叫。 當然，我們遊戲將只會包含單一實體執行個體，但更複雜的遊戲將受益於可讓多個實體以使用相同的模式`SpriteBatch`執行個體。


# <a name="adding-characterentity-to-the-game"></a>加入遊戲 CharacterEntity

既然我們已經加入我們`CharacterEntity`轉譯本身的程式碼中，我們可以取代中的程式碼`Game1.cs`才能使用這個新的實體執行個體。 若要這樣做我們將取代`Texture2D`欄位`CharacterEntity`欄位`Game1`:


```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

接下來，我們會將此實體中的具現化`Game1.Initialize`:


```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

我們也必須移除`Texture2D`建立從`LoadContent`，現在內部的處理，因為我們`CharacterEntity`。 `Game1.LoadContent` 應該看起來像這樣：


```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

它是值得一提，儘管其名稱`LoadContent`方法不是唯一的地方，其中可以載入內容 – 許多遊戲實作內容載入在其初始化方法中，或甚至做為內容，其更新程式碼可能不需要直到播放程式到達遊戲的特定點。

最後我們可以修改繪製方法如下：


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

當我們執行遊戲時，我們現在可以看到的字元。 X 和 Y 預設值為 0，因為對螢幕左上角放置字元：

![](part2-images/image4.png "由於 X 和 Y 預設值為 0，然後字元位於針對螢幕左上角")


# <a name="creating-the-animation-class"></a>建立動畫類別

目前我們`CharacterEntity`顯示完整**charactersheet.png**檔案。 在一個檔案中的多個映像的這種排列方式指*精靈表*。 通常，精靈會轉譯為一部分的精靈工作表。 我們將會修改`CharacterEntity`來呈現的這一部分**charactersheet.png**，這個部分會變更趨勢，以顯示走的動畫。

我們將建立`Animation`類別來控制的邏輯和 CharacterEntity 動畫的狀態。 動畫類別將會無法用於任何實體，不只是一般類別`CharacterEntity`動畫。 Ultimate`Animation`類別會提供`Rectangle`其中`CharacterEntity`用來繪製本身。 我們也將建立`AnimationFrame`會用來定義動畫的類別。


## <a name="defining-animationframe"></a>定義 AnimationFrame

`AnimationFrame` 不會包含任何與動畫相關的邏輯。 我們會用它只是用來儲存資料。 若要加入`AnimationFrame`類別，以滑鼠右鍵按一下或控制項按一下**WalkingGame**共用專案，然後選取**新增 > 新的檔案...**輸入名稱**AnimationFrame**按一下**新增** 按鈕。 我們會修改`AnimationFrame.cs`檔案，使其包含下列程式碼：


```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

`AnimationFrame`類別包含兩個資訊片段：

 - `SourceRectangle` -定義的區域`Texture2D`，會顯示由`AnimationFrame`。 這個值會以搭配 top 左 （0，0） 像素為單位。
 - `Duration` – 多久定義`AnimationFrame`中使用時，會顯示`Animation`。


## <a name="defining-animation"></a>定義動畫

`Animation`類別將包含`List<AnimationFrame>`以及切換哪一個畫面格目前是否顯示根據經過多少時間的邏輯。

若要加入`Animation`類別，以滑鼠右鍵按一下或控制項按一下**WalkingGame**共用專案，然後選取**新增 > 新的檔案...**.輸入名稱**動畫**按一下**新增** 按鈕。 我們會修改`Animation.cs`檔案，使其包含下列程式碼：


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;


            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

讓我們看看的某些細節，從`Animation`類別。


## <a name="frames-list"></a>框架清單

`frames`成員則是項目儲存的資料，我們的動畫。 動畫會具現化的程式碼會加入`AnimationFrame`執行個體來`frames`清單透過`AddFrame`方法。 提供的更完整的實作可能`public`方法或屬性來修改`frames`，但我們會限制要加入此逐步解說中的畫面格的功能。


## <a name="duration"></a>持續期間

持續時間傳回的總持續時間`Animation,`加上包含所有的持續時間取得`AnimationFrame`執行個體。 如果此值可能會快取`AnimationFrame`不可變的物件，但由於我們 AnimationFrame 實作為動畫加入之後可以變更的類別時，我們需要計算這個值，每當在屬性經過存取時。


## <a name="update"></a>更新

`Update`方法應該呼叫每個畫面格 （也就是整個遊戲會更新每個時間）。 其目的是要增加`timeIntoAnimation`成員，而用來傳回目前所顯示的畫面格。 中的邏輯`Update`防止`timeIntoAnimation`從曾大於整個動畫的持續時間。

因為我們將使用`Animation`類別來顯示走的動畫，我們會想要有這個動畫重複時它已經到期。 我們可以完成此作業檢查`timeIntoAnimation`大於`Duration`值。 如果是它會循環回到起始，並保留其餘部分，因而產生迴圈的動畫。


## <a name="obtaining-the-current-frames-rectangle"></a>取得目前畫面格的矩形

目的`Animation`類別是提供最終`Rectangle`可以用當繪製精靈。 目前`Animation`類別包含邏輯變更`timeIntoAnimation`成員，我們將使用來取得其`Rectangle`應該顯示。 我們將其作法是建立`CurrentRectangle`屬性`Animation`類別。 複製到這個屬性`Animation`類別：

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime;
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

# <a name="adding-the-first-animation-to-characterentity"></a>將第一次的動畫加入至 CharacterEntity

`CharacterEntity`將包含動畫查核時，接手，以及目前的參考`Animation`顯示。

首先，我們會將我們的第一個新增`Animation,`將用於測試的功能撰寫為止。 讓我們 CharacterEntity 類別中加入下列成員：


```csharp
Animation walkDown;
Animation currentAnimation;
```

 接下來，我們定義`walkDown`動畫。 若要執行此修改`CharacterEntity`建構函式，如下所示：

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```
如先前所述，我們需要呼叫`Animation.Update`的播放時間為基礎的動畫。 我們也要指派`currentAnimation`。 現在我們將指派的`currentAnimation`至`walkDown`，但我們來取代此程式碼稍後當我們實作我們移動邏輯。 我們會將新增`Update`方法`CharacterEntity`，如下所示：


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

現在，我們已經`currentAnimation`所指派，並更新，我們可以使用它來執行我們的繪圖。 我們會修改`CharacterEntity.Draw`，如下所示：


```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

快速入門的最後一個步驟`CharacterEntity`動畫是呼叫新加入`Update`方法從`Game1`。 修改`Game1.Update`，如下所示：


```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

現在`CharacterEntity`就可以播放其`walkDown`動畫：

![](part2-images/image5.gif "現在 CharacterEntity 播放 walkDown 動畫")


# <a name="adding-movement-to-the-character"></a>加入移動的字元

接下來，我們將持續加入移動我們使用觸控控制項的字元。 使用者碰觸螢幕，字元會移往的點在接觸到螢幕。 如果偵測不到任何工作時，該字元將會突顯就地。


## <a name="defining-getdesiredvelocityfrominput"></a>Defining GetDesiredVelocityFromInput

我們將使用 MonoGame 的`TouchPanel`類別，可提供的觸控式螢幕的目前狀態的相關資訊。 讓我們加入方法，這個方法會將檢查`TouchPanel`以及傳回我們的字元所需的速度：


```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

`TouchPanel.GetState`方法會傳回`TouchCollection`內含使用者碰觸螢幕的相關資訊。 如果使用者未碰觸螢幕，然後在`TouchCollection`會是空的在此情況下，我們不應該將字元。

如果使用者觸控螢幕，我們將換句話說移動第一個觸控優先字元、`TouchLocation`位於索引 0。 一開始，我們會設定為等於字元的位置與第一個觸控位置之間的差異所需的速度：


```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

以下是一些數學運算，將會保留相同的速度移動的字元。 為了說明這為何如此重要，讓我們假設使用者碰觸螢幕 500 像素遠離字元所在位置。 第一列 where`desiredVelocity.X`是設定所要指派的值為 500。 不過，如果使用者已觸控螢幕的字元，從 100 單位的距離，`desiredVelocity.X `會設定為 100。 結果會是該字元的移動速度會回應如何遠觸控點會從字元。 由於我們想要移動速度的字元，我們必須修改 desiredVelocity。

`if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)`陳述式會檢查速度為非零-也就是說，是否它檢查以確定使用者未碰觸的字元目前位置為相同的位置。 如果不是，則我們需要設定字元的速度遠是一定的不管如何觸控。 我們完成正規化的速度向量而造成它夠長度為 1。 1 表示的字元，會在每秒的 1 個像素移動速度向量。 我們會加速此值乘以 200 的所需的速度。


## <a name="applying-velocity-to-position"></a>套用至位置的速度

從傳回的速度`GetDesiredVelocityFromInput`必須套用至字元`X`和`Y`值在執行階段沒有任何變更。 我們會修改`Update`方法，如下所示：


```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

我們已在此實作會呼叫*以時間為基礎*移動 (與*框架基礎*移動)。 以時間為基礎的移動乘以速度值 (在本例中的值儲存在`velocity`變數) 儲存在上次更新後經過多少時間由`gameTime.ElapsedGameTime.TotalSeconds`。 如果遊戲執行較少每秒畫面格數，畫面格之間經過的時間會-最終結果是使用以時間為基礎的移動物件一定會移動不論畫面播放速率相同的速度。

如果我們現在就執行我們的遊戲，我們會看到的字元是移動朝向觸控位置：

![](part2-images/image6.gif "字元體制觸控位置")


# <a name="matching-movement-and-animation"></a>比對的動作和動畫

一旦我們字元移動和播放單一的動畫，我們可以定義我們動畫的其餘部分，然後使用它們來反映字元移動。 當完成之後，我們必須八個動畫總計：

 - 向上、 向下、 左和右動畫
 - 永久性的動畫仍然和朝上下、 左、 右


## <a name="defining-the-rest-of-the-animations"></a>定義動畫的其餘部分

我們會先將新增`Animation`執行個體來`CharacterEntity`類別所有在相同的位置，其中加入我們動畫`walkDown`。 一旦我們這樣做，請`CharacterEntity`會有下列`Animation`成員：


```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

現在我們會定義在動畫`CharacterEntity`建構函式，如下所示：


```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

我們應該注意上述程式碼已加入`CharacterEntity`建構函式，讓這個逐步解說短。 遊戲通常會分隔成自己的類別的字元組的定義或資料格式，例如 XML 或 JSON 從載入這項資訊。

接下來，我們將調整為使用根據方向移動的字元，或根據最後一個動畫的動畫，如果字元就已停止的邏輯。 若要這樣做，我們會修改`Update`方法：


```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;


    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

若要切換動畫的程式碼分成兩個區塊。 第一個檢查速度是否不等於`Vector2.Zero`– 這告訴我們的字元是否移動。 如果字元是移動，則我們來看看`velocity.X`和`velocity.Y`值來判斷走播放動畫。

如果未移動的字元，則我們想要設定的字元`currentAnimation`至常設動畫 – 但我們只這麼`currentAnimation`是查核動畫，或如果沒有設定動畫。 如果`currentAnimation`不是其中一個的四個走的動畫，則字元已經長久以來所以我們不需要變更`currentAnimation`。

這個程式碼的結果是字元會正確地以動畫顯示，查核，當，然後面臨停止時，它查核的最後一個方向：

![](part2-images/image7.gif "此程式碼的結果是字元會正確地以動畫顯示，查核，當，然後面臨停止時，它查核的最後一個方向")


# <a name="summary"></a>總結

本逐步解說示範了如何使用 MonoGame 建立跨平台遊戲的小、 移動物件、 輸入的偵測及動畫。 其中涵蓋建立一般用途的動畫類別。 它也示範了如何建立用來組織程式碼邏輯的字元實體。

## <a name="related-links"></a>相關連結

- [CharacterSheet 影像資源 （範例）](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [查核遊戲完成 （範例）](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
