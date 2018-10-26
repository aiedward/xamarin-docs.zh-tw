---
title: 第 2 部分-實作 WalkingGame
description: 本逐步解說示範如何新增遊戲邏輯和內容，以空白 MonoGame 專案來建立與移動動畫 sprite 的示範觸控輸入。
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: c761f9f11e8053dcd0960129a28251ed6acf473c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120270"
---
# <a name="part-2--implementing-the-walkinggame"></a>第 2 部分-實作 WalkingGame

_本逐步解說示範如何新增遊戲邏輯和內容，以空白 MonoGame 專案來建立與移動動畫 sprite 的示範觸控輸入。_

本逐步解說的先前部分中，示範了如何建立空的 MonoGame 專案。 藉由簡單的遊戲示範，我們將建置在這些先前的部分。 本文包含下列章節：

- 解壓縮我們遊戲的內容
- MonoGame 類別概觀
- 轉譯我們第一次的 Sprite
- 建立 CharacterEntity
- 加入遊戲 CharacterEntity
- 建立動畫類別
- 將第一個動畫加入至 CharacterEntity
- 加入移動的字元
- 比對的移動和動畫


## <a name="unzipping-our-game-content"></a>解壓縮我們遊戲的內容

我們開始撰寫程式碼之前，我們會想要解壓縮我們遊戲*內容*。 遊戲開發人員通常會使用這個詞彙*內容*參考通常由 visual 演出者、 遊戲的設計工具或音訊的設計工具建立非程式碼檔案。 常見的內容類型包含用來顯示視覺效果、 播放音效，或控制人工智慧 (AI) 行為的檔案。 從遊戲開發非程式設計人員通常會建立小組的檢視方塊的內容。

此處所使用的內容可以找到[github 上](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)。 我們需要這些檔案下載到我們將在此逐步解說稍後存取的位置。

## <a name="monogame-class-overview"></a>MonoGame 類別概觀

首先，我們將探討使用基本的呈現方式的 MonoGame 類別：

- `SpriteBatch` – 用來描繪至螢幕的 2D 圖形。 *Sprite*是用來在螢幕上顯示影像的 2D 視覺項目。 `SpriteBatch`物件可以繪製單一 sprite 之間一次其`Begin`並`End`方法或多個 sprite 可以群組在一起，或*批次*。
- `Texture2D` – 表示在執行階段的映像物件。 `Texture2D` 雖然它們也可以建立以動態方式在執行階段執行個體通常會建立檔案格式，例如.png 或.bmp。 `Texture2D` 以呈現時，執行個體用`SpriteBatch`執行個體。
- `Vector2` – 表示以 2D 的通常用來定位視覺物件的座標系統中的位置。 也包含 MonoGame`Vector3`和`Vector4`但我們只會使用`Vector2`在本逐步解說。
- `Rectangle` – 四邊區域位置、 寬度和高度。 我們將使用此定義的哪部分我們`Texture2D`當我們開始使用動畫来呈現。

我們也應該注意，MonoGame 就不會維護 microsoft-儘管其命名空間。 `Microsoft.Xna`命名空間用於 MonoGame 以輕鬆地將現有的 XNA 專案移轉至 MonoGame。

## <a name="rendering-our-first-sprite"></a>轉譯我們第一次的 Sprite

接下來我們將會繪製單一 sprite 到螢幕上以示範如何在 MonoGame 執行 2D 轉譯。

### <a name="creating-a-texture2d"></a>建立 Texture2D

我們需要建立`Texture2D`轉譯我們 sprite 時使用的執行個體。 最終包含在名為資料夾中所有的遊戲內容**內容，** 平台專屬專案中。 共用的 MonoGame 專案不能包含的內容，因為內容必須使用平台特定的建置動作。 CocosSharp 開發人員會發現 [Content] 資料夾的熟悉的概念，它們位於相同的位置，CocosSharp 和 MonoGame 專案中。 在 iOS 專案中，並在 Android 專案中的 [Assets] 資料夾內，就可以找到 [Content] 資料夾。

若要加入我們的遊戲內容，以滑鼠右鍵按一下**內容**資料夾，然後選取**新增 > 新增檔案...** 瀏覽至解壓縮 content.zip 檔案的位置，然後選取**charactersheet.png**檔案。 如果系統詢問有關如何將檔案加入資料夾，最好還是選擇**複製**選項：

![](part2-images/image1.png "如果系統詢問有關如何將檔案加入資料夾，選取 [複製] 選項")

[Content] 資料夾現在包含 charactersheet.png 檔案：

