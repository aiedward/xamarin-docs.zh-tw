---
title: MonoGame 中的3D 座標
description: 瞭解3D 座標系統是開發3D 遊戲的一個重要步驟。 MonoGame 提供數個類別來定位、調整和縮放3D 空間中的物件。
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 54a4c6e32059b6ff32b3a93abf5fd30c65f16b5f
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936626"
---
# <a name="3d-coordinates-in-monogame"></a>MonoGame 中的3D 座標

_瞭解3D 座標系統是開發3D 遊戲的一個重要步驟。MonoGame 提供數個類別來定位、調整和縮放3D 空間中的物件。_

開發3D 遊戲時，必須先瞭解如何操作3D 座標系統中的物件。 本逐步解說將涵蓋如何操作視覺物件（特別是模型）。 我們將建立控制模型以建立3D 攝影機類別的概念。

呈現的概念來自線性代數，但我們將採取實用的方法，讓沒有強式運算背景的任何使用者都能在自己的遊戲中套用這些概念。

我們將涵蓋下列主題：

- 建立專案
- 建立機器人實體
- 移動機器人實體
- 矩陣乘法
- 建立相機實體
- 移動具有輸入的相機

完成後，我們會有一個專案，其中的機器人在圓形和相機上移動，可由觸控輸入控制：

![完成後，應用程式將會包含一個專案，其中的機器人會在圓形中移動，而相機則可透過觸控輸入來控制](part3-images/image1.gif)

## <a name="creating-a-project"></a>建立專案

本逐步解說著重于在3D 空間中移動物件。 我們會從用於轉譯模型和頂點陣列的專案開始，[可在這裡找到](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelsandvertsmg/)。 下載後，解壓縮並開啟專案以確定它會執行，而且應該會看到下列內容：

![下載後，解壓縮並開啟專案以確定它會執行，而且應該會顯示此視圖](part3-images/image2.png)

## <a name="creating-a-robot-entity"></a>建立機器人實體

開始移動機器人之前，我們會建立一個 `Robot` 類別，以包含繪製和移動的邏輯。 遊戲開發人員指的是將邏輯和資料封裝為*實體*。

將新的空白類別檔案新增至**MonoGame3D**的可移植類別庫（而不是平臺特定的 ModelAndVerts）。 將它命名為**機器人**，然後按一下 [**新增**]：

![為機器人命名，然後按一下 [新增]](part3-images/image3.png)

修改 `Robot` 類別，如下所示：

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

程式 `Robot` 代碼在中的程式碼基本上與 `Game1` 繪製相同 `Model` 。 如需有關 `Model` 載入和繪製的評論，請參閱[使用模型的這份指南](~/graphics-games/monogame/3d/part1.md)。 我們現在可以從移除所有的 `Model` 載入和呈現程式碼 `Game1` ，並以實例取代它 `Robot` ：

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

如果現在執行程式碼，就會有一個只有一個機器人的場景，其中大部分都是在樓層下繪製：

![如果現在執行程式碼，應用程式將會顯示一個場景，其中只有一個自動繪製在地面底下的機器人](part3-images/image4.png)

## <a name="moving-the-robot"></a>移動機器人

現在我們已經有一個 `Robot` 類別，可以將移動邏輯新增到機器人。 在此情況下，我們只會根據遊戲時間，讓機器人移到圓形中。 這對實際遊戲而言有點不切實際，因為字元通常會回應輸入或人工智慧，但它會提供環境讓我們探索3D 定位和旋轉。

我們從類別外部所需要的唯一資訊 `Robot` 就是目前的遊戲時間。 我們會新增 `Update` 將接受參數的方法 `GameTime` 。 這個 `GameTime` 參數將用來遞增角度變數，我們將用它來判斷機器人的最終位置。

首先，我們會將 [角度] 欄位新增至 [欄位] 底下的 `Robot` 類別 `model` ：

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 現在，我們可以在函式中遞增此值 `Update` ：

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

我們需要確定 `Update` 已從呼叫方法 `Game1.Update` ：

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

當然，[角度] 欄位不會執行任何工作，我們需要撰寫程式碼來使用它。 我們會修改 `Draw` 方法，讓我們可以 `Matrix` 在專用的方法中計算世界： 

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

接下來，我們將 `GetWorldMatrix` 在類別中執行 `Robot` 方法：

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

執行此程式碼的結果會導致機器人移動到圓形中：

![執行此程式碼會導致機器人在圓形中移動](part3-images/image5.gif)

