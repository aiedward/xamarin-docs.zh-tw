---
title: 在 MonoGame 的 3D 座標
description: 了解 3D 座標系統是 3D 遊戲開發的重要步驟。 MonoGame 提供數個定位、 指定，和縮放比例物件在 3D 空間中的類別。
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: dc21228f1daa74a90ff8f0ea346bc01b109f0987
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61229292"
---
# <a name="3d-coordinates-in-monogame"></a>在 MonoGame 的 3D 座標

_了解 3D 座標系統是 3D 遊戲開發的重要步驟。MonoGame 提供數個定位、 指定，和縮放比例物件在 3D 空間中的類別。_

開發 3D 遊戲需要了解如何操作 3D 座標系統中的物件。 本逐步解說將討論如何操作視覺物件 （特別是模型）。 我們會建置控制模型來建立 3D 攝影機類別的概念。

所呈現的概念是來自線性代數，但我們將實用的方法，讓沒有強式數學背景的任何使用者都可以套用這些概念的自己的遊戲。

我們將涵蓋下列主題：

- 建立專案
- 建立機器人的實體
- 移動機器人的實體
- 矩陣乘法
- 建立數位相機實體
- 移動相機與輸入

完成之後，我們必須在專案移動中的圓形和相機，它可以由具備觸控輸入控制機器人以：

![](part3-images/image1.gif "應用程式完成之後，將包含的移動中的圓形和相機，它可以由具備觸控輸入控制機器人的專案")


## <a name="creating-a-project"></a>建立專案

本逐步解說著重於在 3D 空間中移動物件。 我們一開始呈現模型和頂點陣列專案[這可以在這裡找到](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)。 下載完成後，請解壓縮，然後開啟專案，以確定它會執行，而且我們應該會看到下列：

![](part3-images/image2.png "下載完成後，請解壓縮，然後開啟專案，以確定它會執行，而且應該會顯示此檢視")


## <a name="creating-a-robot-entity"></a>建立機器人的實體

我們開始移動機器人解決之前，我們將建立`Robot`類別以包含用於繪圖和移動邏輯。 遊戲開發人員是指這種封裝符合邏輯和資料*實體*。

加入新的空的類別檔案**MonoGame3D**可攜式類別庫 (而不是平台專屬 ModelAndVerts.Android)。 命名**機器人**然後按一下**新增**:

![](part3-images/image3.png "它命名為機器人，然後按一下 [新增]")

修改`Robot`類別，如下所示：

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace MonoGame3D
{
    public class Robot
    {
        Model model;

        public void Initialize(ContentManager contentManager)
        {
            model = contentManager.Load<Model> ("robot");

        }

        // For now we'll take these values in, eventually we'll
        // take a Camera object
        public void Draw(Vector3 cameraPosition, float aspectRatio)
        {
            foreach (var mesh in model.Meshes)
            {
                foreach (BasicEffect effect in mesh.Effects)
                {
                    effect.EnableDefaultLighting ();
                    effect.PreferPerPixelLighting = true;

                    effect.World = Matrix.Identity; 
                    var cameraLookAtVector = Vector3.Zero;
                    var cameraUpVector = Vector3.UnitZ;

                    effect.View = Matrix.CreateLookAt (
                        cameraPosition, cameraLookAtVector, cameraUpVector);

                    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                    float nearClipPlane = 1;
                    float farClipPlane = 200;

                    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);


                }

                // Now that we've assigned our properties on the effects we can
                // draw the entire mesh
                mesh.Draw ();
            }
        }
    }
}
```

`Robot`程式碼是基本上相同的程式碼中`Game1`繪圖`Model`。 在檢閱`Model`載入及繪製，請參閱[本指南使用模型](~/graphics-games/monogame/3d/part1.md)。 我們現在可以移除所有`Model`載入和轉譯來自程式碼`Game1`，並將它取代為`Robot`執行個體：

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        Vector3 cameraPosition = new Vector3(15, 10, 10);

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

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

            robot = new Robot ();
            robot.Initialize (Content);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            robot.Draw (cameraPosition, aspectRatio);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
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
    }
}
```

如果我們執行程式碼現在我們就必須使用只有一個機器人大多在最低限度值所繪製的場景：

![](part3-images/image4.png "如果現在執行的程式碼時，應用程式會顯示場景只有一個機器人所繪製大多在最低限度值")

## <a name="moving-the-robot"></a>移動機器人