![](part2-images/image2.png "[Content] 資料夾現在包含 charactersheet.png 檔案")

接下來，我們要在其中加入程式碼載入 charactersheet.png 檔案，並建立`Texture2D`。 若要開啟`Game1.cs`檔案，並將下列欄位新增至 Game1.cs 類別：

```csharp
Texture2D characterSheetTexture;
```

接下來，我們將建立`characterSheetTexture`在`LoadContent`方法。 任何修改之前`LoadContent`方法看起來像這樣：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

我們應該注意，預設的專案已具現化`spriteBatch`為我們的執行個體。 我們將使用此更新版本，但我們不會新增任何其他的程式碼，以準備`spriteBatch`供使用。 相反地，我們`spriteSheetTexture`需要初始化，因此我們將會初始化之後`spriteBatch`建立：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

既然我們已經`SpriteBatch`執行個體和`Texture2D`我們可以加入我們的轉譯程式碼，以執行個體`Game1.Draw`方法：

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

執行遊戲，現在會顯示單一顯示從 charactersheet.png 建立材質的 sprite:

![](part2-images/image3.png "執行遊戲，現在會顯示單一原件，顯示從 charactersheet.png 建立的材質")

## <a name="creating-the-characterentity"></a>建立 CharacterEntity

到目前為止，我們已新增程式碼，以呈現單一的 sprite 螢幕;不過，如果我們開發完整的遊戲，而不需要建立任何其他類別，我們會遇到程式碼組織的問題。

### <a name="what-is-an-entity"></a>什麼是實體？

組織遊戲的程式碼的常見模式是要建立新的類別，針對每個遊戲*實體*型別。 遊戲開發中的實體是物件可包含的一些 （並非全部都是必要） 的下列特性：

- 視覺化的項目，例如 sprite、 文字或 3D 模型
- 物理或每個畫面格 」 行為，例如偵查集路徑或播放程式字元回應輸入單位
- 您可以建立及終結以動態方式，例如電源總出現和所收集的播放程式

實體組織系統可能很複雜，而且許多的遊戲引擎提供可協助管理實體的類別。 我們將會實作非常簡單的實體系統，因此值得注意的是完整的遊戲通常需要更多的組織，開發人員的組件上。


### <a name="defining-the-characterentity"></a>定義 CharacterEntity

我們的實體，我們將稱之為`CharacterEntity`，將具有下列特性：

- 載入它自己的能力 `Texture2D`
- 能夠呈現其本身，包括其中包含呼叫的方法，來更新走的動畫
- `X` 與 Y 屬性，以控制字元的位置。
- 特別是更新本身，以讀取值觸控螢幕，並適當地調整位置的能力。

若要新增`CharacterEntity`我們的遊戲、 以滑鼠右鍵按一下或 Control + 按一下**WalkingGame**專案，然後選取**新增 > 新的檔案...**.選取 **空類別**選項，然後將新檔案命名**CharacterEntity**，然後按一下**新增**。

首先，我們將新增的能力`CharacterEntity`載入`Texture2D`以及繪製本身。 我們將修改新增`CharacterEntity.cs`檔案，如下所示：

