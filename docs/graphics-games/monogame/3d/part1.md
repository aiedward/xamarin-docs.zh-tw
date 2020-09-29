---
title: 使用模型類別
description: 相較于呈現3D 圖形的傳統方法，模型類別可大幅簡化轉譯複雜3D 物件的工作。 模型物件是從內容檔建立的，可讓您輕鬆地整合內容，無需自訂程式碼。
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 06d585e976ce41080682f531b9661ff06cedaebe
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437012"
---
# <a name="using-the-model-class"></a>使用模型類別

_相較于呈現3D 圖形的傳統方法，模型類別可大幅簡化轉譯複雜3D 物件的工作。模型物件是從內容檔建立的，可讓您輕鬆地整合內容，無需自訂程式碼。_

MonoGame API 包含類別， `Model` 可用來儲存從內容檔案載入的資料，以及執行轉譯。 模型檔案可能是非常簡單的 (例如純色三角形) 或可能包含複雜轉譯的資訊，包括紋理和照明。

本逐步解說會使用 [機器人的3d 模型](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) ，並涵蓋下列各項：

- 開始新的遊戲專案
- 建立模型及其材質的 XNBs
- 在遊戲專案中包含 XNBs
- 繪製3D 模型
- 繪製多個模型

完成時，我們的專案會顯示如下：