既然我們已經`Robot`類別中，我們可以將移動邏輯新增至機器人。 在此情況下，我們只是要根據的遊戲時間是圓形中移動機器人。 因為字元通常可能會回應輸入或人工智慧，但是它所提供的環境，讓我們探索 3D 定位和循環，這是有點不切實際的實作實際的遊戲。

我們需要從外部的唯一資訊`Robot`類別是目前的遊戲時間。 我們會新增`Update`方法會採用`GameTime`參數。 這`GameTime`參數會用來遞增的角度變數，我們將用來判斷機器人的最後一個位置。

首先，我們將在其中加入角度欄位`Robot`類別下`model`欄位：

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 現在我們可以增加此值`Update`函式：

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

我們需要先確定`Update`方法從呼叫`Game1.Update`:

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

當然，此時 [角度] 欄位不做任何事 – 我們需要撰寫程式碼來使用它。 我們要修改`Draw`方法，讓我們可以計算世界`Matrix`專用方法中： 

```csharp
public void Draw(Vector3 cameraPosition, float aspectRatio)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            // We’ll be doing our calculations here...
            effect.World = GetWorldMatrix();

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }

        mesh.Draw ();
    }
}
```

接下來，我們將實作`GetWorldMatrix`方法中的`Robot`類別：

```csharp
Matrix GetWorldMatrix()
{
    const float circleRadius = 8;
    const float heightOffGround = 3;

    // this matrix moves the model "out" from the origin
    Matrix translationMatrix = Matrix.CreateTranslation (
        circleRadius, 0, heightOffGround);

    // this matrix rotates everything around the origin
    Matrix rotationMatrix = Matrix.CreateRotationZ (angle);

    // We combine the two to have the model move in a circle:
    Matrix combined = translationMatrix * rotationMatrix;

    return combined;
}
```

執行此程式碼的結果會導致在圓形中移動機器人：

![](part3-images/image5.gif "執行此程式碼會在圓形中移動機器人")

## <a name="matrix-multiplication"></a>矩陣乘法

上述程式碼會藉由建立旋轉機器人`Matrix`在`GetWorldMatrix`方法。 `Matrix`結構包含 16 的浮點值可以用來平移 （組位置）、 旋轉和縮放 （設定大小）。 當我們將指派`effect.World`屬性中，我們會告訴基礎的呈現系統如何放置、 大小及熟悉任何我們剛好在繪圖 (`Model`或從頂點的幾何)。 

幸運的是，`Matrix`結構包含數個方法可簡化建立一般類型的矩陣。 在上述程式碼中使用的第一個是`Matrix.CreateTranslation`。 數學術語 「*翻譯*可產生一個點 （或在我們的案例，模型） 中的作業是指從某個位置移動到另一個不需要任何其他修改 （例如旋轉或調整大小）。 此函數會採用轉譯的 X、 Y 和 Z 值。 如果我們檢視由上而下，從我們場景我們`CreateTranslation`（以隔離） 的方法會執行下列：

![](part3-images/image6.png "在隔離的 CreateTranslation 方法來執行這個動作")

我們建立的第二個矩陣是以一個旋轉矩陣使用`CreateRotationZ`矩陣。 這是其中一種方法可用來建立循環：

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

每個方法會建立一個旋轉矩陣，需指定軸旋轉。 在我們的案例中，我們要輪替繞 Z 軸，指向 「 向上 」。 下列可協助視覺化方式根據座標軸的旋轉運作：

![](part3-images/image7.png "這可協助視覺化方式根據座標軸的旋轉運作方式")

我們也使用`CreateRotationZ`方法，使用 [角度] 欄位，即可遞增一段時間，因為我們`Update`所呼叫的方法。 結果是`CreateRotationZ`方法會讓我們準備機器人即可軌跡原點為中心，經過一段時間。

最後一行程式碼會將兩個矩陣結合成一個：

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

這稱為矩陣相乘，運作方式稍有不同於一般的乘法。 *交替屬性乘法*狀態的乘法運算中的數字順序不會變更結果。 也就是 3 * 4 就相當於 4 * 3。 矩陣相乘不同，因為它不可以交替。 也就是上述程式碼可以讀取做為 「 套用 translationMatrix 移動模型，然後藉由套用 rotationMatrix 旋轉的所有項目 」。 我們無法將視覺化的方式，上述程式碼影響的位置和旋轉，如下所示：

![](part3-images/image8.png "視覺效果 pf，上述程式碼會影響的位置和旋轉的方式")

若要協助您了解矩陣相乘的順序可能會如何影響結果，請考慮的下列項目，在已反轉矩陣相乘：

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

