---
title: 第2部分-執行 WalkingGame
description: 本逐步解說將示範如何將遊戲邏輯和內容新增至空的 MonoGame 專案，以建立動畫 sprite 的示範，並移動觸控輸入。
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 835beca6866f5d3f82ae48d1c99557ceafe360ba
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429773"
---
# <a name="part-2--implementing-the-walkinggame"></a>第2部分-執行 WalkingGame

_本逐步解說將示範如何將遊戲邏輯和內容新增至空的 MonoGame 專案，以建立動畫 sprite 的示範，並移動觸控輸入。_

本逐步解說的先前部分示範如何建立空的 MonoGame 專案。 我們會建立簡單的遊戲示範，以建立這些先前的部分。 本文包含下列各節：

- 將遊戲內容解壓縮
- MonoGame 類別總覽
- 轉譯我們的第一個 Sprite
- 建立 CharacterEntity
- 將 CharacterEntity 新增至遊戲
- 建立動畫類別
- 將第一個動畫新增至 CharacterEntity
- 將移動新增至字元
- 相符的移動和動畫

## <a name="unzipping-our-game-content"></a>將遊戲內容解壓縮

在開始撰寫程式碼之前，我們要先將遊戲 *內容*解壓縮。 遊戲開發人員通常會使用詞彙 *內容* 來參考非程式碼檔案，這些檔案通常是由視覺演出者、遊戲設計工具或音訊設計師所建立。 常見的內容類型包括用來顯示視覺效果、播放音效或控制人工智慧 (AI) 行為的檔案。 從遊戲開發小組的觀點來看，通常是由非程式設計人員所建立。

您可以 [在 GitHub 上](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)找到此處所使用的內容。 我們將需要將這些檔案下載到稍後將在本逐步解說中存取的位置。

## <a name="monogame-class-overview"></a>MonoGame 類別總覽

針對初學者，我們將探索基本轉譯中使用的 MonoGame 類別：

- `SpriteBatch` –用來在螢幕上繪製2D 圖形。 *Sprite* 是2d 視覺元素，用來在螢幕上顯示影像。 `SpriteBatch`物件可以在其和方法之間的時間繪製單一 sprite `Begin` `End` ，或多個 sprite 可以群組在一起或*批次處理*。
- `Texture2D` –在運行時程表示影像物件。 `Texture2D` 實例通常是從檔案格式（例如 .png 或 .bmp）建立的，雖然它們也可以在執行時間動態建立。 `Texture2D` 使用實例轉譯時，會使用實例 `SpriteBatch` 。
- `Vector2` –代表2D 座標系統中的位置，這個位置通常用來定位視覺物件。 MonoGame 也包含 `Vector3` 和， `Vector4` 但是我們只會 `Vector2` 在此逐步解說中使用。
- `Rectangle` –具有位置、寬度和高度的四邊區域。 當我們開始使用動畫時，我們將使用此定義來定義要轉譯的部分 `Texture2D` 。

另外也請注意，MonoGame 不是由 Microsoft 維護，而是由其命名空間。 `Microsoft.Xna`命名空間是在 MonoGame 中使用，可讓您更輕鬆地將現有的 [] 專案遷移至 MonoGame。

## <a name="rendering-our-first-sprite"></a>轉譯我們的第一個 Sprite

接下來，我們會在螢幕上繪製一個動畫，以顯示如何在 MonoGame 中執行2D 轉譯。

### <a name="creating-a-texture2d"></a>建立 Texture2D

我們必須建立 `Texture2D` 要在呈現 sprite 時使用的實例。 所有遊戲內容最後都會包含在名為 Content 的資料夾中 **，** 該資料夾位於平臺特定專案中。 MonoGame 共用專案不能包含內容，因為內容必須使用平臺特定的組建動作。 您可以在 iOS 專案中找到 [內容] 資料夾，也可以在 Android 專案的 [資產] 資料夾內找到。

若要新增遊戲的內容，請以滑鼠右鍵按一下 [ **內容** ] 資料夾，然後選取 [ **新增 > 新增檔案 ...** ]流覽至解壓縮 content.zip 檔案的位置，然後選取 **charactersheet.png** 檔案。 如果系統詢問您如何將檔案新增至資料夾，則應該選取 [ **複製** ] 選項：

![如果系統詢問您如何將檔案新增至資料夾，請選取 [複製] 選項](part2-images/image1.png)

Content 資料夾現在包含 charactersheet.png 檔案：

![Content 資料夾現在包含 charactersheet.png 檔案](part2-images/image2.png)