```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;


namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

上述程式碼會將定位、 轉譯，以及載入至內容的責任`CharacterEntity`。 讓我們花一點時間點出上述程式碼中所採取的一些考量。

### <a name="why-are-x-and-y-floats"></a>為何 X 和 Y 的浮點數？

是遊戲程式設計的新手開發人員可能會納悶為什麼`float`型別使用相對於`int`或`double`。 原因是 32 位元值是最常見的低階轉譯程式碼中的定位。 Double 型別所佔用的精確度，很少需要定位物件的 64 位元。 雖然 32 位元差異似乎微不足道，許多現今的遊戲包含需要處理數以萬計的位置值的圖形 (30 個或每秒 60 次），每個畫面格。 剪下的記憶體數量，必須將透過圖形一半的管線可以對遊戲的效能有重大影響。

`int`資料型別可以是適當的單位的度量定位，但它可限制對位於實體的方式。 例如，使用整數值就更難實作的奧運賽縮放或 3D 攝影機所支援的平滑移動 (藉由允許`SpriteBatch`)。

最後，我們會看到邏輯會將我們在螢幕上的字元將會使用該遊戲的計時值進行。 乘以指定的範圍內已經過多少時間的速度的結果很少會為整數，因此我們需要使用`float`for`X`和`Y`。

### <a name="why-is-charactersheettexture-static"></a>為何 characterSheetTexture 靜態？

`characterSheetTexture` `Texture2D`執行個體是 charactersheet.png 檔案的執行階段表示法。 換句話說，它包含每個像素的色彩值從來源擷取**charactersheet.png**檔案。 如果要建立兩個我們遊戲`CharacterEntity`執行個體，則每個需要存取資訊從 charactersheet.png。 在此情況下，我們不想產生兩次，載入 charactersheet.png 的效能成本，也會想要有重複的紋理記憶體是儲存在 ram 中。 使用`static`欄位可讓我們能夠共用相同`Texture2D`所有`CharacterEntity`執行個體。

使用`static`內容的執行階段表示法的成員是一個簡單的解決方案，並不會處理從一個層級移至另一個時，較大的遊戲，例如卸載內容中遇到的問題。 較複雜的解決方案，已超出本逐步解說的範圍，包括使用 MonoGame 的內容管線，或建立自訂的內容管理系統。

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>為什麼是 SpriteBatch 傳遞做為引數的 instead Of 來具現化實體？

`Draw`方法實作會採用上述`SpriteBatch`引數，但相較之下，`characterSheetTexture`具現化`CharacterEntity`。

這是因為最有效率的轉譯時可能發生相同`SpriteBatch`執行個體用於所有`Draw`呼叫中，和當所有`Draw`所進行的呼叫之間的單一組`Begin`和`End`呼叫。 當然，我們的遊戲只會包含單一實體執行個體，但更複雜的遊戲會受益於允許多個實體使用相同的模式`SpriteBatch`執行個體。


## <a name="adding-characterentity-to-the-game"></a>加入遊戲 CharacterEntity

現在，我們新增了我們`CharacterEntity`轉譯本身的程式碼，我們可以取代中的程式碼`Game1.cs`使用這個新的實體的執行個體。 若要這樣做，我們會將取代`Texture2D`欄位`CharacterEntity`欄位中`Game1`:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

接下來，我們要在其中加入此實體中的具現化`Game1.Initialize`:

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

我們也要移除`Texture2D`建立從`LoadContent`因為，現在會處理內我們`CharacterEntity`。 `Game1.LoadContent` 應該看起來像這樣：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

值得注意的是，儘管其名稱如此`LoadContent`方法不是唯一的地方，可以載入內容 – 許多遊戲實作內容載入在其 Initialize 方法中，或甚至是在做為內容，其更新程式碼可能不需要直到到達播放程式遊戲的特定點。

最後我們可以修改 Draw 方法如下：


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

如果我們執行遊戲，我們現在會看到的字元。 X 和 Y 預設值為 0，因為對螢幕的左上角放置字元：

![](part2-images/image4.png "因為 X 和 Y 預設值為 0，則字元位於對螢幕的左上角")

## <a name="creating-the-animation-class"></a>建立動畫類別

目前我們`CharacterEntity`會顯示完整**charactersheet.png**檔案。 在一個檔案中的多個映像的這種排列方式指*sprite 表*。 一般而言，sprite 會呈現 sprite 工作表的一小部分。 我們將修改`CharacterEntity`呈現的這一部分**charactersheet.png**，與這個部分會隨著顯示走的動畫時間而變更。

我們將建立`Animation`類別來控制的邏輯與 CharacterEntity 動畫的狀態。 動畫類別將會無法使用的任何實體中，不只是一般類別`CharacterEntity`動畫。 Ultimate`Animation`類別會提供`Rectangle`其中`CharacterEntity`繪製本身時，會使用。 我們也會建立`AnimationFrame`類別將用來定義動畫。


### <a name="defining-animationframe"></a>定義 AnimationFrame

`AnimationFrame` 將不會包含與動畫相關的任何邏輯。 我們將使用它只是用來儲存資料。 若要新增`AnimationFrame`類別，以滑鼠右鍵按一下或 Control + 按一下**WalkingGame**共用專案，然後選取**新增 > 新增檔案...** 輸入名稱**AnimationFrame**然後按一下**新增** 按鈕。 我們要修改`AnimationFrame.cs`檔案，使其包含下列程式碼：


```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

`AnimationFrame`類別包含兩項資訊：

- `SourceRectangle` – 定義的區域`Texture2D`藉由將顯示哪些`AnimationFrame`。 這個值被以像素為單位，其中最左邊是 （0，0）。
- `Duration` – 多久定義`AnimationFrame`中使用時，會顯示`Animation`。

### <a name="defining-animation"></a>定義動畫

`Animation`類別將包含`List<AnimationFrame>`以及切換哪一個畫面格目前是否顯示根據經過多少時間的邏輯。

