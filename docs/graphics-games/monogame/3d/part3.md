---
title: 3D MonoGame 座標
description: 了解 3D 座標系統是開發 3D 遊戲的重要步驟。 MonoGame 提供數個位置、 指定的方向，以及調整在 3D 空間中的物件類別。
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 0273b4f13c91fd766530ff7c0976096de3239dc5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="3d-coordinates-in-monogame"></a>3D MonoGame 座標

_了解 3D 座標系統是開發 3D 遊戲的重要步驟。MonoGame 提供數個位置、 指定的方向，以及調整在 3D 空間中的物件類別。_

開發 3D 遊戲需要了解如何操作 3D 座標系統中的物件。 這個逐步解說涵蓋如何管理視覺物件 （特別是模型）。 我們會建置控制模型來建立 3D 相機類別的概念。

所呈現的概念是來自線性代數中，但我們將相當實用的方法，讓沒有強式數學背景的任何使用者都可以套用這些概念，在他們自己的遊戲。

我們會涵蓋下列主題：

 - 建立專案
 - 建立機器人實體
 - 移動機器人實體
 - 矩陣乘法
 - 建立數位相機實體
 - 移動與輸入相機

一旦完成之後，我們會與移動中的圓形和具備觸控輸入可以受控制的相機是機器人擁有專案：

![](part3-images/image1.gif "應用程式完成之後，會包含機器人移動中的圓形和相機具備觸控輸入可以受控制的專案")


# <a name="creating-a-project"></a>建立專案

本逐步解說著重於在 3D 空間中移動物件。 我們一開始會與專案的轉譯模型和頂點陣列[這可以在這裡找到](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)。 下載之後，解壓縮，然後開啟專案，以確定它會執行，而且應該看到下列項目：

![](part3-images/image2.png "下載之後，解壓縮，並開啟專案，以確定它會執行，而且應該會顯示此檢視")


# <a name="creating-a-robot-entity"></a>建立機器人實體

開始移動周圍我們機器人之前，我們將建立`Robot`類別包含用於繪圖和移動邏輯。 此封裝的邏輯和資料做為參考遊戲開發人員*實體*。

加入新的空的類別檔案**MonoGame3D**可攜式類別庫 (不平台專屬 ModelAndVerts.Android)。 命名**機器人**按一下**新增**:

![](part3-images/image3.png "機器人將它命名，並按一下 [新增]")

修改`Robot`類別，如下所示：


```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace MonoGame3D
{
    public class Robot
    {
        Model model;

        public void Initialize(ContentManager contentManager)
        {
            model = contentManager.Load<Model> ("robot");

        }

        // For now we'll take these values in, eventually we'll
        // take a Camera object
        public void Draw(Vector3 cameraPosition, float aspectRatio)
        {
            foreach (var mesh in model.Meshes)
            {
                foreach (BasicEffect effect in mesh.Effects)
                {
                    effect.EnableDefaultLighting ();
                    effect.PreferPerPixelLighting = true;

                    effect.World = Matrix.Identity; 
                    var cameraLookAtVector = Vector3.Zero;
                    var cameraUpVector = Vector3.UnitZ;

                    effect.View = Matrix.CreateLookAt (
                        cameraPosition, cameraLookAtVector, cameraUpVector);
                        
                    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                    float nearClipPlane = 1;
                    float farClipPlane = 200;

                    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);


                }

                // Now that we've assigned our properties on the effects we can
                // draw the entire mesh
                mesh.Draw ();
            }
        }
    }
}
```

`Robot`是基本上相同的程式碼中`Game1`繪圖`Model`。 在檢閱`Model`載入及繪製，請參閱[本指南使用模型](~/graphics-games/monogame/3d/part1.md)。 現在我們可以移除所有`Model`載入和轉譯程式碼從`Game1`，並將它取代為`Robot`執行個體：


```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        Vector3 cameraPosition = new Vector3(15, 10, 10);

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;
                        
            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

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

            robot = new Robot ();
            robot.Initialize (Content);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            robot.Draw (cameraPosition, aspectRatio);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
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
    }                                          
}
```

當我們執行程式碼現在我們將會有與它繪製大部分在最低限度下只有一個機器人場景：

![](part3-images/image4.png "如果程式碼現在執行時，應用程式將會顯示場景的繪製大部分在最低限度下只有一個機器人")


# <a name="moving-the-robot"></a>移動機器人