接下來，我們將新增程式碼以載入 charactersheet.png 檔案並建立 `Texture2D` 。 若要這樣做，請開啟檔案 `Game1.cs` ，然後將下欄欄位加入至 Game1.cs 類別：

```csharp
Texture2D characterSheetTexture;
```

接下來，我們將 `characterSheetTexture` 在方法中建立 `LoadContent` 。 在任何修改方法之前如下所示 `LoadContent` ：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

我們應該要注意的是，預設專案已經 `spriteBatch` 為我們具現化實例。 我們稍後將會用到，但我們將不會新增任何額外的程式碼，以準備好 `spriteBatch` 使用。 另一方面，我們的 `spriteSheetTexture` 需要初始化，因此我們會在建立之後將它初始化 `spriteBatch` ：

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

現在我們已經有 `SpriteBatch` 實例和實例， `Texture2D` 我們可以將轉譯程式碼加入至 `Game1.Draw` 方法：

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

執行遊戲現在會顯示一個顯示從 charactersheet.png 建立之材質的單一 sprite：

![執行遊戲現在會顯示一個顯示從 charactersheet.png 建立之材質的單一 sprite](part2-images/image3.png)

## <a name="creating-the-characterentity"></a>建立 CharacterEntity

到目前為止，我們已新增程式碼，以在螢幕上呈現單一 sprite;但是，如果我們要開發完整的遊戲，而不建立任何其他類別，我們就會遇到程式碼組織的問題。

### <a name="what-is-an-entity"></a>什麼是實體？

組織遊戲程式碼的常見模式是為每個遊戲 *實體* 類型建立新的類別。 遊戲開發中的實體是一個物件，其中可包含下列部分特性 (並非全部都需要) ：

- 視覺效果元素，例如 sprite、text 或3D 模型
- 物理或每個畫面格的行為，例如單位 patrolling 設定路徑或回應輸入的播放機字元
- 可以動態建立和終結，例如播放程式顯示和收集的電源

實體組織系統可能很複雜，而且許多遊戲引擎都會提供類別來協助管理實體。 我們將會執行一個非常簡單的實體系統，值得一提的是，完整的遊戲通常需要開發人員更多的組織。

### <a name="defining-the-characterentity"></a>定義 CharacterEntity

我們將呼叫的實體 `CharacterEntity` 具有下列特性：

- 載入本身的能力 `Texture2D`
- 轉譯本身的能力，包括包含呼叫方法來更新行走動畫
- `X` 和 Y 屬性，以控制字元的位置。
- 自行更新的能力–特別是從觸控畫面讀取值並適當地調整位置。

若要將新增 `CharacterEntity` 至遊戲，請按一下滑鼠右鍵，或在 **WalkingGame** 專案上按一下滑鼠右鍵，然後選取 [ **加入 > 新**檔案 ...]。選取 [ **空的類別** ] 選項，並將新的檔案命名為 **CharacterEntity**，然後按一下 [ **新增**]。

首先，我們將新增 `CharacterEntity` 載入和繪製本身的能力 `Texture2D` 。 我們將修改新增的檔案，如下所示 `CharacterEntity.cs` ：

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

上述程式碼會將定位、轉譯和載入內容的責任新增至 `CharacterEntity` 。 讓我們花點時間來說明上述程式碼中所採取的一些考慮。

### <a name="why-are-x-and-y-floats"></a>為什麼 X 和 Y 浮動？

對遊戲程式設計新手的開發人員，可能會想知道為何 `float` 使用類型而非 `int` 或 `double` 。 原因是，在低層級轉譯程式碼中放置32位值最常見。 Double 類型會佔用64位的精確度，很少需要用來放置物件。 雖然32位的差異看似不重要，但許多新式遊戲都包含圖形，需要處理數十個位置值，每個畫面格 (每秒30或60次的) 。 將必須透過圖形管線移動的記憶體數量，可能會對遊戲效能造成重大影響。

`int`資料類型可以是定位的適當測量單位，但它可以對實體的放置方式有限制。 例如，使用整數值時，對於支援放大或立體攝影機 (（) 允許的遊戲，更難順利地執行移動 `SpriteBatch` 。

最後，我們會看到在畫面周圍移動字元的邏輯，會使用遊戲的時間值來完成。 將速度乘以指定框架中所經過時間的結果，很少會產生整數，因此我們必須使用 `float` for `X` 和 `Y` 。

### <a name="why-is-charactersheettexture-static"></a>為什麼 characterSheetTexture 靜態？