若要新增`Animation`類別，以滑鼠右鍵按一下或 Control + 按一下**WalkingGame**共用專案，然後選取**新增 > 新增檔案...**.輸入名稱**動畫**然後按一下**新增** 按鈕。 我們要修改`Animation.cs`檔案使其包含下列程式碼：


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;


            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

讓我們看看一些詳細資料，從`Animation`類別。

### <a name="frames-list"></a>畫面格清單

`frames`成員可讓您為儲存我們的動畫資料的功能。 具現化動畫的程式碼會新增`AnimationFrame`執行個體`frames`列出透過`AddFrame`方法。 更完整的實作可能會提供`public`方法或屬性來修改`frames`，但我們會限制功能，以加入此逐步解說中的畫面格。

### <a name="duration"></a>持續期間

持續時間傳回的總持續時間`Animation,`從其中取得加上包含所有的持續時間`AnimationFrame`執行個體。 如果此值可能會快取`AnimationFrame`不可變的物件，但由於我們 AnimationFrame 實作為之後加入至動畫可以變更的類別時，我們需要計算這個值，每當在屬性經過存取時。

### <a name="update"></a>更新

`Update`方法應該呼叫每個畫面格 （也就是整個遊戲會更新每個時間）。 其目的是要增加`timeIntoAnimation`成員用來傳回目前所顯示的畫面格。 中的邏輯`Update`可防止`timeIntoAnimation`防止曾大於整個動畫的持續時間。

因為我們將使用`Animation`類別，以顯示走的動畫，則我們想要有這個動畫重複時就已到達結尾。 我們可以檢查是否可以完成`timeIntoAnimation`大於`Duration`值。 如果是它會循環回到起始，並保留其餘部分，導致迴圈的動畫。

### <a name="obtaining-the-current-frames-rectangle"></a>取得目前畫面格的矩形

目的`Animation`類別是提供最終`Rectangle`可用時繪製 sprite。 目前`Animation`類別包含變更的邏輯`timeIntoAnimation`成員，我們將使用來取得其`Rectangle`應該會顯示。 將執行此作業，建立`CurrentRectangle`屬性中的`Animation`類別。 複製到這個屬性`Animation`類別：

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime;
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

## <a name="adding-the-first-animation-to-characterentity"></a>將第一個動畫加入至 CharacterEntity

`CharacterEntity`會包含動畫的逐一查看的地位，以及目前的參考`Animation`顯示。

首先，我們將在這裡新增我們的第一個`Animation,`，我們會用來寫入到目前為止，執行功能測試。 讓我們加入 CharacterEntity 類別的下列成員：

```csharp
Animation walkDown;
Animation currentAnimation;
```

接下來，讓我們定義`walkDown`動畫。 若要執行此修改`CharacterEntity`建構函式，如下所示：

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

如先前所述，我們必須呼叫`Animation.Update`播放時間為基礎的動畫。 我們也需要指派`currentAnimation`。 如接下來我們將在其中指派`currentAnimation`至`walkDown`，但我們將會取代此程式碼稍後當我們實作我們移動邏輯。 我們會新增`Update`方法，以`CharacterEntity`，如下所示：


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

既然我們已經`currentAnimation`所指派，並且更新，我們可以使用它來執行我們的繪圖。 我們要修改`CharacterEntity.Draw`，如下所示：

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

若要取得的最後一個步驟`CharacterEntity`動畫是呼叫新加入`Update`方法從`Game1`。 修改`Game1.Update`，如下所示：

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

現在`CharacterEntity`就會播放其`walkDown`動畫：

![](part2-images/image5.gif "現在 CharacterEntity 會播放它 walkDown 的動畫")

## <a name="adding-movement-to-the-character"></a>加入移動的字元

接下來，我們將持續加入移動到我們使用觸控式控制項的字元。 當使用者觸控螢幕時，字元會朝向也在接觸到螢幕的點。 如果偵測到任何修飾，則該字元將展露頭角就地。

### <a name="defining-getdesiredvelocityfrominput"></a>定義 GetDesiredVelocityFromInput

我們將使用 MonoGame 的`TouchPanel`類別，可提供觸控式螢幕的目前狀態的相關資訊。 讓我們將新增方法，這個方法會將檢查`TouchPanel`並傳回我們的字元所需的速度：


