---
title: 第2部分–執行 WalkingGame
description: 本逐步解說示範如何將遊戲邏輯和內容新增至空白的 MonoGame 專案，以建立使用觸控輸入移動動畫 sprite 的示範。
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 2c290ac7d66147342087342bda5e5a19b4e6e6f7
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70763626"
---
# <a name="part-2--implementing-the-walkinggame"></a>第2部分–執行 WalkingGame

_本逐步解說示範如何將遊戲邏輯和內容新增至空白的 MonoGame 專案，以建立使用觸控輸入移動動畫 sprite 的示範。_

本逐步解說的先前部分示範了如何建立空的 MonoGame 專案。 我們將藉由製作簡單的遊戲示範，來建立這些先前的元件。 本文包含下列章節：

- 解壓縮遊戲內容
- MonoGame 類別總覽
- 呈現我們的第一個 Sprite
- 建立 CharacterEntity
- 將 CharacterEntity 新增至遊戲
- 建立動畫類別
- 將第一個動畫新增至 CharacterEntity
- 將移動加入至字元
- 符合的移動和動畫

## <a name="unzipping-our-game-content"></a>解壓縮遊戲內容

在我們開始撰寫程式碼之前，我們會想要將遊戲*內容*解壓縮。 遊戲開發人員通常會使用「*內容*」一詞來指稱非程式碼檔案，這些檔案通常是由 visual 演出者、遊戲設計人員或音訊設計師所建立。 常見的內容類型包括用來顯示視覺效果、播放音效或控制人工智慧（AI）行為的檔案。 從遊戲開發小組的觀點來看，通常是由非程式設計人員所建立。

這裡所使用的內容可以[在 GitHub 上](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)找到。 我們需要將這些檔案下載到稍後將在本逐步解說中存取的位置。

## <a name="monogame-class-overview"></a>MonoGame 類別總覽

針對初學者，我們將探索用於基本轉譯的 MonoGame 類別：

- `SpriteBatch`–用來將2D 圖形繪製到螢幕上。 *Sprite*是2d 視覺效果元素，用來在螢幕上顯示影像。 物件可以在其`Begin`和`End`方法之間一次繪製單一 sprite，或多個 sprite 可以群組在一起或*批次處理*。 `SpriteBatch`
- `Texture2D`–表示在執行時間的影像物件。 `Texture2D`實例通常是從檔案格式（例如 .png 或 .bmp）建立的，不過，它們也可以在執行時間動態建立。 `Texture2D`當使用`SpriteBatch`實例轉譯時，會使用實例。
- `Vector2`–代表2D 座標系統中的位置，通常用來定位視覺物件。 MonoGame 也包含`Vector3`和`Vector4` ，但我們只會`Vector2`在此逐步解說中使用。
- `Rectangle`–具有位置、寬度和高度的四側區域。 我們會使用此來定義我們開始使用動畫時`Texture2D`要呈現的部分。

我們也應該注意到，MonoGame 不是由 Microsoft 維護，而是使用其命名空間。 `Microsoft.Xna`命名空間會在 MonoGame 中使用，讓您更輕鬆地將現有的專案名稱遷移至 MonoGame。

## <a name="rendering-our-first-sprite"></a>呈現我們的第一個 Sprite

接下來，我們會在畫面上繪製一個單一 sprite，以示範如何在 MonoGame 中執行2D 轉譯。

### <a name="creating-a-texture2d"></a>建立 Texture2D

我們必須建立要在`Texture2D`轉譯 sprite 時使用的實例。 所有遊戲內容最終都會包含在平臺特定專案中名為**content**的資料夾中。 MonoGame 共用的專案不能包含內容，因為內容必須使用平臺特定的組建動作。 [內容] 資料夾可在 iOS 專案中找到，並在 Android 專案的 [資產] 資料夾內。

若要新增遊戲內容，請以滑鼠右鍵按一下 **內容** 資料夾，然後選取 **新增 > 新增檔案 ...** 流覽至已解壓縮內容 .zip 檔案的位置，然後選取  **charactersheet**  檔案。 如果系統詢問您如何將檔案新增至資料夾，我們應該選取 [**複製**] 選項：

![如果系統詢問您如何將檔案新增至資料夾，請選取 [複製] 選項](part2-images/image1.png)

Content 資料夾現在包含 charactersheet .png 檔案：

![Content 資料夾現在包含 charactersheet .png 檔案](part2-images/image2.png)

