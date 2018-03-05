---
title: "在 MonoGame 中繪製頂點與 3D 圖形"
description: "MonoGame 支援使用陣列的頂點定義以每個點為基礎呈現 3D 物件的方式。 使用者可以利用頂點陣列，以建立動態的幾何，實作特殊效果，改善其呈現透過挑選的效率。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 1b139e460a4841f2174df166bf50bb276802d8d3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>在 MonoGame 中繪製頂點與 3D 圖形

_MonoGame 支援使用陣列的頂點定義以每個點為基礎呈現 3D 物件的方式。使用者可以利用頂點陣列，以建立動態的幾何，實作特殊效果，改善其呈現透過挑選的效率。_

使用者已閱讀[上呈現模型指南](~/graphics-games/monogame/3d/part1.md)會很熟悉呈現 3D 模型 MonoGame 中的。 `Model`類別是有效的方式來呈現 3D 圖形，以及處理靜態資料時使用的檔案 （例如.fbx) 中定義的資料。 某些遊戲需要 3D 幾何來定義或在執行階段以動態方式操作。 在這些情況下，我們可以使用陣列的*頂點*定義和轉譯的幾何。 端點是泛稱： 這是用來定義幾何的已排序清單的一部分的 3D 空間中的點。 通常這種方式定義一系列的三角形中排序頂點。

為協助視覺化頂點建立 3D 物件的使用方式，讓我們考量有下列範圍：

![](part2-images/image1.png "為協助視覺化頂點建立 3D 物件的使用方式，請考慮此範圍")

如上所示，球體是清楚的多個三角形組成。 我們可以檢視以查看頂點如何連接到表單三角形球體的框線：

![](part2-images/image2.png "檢視以查看頂點如何連接到表單三角形球體的框線")

本逐步解說涵蓋下列主題：

 - 建立專案
 - 建立頂點
 - 加入的繪圖程式碼
 - 使用紋理的呈現
 - 修改材質座標
 - 呈現頂點的模型

已完成的專案將會包含使用頂點陣列來繪製其棋盤式的樓層：

![](part2-images/image3.png "已完成的專案會包含使用頂點陣列來繪製其棋盤式的樓層")


# <a name="creating-a-project"></a>建立專案

首先，我們將下載的專案將做為我們的起點。 我們將使用模型專案[這可以在這裡找到](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/)。

一旦下載並解壓縮，請開啟並執行專案。 我們預期會看見螢幕上繪製六個機器人模型：

![](part2-images/image4.png "正在繪製在螢幕上的六個機器人模型")

