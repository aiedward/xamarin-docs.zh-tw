---
title: 3D 圖形使用頂點繪製 MonoGame
description: MonoGame 支援使用陣列的頂點定義 3D 物件每個點為基礎的呈現方式。 使用者可以利用頂點的陣列，以建立動態的幾何，實作特殊效果，並改善其呈現透過剔除的效率。
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: df36c149e98e8c0cbb16de4c2cf52def5713ec13
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61178523"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>3D 圖形使用頂點繪製 MonoGame

_MonoGame 支援使用陣列的頂點定義 3D 物件每個點為基礎的呈現方式。使用者可以利用頂點的陣列，以建立動態的幾何，實作特殊效果，並改善其呈現透過剔除的效率。_

使用者已閱讀[在呈現模型的指南](~/graphics-games/monogame/3d/part1.md)不會感到陌生轉譯 MonoGame 的 3D 模型。 `Model`類別是有效的方式來呈現 3D 圖形，以及處理靜態資料時使用 （例如.fbx) 檔案中定義的資料。 某些遊戲需要定義或在執行階段以動態方式操作 3D 幾何圖形。 在這些情況下，我們可以使用的陣列*頂點*來定義和轉譯幾何。 頂點指的是一般的排序清單，用來定義幾何的一部分的 3D 空間中的點。 通常頂點會排序的方式定義一系列三角形。

若要協助以視覺化方式檢視 頂點會用來建立 3D 物件的方式，請考慮下列球體：

![](part2-images/image1.png "若要協助以視覺化方式檢視 頂點會用來建立 3D 物件的方式，請考慮此球形")

如上所示，球體是清楚組成多個三角形。 我們可以檢視以查看頂點如何連接到表單三角形球體的框線：

![](part2-images/image2.png "檢視以查看頂點如何連接到表單三角形球體的框線")

本逐步解說涵蓋下列主題：

- 建立專案
- 建立頂點
- 新增繪圖程式碼
- 使用紋理轉譯
- 修改材質座標
- 呈現模型的頂點

已完成的專案將會包含使用頂點陣列來繪製其棋盤式的 floor:

![](part2-images/image3.png "已完成的專案會包含使用頂點陣列來繪製其棋盤式的 floor")

## <a name="creating-a-project"></a>建立專案

首先，我們將下載的專案，這將做為我們的起點。 我們將使用模型專案[這可以在這裡找到](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/)。

一旦下載並解壓縮，請開啟，並執行專案。 我們期待能看到六個要繪製在螢幕上的機器人模型：

![](part2-images/image4.png "六個要繪製在螢幕上的機器人模型")

此專案結束時我們將會結合我們自己的自訂頂點轉譯與機器人`Model`，因此我們不會刪除機器人轉譯程式碼。 相反地，我們將剛清除`Game1.Draw`若要移除的 6 個機器人的繪圖，目前的呼叫。 若要這樣做，請開啟**Game1.cs**檔案，並找出`Draw`方法。 修改它，使它包含下列程式碼：

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

這會導致顯示空白的藍色螢幕我們遊戲：

![](part2-images/image5.png "這會導致顯示空白的藍色螢幕的遊戲")

## <a name="creating-the-vertices"></a>建立頂點

我們將建立頂點來定義我們 geometry 的陣列。 在本逐步解說中，我們將建立 3D 的平面 （正方形在 3D 空間中的，不飛機）。 雖然我們平面具有四個邊和四個角落，它會包含兩個三角形，其中每一個需要三個頂點。 因此，我們將在這裡定義總共六個點。

到目前為止已談到在一般意義的頂點，但 MonoGame 提供一些標準的結構可以用於頂點：

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

每個型別名稱表示其包含的元件。 比方說，`VertexPositionColor`包含位置和色彩的值。 讓我們看看每個元件：