`characterSheetTexture` `Texture2D` 實例是 charactersheet.png 檔案的運行時程表示法。 換句話說，它包含從來源 **charactersheet.png** 檔案解壓縮之每個圖元的色彩值。 如果我們的遊戲是要建立兩個 `CharacterEntity` 實例，則每個實例都需要存取 charactersheet.png 的資訊。 在這種情況下，我們不想要在載入 charactersheet.png 兩次時產生效能成本，也不想要將重複的材質記憶體儲存在 ram 中。 使用 `static` 欄位可讓我們在 `Texture2D` 所有實例之間共用相同的 `CharacterEntity` 。

使用 `static` 成員進行內容的運行時程表示是一種簡單的解決方案，而且無法解決大型遊戲中發生的問題，例如，從一個層級移至另一個層級時卸載內容。 更複雜的解決方案（不在本逐步解說的討論範圍內）包括使用 MonoGame 的內容管線或建立自訂的內容管理系統。

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>為什麼 SpriteBatch 會以引數的形式傳遞，而不是由實體具現化？

`Draw`上述所執行的方法會採用 `SpriteBatch` 引數，但相反地， `characterSheetTexture` 會由具現化 `CharacterEntity` 。

原因是，當相同的 `SpriteBatch` 實例用於所有 `Draw` 呼叫，以及在 `Draw` 一組和呼叫之間進行所有呼叫時，可能會有最有效率的轉譯 `Begin` `End` 。 當然，我們的遊戲只會包含單一實體實例，但更複雜的遊戲將受益于允許多個實體使用相同實例的模式 `SpriteBatch` 。

## <a name="adding-characterentity-to-the-game"></a>將 CharacterEntity 新增至遊戲

既然我們已加入我們的 `CharacterEntity` 程式碼以供轉譯，我們可以取代中的程式碼， `Game1.cs` 以使用這個新實體的實例。 若要這樣做，我們會以 `Texture2D` 中的 `CharacterEntity` 欄位取代欄位 `Game1` ：

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

接下來，我們會在中加入此實體的具現化 `Game1.Initialize` ：

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

我們也需要 `Texture2D` 從移除建立 `LoadContent` ，因為現在已在我們的中處理 `CharacterEntity` 。 `Game1.LoadContent` 看起來應該像這樣：

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

值得注意的是，雖然此方法的名稱 `LoadContent` 不是可以載入內容的唯一位置，但許多遊戲都是在其 Initialize 方法中執行內容載入，甚至是在其更新程式碼中執行內容，直到播放程式到達遊戲的某個點為止。

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

如果執行遊戲，我們現在會看到該字元。 由於 X 和 Y 預設值為0，因此字元會位於畫面的左上角：

![由於 X 和 Y 預設值為0，因此該字元會位於畫面的左上角。](part2-images/image4.png)

## <a name="creating-the-animation-class"></a>建立動畫類別

目前，我們會 `CharacterEntity` 顯示完整的 **charactersheet.png** 檔案。 單一檔案中的多個影像相片順序稱為「sprite」 *工作表*。 通常，sprite 只會轉譯一部分的 sprite 工作表。 我們將修改 `CharacterEntity` 以轉譯此 **charactersheet.png**的一部分，而此部分會隨著時間而改變，以顯示「行走」動畫。

我們將建立 `Animation` 類別來控制 CharacterEntity 動畫的邏輯和狀態。 動畫類別會是可用於任何實體的一般類別，而不只是 `CharacterEntity` 動畫。 最後， `Animation` 類別將會提供在 `Rectangle` `CharacterEntity` 繪圖本身時使用的。 我們也將建立 `AnimationFrame` 用來定義動畫的類別。

### <a name="defining-animationframe"></a>定義 AnimationFrame

`AnimationFrame` 將不會包含任何與動畫相關的邏輯。 我們只會使用它來儲存資料。 若要加入 `AnimationFrame` 類別，請以滑鼠右鍵按一下或按一下 [ **WalkingGame** 共用專案]，然後選取 [ **加入 > 新** 檔案 ...]。輸入名稱 **AnimationFrame** ，然後按一下 [ **新增** ] 按鈕。 我們會修改檔案， `AnimationFrame.cs` 使其包含下列程式碼：

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

- `SourceRectangle` –定義將顯示的區域 `Texture2D` `AnimationFrame` 。 這個值是以圖元為單位來測量，左上角是 (0，0) 。
- `Duration` –定義 `AnimationFrame` 在中使用時所顯示的時間長度 `Animation` 。

### <a name="defining-animation"></a>定義動畫

此 `Animation` 類別會包含和 `List<AnimationFrame>` 邏輯，以根據已通過的時間來切換目前顯示的畫面格。