現在，我們已經`Robot`類別中，我們可以將移動邏輯加入至機器人。 在此情況下，我們只是要移動是圓形中有遊戲時間根據機器人。 這是有點不太實用實作實際的遊戲，因為字元通常可能回應輸入或人工地智慧，但是它所提供的環境，讓我們來瀏覽 3D 位置和旋轉。

我們需要從之外的唯一資訊`Robot`類別是目前的遊戲時間。 我們會將新增`Update`方法，以便前往`GameTime`參數。 這`GameTime`參數將用來遞增的角度變數，我們會用來判斷機器人的最後一個位置。

首先，我們會將新增到 [角度] 欄位`Robot`類別下`model`欄位：


```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ... 
```

 現在我們可以增加此值在`Update`函式：


```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

我們需要確定`Update`方法從呼叫`Game1.Update`:


```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
} 
```

當然，此時 [角度] 欄位不做任何動作 – 我們必須撰寫程式碼以使用它。 我們會修改`Draw`方法，讓我們可以計算世界`Matrix`專用方法中： 


```csharp
public void Draw(Vector3 cameraPosition, float aspectRatio)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            // We’ll be doing our calculations here...
            effect.World = GetWorldMatrix();

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);
                
            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }

        mesh.Draw ();
    }
} 
```

接下來，我們會實作`GetWorldMatrix`方法中的`Robot`類別：


```csharp
Matrix GetWorldMatrix()
{
    const float circleRadius = 8;
    const float heightOffGround = 3;
    
    // this matrix moves the model "out" from the origin
    Matrix translationMatrix = Matrix.CreateTranslation (
        circleRadius, 0, heightOffGround);

    // this matrix rotates everything around the origin
    Matrix rotationMatrix = Matrix.CreateRotationZ (angle);

    // We combine the two to have the model move in a circle:
    Matrix combined = translationMatrix * rotationMatrix;

    return combined;
} 
```

執行此程式碼的結果會導致機器人移動是圓形中有：

![](part3-images/image5.gif "機器人移動是圓形中有在執行此程式碼結果")


# <a name="matrix-multiplication"></a>矩陣乘法

上述程式碼會藉由建立旋轉機器人`Matrix`中`GetWorldMatrix`方法。 `Matrix`結構包含 16 的浮點值可以用來轉譯 （組位置）、 旋轉和縮放 （設定大小）。 當指派`effect.World`屬性，我們會告訴呈現系統如何位置、 大小及設定任何基礎我們就可能發生在繪圖 (`Model`或從頂點幾何)。 

幸運的是，`Matrix`結構包含數種方法簡化建立一般類型的矩陣。 在上述程式碼中使用的第一個是`Matrix.CreateTranslation`。 數學術語 「*轉譯*產生點 （或在我們的案例，模型） 中的作業是指不同的位置移動到另一個不需要任何其他修改 （例如旋轉或調整大小）。 此函數會採用轉譯的 X、 Y 和 Z 值。 如果我們檢視從由上而下，我們場景我們`CreateTranslation`（以隔離） 的方法會執行下列：

![](part3-images/image6.png "在隔離的 CreateTranslation 方法來執行這個動作")

我們建立的第二個矩陣的旋轉矩陣使用`CreateRotationZ`矩陣。 這是可以用來建立循環的三種方法之一：

 - `CreateRotationX`
 - `CreateRoationY`
 - `CreateRotationZ`

每一種方法指定軸的旋轉建立一個旋轉矩陣。 在此案例中，我們要輪替繞 Z 軸，指向 「 向上 」。 下列可協助視覺化方式根據軸旋轉的運作：

![](part3-images/image7.png "這可協助視覺化方式根據軸旋轉的運作")

我們也正在使用`CreateRotationZ`方法 [角度] 欄位，會累加一段時間，因為我們`Update`所呼叫方法。 結果是`CreateRotationZ`方法會讓我們來隨著時間推移的原點 orbit 機器人。

最後一行程式碼會將兩個矩陣結合成一個：


```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

這被指矩陣乘法，運作方式稍有不同於一般乘法。 *交替屬性乘法*狀態的乘法運算中的數字順序不會變更結果。 也就是 3 * 4 就相當於 4 * 3。 矩陣乘法不同，不可以交替。 也就是上述列可讀取為 「 套用 translationMatrix 移動模型，然後藉由套用 rotationMatrix 旋轉的所有項目 」。 我們無法將視覺化上行影響的位置和旋轉，如下所示的方式：

![](part3-images/image8.png "視覺效果 pf 上行影響的位置和旋轉的方式")

若要協助您了解矩陣乘法的順序可能會如何影響結果，請考慮的下列項目，會在被反轉矩陣乘法：