- 位置 – 所有頂點類型都包括`Position`元件。 `Position`值會定義頂點在 3D 空間 （X、 Y 和 Z） 所在的位置。
- 選擇性地指定色彩 – 頂點可以`Color`值來執行自訂的濃淡。
- Normal-Normals 定義物件的表面會對向的方式。 Normals 所需轉譯自方向的光線的物件，如果介面面向的影響多少淺接收。 法線通常指定為*單位向量*– 3D 向量，其長度為 1。
- 材質 – 材質的材質座標 – 也就是材質的哪部分應該會出現在指定的頂點指。 材質的值為必要轉譯材質的 3D 物件。 材質座標是標準化的座標，這表示，值會落在 0 和 1 之間。 我們將涵蓋本指南稍後的更詳細的材質座標。

我們的平面將做為樓層、，我們會想要執行我們的轉譯，因此我們將使用時，套用材質`VertexPositionTexture`類型來定義我們的頂點。

首先，我們會將成員加入我們 Game1 類別：

```csharp
VertexPositionTexture[] floorVerts; 
```

接下來，定義在我們頂點`Game1.Initialize`。 請注意，參考稍早在本文中所提供的範本不包含`Game1.Initialize`方法，因此我們需要將整個方法加入`Game1`:

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

若要協助以視覺化方式檢視我們的頂點的樣子，請考慮下圖：

![](part2-images/image6.png "若要協助以視覺化方式檢視 頂點的樣子，請考慮此圖表")

我們需要仰賴我們的圖表以視覺化方式檢視頂點，直到我們完成實作轉譯程式碼。

## <a name="adding-drawing-code"></a>新增繪圖程式碼

既然我們已經定義我們 geometry 的位置，我們可以撰寫轉譯程式碼。

首先，我們必須定義`BasicEffect`會保留轉譯，例如位置和光源參數的執行個體。 若要這樣做，請新增`BasicEffect`成員，才能`Game1`低於類別`floorVerts`欄位定義：


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

接下來，修改`Initialize`方法來定義效果：

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

現在我們可以將程式碼來執行活動，抽獎：

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

我們需要呼叫`DrawGround`在我們`Game1.Draw`:

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

應用程式將會顯示下列執行時：

![](part2-images/image7.png "執行時，應用程式會顯示此")

讓我們看看一些上面的程式碼的詳細資料。

### <a name="view-and-projection-properties"></a>檢視和投影的屬性

`View`和`Projection`屬性可控制我們該如何檢視場景。 我們將修改此程式碼稍後當我們重新加入模型轉譯程式碼。 具體而言，`View`控制的位置和方向的相機，並`Projection`控制項*視野*（這可以用來縮放觀景窗）。

### <a name="techniques-and-passes"></a>技術和傳遞

一次我們已指派屬性，我們可以執行我們效果上的實際呈現。 

我們不會變更`CurrentTechnique`在此逐步解說中，但更進階的遊戲中的屬性可能會有單一的效果，以繪製不同的方式 （例如，如何套用的色彩值） 執行。 每一種轉譯模式可以表示為可以在呈現之前指派的技術。 此外，每一種技巧可能需要多次傳遞才能正確轉譯。 如果轉譯複雜的視覺效果，例如發光的介面或毛皮，效果可能需要多個行程。