```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

`TouchPanel.GetState`方法會傳回`TouchCollection`內含使用者會觸碰螢幕的相關資訊。 如果使用者不會觸碰螢幕，則`TouchCollection`會是空的在此情況下，我們不應該移動的字元。

如果使用者觸控螢幕，我們會換句話說將移入第一個觸控，字元，則`TouchLocation`位於索引 0。 一開始我們會設定為等於字元的位置與第一個觸控位置之間的差異所需的速度：

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

以下是一些數學運算，這會讓移動的字元繼續相同的速度。 為了協助說明為什麼這是很重要，讓我們假設使用者會觸碰螢幕 500 像素遠離字元所在位置。 第一行的位置`desiredVelocity.X`是集合會將指派的值為 500。 不過，如果使用者觸控螢幕的字元，從 100 單位的距離，`desiredVelocity.X `會設定為 100。 結果會是該字元的移動速度會回應如何遠處觸控點是從字元。 由於我們想要一律以相同的速度移動的字元時，我們必須修改 desiredVelocity。

`if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)`陳述式會檢查速度為非零 – 也就是說，是否它會檢查確定使用者未碰觸的字元目前位置為相同的位置。 如果不是，然後我們需要設定字元的速度很遠的地方是一定的不管如何觸控。 我們完成正規化速度向量而造成它的長度為 1。 1 表示該字元會移動在 1 個像素，每秒的速度向量。 我們將會加速處理的值乘以 200 的所需的速度。


### <a name="applying-velocity-to-position"></a>套用至位置的速度

從傳回的速度`GetDesiredVelocityFromInput`必須在套用到的字元`X`和`Y`在執行階段沒有任何變更的值。 我們要修改`Update`方法，如下所示：

```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

我們已在此實作會呼叫*時間為基礎*移動 (相對於*框架為基礎*移動)。 以時間為基礎的移動乘以速度值 (在本例中的值儲存在`velocity`變數) 所儲存在上次更新後經過多少時間`gameTime.ElapsedGameTime.TotalSeconds`。 如果遊戲在較少每秒畫面格數執行時，畫面格之間經過的時間會增加，最終結果是使用以時間為基礎的移動的物件一律會移動不論畫面播放速率相同的速度。

如果我們現在執行我們的遊戲，我們會看到的字元是採用觸控位置：

![](part2-images/image6.gif "字元採用觸控位置")

## <a name="matching-movement-and-animation"></a>比對的移動和動畫

一旦我們擁有我們字元移動及播放單一動畫時，我們可以定義我們動畫的其餘部分，然後使用它們來反映移動的字元。 當完成我們都會在八個動畫總計：

- 向上、 向下、 左和右的動畫
- 永久性的動畫仍然和面向上、 下、 左和右

### <a name="defining-the-rest-of-the-animations"></a>定義動畫的其餘部分

我們會先新增`Animation`執行個體`CharacterEntity`所有相同的位置，其中我們加入我們動畫類別`walkDown`。 一旦我們這樣做，請`CharacterEntity`會有下列`Animation`成員：

```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

現在我們會定義在動畫`CharacterEntity`建構函式，如下所示：

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

我們應該注意上述程式碼已加入至`CharacterEntity`將短本逐步解說的建構函式。 遊戲通常會分隔成自己的類別的字元動畫的定義或載入此資訊，例如 XML 或 JSON 資料格式。

接下來，我們會調整邏輯，以便使用根據方向移動的字元，或根據最後一個動畫的動畫，如果該字元就已停止。 若要這樣做，我們要修改`Update`方法：


```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;


    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

若要切換 動畫的程式碼會分成兩個區塊。 第一個檢查的速度是否不等於`Vector2.Zero`– 這會告訴我們是否移動的字元。 如果字元是移動，那麼我們來看看`velocity.X`和`velocity.Y`值來判斷哪些走動畫播放。

如果未移動的字元，則我們想要設定的字元`currentAnimation`常設動畫-但我們只這麼`currentAnimation`會查核動畫，或如果尚未設定動畫。 如果`currentAnimation`不是其中的四個走動畫，因此我們不需要變更，將已經釐字元則`currentAnimation`。

此程式碼的結果是字元會正確建立動畫時逐一查看，並再面對它走它停止時的最後一個方向：

![](part2-images/image7.gif "此程式碼的結果是字元會正確建立動畫時逐一查看，並再面對它走它停止時的最後一個方向")

## <a name="summary"></a>總結

本逐步解說示範了如何使用 MonoGame 以建立跨平台遊戲的 sprite、 移動物件、 輸入的偵測、 及動畫。 它說明如何建立一般用途的動畫類別。 它也會示範如何建立用來組織程式碼邏輯的字元實體。

## <a name="related-links"></a>相關連結

- [CharacterSheet 影像資源 （範例）](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [逐一查看遊戲的完整 （範例）](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