## <a name="matrix-multiplication"></a>矩陣乘法

上述程式碼會 `Matrix` 在方法中建立，以旋轉機器人 `GetWorldMatrix` 。 `Matrix`結構包含16個 float 值，可用來轉譯（設定位置）、旋轉和縮放（設定大小）。 當我們指派 `effect.World` 屬性時，我們會告訴基礎轉譯系統如何定位、調整大小，以及決定要繪製的內容（頂點的 a `Model` 或 geometry）。 

幸好 `Matrix` 結構包含一些方法，可簡化常見類型矩陣的建立。 上述程式碼中第一個使用的是 `Matrix.CreateTranslation` 。 「數學詞彙*轉譯*」指的是一種作業，它會產生一個位置（或在我們的案例中為模型）在不進行任何其他修改的情況下（例如，旋轉或調整大小）從一個位置移到另一個 函式會接受 X、Y 和 Z 值來進行轉譯。 如果我們從上而下查看我們的場景，我們 `CreateTranslation` 的方法（隔離）會執行下列動作：

![隔離中的 CreateTranslation 方法會執行此動作](part3-images/image6.png)

我們所建立的第二個矩陣是使用矩陣的旋轉矩陣 `CreateRotationZ` 。 這是可以用來建立旋轉的三種方法之一：

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

每個方法都會藉由旋轉指定的軸來建立旋轉矩陣。 在我們的案例中，我們會旋轉 Z 軸，其指向「向上」。 下列內容有助於視覺化以軸為基礎的旋轉如何運作：

![這有助於視覺化以軸為基礎的旋轉如何運作](part3-images/image7.png)

我們也會使用 `CreateRotationZ` 方法搭配 [角度] 欄位，因為我們呼叫的方法會隨著時間而遞增 `Update` 。 結果是，此 `CreateRotationZ` 方法會使機器人在一段時間後，將其移到原點周圍。

最後一行程式碼會將這兩個矩陣結合成一個：

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

這稱為矩陣乘法，其運作方式與一般乘法略有不同。 *乘法的交換屬性*會指出乘法運算中數位的順序不會變更結果。 也就是，3 \* 4 相當於 4 \* 3。 矩陣乘法不同之處在于它不是可交換的。 也就是說，可以將上述程式碼讀取為「套用 translationMatrix 來移動模型，然後藉由套用 rotationMatrix 來旋轉所有專案」。 我們可以將上述線條影響位置和旋轉的方式視覺化，如下所示：

![視覺效果 pf 上方線條影響位置和旋轉的方式](part3-images/image8.png)

為了協助您瞭解矩陣乘法的順序會如何影響結果，請考慮下列各項，其中矩陣乘法會反轉：

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

上述程式碼會先就地旋轉模型，然後加以轉譯：

![上述程式碼會先就地旋轉模型，然後將它轉譯](part3-images/image9.png)

如果我們使用反向乘法來執行程式碼，我們會注意到，因為旋轉會先套用，它只會影響模型的方向，而且模型的位置會保持不變。 換句話說，此模型會就地旋轉：

![模型就地旋轉](part3-images/image10.gif)

## <a name="creating-the-camera-entity"></a>建立相機實體

`Camera`實體會包含執行以輸入為基礎之移動所需的所有邏輯，並提供屬性來指派類別的屬性 `BasicEffect` 。

首先，我們將執行靜態攝影機（不以輸入為基礎的移動），並將其整合到現有的專案中。 將新類別新增至**MonoGame3D**可移植類別庫（與相同的專案 `Robot.cs` ），並將它命名為**攝影機**。 使用下列程式碼取代檔案的內容：

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

上述程式碼非常類似于的程式碼 `Game1` ，並會 `Robot` 在上指派矩陣 `BasicEffect` 。 

現在我們可以將新 `Camera` 類別整合到現有的專案中。 首先，我們將修改 `Robot` 類別 `Camera` ，以在其方法中採用實例 `Draw` ，這將會消除許多重複的程式碼。 將方法取代為 `Robot.Draw` 下列內容：

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

接下來，修改檔案 `Game1.cs` ：

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

`Game1`先前版本（以識別）的對的修改 `// New camera code` 如下：

