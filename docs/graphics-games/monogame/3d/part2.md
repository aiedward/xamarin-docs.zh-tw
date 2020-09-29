---
title: 在 MonoGame 中使用頂點繪製3D 圖形
description: MonoGame 支援使用頂點陣列來定義如何以每個點為基礎呈現3D 物件。 使用者可以利用頂點陣列來建立動態幾何、實行特殊效果，並透過剔除來改善其轉譯的效率。
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: ccec89253c58d7d43cb09f33777c253a2f0da56d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431126"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>在 MonoGame 中使用頂點繪製3D 圖形

_MonoGame 支援使用頂點陣列來定義如何以每個點為基礎呈現3D 物件。使用者可以利用頂點陣列來建立動態幾何、實行特殊效果，並透過剔除來改善其轉譯的效率。_

已閱讀轉譯 [模型指南](~/graphics-games/monogame/3d/part1.md) 的使用者，將會很熟悉如何在 MonoGame 中呈現3d 模型。 `Model`當使用檔案 (中所定義的資料時（例如 fbx) ）以及處理靜態資料時，類別是轉譯3d 圖形的有效方式。 有些遊戲需要在執行時間動態定義或操作3D 幾何。 在這些情況下，我們可以使用 *頂點* 陣列來定義和呈現幾何。 頂點是3D 空間中某個點的一般詞彙，這是用來定義幾何的已排序清單的一部分。 頂點通常是以定義一連串三角形的方式來排序。

為了協助視覺化頂點如何用來建立3D 物件，讓我們看看下列球體：

![若要協助視覺化如何使用頂點來建立3D 物件，請考慮使用此球體](part2-images/image1.png)

如上所示，球體明確地由多個三角形組成。 我們可以查看球體的框線，以查看頂點如何連接來形成三角形：

![查看球體的框線以查看頂點如何連接至表單三角形](part2-images/image2.png)

本逐步解說將討論下列主題：

- 建立專案
- 建立頂點
- 新增繪圖程式碼
- 以材質呈現
- 修改材質座標
- 使用模型呈現頂點

完成的專案將會包含會使用頂點陣列繪製的棋盤樓層：

![完成的專案將包含將使用頂點陣列繪製的棋盤樓層](part2-images/image3.png)

## <a name="creating-a-project"></a>建立專案

首先，我們將下載將作為起點的專案。 我們將使用 [可在此找到](/samples/xamarin/mobile-samples/modelrenderingmg/)的模型專案。

一旦下載並解壓縮之後，請開啟並執行專案。 我們預期會看到六個在螢幕上繪製的機器人模型：

![在螢幕上繪製六個機器人模型](part2-images/image4.png)

在此專案結束時，我們會將自己的自訂頂點轉譯與機器人結合 `Model` ，因此我們不會刪除機器人轉譯程式碼。 相反地，我們會直接清除將 `Game1.Draw` 6 個機器人的繪圖移除的呼叫。 若要這樣做，請開啟 **Game1.cs** 檔案，並找出 `Draw` 方法。 修改它，使其包含下列程式碼：

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

這會導致遊戲顯示空白的藍色畫面：

![這會導致遊戲顯示空白的藍色畫面](part2-images/image5.png)

## <a name="creating-the-vertices"></a>建立頂點

我們會建立一個頂點陣列來定義我們的幾何。 在本逐步解說中，我們將建立3d 平面 (正方形的立體空間，而不是飛機) 。 雖然我們的平面有四個邊和四個角落，但它會由兩個三角形組成，每個三角形都需要三個頂點。 因此，我們會定義總共六個點。

到目前為止，我們一直都在討論頂點，但 MonoGame 提供一些可用於頂點的標準結構：

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

每個類型的名稱都表示它所包含的元件。 例如， `VertexPositionColor` 包含位置和色彩的值。 讓我們看看每個元件：

- 位置–所有頂點類型都包含一個 `Position` 元件。 這些 `Position` 值會定義頂點在3d 空間中的位置， (X、Y 和 Z) 。
- Color –頂點可以選擇性地指定 `Color` 值，以執行自訂色彩。
- Normal –法線定義物件介面面向的方向。 如果呈現具有光源的物件（因為表面面對的方向會影響它所收到的光線），就必須使用法線。 法線通常會指定為 *單位向量* （長度為1的3d 向量）。
- 材質–材質指材質座標，也就是材質應出現在指定頂點的部分。 如果以材質呈現3D 物件，則需要材質值。 材質座標是標準化座標，表示值會落在0和1之間。 本指南稍後會更詳細討論材質座標。

我們的平面將作為樓層，而且我們會想要在執行轉譯時套用材質，因此我們將使用 `VertexPositionTexture` 類型來定義頂點。

首先，我們會將成員新增至我們的 Game1 類別：

```csharp
VertexPositionTexture[] floorVerts;
```

接下來，在中定義頂點 `Game1.Initialize` 。 請注意，本文稍早所述的所提供範本不包含 `Game1.Initialize` 方法，因此我們必須將整個方法新增至 `Game1` ：

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

若要協助視覺化頂點的外觀，請考慮下列圖表：

![若要協助視覺化頂點的外觀，請考慮此圖表](part2-images/image6.png)

我們需要依賴圖表來視覺化頂點，直到我們完成轉譯程式碼為止。

## <a name="adding-drawing-code"></a>新增繪圖程式碼

既然我們已定義幾何的位置，我們可以撰寫轉譯程式碼。

