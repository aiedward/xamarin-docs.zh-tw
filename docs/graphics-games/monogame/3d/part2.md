---
title: 在 MonoGame 中使用頂點繪製3D 圖形
description: MonoGame 支援使用頂點陣列來定義如何以每個點為基礎來呈現3D 物件。 使用者可以利用「頂點陣列」來建立動態幾何、執行特殊效果，並透過「剔除」來改善其呈現的效率。
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: dfb03815f8642519cecf49ab7b626b9575821af1
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937627"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>在 MonoGame 中使用頂點繪製3D 圖形

_MonoGame 支援使用頂點陣列來定義如何以每個點為基礎來呈現3D 物件。使用者可以利用「頂點陣列」來建立動態幾何、執行特殊效果，並透過「剔除」來改善其呈現的效率。_

已閱讀轉譯[模型指南](~/graphics-games/monogame/3d/part1.md)的使用者，將會很熟悉如何在 MonoGame 中呈現3d 模型。 當您使用檔案 `Model` 中定義的資料（例如 fbx），以及處理靜態資料時，類別是轉譯3d 圖形的有效方式。 有些遊戲需要在執行時間以動態方式定義或操作 3D geometry。 在這些情況下，我們可以使用*頂點*陣列來定義和呈現 geometry。 頂點是3D 空間中某個點的一般詞彙，這是用來定義 geometry 之已排序清單的一部分。 通常頂點的排序方式就像是定義一系列三角形。

為了協助將頂點用來建立3D 物件的方式視覺化，讓我們來看看下列球體：

![若要協助將頂點用來建立3D 物件的方式視覺化，請考慮使用這個球體](part2-images/image1.png)

如上所示，球體會清楚地由多個三角形組成。 我們可以查看球體的線框，以查看頂點如何連接以形成三角形：

![查看球體的框線，以查看頂點如何連接以形成三角形](part2-images/image2.png)

本逐步解說將涵蓋下列主題：

- 建立專案
- 建立頂點
- 加入繪圖程式碼
- 使用材質呈現
- 修改材質座標
- 使用模型呈現頂點

完成的專案將包含以頂點陣列繪製的棋盤樓層：

![完成的專案將包含以頂點陣列繪製的棋盤樓層](part2-images/image3.png)

## <a name="creating-a-project"></a>建立專案

首先，我們將下載一個專案，做為我們的起點。 我們將使用模型專案，[可以在這裡找到](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelrenderingmg/)。

下載並解壓縮之後，請開啟並執行專案。 我們預期會看到六個機器人模型在螢幕上繪製：

![在螢幕上繪製六個機器人模型](part2-images/image4.png)

在此專案結束時，我們將結合自己的自訂頂點呈現與機器人 `Model` ，因此我們不會刪除機器人轉譯程式碼。 相反地，我們只會清除 `Game1.Draw` 呼叫來移除6部機器人的繪製。 若要這麼做，請開啟**Game1.cs**檔案，並找出 `Draw` 方法。 修改它，使其包含下列程式碼：

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

這會導致我們的遊戲顯示空的藍色畫面：

![這會導致遊戲顯示空的藍色畫面](part2-images/image5.png)

## <a name="creating-the-vertices"></a>建立頂點

我們會建立一個頂點陣列，以定義我們的幾何。 在此逐步解說中，我們將建立3D 平面（3D 空間中的正方形，而不是飛機）。 雖然我們的平面有四個邊和四個角落，它會由兩個三角形組成，而每個都需要三個頂點。 因此，我們將總共定義六個點。

到目前為止，我們一直在討論頂點，但 MonoGame 提供一些可用於頂點的標準結構：

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

每個類型的名稱都會指出它所包含的元件。 例如， `VertexPositionColor` 包含位置和色彩的值。 讓我們看一下每個元件：