- `Camera`中的欄位`Game1`
- `Camera`中的具現化`Game1.Initialize`
- `Camera.Update`在中呼叫`Game1.Update`
- `Robot.Draw`現在接受 `Camera` 參數
- `Game1.Draw`現在會使用 `Camera.ViewMatrix` 和`Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>移動具有輸入的相機

到目前為止，我們已新增 `Camera` 實體，但尚未執行任何動作來變更執行時間行為。 我們會新增行為，讓使用者能夠：

- 觸碰螢幕左邊，將相機向左旋轉
- 觸碰螢幕右側以將相機轉向右方
- 觸碰螢幕中央以將相機向前移動

### <a name="making-lookat-relative"></a>使 lookAt 相對

首先，我們會更新 `Camera` 類別，以包含 `angle` 將用來設定所面向方向的欄位 `Camera` 。 目前，我們會 `Camera` 透過指派給的本機來決定其所面向的方向 `lookAtVector` `Vector3.Zero` 。 換句話說，我們一律會 `Camera` 查看原點。 如果相機移動，則相機面臨的角度也會變更：

![如果相機移動，則相機面臨的角度也會改變](part3-images/image11.gif)

我們希望 `Camera` 無論位置為何，都要有相同的方向–至少直到我們執行使用輸入旋轉的邏輯為止 `Camera` 。 第一項變更是將變數調整 `lookAtVector` 為根據我們目前的位置，而不是查看絕對位置：

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

這會導致 `Camera` 世界上的即時觀賞。 請注意，初始值已 `position` 修改為， `(0, 20, 10)` 因此是以 `Camera` X 軸為中心。 執行遊戲會顯示：

![執行遊戲會顯示此視圖](part3-images/image12.png)

### <a name="creating-an-angle-variable"></a>建立角度變數

`lookAtVector`變數可控制我們的攝影機所觀賞的角度。 目前已修正，以向下查看負 Y 軸，並稍微傾斜（從 `-.5f` Z 值）。 我們將建立 `angle` 用來調整屬性的變數 `lookAtVector` 。 

在本逐步解說的先前章節中，我們示範了矩陣可以用來旋轉物件的繪製方式。 我們也可以使用矩陣來旋轉向量，例如 `lookAtVector` 使用 `Vector3.Transform` 方法。 

新增 `angle` 欄位，並修改 `ViewMatrix` 屬性，如下所示：

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

我們 `Camera` 的實體現在可以透過其位置和角度變數完全控制，我們只需要根據輸入來變更它們。

首先，我們會取得 `TouchPanel` 狀態，以找出使用者觸及螢幕的位置。 如需使用類別的詳細資訊 `TouchPanel` ，請參閱[觸控式螢幕 API 參考](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel)。

如果使用者觸及左邊的第三個，我們會調整 `angle` 值，使其 `Camera` 向左旋轉，如果使用者在協力廠商觸及，則會以另一種方式旋轉。 如果使用者在畫面的中間協力廠商觸及，就會 `Camera` 向前移動。

首先，新增 using 語句以限定中的 `TouchPanel` 和 `TouchCollection` 類別 `Camera.cs` ：

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

接下來，修改 `Update` 方法以讀取觸控面板，並 `angle` `position` 適當地調整和變數：

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

現在 `Camera` 會回應觸控輸入：

![現在相機會回應觸控輸入](part3-images/image1.gif)

Update 方法一開始會呼叫 `TouchPanel.GetState` ，它會傳回觸控的集合。 雖然 `TouchPanel.GetState` 可以傳回多個觸控點，但為了簡單起見，我們只會擔心第一個。

如果使用者觸及螢幕，則程式碼會檢查第一個觸控是在螢幕的左邊、中間或右方協力廠商。 左右三分之二會根據值來增加或減少變數以旋轉相機 `angle` `TotalSeconds` （如此一來，不論畫面的速率為何，遊戲的行為都相同）。

如果使用者觸及畫面的第三部，則相機會向前移動。 首先是藉由取得正向向量（一開始定義為指向負 Y 軸）來完成這項作業，然後使用以建立的矩陣旋轉， `Matrix.CreateRotationZ` 以及 `angle` 值。 最後， `forwardVector` 會套用至 `position` 使用 `unitsPerSecond` 係數。

## <a name="summary"></a>總結

本逐步解說將說明如何 `Models` 使用和屬性，在3d 空間中移動和旋轉 `Matrices` `BasicEffect.World` 。 這種形式的移動提供了在3D 遊戲中移動物件的基礎。 本逐步解說也會說明如何執行 `Camera` 實體，從任何位置和角度來觀看世界。

## <a name="related-links"></a>相關連結

- [MonoGame API 連結](http://www.monogame.net/documentation/?page=api)
- [完成的專案（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/monogame3dcamera)