這個專案的結束我們將會結合自己自訂的頂點轉譯機器人`Model`，因此我們不會刪除機器人轉譯程式碼。 相反地，我們將剛清除`Game1.Draw`移除 6 robots 現在在繪製呼叫。 若要這樣做，請開啟**Game1.cs**檔案，並尋找`Draw`方法。 修改，它包含下列程式碼：


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);
    base.Draw(gameTime);
}
```

這會導致顯示空白的藍色螢幕我們遊戲：

![](part2-images/image5.png "這將導致顯示空白的藍色螢幕遊戲")


# <a name="creating-the-vertices"></a>建立頂點

我們將建立來定義我們幾何頂點的陣列。 在本逐步解說中，我們將建立 3D 平面上 （在 3D 空間中的方塊，不飛機）。 雖然我們平面有四個邊與四個角落，但是它會組成每一個都需要三個頂點的兩個三角形。 因此，我們將在這裡定義六個點總數。

到目前為止我們已經被指頂點的一般意義上，但 MonoGame 提供可用的頂點一些標準結構：

 - `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
 - `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
 - `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
 - `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

每個型別名稱會指出它所包含的元件。 例如，`VertexPositionColor`包含值的位置和色彩。 讓我們看看每個元件：

 - 位置 – 所有的頂點類型包含`Position`元件。 `Position`值會定義在 3D 空間 （X、 Y 和 Z） 中的頂點位置。
 - 選擇性地指定色彩 – 頂點可以`Color`來執行自訂濃淡。
 - 標準 – 法線定義物件的表面遇到何種方法。 法線所需介面呈現具有光源自方向的物件，如果遇到的影響多少淺色接收。 法線通常會指定為*單位向量*– 3D 向量的長度為 1。
 - 紋理 – 紋理指材質座標 – 也就是對紋理的哪些部分應該會出現在指定的頂點。 紋理值如果是必要的呈現 3D 與紋理物件。 材質座標是標準化的座標，也就是說值將會介於 0 和 1。 我們會在本指南稍後的更詳細的材質座標。

我們平面將做為下限，，我們會想要執行我們的轉譯，所以我們將使用時，套用紋理`VertexPositionTexture`類型來定義我們的頂點。

首先，我們會將成員新增到 Game1 類別：


```csharp
VertexPositionTexture[] floorVerts; 
```

接下來，定義我們的頂點`Game1.Initialize`。 請注意，參考本文章中稍早提供的範本不包含`Game1.Initialize`方法，所以我們需要將整個方法加入`Game1`:


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

為協助視覺化我們的頂點看起來像，請考慮下圖：

![](part2-images/image6.png "為協助視覺化頂點看起來像，請考慮此圖表")

我們需要依賴我們的圖表以視覺化方式檢視頂點直到我們完成實作呈現的程式碼。


# <a name="adding-drawing-code"></a>加入的繪圖程式碼

現在，我們已經定義我們幾何的位置，我們可以撰寫轉譯程式碼。

首先，我們必須定義`BasicEffect`會保留參數等位置的轉譯和光源的執行個體。 若要這樣做，請加入`BasicEffect`成員`Game1`類別下方 where`floorVerts`欄位定義：


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect; 
```

接下來，修改`Initialize`方法，以定義效果：


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
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

現在我們可以將執行繪圖程式碼：


```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);


    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
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

我們需要呼叫`DrawGround`中我們`Game1.Draw`:

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

應用程式將會顯示下執行時：

![](part2-images/image7.png "當執行時，應用程式將會顯示這")

讓我們看看一些上述程式碼中的詳細資料。


## <a name="view-and-projection-properties"></a>檢視及投影內容

`View`和`Projection`屬性控制我們該如何檢視場景。 我們將會修改這個程式碼稍後當我們重新將加入模型轉譯程式碼。 具體來說，`View`控制的位置和方向的相機，和`Projection`控制項*視野*（這可以用來縮放相機）。


## <a name="techniques-and-passes"></a>技術，以及傳遞

一次我們已指派屬性，在我們效果，我們可以執行實際的轉譯。 

我們不會變更`CurrentTechnique`屬性在此逐步解說中，但更進階的遊戲可能會有單一的效果可以同時執行 （例如色彩值套用方式） 不同的方式繪製。 每一種轉譯模式可以表示為一項技術可在呈現之前可以指派。 此外，每個技術可能需要多個傳遞到正確轉譯。 如果呈現複雜的視覺效果，例如發光的介面或端效果可能需要多個行程。

要牢記的重點在於`foreach`迴圈可讓 C# 程式碼轉譯任何作用，無論基礎複雜性`BasicEffect`。


## <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives` 是頂點呈現的位置。 第一個參數會告知方法，我們有組織我們的頂點的方式。 我們有結構化它們，因此我們使用三個已排序的頂點，定義每個三角形`PrimitiveType.TriangleList`值。

第二個參數是我們先前定義的頂點的陣列。

第三個參數指定要繪製的第一個索引。 因為我們想要呈現整個頂點陣列時，我們將會傳遞值為 0。

最後，我們會指定多少呈現的三角形。 我們的頂點陣列包含兩個三角形，因此傳遞的值為 2。


# <a name="rendering-with-a-texture"></a>使用紋理的呈現

此時我們的應用程式呈現白色平面上 （在檢視方塊）。 接下來我們要加入紋理轉譯我們平面時所要使用專案。 