接下來，我們將新增程式碼以載入 charactersheet 檔案，並建立`Texture2D`。 若要這麼做， `Game1.cs`請開啟檔案，並將下欄欄位新增至 Game1.cs 類別：

```csharp
Texture2D characterSheetTexture;
```

接下來，我們將`characterSheetTexture` `LoadContent`在方法中建立。 在任何修改`LoadContent`方法看起來像這樣：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

我們應該要注意的是，預設專案已經`spriteBatch`為我們具現化實例。 我們稍後會用到，但我們不會新增任何額外的程式碼來`spriteBatch`準備要使用的。 另一方面，我們`spriteSheetTexture`的需要初始化，因此我們會在建立`spriteBatch`之後將它初始化：

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

既然我們已經有`SpriteBatch`實例`Texture2D`和實例，我們可以將轉譯程式碼加入至`Game1.Draw`方法：

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

執行遊戲現在會顯示一個顯示從 charactersheet 建立之材質的單一 sprite：

![執行遊戲現在會顯示一個顯示從 charactersheet 建立之材質的單一 sprite](part2-images/image3.png)

## <a name="creating-the-characterentity"></a>建立 CharacterEntity

到目前為止，我們已新增程式碼，以在螢幕上呈現單一 sprite;不過，如果我們在不建立任何其他類別的情況下開發完整的遊戲，我們就會遇到程式碼組織的問題。

### <a name="what-is-an-entity"></a>什麼是實體？

組織遊戲程式碼的常見模式是為每個遊戲*實體*類型建立新的類別。 遊戲開發中的實體是一個物件，它可以包含下列部分特性（並非全部都是必要的）：

- 視覺效果元素，例如 sprite、文字或3D 模型
- 物理或每個畫面格行為（例如單位） patrolling 設定路徑或播放機字元，以回應輸入
- 可以動態建立和終結，例如播放程式出現並收集的電源

實體組織系統可能很複雜，而且許多遊戲引擎都提供類別來協助管理實體。 我們將會實行非常簡單的實體系統，因此值得注意的是，完整的遊戲通常在開發人員的部分需要更多的組織。

### <a name="defining-the-characterentity"></a>定義 CharacterEntity

我們稱之為`CharacterEntity`的實體將具有下列特性：

- 載入它自己的能力`Texture2D`
- 呈現本身的能力，包括包含呼叫方法來更新流覽動畫
- `X`和 Y 屬性，以控制字元的位置。
- 能夠自行更新–具體而言，就是從觸控式螢幕讀取值，並適當地調整位置。

若要將`CharacterEntity`加入至遊戲，請以滑鼠右鍵按一下**WalkingGame**專案，然後選取 [**加入 > 新增**檔案 ...]。選取 [**空白類別**] 選項，並將新檔案命名為**CharacterEntity**，然後按一下 [**新增**]。

首先，我們會新增`CharacterEntity` `Texture2D`載入的功能，以及自行繪製。 我們將修改新增`CharacterEntity.cs`的檔案，如下所示：

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

上述程式碼會將定位、轉譯和載入內容的責任新增至`CharacterEntity`。 讓我們花點時間指出上述程式碼中所採取的一些考慮。

### <a name="why-are-x-and-y-floats"></a>為什麼 X 和 Y 會浮動？

不熟悉遊戲程式設計的`float`開發人員可能會想知道為什麼要使用類型， `int`而不`double`是或。 原因是，32位值最常用於低層級呈現程式碼中的位置。 Double 類型會佔用64位的有效位數，這在定位物件時很少需要。 雖然32位的差異看似不重要，但許多新式遊戲都包含圖形，需要處理數十個位置值，每個畫面格（每秒30或60次）。 將必須透過圖形管線移動的記憶體數量減少一半，可能會對遊戲的效能造成重大影響。

`int`資料類型可以是用於定位的適當測量單位，但可能會對實體的定位方式有所限制。 例如，使用整數值可讓您更難以針對支援放大或3D 攝影機的遊戲（允許`SpriteBatch`）執行順暢的移動。

最後，我們會看到沿著螢幕移動字元的邏輯會使用遊戲的時間值來執行。 將速度乘以指定框架中所傳遞的時間，會有很少的結果，因此我們必須針對`float` `X`和`Y`使用。

### <a name="why-is-charactersheettexture-static"></a>為什麼 characterSheetTexture 靜態？