若要加入 `Animation` 類別，請以滑鼠右鍵按一下或按一下 [ **WalkingGame** 共用專案]，然後選取 [ **加入 > 新**檔案 ...]。輸入名稱 **動畫** ，然後按一下 [ **新增** ] 按鈕。 我們會修改檔案 `Animation.cs` ，使其包含下列程式碼：

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

讓我們看看類別的一些詳細資料 `Animation` 。

### <a name="frames-list"></a>畫面格清單

`frames`成員會儲存動畫的資料。 具現化動畫的程式碼會 `AnimationFrame` 透過方法將實例加入至 `frames` 清單 `AddFrame` 。 更完整的實施可能會提供 `public` 方法或屬性來進行修改 `frames` ，但是我們會限制在此逐步解說中加入框架的功能。

### <a name="duration"></a>持續時間

Duration 會傳回的總持續時間，藉 `Animation,` 由加入所有包含實例的持續時間來取得 `AnimationFrame` 。 如果 `AnimationFrame` 是不可變的物件，則可以快取此值，但由於我們將 AnimationFrame 實作為類別，以在將其加入動畫之後變更，所以每當存取屬性時，都需要計算這個值。

### <a name="update"></a>更新

您 `Update` 應該在每個畫面上呼叫方法 (也就是每次) 更新整個遊戲時。 其目的是要增加 `timeIntoAnimation` 用來傳回目前所顯示框架的成員。 中的邏輯 `Update` 可防止超出 `timeIntoAnimation` 整個動畫的持續時間。

因為我們會使用 `Animation` 類別來顯示「行走」動畫，所以我們想要在到達結尾時重複此動畫。 我們可以藉由檢查是否大於值來完成這項 `timeIntoAnimation` 操作 `Duration` 。 如果是這樣，它會回復為開頭，並保留其餘部分，以產生迴圈動畫。

### <a name="obtaining-the-current-frames-rectangle"></a>取得目前框架的矩形

類別的目的 `Animation` 是要提供 `Rectangle` 可在繪圖 sprite 時使用的。 目前 `Animation` 類別包含變更成員的邏輯 `timeIntoAnimation` ，我們將使用此邏輯來取得 `Rectangle` 應顯示的成員。 我們將 `CurrentRectangle` 在類別中建立屬性來完成這項作業 `Animation` 。 將此屬性複製到 `Animation` 類別中：

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

`CharacterEntity`會包含用於行走和走的動畫，以及目前所顯示之的參考 `Animation` 。

首先，我們會新增我們 `Animation,` 將用來測試到目前為止所撰寫功能的第一個。 讓我們將下列成員新增至 CharacterEntity 類別：

```csharp
Animation walkDown;
Animation currentAnimation;
```

接下來，我們要定義 `walkDown` 動畫。 若要這樣做，請修改此函式，如下所示 `CharacterEntity` ：

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

如先前所述，我們需要呼叫以 `Animation.Update` 時間為基礎的動畫播放。 我們也需要指派 `currentAnimation` 。 現在，我們會將指派 `currentAnimation` 給 `walkDown` ，但我們稍後會在執行移動邏輯時，取代此程式碼。 我們會將方法新增至，如下所示 `Update` `CharacterEntity` ：