```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

上述程式碼會先旋轉模型就地，，然後將它解譯：

![](part3-images/image9.png "上述程式碼會先旋轉模型就地，然後將它轉譯")

如果我們以反向的乘法執行程式碼，我們會發現旋轉會先套用，因為它只會影響模型的方向和模型的位置維持不變。 換句話說，此模型旋轉就地：

![](part3-images/image10.gif "就地旋轉模型")


# <a name="creating-the-camera-entity"></a>建立數位相機實體

`Camera`實體會包含所有的邏輯來執行輸入為基礎的移動，以及提供屬性讓您將內容指派上`BasicEffect`類別。

首先我們會實作靜態的相機 （沒有輸入基礎動作），並將其整合到我們現有的專案。 將新類別加入**MonoGame3D**可攜式類別庫 (相同的專案`Robot.cs`) 並將其命名**相機**。 以下列程式碼取代檔案的內容：


```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Camera
    {
        // We need this to calculate the aspectRatio
        // in the ProjectionMatrix property.
        GraphicsDevice graphicsDevice;

        Vector3 position = new Vector3(15, 10, 10);

        public Matrix ViewMatrix
        {
            get
            {
                var lookAtVector = Vector3.Zero;
                var upVector = Vector3.UnitZ;

                return Matrix.CreateLookAt (
                    position, lookAtVector, upVector);
            }
        }

        public Matrix ProjectionMatrix
        {
            get
            {
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                float nearClipPlane = 1;
                float farClipPlane = 200;
                float aspectRatio = graphicsDevice.Viewport.Width / (float)graphicsDevice.Viewport.Height;
                
                return Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
            }
        }

        public Camera(GraphicsDevice graphicsDevice)
        {
            this.graphicsDevice = graphicsDevice;
        }

        public void Update(GameTime gameTime)
        {
            // We'll be doing some input-based movement here
        }
    }
}
```

上述程式碼是非常類似的程式碼從`Game1`和`Robot`其中上指派矩陣`BasicEffect`。 

現在我們可以整合新`Camera`到我們的現有專案的類別。 首先，我們會修改`Robot`類別`Camera`執行個體中其` Draw `方法，會消除大量複製程式碼。 取代`Robot.Draw`以下列方法：


```csharp
public void Draw(Camera camera)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = GetWorldMatrix();
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;
        }

        mesh.Draw ();
    }
} 
```

接下來，修改`Game1.cs`檔案：


```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        // New camera code
        Camera camera;

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;
                        
            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

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

            robot = new Robot ();
            robot.Initialize (Content);

            // New camera code
            camera = new Camera (graphics.GraphicsDevice);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            robot.Update (gameTime);
            // New camera code
            camera.Update (gameTime);
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            // New camera code
            robot.Draw (camera);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            // New camera code
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;

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
    }
} 
```

若要修改`Game1`來自舊版本 (其中會識別與`// New camera code`) 是：

 - `Camera` 中的欄位 `Game1`
 - `Camera` 在具現化 `Game1.Initialize`
 - `Camera.Update` 呼叫 `Game1.Update`
 - `Robot.Draw` 現在採用`Camera`參數
 - `Game1.Draw` 現在使用`Camera.ViewMatrix`和 `Camera.ProjectionMatrix`


# <a name="moving-the-camera-with-input"></a>移動與輸入相機

目前為止，我們已加入`Camera`實體，但是您尚未這樣做任何動作，以變更執行階段行為。 我們會將行為可讓使用者新增至：

 - 觸控式螢幕開啟方相機的左半部
 - 若要開啟右邊相機螢幕的右側的觸控
 - 觸控向前移動相機螢幕的中央


## <a name="making-lookat-relative"></a>進行支票相對

我們會將第一次更新`Camera`類別，以包含`angle`欄位會用來將方向設定`Camera`遇到。 目前，我們`Camera`決定遇到透過本機方向`lookAtVector`，這指派給`Vector3.Zero`。 換句話說，我們`Camera`一律會尋找原始位置。 如果會相機移動，然後也會變更遇到相機角度：

![](part3-images/image11.gif "如果會相機移動，然後遇到相機角度也會變更")

我們想要`Camera`到至少會遇到相同的方向，不論其位置 – 我們實作的邏輯旋轉直到` Camera `使用輸入。 第一項變更將會是調整`lookAtVector`變數根據我們目前的位置，而不是查看絕對位置：


```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    // Let's start at X = 0 so we're looking at things head-on
    Vector3 position = new Vector3(0, 20, 10);

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    } 
    ...
```

