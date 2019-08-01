---
title: 使用模型類別
description: 相較于轉譯3D 圖形的傳統方法, 此模型類別可大幅簡化轉譯複雜3D 物件的工作。 模型物件是從內容檔案建立的, 可讓您輕鬆地整合內容, 而不需自訂程式碼。
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 35df6e4ca799a875bcd7db50adbc7a300460885c
ms.sourcegitcommit: f255aa286bd52e8a80ffa620c2e93c97f069f8ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68680959"
---
# <a name="using-the-model-class"></a>使用模型類別

_相較于轉譯3D 圖形的傳統方法, 此模型類別可大幅簡化轉譯複雜3D 物件的工作。模型物件是從內容檔案建立的, 可讓您輕鬆地整合內容, 而不需自訂程式碼。_

MonoGame API 包含一個`Model`類別, 可以用來儲存從內容檔案載入的資料, 以及執行轉譯。 模型檔案可能非常簡單 (例如純色三角形), 或可能包含複雜呈現的資訊, 包括紋理和光源。

本逐步解說會使用[機器人的3d 模型](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true), 並涵蓋下列各項:

- 啟動新的遊戲專案
- 建立模型和其材質的 XNBs
- 在遊戲專案中包含 XNBs
- 繪製3D 模型
- 繪製多個模型

完成後, 我們的專案會如下所示:

![完成顯示六個機器人的範例](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>建立空白遊戲專案

我們必須先設定一個名為 MonoGame3D 的遊戲專案。 如需建立新 MonoGame 專案的相關資訊, 請參閱[建立跨平臺 MonoGame 專案的這個逐步](~/graphics-games/monogame/introduction/part1.md)解說。

在繼續進行之前, 請先確認專案已開啟並正確部署。 一旦部署之後, 我們應該會看到一個空白的藍色畫面:

![空白藍色遊戲畫面](part1-images/image2.png)


## <a name="including-the-xnbs-in-the-game-project"></a>在遊戲專案中包含 XNBs

Xnb 檔案格式是適用于內建內容的標準延伸模組 (已由[MonoGame 管線工具](http://www.monogame.net/documentation/?page=Pipeline)建立的內容)。 所有的建立內容都有原始程式檔 (在我們模型的情況下為 fbx 檔案) 和目的地檔案 (xnb 檔案)。 Fbx 格式是常用的3D 模型格式, 可由應用程式 (例如[Maya](http://www.autodesk.com/products/maya/overview)和[Blender](http://www.blender.org/)) 建立。 

您可以從包含 3d geometry 資料的磁片載入 xnb 檔案, 以建立類別。`Model`   這個 xnb 檔案是透過內容專案所建立。 Monogame 範本會在內容資料夾中自動包含內容專案 (副檔名為 mgcp)。 如需 MonoGame 管線工具的詳細討論, 請參閱[內容管線指南](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md)。

在本指南中, 我們將略過使用 MonoGame 管線工具, 並將使用。此處包含的 XNB 檔案。 請注意,。每個平臺的 XNB 檔案不同, 因此請務必針對您所使用的平臺, 使用一組正確的 XNB 檔案。

我們會將[內容 .zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)檔案解壓縮, 讓我們可以在遊戲中使用包含的 xnb 檔案。 如果使用 Android 專案, 請以滑鼠右鍵按一下 [ **WalkingGame** ] 專案中的 [**資產**] 資料夾。 如果要使用 iOS 專案, 請以滑鼠右鍵按一下 [ **WalkingGame** ] 專案。 選取 [**新增-> 新增檔案 ...** ], 然後選取您要處理之平臺的資料夾中的 xnb 檔案。

這兩個檔案現在應該是專案的一部分:

![具有 xnb 檔案的方案瀏覽器內容資料夾](part1-images/xnbsinxs.png)

Visual Studio for Mac 可能不會自動為新加入的 XNBs 設定組建動作。 若是 iOS, 請以滑鼠右鍵按一下每個檔案, 然後選取 [**建立動作-> BundleResource**]。 若為 Android, 請在每個檔案上按一下滑鼠右鍵, 然後選取 [**建立動作-> AndroidAsset**]。

## <a name="rendering-a-3d-model"></a>呈現3D 模型

在螢幕上查看模型所需的最後一個步驟, 就是加入載入和繪製程式碼。 具體而言, 我們將執行下列動作:

- 在我們`Game1`的類別中定義`Model`實例
- 載入中`Model`的實例`Game1.LoadContent`
- 在中繪製實例`Model``Game1.Draw`

將程式碼檔案 (位於 WalkingGame PCL 中) 取代為下列內容: `Game1.cs`

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

如果執行此程式碼, 我們會在畫面上看到此模型:

![螢幕上顯示的模型](part1-images/image8.png "如果執行此程式碼, 則會在畫面上顯示模型")

### <a name="model-class"></a>模型類別

`Model`類別是用來從內容檔案 (例如 fbx 檔案) 執行3d 呈現的核心類別。 其中包含呈現所需的所有資訊, 包括3d 幾何、材質參考, 以及`BasicEffect`控制位置、光源和相機值的實例。

`Model`類別本身不會直接擁有用於定位的變數, 因為單一模型實例可以轉譯在多個位置, 因為我們稍後會在本指南中說明。

每`Model`個都是由一或`ModelMesh`多個實例所組成, 它們`Meshes`會透過屬性公開。 雖然我們可能會`Model`將視為單一遊戲物件 (例如機器人或汽車), 但每`ModelMesh`一個都可以使用不同`BasicEffect`的值來繪製。 例如, 個別的網格零件可能代表機器人的腿或汽車上的輪子, 而我們可以指派`BasicEffect`值, 讓輪子旋轉或支線移動。 

### <a name="basiceffect-class"></a>BasicEffect 類別

`BasicEffect`類別提供用來控制轉譯選項的屬性。 我們`BasicEffect`對進行的第一次修改是`EnableDefaultLighting`呼叫方法。 顧名思義, 這會啟用預設光源, 這非常方便用來驗證`Model`是否如預期般出現在遊戲中。 如果我們將`EnableDefaultLighting`呼叫標記為批註, 則只會看到以其材質呈現的模型, 但不含陰影或反射發光:

```csharp
//effect.EnableDefaultLighting ();
```

![僅以其材質呈現的模型, 但不含陰影或反射發光](part1-images/image9.png "僅以其材質呈現的模型, 但不含陰影或反射發光")

`World`屬性可以用來調整模型的位置、旋轉和縮放比例。 上述程式碼會使用`Matrix.Identity`值, 這表示會以`Model` fbx 檔案中所指定的方式呈現遊戲中的。 在[第3部分](~/graphics-games/monogame/3d/part3.md)中, 我們將更詳細地說明矩陣和3d 座標, 但我們可以變更`Model` `World`屬性, 如下所示變更的位置:

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

此程式碼會導致物件由3個世界單位移動:

![此程式碼會導致物件由3個世界單位移動](part1-images/image10.png "此程式碼會導致物件由3個世界單位移動")

在上`BasicEffect`指派的最後兩個屬性`View`是`Projection`和。 我們將在[第3部分](~/graphics-games/monogame/3d/part3.md)涵蓋3d 攝影機, 但舉例來說, 我們可以藉由變更本機`cameraPosition`變數來修改相機的位置:

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

我們可以看到相機已向後移動, 使其`Model`因觀點而顯示較小:

![相機已向後移動, 導致模型因觀點而顯示較小](part1-images/image11.png "相機已向後移動, 導致模型因觀點而顯示較小")

## <a name="rendering-multiple-models"></a>呈現多個模型

如先前所述, 單一`Model`可以繪製多次。 為了簡化這種作業, 我們會將`Model`繪圖程式碼移至自己的方法, 將`Model`所需的位置當做參數使用。 完成之後, 我們`Draw`的`DrawModel`和方法看起來會像這樣:


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

這會導致機器人模型繪製六次:

![這會導致機器人模型繪製六次](part1-images/image1.png "這會導致機器人模型繪製六次")

## <a name="summary"></a>總結

本逐步解說引進了`Model` MonoGame 的類別。 它涵蓋將 fbx 檔案轉換成 xnb, 它可以再載入至`Model`類別。 它也會顯示對實例進行`BasicEffect`修改的方式`Model` , 對繪圖的影響。

## <a name="related-links"></a>相關連結

- [MonoGame 模型參考](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [完成的專案 (範例)](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelrenderingmg/)