上述程式碼會先旋轉模型就緒，然後將其轉譯：

![](part3-images/image9.png "上述程式碼會先旋轉模型就緒，然後將其轉譯")

如果我們使用反向乘法執行程式碼，我們會發現，旋轉會套用第一次，因為它只會影響模型的方向和模型的位置維持不變。 換句話說，此模型會就地旋轉：

![](part3-images/image10.gif "模型會就地旋轉")

## <a name="creating-the-camera-entity"></a>建立數位相機實體

`Camera`實體會包含所有的邏輯，來執行輸入為基礎的移動，並提供內容上指派屬性`BasicEffect`類別。

首先我們會實作靜態攝影機 （沒有輸入為基礎的移動），並將它整合到我們現有的專案。 將新類別加入**MonoGame3D**可攜式類別庫 (相同專案`Robot.cs`) 並將它命名**相機**。 以下列程式碼取代檔案的內容：

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Camera
    {
        // We need this to calculate the aspectRatio
        // in the ProjectionMatrix property.
        GraphicsDevice graphicsDevice;

        Vector3 position = new Vector3(15, 10, 10);

        public Matrix ViewMatrix
        {
            get
            {
                var lookAtVector = Vector3.Zero;
                var upVector = Vector3.UnitZ;

                return Matrix.CreateLookAt (
                    position, lookAtVector, upVector);
            }
        }

        public Matrix ProjectionMatrix
        {
            get
            {
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                float nearClipPlane = 1;
                float farClipPlane = 200;
                float aspectRatio = graphicsDevice.Viewport.Width / (float)graphicsDevice.Viewport.Height;

                return Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
            }
        }

        public Camera(GraphicsDevice graphicsDevice)
        {
            this.graphicsDevice = graphicsDevice;
        }

        public void Update(GameTime gameTime)
        {
            // We'll be doing some input-based movement here
        }
    }
}
```

上述程式碼是從程式碼非常類似`Game1`並`Robot`其中上指派矩陣`BasicEffect`。 

現在我們可以整合新`Camera`類別到我們現有的專案。 首先，我們要在其中修改`Robot`類別，以充分`Camera`執行個體在其`Draw`方法，它會消除許多重複的程式碼。 取代`Robot.Draw`以下列方法：

```csharp
public void Draw(Camera camera)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = GetWorldMatrix();
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;
        }

        mesh.Draw ();
    }
}
```

接下來，修改`Game1.cs`檔案：

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        // New camera code
        Camera camera;

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

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

            robot = new Robot ();
            robot.Initialize (Content);

            // New camera code
            camera = new Camera (graphics.GraphicsDevice);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            robot.Update (gameTime);
            // New camera code
            camera.Update (gameTime);
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            // New camera code
            robot.Draw (camera);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            // New camera code
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;

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
    }
}
```

若要修改`Game1`來自舊版本 (這會識別與`// New camera code`) 是：