這會導致`Camera`檢視直線上世界。 請注意，初始`position`值已修改成`(0, 20, 10)`所以`Camera`X 軸上置中。 遊戲會顯示在執行：

![](part3-images/image12.png "執行遊戲會顯示此檢視")


## <a name="creating-an-angle-variable"></a>建立變數的角度

`lookAtVector`變數控制正在檢視我們的攝影機角度。 它目前固定為負數的 Y 軸，向檢視並稍微向下傾斜 (從`-.5f`Z 值)。 我們將建立`angle`變數會用來調整`lookAtVector`屬性。 

在本逐步解說稍早章節介紹矩陣可以用於旋轉物件的繪製方式。 我們也可以使用矩陣來旋轉像向量`lookAtVector`使用`Vector3.Transform`方法。 

新增`angle`欄位，然後修改`ViewMatrix`屬性，如下所示：


```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    Vector3 position = new Vector3(0, 20, 10);

    float angle;

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            // We'll create a rotation matrix using our angle
            var rotationMatrix = Matrix.CreateRotationZ (angle);
            // Then we'll modify the vector using this matrix:
            lookAtVector = Vector3.Transform (lookAtVector, rotationMatrix);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    } 
    ...
```


## <a name="reading-input"></a>讀取輸入

我們`Camera`實體現在完全透過可以控制其位置和角度變數 – 我們只需要根據輸入變更它們。

首先，我們會讓`TouchPanel`狀態尋找其中的使用者觸控螢幕。 如需有關使用`TouchPanel`類別，請參閱[TouchPanel 應用程式開發介面參考](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel)。

如果使用者會在左邊的第三個接觸，則我們將調整`angle`值而`Camera`靠左、 一個圖示旋轉，如果使用者會在右邊的第三個接觸，我們會旋轉另一種方式。 如果使用者在中間的第三個畫面上，觸碰接著`Camera`向前復原。

首先，加入 using 陳述式來限定`TouchPanel`和`TouchCollection`中的類別`Camera.cs`:


```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

接下來，修改`Update`方法來讀取 [觸控] 面板，並調整`angle`和`position`變數適當地：


```csharp
public void Update(GameTime gameTime)
{
    TouchCollection touchCollection = TouchPanel.GetState();

    bool isTouchingScreen = touchCollection.Count > 0;
    if (isTouchingScreen)
    {
        var xPosition = touchCollection [0].Position.X;

        float xRatio = xPosition / (float)graphicsDevice.Viewport.Width;

        if (xRatio < 1 / 3.0f)
        {
            angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
        else if (xRatio < 2 / 3.0f)
        {
            var forwardVector = new Vector3 (0, -1, 0);

            var rotationMatrix = Matrix.CreateRotationZ (angle);
            forwardVector = Vector3.Transform (forwardVector, rotationMatrix);

            const float unitsPerSecond = 3;

            this.position += forwardVector * unitsPerSecond *
                (float)gameTime.ElapsedGameTime.TotalSeconds ;
        }
        else
        {
            angle -= (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
    }
} 
```

現在`Camera`觸控輸入回應：

![](part3-images/image1.gif "現在相機會回應觸控輸入")

藉由呼叫的 Update 方法開始`TouchPanel.GetState`，它會傳回工作的集合。 雖然`TouchPanel.GetState`可以傳回多個觸控點，我們只會擔心為了簡化的第一個。

如果使用者觸控螢幕，然後程式碼會檢查以查看第一個觸控是否屬於左、 置中 或螢幕的權限第三個。 左邊和右邊三分之一旋轉由增加或減少相機`angle`變數根據`TotalSeconds`值 （如此遊戲的行為相同，不論畫面播放速率）。

如果使用者第三個接觸 center 的畫面上，然後相機會向前移動。 做法是先取得一開始定義為指向負的 Y 軸，然後據以旋轉的矩陣使用建立的前向量`Matrix.CreateRotationZ`和`angle`值。 最後`forwardVector`套用至`position`使用`unitsPerSecond`係數。


# <a name="summary"></a>總結

這個逐步解說將說明如何移動和旋轉`Models`在 3D 空間使用`Matrices`和`BasicEffect.World`屬性。 這種形式的移動 3D 遊戲中移動的物件提供的基礎。 本逐步解說也將說明如何實作`Camera`檢視從任何位置和角度全球實體。

## <a name="related-links"></a>相關連結

- [MonoGame API 連結](http://www.monogame.net/documentation/?page=api)
- [已完成的專案 （範例）](https://developer.xamarin.com/samples/monodroid/MonoGame3DCamera/)