首先，我們必須定義一個實例， `BasicEffect` 此實例會保留用於呈現的參數，例如位置和光源。 若要這樣做，請將 `BasicEffect` 成員新增至 `Game1` 下列類別的 `floorVerts` 定義欄位：

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

我們必須 `DrawGround` 在下列情況中呼叫 `Game1.Draw` ：

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

應用程式會在執行時顯示下列內容：

![應用程式會在執行時顯示此應用程式](part2-images/image7.png)

讓我們看看上述程式碼中的一些詳細資料。

### <a name="view-and-projection-properties"></a>視圖和投射屬性

`View`和 `Projection` 屬性會控制如何查看場景。 稍後當我們重新新增模型轉譯程式碼時，我們將會修改此程式碼。 具體而言， `View` 會控制攝影機的位置和方向，並 `Projection` 控制可用於縮放相機) *的視圖* (的欄位。

### <a name="techniques-and-passes"></a>技術與通過

一旦將屬性指派給我們的效果之後，我們就可以執行實際的呈現。

我們不會 `CurrentTechnique` 在此逐步解說中變更屬性，但是更先進的遊戲可能會有單一效果，可使用不同的方式來執行繪圖 (例如色彩值的套用方式) 。 這兩種轉譯模式都可以表示為一種技術，可在轉譯之前指派。 此外，每個技巧都可能需要多次轉譯才能正確呈現。 如果轉譯複雜的視覺效果（例如發光表面或毛），效果可能需要多個階段。

要牢記在心的重要事項是， `foreach` 迴圈會讓相同的 c # 程式碼轉譯任何效果，而不考慮基礎的複雜度 `BasicEffect` 。

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives` 是指頂點的呈現位置。 第一個參數會告訴方法我們如何組織頂點。 我們已將其結構化，因此每個三角形都是由三個已排序的頂點所定義，因此我們會使用此 `PrimitiveType.TriangleList` 值。

第二個參數是我們稍早定義的頂點陣列。

第三個參數指定要繪製的第一個索引。 由於我們想要呈現整個頂點陣列，因此我們會傳遞0值。

最後，我們會指定要呈現的三角形數目。 我們的頂點陣列包含兩個三角形，因此傳遞值為2。

## <a name="rendering-with-a-texture"></a>以材質呈現

此時，我們的應用程式會在) 的觀點 (呈現白色平面。 接下來，我們會將材質新增至專案，以在轉譯我們的平面時使用。

為了簡單起見，我們會將 .png 直接新增至我們的專案，而不是使用 MonoGame 管線工具。 若要這樣做，請將 [這個 .png](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true) 檔案下載到您的電腦。 下載之後，以滑鼠右鍵按一下 Solution pad 中的 **Content** 資料夾，然後選取 [ **新增>新增檔案 ...** ]。 如果使用 Android，此資料夾將位於 Android 特定專案的 [ **資產** ] 資料夾底下。 在 iOS 上，此資料夾會位於 iOS 專案的根目錄中。 流覽至儲存 **checkerboard.png** 的位置，然後選取此檔案。 選取此檔案可將檔案複製到目錄。

接下來，我們將新增程式碼來建立 `Texture2D` 實例。 首先，將新增 `Texture2D` 為實例底下的成員 `Game1` `BasicEffect` ：

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

修改 `Game1.LoadContent` 方式如下：

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

接下來，修改 `DrawGround` 方法。 唯一必要的修改是指派 `effect.TextureEnabled` 給 `true` 和，將設定為 `effect.Texture` `checkerboardTexture` ：

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

最後，我們需要修改 `Game1.Initialize` 方法，以同時在頂點上指派紋理座標：

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

正規化紋理座標允許紋理調整大小，而不需要重寫程式碼或重新建立模型 (例如 fbx 檔案) 。 這是可能的，因為標準化座標表示比例，而不是特定的圖元。 例如，不論材質大小為何， (1，1) 一律會代表右下角。

我們可以變更紋理座標指派，以使用單一變數進行重複的次數：

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

現在，我們的平面已正確轉譯，我們可以重新新增模型，以同時查看所有專案。 首先，我們會將模型程式碼重新新增至我們 `Game1.Draw` 的方法 (並) 修改的位置：

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

我們也會 `Vector3` 在中建立 `Game1` ，代表相機的位置。 我們將在我們的宣告下新增欄位 `checkerboardTexture` ：

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

同樣地，請 `cameraPosition` 從方法中移除本機變數 `DrawGround` ：

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

![模型和地面都會同時顯示](part2-images/image11.png)

如果我們修改攝影機位置 (例如藉由增加其 X 值（在此案例中，會將相機移至左邊）) 我們可以看到值同時影響地面和模型：

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

這段程式碼會產生下列結果：

![此程式碼會產生此視圖](part2-images/image3.png)

## <a name="summary"></a>摘要

本逐步解說示範如何使用頂點陣列來執行自訂轉譯。 在此情況下，我們藉由結合以頂點呈現的材質和紋理來建立棋盤樓層 `BasicEffect` ，但此處所呈現的程式碼可做為任何3d 轉譯的基礎。 我們也示範了以頂點為基礎的轉譯可以與相同場景中的模型混合使用。

## <a name="related-links"></a>相關連結

- [棋盤檔案 (範例) ](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [完成的專案 (範例) ](/samples/xamarin/mobile-samples/modelsandvertsmg/)