```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

既然我們已 `currentAnimation` 指派及更新，就可以用它來執行我們的繪圖。 修改方式如下 `CharacterEntity.Draw` ：

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

取得動畫的最後一個步驟 `CharacterEntity` 是從呼叫新加入的 `Update` 方法 `Game1` 。 修改 `Game1.Update` 方式如下：

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

現在 `CharacterEntity` 會播放其 `walkDown` 動畫：

![現在 CharacterEntity 會播放其 walkDown 動畫](part2-images/image5.gif)

## <a name="adding-movement-to-the-character"></a>將移動新增至字元

接下來，我們將使用觸控控制項將移動新增至我們的字元。 當使用者接觸螢幕時，該字元將會移到螢幕觸及的位置。 如果未偵測到任何觸控，則字元將會被取代。

### <a name="defining-getdesiredvelocityfrominput"></a>定義 GetDesiredVelocityFromInput

我們將使用 MonoGame 的 `TouchPanel` 類別，它會提供觸控式螢幕目前狀態的相關資訊。 讓我們新增一個方法，此方法會檢查 `TouchPanel` 並傳回我們的字元所需的速度：

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

`TouchPanel.GetState`方法會傳回， `TouchCollection` 其中包含使用者觸碰螢幕的相關資訊。 如果使用者沒有觸及螢幕，則 `TouchCollection` 會是空的，在這種情況下，我們不應移動字元。

如果使用者觸及螢幕，則會將字元移到第一個觸控，也就是 `TouchLocation` 位於索引0的位置。 一開始，我們會將所需的速度設定為等於字元位置與第一個觸控位置之間的差異：

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

接下來要做的一點是，會讓字元保持相同的速度。 為了協助說明這是很重要的原因，讓我們看看使用者觸及螢幕500圖元離開字元位置的情況。 第一行的 `desiredVelocity.X` 設定會指派500的值。 不過，如果使用者只是在螢幕上以只有100單位的距離觸及螢幕，則 `desiredVelocity.X` 會設定為100。 結果就是字元的移動速度會回應觸控點距離字元的距離。 因為我們希望字元一律以相同的速度移動，所以我們需要修改 desiredVelocity。

`if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)`語句正在檢查速度是否為非零-換句話說，它會檢查以確定使用者不會觸及與字元目前位置相同的位置。 如果不是，則必須將字元的速度設定為常數，不論觸控的距離為何。 我們藉由將速度向量正規化來完成這項工作，結果會是1的長度。 以1為單位的速度向量表示字元將每秒移動1圖元。 我們會將值乘以所需的速度200來加速。

### <a name="applying-velocity-to-position"></a>將速度套用至位置

從傳回的速度必須套用 `GetDesiredVelocityFromInput` 至字元 `X` 和 `Y` 值，才能在執行時間產生任何作用。 我們將修改方法，如下所示 `Update` ：

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

我們在此所做的就是所謂的「以 *時間為基礎* 的移動 (，而不是以 *框架為基礎的* 移動) 。 以時間為基礎的移動會將速度值相乘 (在我們的案例中，儲存在變數中的值) 的值， `velocity` 是從儲存在中的最後一個更新以來已經過的時間 `gameTime.ElapsedGameTime.TotalSeconds` 。 如果遊戲以每秒較少的畫面格執行，則畫面格之間的經過時間就會出現，最後的結果是，使用以時間為基礎之移動的物件一律會以相同的速度移動，不論畫面播放速率為何。

如果現在執行遊戲，我們會看到該字元正移至觸控位置：

![字元正移往觸控位置](part2-images/image6.gif)

## <a name="matching-movement-and-animation"></a>相符的移動和動畫

當我們的字元移動並播放單一動畫之後，我們就可以定義動畫的其餘部分，然後使用它們來反映字元的移動。 完成之後，總共會有八個動畫：

- 向上、向下、向左和向右的動畫
- 靜止的動畫以及朝上、向下、向左和向右的動畫

### <a name="defining-the-rest-of-the-animations"></a>定義動畫的其餘部分

我們會先將 `Animation` 所有動畫的實例加入至 `CharacterEntity` 我們新增的相同位置中 `walkDown` 。 一旦這麼做， `CharacterEntity` 就會有下列 `Animation` 成員：

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

現在，我們將在函式中定義動畫，如下所示 `CharacterEntity` ：

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

我們應該要注意的是，上述程式碼已新增至函式， `CharacterEntity` 以縮短此逐步解說。 遊戲通常會將字元動畫的定義分隔成自己的類別，或從資料格式（例如 XML 或 JSON）載入這項資訊。

接下來，我們將根據字元移動的方向來調整邏輯，以使用動畫，或者，如果字元剛剛停止，則會根據最後一個動畫。 若要這樣做，我們要修改 `Update` 方法：

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

切換動畫的程式碼分成兩個區塊。 第一次檢查速度是否不等於 `Vector2.Zero` –這會告訴我們是否正在移動字元。 如果移動字元，則可以查看 `velocity.X` 和 `velocity.Y` 值來判斷要播放的動畫。

如果字元不在移動中，則我們想要將字元設定為持續的 `currentAnimation` 動畫–但是，如果 `currentAnimation` 是「行走」動畫或未設定動畫，則只會進行此動作。 如果 `currentAnimation` 不是四個行走動畫的其中一種，則表示該字元已存在，因此我們不需要變更 `currentAnimation` 。

這段程式碼的結果是字元會在進行時適當地建立動畫，然後面對它停止時的最後一個方向：

![這段程式碼的結果是在進行時，該字元會正確建立動畫，然後面對它停止時的最後一個方向](part2-images/image7.gif)

## <a name="summary"></a>摘要

本逐步解說示範如何使用 MonoGame 來建立具有 sprite、移動物件、輸入偵測和動畫的跨平臺遊戲。 它涵蓋建立一般用途的動畫類別。 它也示範了如何建立用來組織程式碼邏輯的字元實體。

## <a name="related-links"></a>相關連結

- [CharacterSheet 映射資源 (範例) ](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [逐步完成遊戲 (範例) ](/samples/xamarin/mobile-samples/walkinggamemg/)