- `Camera` 中的欄位 `Game1`
- `Camera` 在具現化 `Game1.Initialize`
- `Camera.Update` 呼叫 `Game1.Update`
- `Robot.Draw` 現在只需要`Camera`參數
- `Game1.Draw` 現在會使用`Camera.ViewMatrix`和 `Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>移動相機與輸入

到目前為止，我們已新增`Camera`實體，但還沒有做任何動作，以變更執行階段行為。 我們會將行為可讓使用者加入至：

- 觸控開啟觀景窗朝向左邊畫面左下的方
- 觸控式螢幕右方之開啟右邊相機
- 觸控式螢幕上以向前移動觀景窗的中心

### <a name="making-lookat-relative"></a>讓支票相對

首先，我們會更新`Camera`類別，以包含`angle`欄位會用來將方向設定`Camera`面向。 目前，我們`Camera`判斷它透過本機對著方向`lookAtVector`，為指派給`Vector3.Zero`。 換句話說，我們`Camera`一律會對準其原點。 如果相機有移動，然後也會變更觀景窗會面臨的角度：

![](part3-images/image11.gif "如果相機有移動，然後在面向觀景窗的角度也會變更")

我們希望`Camera`到至少會遇到相同的方向，不論其位置 – 我們實作的邏輯旋轉之前`Camera`使用輸入。 第一項變更將會調整`lookAtVector`變數以我們目前的位置為基礎，而不是查看絕對位置：

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    // Let's start at X = 0 so we're looking at things head-on
    Vector3 position = new Vector3(0, 20, 10);

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

這會導致`Camera`直入世界。 請注意，初始`position`值已修改成`(0, 20, 10)`因此`Camera`置中 X 軸上。 執行遊戲的顯示：

![](part3-images/image12.png "執行遊戲，會顯示此檢視")

### <a name="creating-an-angle-variable"></a>建立變數的角度

`lookAtVector`變數會控制在檢視我們的數位相機的角度。 目前固定為檢視下負 Y 軸，並向下稍微傾斜 (從`-.5f`Z 值)。 我們將建立`angle`變數會用來調整`lookAtVector`屬性。 

在本逐步解說稍早章節中，我們示範矩陣可用來旋轉如何繪製物件。 我們也可以使用矩陣來旋轉等的向量`lookAtVector`使用`Vector3.Transform`方法。 

新增`angle`欄位，然後修改`ViewMatrix`屬性，如下所示：

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    Vector3 position = new Vector3(0, 20, 10);

    float angle;

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            // We'll create a rotation matrix using our angle
            var rotationMatrix = Matrix.CreateRotationZ (angle);
            // Then we'll modify the vector using this matrix:
            lookAtVector = Vector3.Transform (lookAtVector, rotationMatrix);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

### <a name="reading-input"></a>讀取輸入

我們`Camera`實體現在可完全控制透過其位置和角度變數 – 我們只需要加以變更，根據輸入。

首先，我們會得到`TouchPanel`來尋找使用者會觸碰螢幕的狀態。 如需有關使用`TouchPanel`類別，請參閱[TouchPanel API 參考](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel)。

如果在使用者觸碰在左邊的第三個，則我們會調整`angle`值，因此`Camera`左旋轉並在使用者觸碰上正確的第三個，如果我們將旋轉其他方式。 如果在使用者觸碰在中間的第三個畫面上，則我們將繼續`Camera`轉寄。

首先，新增 using 陳述式來限定`TouchPanel`並`TouchCollection`中的類別`Camera.cs`:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

接下來，修改`Update`方法，以讀取 [觸控] 面板並調整`angle`和`position`變數適當：

```csharp
public void Update(GameTime gameTime)
{
    TouchCollection touchCollection = TouchPanel.GetState();

    bool isTouchingScreen = touchCollection.Count > 0;
    if (isTouchingScreen)
    {
        var xPosition = touchCollection [0].Position.X;

        float xRatio = xPosition / (float)graphicsDevice.Viewport.Width;

        if (xRatio < 1 / 3.0f)
        {
            angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
        else if (xRatio < 2 / 3.0f)
        {
            var forwardVector = new Vector3 (0, -1, 0);

            var rotationMatrix = Matrix.CreateRotationZ (angle);
            forwardVector = Vector3.Transform (forwardVector, rotationMatrix);

            const float unitsPerSecond = 3;

            this.position += forwardVector * unitsPerSecond *
                (float)gameTime.ElapsedGameTime.TotalSeconds ;
        }
        else
        {
            angle -= (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
    }
}
```

現在`Camera`回應觸控輸入：

![](part3-images/image1.gif "現在將觀景窗會回應觸控輸入")

藉由呼叫的 Update 方法開始`TouchPanel.GetState`，它會傳回修飾的集合。 雖然`TouchPanel.GetState`可以傳回多個觸控點，我們只會擔心第一個為了簡單起見。

如果使用者觸控螢幕，程式碼就會檢查有第一個觸控在置中 或向右第三個畫面的左側。 左邊和右邊的三等份旋轉觀景窗增加或減少`angle`變數根據`TotalSeconds`值 （如此遊戲的行為相同，不論畫面播放速率）。

如果在使用者觸碰 center 第三個畫面，然後將觀景窗會向前移動。 做法是先取得的前向量，一開始定義為指向負 Y 軸，然後使用建立矩陣的旋轉`Matrix.CreateRotationZ`而`angle`值。 最後`forwardVector`套用至`position`使用`unitsPerSecond`係數。

## <a name="summary"></a>總結

本逐步解說將說明如何移動和旋轉`Models`在 3D 空間使用`Matrices`而`BasicEffect.World`屬性。 這種形式的移動提供 3D 遊戲中移動物件的基礎。 本逐步解說也將說明如何實作`Camera`檢視從任何位置和角度世界的實體。

## <a name="related-links"></a>相關連結

- [MonoGame API 連結](http://www.monogame.net/documentation/?page=api)
- [已完成的專案 （範例）](https://developer.xamarin.com/samples/monodroid/MonoGame3DCamera/)