要記住的重點在於`foreach`迴圈可讓相同C#程式碼，以呈現任何影響，不論基礎複雜度`BasicEffect`。

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives` 是頂點呈現的位置。 第一個參數會告知方法，我們有組織我們頂點的方式。 我們有安排它們以便每一個三角形定義由三個已排序的頂點，因此我們會使用`PrimitiveType.TriangleList`值。

第二個參數是我們稍早定義的頂點的陣列。

第三個參數會指定要繪製的第一個索引。 因為我們想要呈現整個頂點陣列時，我們要代入值為 0。

最後，我們會指定多少三角形來呈現。 我們的頂點陣列會包含兩個三角形，因此傳遞的值為 2。

## <a name="rendering-with-a-texture"></a>使用紋理轉譯

此時我們的應用程式呈現白色平面上的 （在檢視方塊）。 我們會將材質加入我們的專案，呈現我們平面時要使用下一步。 

為了簡單起見中，我們將新增.png 直接向我們的專案，而不是使用 MonoGame 管線工具。 若要這樣做，請下載[這個.png 檔案](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)到您的電腦。 下載完成後，以滑鼠右鍵按一下**內容**Solution pad 中選取的資料夾**新增 > 新增檔案...** . 如果使用在 Android 上，然後此資料夾會位於下面**資產**Android 特定專案中的資料夾。 如果在 iOS 上，則此資料夾會在 iOS 專案的根目錄中。 瀏覽到位置所在**checkerboard.png**儲存，然後選取 此檔案。 選取此選項，將檔案複製到目錄。

接下來，我們要在其中加入程式碼，以建立我們`Texture2D`執行個體。 首先，新增`Texture2D`的成員身分`Game1`下方`BasicEffect`執行個體：

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

修改`Game1.LoadContent`，如下所示：


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

接下來，修改`DrawGround`方法。 唯一必要的修改是將指派`effect.TextureEnabled`要`true`，並設定`effect.Texture`到`checkerboardTexture`:

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

最後，我們需要修改`Game1.Initialize`方法也將材質座標上我們的頂點：


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

如果我們執行程式碼，我們可以看到我們平面現在會顯示以棋盤式圖樣：

![](part2-images/image8.png "平面現在會顯示以棋盤式圖樣")

## <a name="modifying-texture-coordinates"></a>修改材質座標

使用 MonoGame 標準化材質座標，也就是介於 0 和 1 之間，而不是介於 0 和紋理的寬度或高度的座標。 下圖可協助您以視覺化方式檢視標準化的座標：

![](part2-images/image9.png "此圖表可以協助視覺化標準化的座標")

標準化的材質座標允許紋理調整大小而不需要重寫程式碼，或重新建立模型 （例如.fbx 檔案）。 這可能是因為標準化的座標代表比率，而不是特定像素為單位。 例如，(1，1) 將一律會代表右下角，而不論材質大小為何。

我們可以變更材質座標指派来用於重複次數的單一變數：


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

這會導致重複 20 次的材質：

![](part2-images/image10.png "這會導致重複 20 次的材質")


## <a name="rendering-vertices-with-models"></a>呈現模型的頂點

既然我們平面轉譯正確，我們可以重新加入模型，以同時檢視所有項目。 首先，我們會重新加入模型程式碼，我們`Game1.Draw`方法 （含有已修改的位置）：

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

我們也會建立`Vector3`在`Game1`來代表我們的數位相機的位置。 我們將新增的欄位，在我們`checkerboardTexture`宣告：

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10); 
```

接下來，移除本機`cameraPosition`變數的`DrawModel`方法：

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

同樣地移除本機`cameraPosition`變數的`DrawGround`方法：

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

現在如果我們執行程式碼可以看到模型和基礎在相同的時間：

![](part2-images/image11.png "模型及全新會顯示在相同的時間")

如果我們修改觀景窗位置 （例如藉由增加它的 X 值，這在此情況下將觀景窗向左移動） 的值會影響所打造和模型，我們可以看到：

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

此程式碼是由下列項目所導致：

![](part2-images/image3.png "此程式碼會產生此檢視")

## <a name="summary"></a>總結

本逐步解說示範了如何使用頂點陣列來執行自訂的轉譯。 在此情況下，我們建立棋盤式的 floor 藉由結合我們頂點轉譯使用材質和`BasicEffect`，但顯示的程式碼在此可作為任何 3D 呈現的基礎。 此外，我們也示範了使用相同的場景中的模型，可以混合使用基礎的頂點轉譯。

## <a name="related-links"></a>相關連結

- [棋盤式檔案 （範例）](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [已完成的專案 （範例）](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)