`characterSheetTexture` 實例是charactersheet的`Texture2D`運行時程表示法。 換句話說，它包含從來源**charactersheet .png**檔案中解壓縮之每個圖元的色彩值。 如果我們的遊戲要建立兩`CharacterEntity`個實例，則每個實例都需要從 charactersheet 存取訊號。 在此情況下，我們不想要產生 charactersheet 的效能成本，而是要將重複的材質記憶體儲存在 ram 中。 使用欄位可讓我們在所有`CharacterEntity`實例之間`Texture2D`共用相同的。 `static`

針對`static`內容的運行時程表示法使用成員是一個簡單的解決方案，而且不會解決在較大的遊戲中遇到的問題，例如，從一個層級移至另一個時，卸載內容。 更複雜的解決方案不在本逐步解說的討論範圍內，包括使用 MonoGame 的內容管線或建立自訂的內容管理系統。

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>為什麼 SpriteBatch 會當做引數傳遞，而不是由實體具現化？

如上`Draw`所示的方法`SpriteBatch`會採用引數， `characterSheetTexture`但相反地，會由`CharacterEntity`具現化。

這是因為當相同`SpriteBatch`的實例用於所有`Draw`呼叫，以及在單一集合`Begin`和`End`呼叫之間進行所有`Draw`呼叫時，最有效率的呈現方式。 當然，我們的遊戲只會包含單一實體實例，但更複雜的遊戲將受益于允許多個實體使用相同`SpriteBatch`實例的模式。

## <a name="adding-characterentity-to-the-game"></a>將 CharacterEntity 新增至遊戲

既然我們已加入我們`CharacterEntity`的程式碼來呈現本身，我們可以取代中`Game1.cs`的程式碼，以使用這個新實體的實例。 若要這樣做，我們會`Texture2D`將欄位`CharacterEntity`取代為中`Game1`的欄位：

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

接下來，我們會在中`Game1.Initialize`新增此實體的具現化：

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

我們也需要從`Texture2D` `LoadContent`移除建立，因為`CharacterEntity`現在已在中處理。 `Game1.LoadContent`看起來應該像這樣：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

值得注意的是，雖然它的名稱`LoadContent`不是可以載入內容的唯一位置–許多遊戲會在其 Initialize 方法中執行內容載入，甚至是在其更新程式碼中作為內容，直到播放程式到達遊戲的特定點。

最後，我們可以修改 Draw 方法，如下所示：

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

如果我們執行遊戲，我們現在會看到該字元。 由於 X 和 Y 預設為0，因此字元會放在畫面的左上角：

![因為 X 和 Y 預設為0，所以字元會放在畫面的左上角。](part2-images/image4.png)

## <a name="creating-the-animation-class"></a>建立動畫類別

我們`CharacterEntity`目前會顯示完整的**charactersheet .png**檔案。 在一個檔案中，多個影像的這種相片順序稱為「 *sprite」工作表*。 通常，sprite 只會轉譯 sprite 工作表的一部分。 我們會修改`CharacterEntity`以轉譯此**charactersheet**的一部分，而此部分會隨著時間而改變，以顯示進行中的動畫。

我們將建立`Animation`類別，以控制 CharacterEntity 動畫的邏輯和狀態。 動畫類別是可用於任何實體的一般類別，而不只`CharacterEntity`是動畫。 最後， `Animation`類別會`Rectangle`提供`CharacterEntity` ，以在繪製本身時使用。 我們也會建立一個`AnimationFrame`類別，用來定義動畫。

### <a name="defining-animationframe"></a>定義 AnimationFrame

`AnimationFrame`將不會包含與動畫相關的任何邏輯。 我們只會使用它來儲存資料。 若要加入`AnimationFrame`類別，請以滑鼠右鍵按一下，或按一下 [ **WalkingGame** ] 共用專案，然後選取 [**加入 > 新增**檔案 ...]。輸入名稱**AnimationFrame** ，然後按一下 [**新增**] 按鈕。 我們會修改`AnimationFrame.cs`檔案，使其包含下列程式碼：

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

此`AnimationFrame`類別包含兩個資訊片段：

- `SourceRectangle`–定義將顯示`Texture2D` `AnimationFrame`的區域。 這個值是以圖元為單位測量，左上方為（0，0）。
- `Duration`–定義`AnimationFrame` `Animation`在中使用時顯示的時間長度。

### <a name="defining-animation"></a>定義動畫

`Animation`類別會`List<AnimationFrame>`包含，以及根據已通過的時間，切換目前所顯示之框架的邏輯。