![顯示六個機器人的完成範例](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>建立空的遊戲專案

我們必須先設定一個稱為 MonoGame3D 的遊戲專案。 如需建立新 MonoGame 專案的詳細資訊，請參閱 [本逐步解說中有關建立跨平臺 MonoGame 專案的逐步](~/graphics-games/monogame/introduction/part1.md)解說。

在繼續進行之前，請先確認專案開啟並正確部署。 一旦部署之後，我們應該會看到一個空白的藍色畫面：

![空白藍色遊戲畫面](part1-images/image2.png)

## <a name="including-the-xnbs-in-the-game-project"></a>在遊戲專案中包含 XNBs

Xnb 檔案格式是 [MonoGame 管線工具](http://www.monogame.net/documentation/?page=Pipeline)) 所建立 (內容的標準延伸模組。 所有的內建內容都有一個原始程式檔 (在我們的模型) 的情況下是 fbx 檔案，而 (xnb 檔案) 的目的地檔案。 Fbx 格式是一種常見的3D 模型格式，可由應用程式（例如 [Maya](https://www.autodesk.com/products/maya/overview) 和 [Blender](https://www.blender.org/)）建立。 

您 `Model` 可以從包含3d 幾何資料的磁片載入 xnb 檔案來建立類別。   這個 xnb 檔案是透過內容專案所建立。 Monogame 範本會在內容資料夾中自動包含 mgcp) 的內容專案 (。 如需 MonoGame 管線工具的詳細討論，請參閱 [內容管線指南](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md)。

在本指南中，我們將略過使用 MonoGame 管線工具，並使用。XNB 檔案包含在此。 請注意，XNB 檔案會因平臺而異，因此請務必針對您正在使用的任何平臺使用一組正確的 XNB 檔案。

我們會將 [Content.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) 檔案解壓縮，讓我們可以在遊戲中使用包含的 xnb 檔案。 如果使用 Android 專案，請以滑鼠右鍵按一下**WalkingGame android**專案中的 [**資產**] 資料夾。 如果是使用 iOS 專案，請以滑鼠右鍵按一下 **WalkingGame ios** 專案。 選取 [新增 **->新增檔案 ...** ]，然後選取您正在處理之平臺的資料夾中的 xnb 檔案。

這兩個檔案現在應該是專案的一部分：

![具有 xnb 檔案的 Solution explorer 內容資料夾](part1-images/xnbsinxs.png)

Visual Studio for Mac 可能不會自動為新加入的 XNBs 設定組建動作。 若是 iOS，請以滑鼠右鍵按一下每個檔案，然後選取 [ **建立動作->套件套件**]。 針對 Android，以滑鼠右鍵按一下每個檔案，然後選取 [ **建立動作->AndroidAsset**]。

## <a name="rendering-a-3d-model"></a>轉譯3D 模型

在畫面上查看模型所需的最後一個步驟是新增載入和繪製程式碼。 具體而言，我們將執行下列動作：

- `Model`在類別中定義實例 `Game1`
- 載入 `Model` 實例 `Game1.LoadContent`
- `Model`在中繪製實例`Game1.Draw`

`Game1.cs`以下列程式碼取代位於**WalkingGame** PCL) 中的程式碼檔案 (：

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

如果執行此程式碼，我們會在螢幕上看到模型：

![顯示在畫面上的模型](part1-images/image8.png "如果執行此程式碼，則會在畫面上顯示模型")

### <a name="model-class"></a>模型類別

`Model`類別是一種核心類別，可從內容檔 (（例如 fbx 檔案) ）執行3d 轉譯。 它包含轉譯所需的所有資訊，包括3D 幾何、材質參考，以及 `BasicEffect` 控制定位、光源和相機值的實例。

`Model`類別本身不會直接有用於定位的變數，因為單一模型實例可以在多個位置中轉譯，如本指南稍後所示。

每個都 `Model` 是由一或多個實例所組成 `ModelMesh` ，這些實例會透過 `Meshes` 屬性公開。 雖然我們可能將 a `Model` 視為單一遊戲物件 (例如機器人或汽車) ，但每個都 `ModelMesh` 可以使用不同的值來繪製 `BasicEffect` 。 例如，個別的網格零件可能代表機器人的腿或汽車上的輪子，我們可能會指派 `BasicEffect` 值來讓輪子旋轉或移動腿。 

### <a name="basiceffect-class"></a>BasicEffect 類別

`BasicEffect`類別提供控制轉譯選項的屬性。 我們對進行的第一項修改 `BasicEffect` 是呼叫 `EnableDefaultLighting` 方法。 顧名思義，這會啟用預設光源，這非常方便您確認 `Model` 如預期般出現在遊戲中。 如果我們將呼叫標記為批註 `EnableDefaultLighting` ，則只會看到以其材質呈現的模型，但不會顯示陰影或反光：

```csharp
//effect.EnableDefaultLighting ();
```

![只以材質材質呈現的模型，但不含陰影或反光](part1-images/image9.png "只以材質材質呈現的模型，但不含陰影或反光")

`World`屬性可以用來調整模型的位置、旋轉和縮放比例。 上述程式碼使用此 `Matrix.Identity` 值，這表示會完全依照 fbx 檔中所指定的方式轉譯 `Model` 遊戲。 我們將在 [第3部分](~/graphics-games/monogame/3d/part3.md)中更詳細地討論矩陣和3d 座標，但作為範例，我們可以變更屬性來變更的位置，如下所示 `Model` `World` ：

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

這段程式碼會導致物件由3個全球單位進行移動：

![此程式碼會導致物件由3個全球單位移動](part1-images/image10.png "此程式碼會導致物件由3個全球單位移動")

在上指派的最後兩個屬性 `BasicEffect` 為 `View` 和 `Projection` 。 我們將在 [第3部分](~/graphics-games/monogame/3d/part3.md)介紹3d 攝影機，但舉例來說，我們可以變更本機變數來修改攝影機的位置 `cameraPosition` ：

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

我們可以看到相機已回頭移回，導致 `Model` 顯示較小的原因：

![攝影機已進一步移回，導致模型因為觀點而出現較小](part1-images/image11.png "攝影機已進一步移回，導致模型因為觀點而出現較小")

## <a name="rendering-multiple-models"></a>呈現多個模型

如前文所述， `Model` 可以多次繪製單一。 為了更輕鬆，我們會將繪圖程式 `Model` 代碼移至它自己的方法，該方法會採用所需的 `Model` 位置做為參數。 完成之後，我們 `Draw` 的和 `DrawModel` 方法看起來會像這樣：

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

## <a name="summary"></a>摘要

本逐步解說介紹 MonoGame 的 `Model` 類別。 它涵蓋將 fbx 檔案轉換成 xnb，進而將它載入類別中。 `Model` 它也會顯示對實例的修改如何 `BasicEffect` 影響 `Model` 繪圖。

## <a name="related-links"></a>相關連結

- [MonoGame 模型參考](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [完成的專案 (範例) ](/samples/xamarin/mobile-samples/modelrenderingmg/)