為了讓事情變簡單我們將.png 直接加入至受測專案，而不是使用 MonoGame 管線工具。 若要這樣做，請下載[.png 檔案](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)到您的電腦。 在下載後，以滑鼠右鍵按一下**內容**方案板然後選取資料夾**新增 > 新增檔案...** . 如果工作在 Android 上，然後這個資料夾會位於**資產**特定的 Android 專案中的資料夾。 如果在 iOS 上，則此資料夾會在 iOS 專案的根目錄中。 瀏覽至位置其中**checkerboard.png**儲存，然後選取 此檔案。 選取即可將檔案複製到目錄。

接下來，我們要在其中加入程式碼，建立我們`Texture2D`執行個體。 首先，新增`Texture2D`成員的身分`Game1`下`BasicEffect`執行個體：


```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

修改`Game1.LoadContent`，如下所示：


```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
} 
```

接下來，修改`DrawGround`方法。 唯一必要的修改，將指派`effect.TextureEnabled`至`true`，並設定`effect.Texture`至`checkerboardTexture`:


```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

    // new code:
    effect.TextureEnabled = true;
    effect.Texture = checkerboardTexture;

    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
                    PrimitiveType.TriangleList,
            floorVerts,
            0,
            2);
    }
} 
```

最後，我們需要修改`Game1.Initialize`方法也指派紋理座標在我們的頂點上：


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

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

當我們執行程式碼，我們可以看到我們平面現在會顯示棋盤式圖樣：

![](part2-images/image8.png "平面現在會顯示棋盤式圖樣")


# <a name="modifying-texture-coordinates"></a>修改材質座標

MonoGame 會使用標準化材質座標，也就是介於 0 和 1 之間，而不是介於 0 和材質的寬度或高度的座標。 下圖可協助視覺化標準化的座標：

![](part2-images/image9.png "這個圖表可協助視覺化標準化的座標")

標準化的材質座標允許紋理調整大小而不需要重寫程式碼，或重新建立 （例如.fbx 檔案） 的模型。 這可能是因為正規化的座標表示的比例，而不是特定像素為單位。 例如，(1，1) 將一律代表對右下角，而不管紋理大小。

我們可以變更單一變數用於重複次數的材質座標指派：


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

    int repetitions = 20;

    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
    floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

這會導致重複的 20 倍的紋理：

![](part2-images/image10.png "這會導致重複的 20 倍的材質")


# <a name="rendering-vertices-with-models"></a>呈現頂點的模型

既然我們平面都會正確轉譯，我們可以重新加入模型，以同時檢視所有項目。 首先，我們會重新加入模型程式碼，我們`Game1.Draw`方法 （包含已修改的位置）：


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
} 
```

我們也會建立`Vector3`中`Game1`來表示我們相機的位置。 我們會將新增的欄位，在我們`checkerboardTexture`宣告：


```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10); 
```

接下來，移除本機`cameraPosition`變數從`DrawModel`方法：


```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector); 
            ...
```

同樣地移除本機`cameraPosition`變數從`DrawGround`方法：


```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ... 
```

現在如果我們執行程式碼我們可以看到模型及地面在相同的時間：

![](part2-images/image11.png "模型及地面會顯示在相同的時間")

如果我們修改相機的位置 （例如藉由增加它的 X 值，會在此情況下將相機左側） 的值影響地面和模型，我們可以看到：


```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

此程式碼會產生下列：

![](part2-images/image3.png "此程式碼會產生這份檢視")


# <a name="summary"></a>總結

本逐步解說示範了如何使用頂點陣列來執行自訂的轉譯。 在此情況下，我們棋盤式的 floor 相結合所產生我們頂點型轉譯滿材質和`BasicEffect`，但列出的程式碼此處可做為任何 3D 呈現的基礎。 我們也示範了頂點基礎呈現可以搭配相同場景中的模型。

## <a name="related-links"></a>相關連結

- [棋盤式檔案 （範例）](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [已完成的專案 （範例）](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)