若要加入`Animation`類別，請以滑鼠右鍵按一下，或按一下 [ **WalkingGame** ] 共用專案，然後選取 [**加入 > 新增**檔案 ...]。輸入名稱**動畫**，然後按一下 [**新增**] 按鈕。 我們會修改`Animation.cs`檔案，使其包含下列程式碼：

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

讓我們來看一下`Animation`類別的一些詳細資料。

### <a name="frames-list"></a>畫面格清單

`frames`成員是儲存動畫資料的目標。 具現化動畫的程式碼會`AnimationFrame`透過`AddFrame`方法， `frames`將實例新增至清單。 較完整的執行可能會`public`提供修改`frames`的方法或屬性，但我們會限制在此逐步解說中新增框架的功能。

### <a name="duration"></a>Duration

Duration 會傳回所有包含`Animation,` `AnimationFrame`之實例的持續時間，以取得的總持續時間。 如果`AnimationFrame`是不可變的物件，就可以快取這個值，但由於我們將 AnimationFrame 實作為可在新增至動畫之後變更的類別，因此每當存取屬性時，都需要計算這個值。

### <a name="update"></a>更新

`Update`方法應該會呼叫每個框架（也就是每次更新整個遊戲時）。 其目的是要增加`timeIntoAnimation`用來傳回目前顯示之框架的成員。 中`Update`的邏輯`timeIntoAnimation`可防止超出整個動畫的持續時間。

因為我們將使用`Animation`類別來顯示「正在進行」動畫，所以我們想要讓這個動畫在到達結尾時重複。 我們可以藉由檢查`timeIntoAnimation`是否大於`Duration`值來完成這項操作。 若是如此，它會迴圈回到開頭，並保留餘數，因而導致迴圈動畫。

### <a name="obtaining-the-current-frames-rectangle"></a>取得目前框架的矩形

`Animation`類別的用途最終是`Rectangle`提供，可在繪製 sprite 時使用。 目前類別包含用來`timeIntoAnimation`變更成員的邏輯，我們將用它來取得應該`Rectangle`顯示的。 `Animation` 我們將`CurrentRectangle` `Animation`在類別中建立屬性來執行此動作。 將此屬性複製到`Animation`類別：

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime = new TimeSpan();
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

## <a name="adding-the-first-animation-to-characterentity"></a>將第一個動畫新增至 CharacterEntity

將包含流覽和走的動畫，以及目前`Animation`所顯示的參考。 `CharacterEntity`

首先，我們會新增我們的`Animation,`第一個，我們將用它來測試到目前為止所寫的功能。 讓我們將下列成員新增至 CharacterEntity 類別：

```csharp
Animation walkDown;
Animation currentAnimation;
```

接下來，讓我們定義`walkDown`動畫。 若要這樣做， `CharacterEntity`請修改此函式，如下所示：

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

如先前所述，我們需要針對`Animation.Update`以時間為基礎的動畫進行呼叫來進行播放。 我們也需要指派`currentAnimation`。 現在`walkDown`，我們會將指派`currentAnimation`給，但稍後當我們執行移動邏輯時，將會取代此程式碼。 我們會將`Update`方法新增至`CharacterEntity` ，如下所示：

