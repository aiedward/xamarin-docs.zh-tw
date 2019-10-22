---
title: 使用模型類別
description: 相較于轉譯3D 圖形的傳統方法，此模型類別可大幅簡化轉譯複雜3D 物件的工作。 模型物件是從內容檔案建立的，可讓您輕鬆地整合內容，而不需自訂程式碼。
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: c5702780b6a0f0732d846a2cd4226aec5e49fc21
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "70766826"
---
# <a name="using-the-model-class"></a>使用模型類別

_相較于轉譯3D 圖形的傳統方法，此模型類別可大幅簡化轉譯複雜3D 物件的工作。模型物件是從內容檔案建立的，可讓您輕鬆地整合內容，而不需自訂程式碼。_

MonoGame API 包含一個 `Model` 類別，可以用來儲存從內容檔案載入的資料，以及執行轉譯。 模型檔案可能非常簡單（例如純色三角形），或可能包含複雜呈現的資訊，包括紋理和光源。

本逐步解說會使用[機器人的3d 模型](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)，並涵蓋下列各項：

- 啟動新的遊戲專案
- 建立模型和其材質的 XNBs
- 在遊戲專案中包含 XNBs
- 繪製3D 模型
- 繪製多個模型

完成後，我們的專案會如下所示：

![完成顯示六個機器人的範例](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>建立空白遊戲專案

我們必須先設定一個名為 MonoGame3D 的遊戲專案。 如需建立新 MonoGame 專案的相關資訊，請參閱[建立跨平臺 MonoGame 專案的這個逐步](~/graphics-games/monogame/introduction/part1.md)解說。

在繼續進行之前，請先確認專案已開啟並正確部署。 一旦部署之後，我們應該會看到一個空白的藍色畫面：

![空白藍色遊戲畫面](part1-images/image2.png)

## <a name="including-the-xnbs-in-the-game-project"></a>在遊戲專案中包含 XNBs

Xnb 檔案格式是適用于內建內容的標準延伸模組（已由[MonoGame 管線工具](http://www.monogame.net/documentation/?page=Pipeline)建立的內容）。 所有的建立內容都有原始程式檔（在我們模型的情況下為 fbx 檔案）和目的地檔案（xnb 檔案）。 Fbx 格式是常用的3D 模型格式，可由應用程式（例如[Maya](http://www.autodesk.com/products/maya/overview)和[Blender](http://www.blender.org/)）建立。 

您可以從包含 3D geometry 資料的磁片載入 xnb 檔案，以建立 `Model` 類別。   這個 xnb 檔案是透過內容專案所建立。 Monogame 範本會在內容資料夾中自動包含內容專案（副檔名為 mgcp）。 如需 MonoGame 管線工具的詳細討論，請參閱[內容管線指南](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md)。

在本指南中，我們將略過使用 MonoGame 管線工具，並將使用。此處包含的 XNB 檔案。 請注意，。每個平臺的 XNB 檔案不同，因此請務必針對您所使用的平臺，使用一組正確的 XNB 檔案。

我們會將[內容 .zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)檔案解壓縮，讓我們可以在遊戲中使用包含的 xnb 檔案。 如果使用 Android 專案，請以滑鼠右鍵按一下 [ **WalkingGame** ] 專案中的 [**資產**] 資料夾。 如果要使用 iOS 專案，請以滑鼠右鍵按一下 [ **WalkingGame** ] 專案。 選取 [**新增-> 新增檔案 ...** ]，然後選取您要處理之平臺的資料夾中的 xnb 檔案。

這兩個檔案現在應該是專案的一部分：

![具有 xnb 檔案的方案瀏覽器內容資料夾](part1-images/xnbsinxs.png)

Visual Studio for Mac 可能不會自動為新加入的 XNBs 設定組建動作。 若是 iOS，請以滑鼠右鍵按一下每個檔案，然後選取 [**建立動作-> BundleResource**]。 若為 Android，請在每個檔案上按一下滑鼠右鍵，然後選取 [**建立動作-> AndroidAsset**]。

## <a name="rendering-a-3d-model"></a>呈現3D 模型

在螢幕上查看模型所需的最後一個步驟，就是加入載入和繪製程式碼。 具體而言，我們將執行下列動作：

- 在我們的 `Game1` 類別中定義 `Model` 實例
- 在 `Game1.LoadContent` 中載入 `Model` 實例
- 在 `Game1.Draw` 中繪製 `Model` 實例

將 `Game1.cs` 的程式碼檔案（位於**WalkingGame** PCL 中）取代為下列內容：

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;

    // This is the model instance that we'll load
    // our XNB into:
    Model model;

    public Game1()
    {
        graphics = new GraphicsDeviceManager(this);
        graphics.IsFullScreen = true;

        Content.RootDirectory = "Content";
    }
    protected override void LoadContent()
    {
        // Notice that loading a model is very similar
        // to loading any other XNB (like a Texture2D).
        // The only difference is the generic type.
        model = Content.Load<Model> ("robot");
    }

    protected override void Update(GameTime gameTime)
    {
        base.Update(gameTime);
    }

    protected override void Draw(GameTime gameTime)
    {
        GraphicsDevice.Clear(Color.CornflowerBlue);

        // A model is composed of "Meshes" which are
        // parts of the model which can be positioned
        // independently, which can use different textures,
        // and which can have different rendering states
        // such as lighting applied.
        foreach (var mesh in model.Meshes)
        {
            // "Effect" refers to a shader. Each mesh may
            // have multiple shaders applied to it for more
            // advanced visuals. 
            foreach (BasicEffect effect in mesh.Effects)
            {
                // We could set up custom lights, but this
                // is the quickest way to get somethign on screen:
                effect.EnableDefaultLighting ();
                // This makes lighting look more realistic on
                // round surfaces, but at a slight performance cost:
                effect.PreferPerPixelLighting = true;

                // The world matrix can be used to position, rotate
                // or resize (scale) the model. Identity means that
                // the model is unrotated, drawn at the origin, and
                // its size is unchanged from the loaded content file.
                effect.World = Matrix.Identity;

                // Move the camera 8 units away from the origin:
                var cameraPosition = new Vector3 (0, 8, 0);
                // Tell the camera to look at the origin:
                var cameraLookAtVector = Vector3.Zero;
                // Tell the camera that positive Z is up
                var cameraUpVector = Vector3.UnitZ;

                effect.View = Matrix.CreateLookAt (
                    cameraPosition, cameraLookAtVector, cameraUpVector);

                // We want the aspect ratio of our display to match
                // the entire screen's aspect ratio:
                float aspectRatio = 
                    graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
                // Field of view measures how wide of a view our camera has.
                // Increasing this value means it has a wider view, making everything
                // on screen smaller. This is conceptually the same as "zooming out".
                // It also 
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                // Anything closer than this will not be drawn (will be clipped)
                float nearClipPlane = 1;
                // Anything further than this will not be drawn (will be clipped)
                float farClipPlane = 200;

                effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            }

            // Now that we've assigned our properties on the effects we can
            // draw the entire mesh
            mesh.Draw ();
        }
        base.Draw(gameTime);
    }
}
```

如果執行此程式碼，我們會在畫面上看到此模型：

![螢幕上顯示的模型](part1-images/image8.png "如果執行此程式碼，則會在畫面上顯示模型")

### <a name="model-class"></a>模型類別

@No__t_0 類別是用來從內容檔案（例如 fbx 檔案）執行3D 轉譯的核心類別。 其中包含呈現所需的所有資訊，包括3D 幾何、材質參考，以及控制位置、光源和相機值的 `BasicEffect` 實例。

@No__t_0 類別本身不會直接擁有用於定位的變數，因為單一模型實例可以轉譯在多個位置，因為我們稍後會在本指南中說明。

每個 `Model` 都是由一或多個 `ModelMesh` 實例所組成，這些實例會透過 `Meshes` 屬性公開。 雖然我們可以將 `Model` 視為單一遊戲物件（例如機器人或汽車），但每個 `ModelMesh` 都可以使用不同的 `BasicEffect` 值來繪製。 例如，個別的網格零件可能代表機器人的腿或汽車上的輪子，而我們可以指派 `BasicEffect` 值，讓輪子旋轉或支線移動。 

### <a name="basiceffect-class"></a>BasicEffect 類別

@No__t_0 類別提供控制轉譯選項的屬性。 我們對 `BasicEffect` 進行的第一次修改是呼叫 `EnableDefaultLighting` 方法。 正如其名，這會啟用預設光源，這非常方便用來驗證 `Model` 如預期般出現在遊戲中。 如果我們將 `EnableDefaultLighting` 呼叫標記為批註，則只會看到以其材質呈現的模型，但不含陰影或反射發光：

```csharp
//effect.EnableDefaultLighting ();
```

![僅以其材質呈現的模型，但不含陰影或反射發光](part1-images/image9.png "僅以其材質呈現的模型，但不含陰影或反射發光")

@No__t_0 屬性可以用來調整模型的位置、旋轉和縮放比例。 上述程式碼會使用 `Matrix.Identity` 值，這表示 `Model` 會完全依照 fbx 檔案中的指定轉譯遊戲。 我們將在[第3部分](~/graphics-games/monogame/3d/part3.md)深入探討矩陣和3d 座標，但作為範例，我們可以變更 `World` 屬性來變更 `Model` 的位置，如下所示：

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

此程式碼會導致物件由3個世界單位移動：

![此程式碼會導致物件由3個世界單位移動](part1-images/image10.png "此程式碼會導致物件由3個世界單位移動")

在 `BasicEffect` 上指派的最後兩個屬性會 `View` 並 `Projection`。 我們將在[第3部分](~/graphics-games/monogame/3d/part3.md)涵蓋3d 攝影機，但舉例來說，我們可以藉由變更本機 `cameraPosition` 變數來修改相機的位置：

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

我們可以看到相機已向後移動，導致 `Model` 因觀點而顯示較小：

![相機已向後移動，導致模型因觀點而顯示較小](part1-images/image11.png "相機已向後移動，導致模型因觀點而顯示較小")

## <a name="rendering-multiple-models"></a>呈現多個模型

如上所述，可以多次繪製單一 `Model`。 為了簡化這種作業，我們會將 `Model` 繪圖程式碼移至自己的方法，將所需的 `Model` 位置當做參數使用。 完成後，我們的 `Draw` 和 `DrawModel` 方法如下所示：

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);
    DrawModel (new Vector3 (-4, 0, 0));
    DrawModel (new Vector3 ( 0, 0, 0));
    DrawModel (new Vector3 ( 4, 0, 0));
    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));
    base.Draw(gameTime);
}
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            effect.World = Matrix.CreateTranslation (modelPosition);
            var cameraPosition = new Vector3 (0, 10, 0);
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
        }
        // Now that we've assigned our properties on the effects we can
        // draw the entire mesh
        mesh.Draw ();
    }
}
```

這會導致機器人模型繪製六次：

![這會導致機器人模型繪製六次](part1-images/image1.png "這會導致機器人模型繪製六次")

## <a name="summary"></a>總結

本逐步解說引進了 MonoGame 的 `Model` 類別。 它涵蓋將 fbx 檔案轉換成 xnb，而該檔案可以再載入 `Model` 類別中。 它也會顯示 `BasicEffect` 實例的修改如何影響 `Model` 繪製。

## <a name="related-links"></a>相關連結

- [MonoGame 模型參考](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [內容 .zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [完成的專案（範例）](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelrenderingmg/)