- Position –所有頂點類型都包含一個 `Position` 元件。 這些 `Position` 值會定義頂點在3d 空間中的位置（X、Y 和 Z）。
- Color –頂點可以選擇性地指定 `Color` 值來執行自訂色調。
- Normal –法線定義物件介面的面向方式。 如果呈現具有光源的物件，則必須使用法線，因為表面所面向的方向會影響它所收到的光線。 法線通常會指定為*單位向量*–長度為1的3d 向量。
- 材質–材質指的是材質座標–也就是說，材質的哪個部分應該會出現在指定的頂點上。 如果使用材質呈現3D 物件，則需要材質值。 材質座標是正規化座標，這表示值會介於0到1之間。 本指南稍後會更詳細說明材質座標。

我們的平面會作為樓層，而我們會想要在執行轉譯時套用材質，因此我們將使用 `VertexPositionTexture` 類型來定義頂點。

首先，我們會將成員新增至我們的 Game1 類別：

```csharp
VertexPositionTexture[] floorVerts;
```

接下來，在中定義我們的頂點 `Game1.Initialize` 。 請注意，本文稍早所參考的提供的範本不包含 `Game1.Initialize` 方法，因此我們需要將整個方法新增至 `Game1` ：

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];
    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);
    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // We’ll be assigning texture values later
    base.Initialize ();
}
```

若要協助將頂點的外觀視覺化，請考慮下列圖表：

![若要協助視覺化頂點的外觀，請考慮此圖表](part2-images/image6.png)

我們需要依賴我們的圖表來視覺化頂點，直到完成轉譯程式碼為止。

## <a name="adding-drawing-code"></a>加入繪圖程式碼

既然我們已定義 geometry 的位置，我們就可以撰寫我們的轉譯程式碼。

首先，我們需要定義一個實例，以 `BasicEffect` 保存用於呈現的參數，例如位置和光源。 若要這麼做，請將 `BasicEffect` 成員新增至 `Game1` 下列類別，其中 `floorVerts` 定義了欄位：

```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

接下來，修改 `Initialize` 方法以定義效果：

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

現在我們可以加入程式碼來執行繪圖：

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
            // We’ll be rendering two trinalges
            PrimitiveType.TriangleList,
            // The array of verts that we want to render
            floorVerts,
            // The offset, which is 0 since we want to start
            // at the beginning of the floorVerts array
            0,
            // The number of triangles to draw
            2);
    }
}
```

我們必須 `DrawGround` 在中呼叫 `Game1.Draw` ：

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

應用程式會在執行時顯示下列內容：

![應用程式會在執行時顯示此](part2-images/image7.png)

讓我們看看上述程式碼中的一些詳細資料。

### <a name="view-and-projection-properties"></a>視圖和投射屬性

`View`和 `Projection` 屬性會控制我們查看場景的方式。 我們稍後會在重新加入模型轉譯程式碼時，修改此程式碼。 具體而言， `View` 會控制相機的位置和方向，並 `Projection` 控制*觀看的欄位*（可用來縮放相機）。

### <a name="techniques-and-passes"></a>技術和階段

一旦將屬性指派給我們的效果，我們就可以執行實際的呈現。

我們不會變更 `CurrentTechnique` 此逐步解說中的屬性，但較高階的遊戲可能會有單一效果，可以透過不同的方式執行繪製（例如，色彩值的套用方式）。 這些轉譯模式中的每一個都可以表示為可在轉譯之前指派的技術。 此外，每種技術都可能需要多次轉譯，才能正確呈現。 如果轉譯複雜的視覺效果（例如發光表面或毛），可能會需要多個階段。

要記住的重點是， `foreach` 不論基礎的複雜度為何，迴圈都會讓相同的 c # 程式碼呈現任何效果 `BasicEffect` 。

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives`這是在其中呈現頂點的位置。 第一個參數會告訴方法我們如何組織頂點。 我們已將其結構化，讓每個三角形都是由三個已排序的頂點所定義，因此我們會使用 `PrimitiveType.TriangleList` 值。

第二個參數是我們稍早定義的頂點陣列。

第三個參數指定要繪製的第一個索引。 因為我們想要呈現整個頂點陣列，所以我們會傳遞值0。

最後，我們會指定要呈現的三角形數目。 我們的頂點陣列包含兩個三角形，因此傳遞值為2。

## <a name="rendering-with-a-texture"></a>使用材質呈現