```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

既然我們已指派並`currentAnimation`更新，我們就可以用它來執行我們的繪圖。 我們將修改`CharacterEntity.Draw`如下：

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

取得`CharacterEntity`動畫的最後一個步驟是從`Game1`呼叫`Update`新增的方法。 修改`Game1.Update` ，如下所示：

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

現在會播放其`walkDown`動畫： `CharacterEntity`

![現在 CharacterEntity 會播放其 walkDown 動畫](part2-images/image5.gif)

## <a name="adding-movement-to-the-character"></a>將移動加入至字元

接下來，我們將使用觸控控制項將移動新增至我們的字元。 當使用者觸及畫面時，字元會移到觸及螢幕的點。 如果未偵測到任何觸控，則會將該字元放在原處。

### <a name="defining-getdesiredvelocityfrominput"></a>定義 GetDesiredVelocityFromInput

我們將使用 MonoGame 的`TouchPanel`類別，它會提供觸控式螢幕目前狀態的相關資訊。 讓我們新增一個方法，它會檢查`TouchPanel`並傳回字元的所需速度：

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

方法會傳回，其中包含使用者觸及螢幕的相關資訊。 `TouchCollection` `TouchPanel.GetState` 如果使用者沒有觸及畫面，則會是空的`TouchCollection` ，在這種情況下，我們不應該移動字元。

如果使用者觸及螢幕，我們會將字元移到第一次觸控，換句話說， `TouchLocation`位於索引0。 一開始，我們會將所需的速度設定為等於字元位置和第一個觸控位置之間的差異：

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

接下來是一些數學運算，可讓字元以相同的速度移動。 為了協助說明這是很重要的原因，讓我們來看看使用者觸及螢幕500圖元遠離該字元所在位置的情況。 設定的第一行`desiredVelocity.X`會指派500的值。 不過，如果使用者只是從字元的100單位觸及螢幕， `desiredVelocity.X`則會設定為100。 結果就是字元的移動速度會回應距離觸控點與字元的距離。 因為我們希望字元一律以相同的速度移動，所以我們需要修改 desiredVelocity。

`if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)`語句正在檢查速度是否為非零，換句話說，它會檢查以確定使用者不會觸及與字元目前位置相同的位置。 如果不是，則不論觸控的距離為何，我們都必須將字元的速度設定為常數。 我們藉由正規化速度向量來完成這項操作，因而導致其長度為1。 「速度向量」為1表示字元會每秒移動1圖元。 我們會將值乘以所需的200速度來加速。

### <a name="applying-velocity-to-position"></a>將速度套用至位置

從`GetDesiredVelocityFromInput`傳回的速度必須套用至字元的`X`和`Y`值，才能在執行時間生效。 我們會修改`Update`方法，如下所示：

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

我們在這裡實行的是稱為以*時間為基礎*的移動（而不是以框架為*基礎的*移動）。 以時間為基礎的移動會將速度值（在我們的案例中， `velocity`儲存在變數中的值）乘以儲存在中`gameTime.ElapsedGameTime.TotalSeconds`的上次更新後所經過的時間。 如果遊戲每秒執行的畫面數較少，畫面格之間的經過時間就會上升–最後的結果是使用以時間為基礎之移動的物件一律會以相同的速度移動，而不論畫面播放速率為何。

如果我們現在執行遊戲，就會看到該字元正移往觸控位置：

![字元正移往觸控位置](part2-images/image6.gif)

## <a name="matching-movement-and-animation"></a>符合的移動和動畫

當我們將字元移動並播放單一動畫之後，我們可以定義動畫的其餘部分，然後使用它們來反映字元的移動。 完成後，我們總共會有八個動畫：

- 向上、向下、向左和向右的動畫
- 置中的動畫仍然和朝上、向下、向左和向右

### <a name="defining-the-rest-of-the-animations"></a>定義動畫的其餘部分

我們會先在我們`Animation`新增`walkDown`的相同`CharacterEntity`位置，將所有動畫的實例新增至類別。 這麼做之後， `CharacterEntity`將會有下列`Animation`成員：

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

現在，我們將在此`CharacterEntity`函式中定義動畫，如下所示：

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

我們應該要注意的是，上述程式碼已`CharacterEntity`新增至函式，讓此逐步解說更短。 遊戲通常會將字元動畫的定義分隔成自己的類別，或從 XML 或 JSON 之類的資料格式載入這項資訊。

接下來，我們會根據字元移動的方向調整邏輯，或根據最後一個動畫（如果該字元剛停止）來使用動畫。 若要這麼做，我們將修改`Update`方法：

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

切換動畫的程式碼會分成兩個區塊。 第一次檢查速度是否不等於`Vector2.Zero` –這會告訴我們是否正在移動該字元。 如果移動字元，我們可以查看`velocity.X`和`velocity.Y`值，以決定要播放的動畫。

如果字元不在移動中，我們想要將字元的`currentAnimation`設定為「動動畫」，但`currentAnimation`如果是「正在進行」動畫，或是尚未設定動畫，我們只會執行此動作。 如果不`currentAnimation`是這四個行走動畫的其中一個，則該字元已經存在，因此我們不需要變更。 `currentAnimation`

這段程式碼的結果是，在進行流覽時，字元會適當製作動畫，然後面對它停止時的最後一個方向：

![這段程式碼的結果是，在進行流覽時，字元會適當製作動畫，然後面對它停止時的最後一個方向](part2-images/image7.gif)

## <a name="summary"></a>總結

本逐步解說示範如何使用 MonoGame 來建立跨平臺遊戲，其中包含 sprite、移動物件、輸入偵測和動畫。 其中涵蓋建立一般用途的動畫類別。 同時示範如何建立用於組織程式碼邏輯的字元實體。

## <a name="related-links"></a>相關連結

- [CharacterSheet 影像資源（範例）](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [遊戲完成（範例）](https://docs.microsoft.com/samples/xamarin/mobile-samples/walkinggamemg/)