此時，我們的應用程式會呈現白色平面（以角度為觀點）。 接下來，我們會將材質新增至我們的專案，以便在轉譯我們的平面時使用。

為了簡單起見，我們會將 .png 直接新增至專案，而不是使用 MonoGame 管線工具。 若要這麼做，請將[這個 .png](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)檔案下載到您的電腦。 下載之後，以滑鼠右鍵按一下 [Solution pad] 中的 [ **Content** ] 資料夾，然後選取 [**新增]>新增檔案 ...** ]。 如果在 Android 上運作，此資料夾將位於 Android 特定專案的 [**資產**] 資料夾底下。 如果在 iOS 上，此資料夾將會在 iOS 專案的根目錄中。 流覽至儲存**checkerboard.png**的位置，然後選取此檔案。 選取此檔案可將檔案複製到目錄。

接下來，我們將新增程式碼來建立 `Texture2D` 實例。 首先，將新增 `Texture2D` 為實例底下的成員 `Game1` `BasicEffect` ：

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

修改 `Game1.LoadContent` ，如下所示：

```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
}
```

接下來，修改 `DrawGround` 方法。 唯一需要的修改是將指派 `effect.TextureEnabled` 至 `true` ，並將設定 `effect.Texture` 為 `checkerboardTexture` ：

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

    // new code:
    effect.TextureEnabled = true;
    effect.Texture = checkerboardTexture;

    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
                    PrimitiveType.TriangleList,
            floorVerts,
            0,
            2);
    }
}
```

最後，我們需要修改方法， `Game1.Initialize` 以同時在我們的頂點上指派材質座標：

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

如果我們執行程式碼，我們可以看到我們的平面現在顯示棋盤圖樣：

![平面現在會顯示棋盤圖樣](part2-images/image8.png)

## <a name="modifying-texture-coordinates"></a>修改材質座標

MonoGame 使用正規化材質座標，其座標介於0和1之間，而不是介於0和材質的寬度或高度之間。 下圖可協助將正規化座標視覺化：

![此圖表可協助將正規化座標視覺化](part2-images/image9.png)

正規化材質座標可調整紋理大小，而不需要重寫程式碼或重新建立模型（例如 fbx 檔案）。 這是可行的，因為正規化座標代表比例，而不是特定圖元。 例如，（1，1）一律會代表右下角，而不論材質大小為何。

我們可以變更材質座標指派，以使用單一變數做為重複的次數：

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    int repetitions = 20;

    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
    floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

這會導致紋理重複20次：

![這會導致紋理重複20次](part2-images/image10.png)

## <a name="rendering-vertices-with-models"></a>使用模型呈現頂點

現在，我們的平面已正確轉譯，我們可以重新加入模型以同時查看所有專案。 首先，我們會將模型程式碼重新加入至我們 `Game1.Draw` 的方法（具有修改過的位置）：

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
}
```

我們也會 `Vector3` 在中建立 `Game1` ，以代表我們的相機位置。 我們會在宣告下新增欄位 `checkerboardTexture` ：

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10);
```

接下來， `cameraPosition` 從方法中移除本機變數 `DrawModel` ：

```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);
            ...
```

同樣地， `cameraPosition` 從方法中移除本機變數 `DrawGround` ：

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ...
```

現在，如果我們執行程式碼，就可以同時看到模型和地面：

![同時也會顯示模型和地面](part2-images/image11.png)

如果我們修改相機位置（例如，藉由增加其 X 值（在此案例中會將相機移至左邊），我們可以看到值會影響地面和模型：

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

這段程式碼會產生下列結果：

![此程式碼會產生此視圖](part2-images/image3.png)

## <a name="summary"></a>總結

本逐步解說示範如何使用頂點陣列來執行自訂轉譯。 在此情況下，我們建立了一個棋盤地板，其方式是結合以頂點為基礎的轉譯與材質和 `BasicEffect` ，但此處所提供的程式碼可做為任何3d 轉譯的基礎。 我們也示範了，以頂點為基礎的呈現可與相同場景中的模型混合。

## <a name="related-links"></a>相關連結

- [棋盤檔案（範例）](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [完成的專案（範例）](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelsandvertsmg/)
