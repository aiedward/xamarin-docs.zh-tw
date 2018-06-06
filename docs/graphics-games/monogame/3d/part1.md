---
title: 使用模型類別
description: 模型類別可以大幅簡化呈現複雜的 3D 物件相較於呈現 3D 圖形的傳統方法。 從內容檔案，以便輕鬆整合的任何自訂程式碼的內容會建立模型物件。
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: e108bc6098d2c754428bf35e7312ebdc89459501
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783463"
---
# <a name="using-the-model-class"></a>使用模型類別

_模型類別可以大幅簡化呈現複雜的 3D 物件相較於呈現 3D 圖形的傳統方法。從內容檔案，以便輕鬆整合的任何自訂程式碼的內容會建立模型物件。_

MonoGame API 包含`Model`類別可以用來儲存資料的載入根據內容的檔案，以及用以執行呈現。 模型檔案可能非常簡單 （例如，實線的彩色三角形），或可能包含複雜的轉譯，包括設定和光源設定紋理的資訊。

本逐步解說使用[機器人 3D 模型](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)並涵蓋下列：

- 開始使用新的遊戲專案
- 建立模型和其紋理 XNBs
- 在 [遊戲] 專案中包含 XNBs
- 繪製 3D 模型
- 繪製多個模型

完成時，受測專案會出現，如下所示：

![完成後顯示六個 robots 的範例](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>建立空的遊戲專案

我們需要設定第一次呼叫 MonoGame3D 遊戲專案。 如需建立新的 MonoGame 專案的資訊，請參閱[本逐步解說建立跨平台 Monogame 專案](~/graphics-games/monogame/introduction/part1.md)。

在繼續之前，我們應該確認專案開啟，並正確部署。 我們應該會看到空白的藍色螢幕一次部署：

![空白的藍色遊戲畫面](part1-images/image2.png)


## <a name="including-the-xnbs-in-the-game-project"></a>在 [遊戲] 專案中包含 XNBs

.Xnb 檔案格式是標準的擴充內建的內容 (內容，這由[MonoGame 管線工具](http://www.monogame.net/documentation/?page=Pipeline))。 所有的內建的內容具有原始程式檔 （這是在我們的模型中的情況下.fbx 檔案） 和目的地檔案 （.xnb 檔案）。 .Fbx 格式是您可以建立應用程式這類常見 3D 模型格式[馬雅](http://www.autodesk.com/products/maya/overview)和[Blender](http://www.blender.org/)。 

`Model`類別可以由.xnb 從磁碟載入檔案，其中包含 3D 幾何資料建構。   透過內容專案建立此.xnb 檔案。 Monogame 範本會自動包含的內容專案 （副檔名.mgcp)，我們內容的資料夾中。 如需 MonoGame 管線工具的詳細討論，請參閱[內容的管線指南](~/graphics-games/cocossharp/content-pipeline/index.md)。

本指南中我們將會略過使用 MonoGame 管線工具，而且將會使用。本文提及的 XNB 檔案。 請注意，。XNB 檔案與每個平台不同，因此請務必針對您正在使用何種平台使用一組正確的 XNB 檔案。

我們會解壓縮[Content.zip 檔案](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)以便我們可以使用包含的.xnb 檔案我們遊戲中。 如果使用 Android 專案，以滑鼠右鍵按一下**資產**資料夾中的**WalkingGame.Android**專案。 如果使用 iOS 專案，以滑鼠右鍵按一下**WalkingGame.iOS**專案。 選取**新增]-> [新增檔案...** 依據您進行平台的資料夾中選取這兩個.xnb 檔案。

兩個檔案現在應該是受測專案的一部分：

![方案總管 內容資料夾 xnb 檔案](part1-images/xnbsinxs.png)

Visual Studio for Mac 可能不會自動設定新加入的 XNBs 的建置動作。 對於 iOS，以滑鼠右鍵按一下每個檔案，然後選取**建置動作]-> [BundleResource**。 若是 Android，以滑鼠右鍵按一下每個檔案，然後選取**建置動作]-> [AndroidAsset**。

## <a name="rendering-a-3d-model"></a>呈現 3D 模型

螢幕上查看模型所需的最後一個步驟是加入的載入和繪圖程式碼。 具體來說，我們將會執行下列動作：

- 定義`Model`執行個體中我們`Game1`類別
- 載入`Model`中執行個體 `Game1.LoadContent`
- 繪製`Model`中執行個體 `Game1.Draw`

取代`Game1.cs`程式碼檔案 (位於**WalkingGame** PCL) 為下列：

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;

    // This is the model instance that we'll load
    // our XNB into:
    Model model;

    public Game1()
    {
        graphics = new GraphicsDeviceManager(this);
        graphics.IsFullScreen = true;

        Content.RootDirectory = "Content";
    }
    protected override void LoadContent()
    {
        // Notice that loading a model is very similar
        // to loading any other XNB (like a Texture2D).
        // The only difference is the generic type.
        model = Content.Load<Model> ("robot");
    }

    protected override void Update(GameTime gameTime)
    {
        base.Update(gameTime);
    }

    protected override void Draw(GameTime gameTime)
    {
        GraphicsDevice.Clear(Color.CornflowerBlue);

        // A model is composed of "Meshes" which are
        // parts of the model which can be positioned
        // independently, which can use different textures,
        // and which can have different rendering states
        // such as lighting applied.
        foreach (var mesh in model.Meshes)
        {
            // "Effect" refers to a shader. Each mesh may
            // have multiple shaders applied to it for more
            // advanced visuals. 
            foreach (BasicEffect effect in mesh.Effects)
            {
                // We could set up custom lights, but this
                // is the quickest way to get somethign on screen:
                effect.EnableDefaultLighting ();
                // This makes lighting look more realistic on
                // round surfaces, but at a slight performance cost:
                effect.PreferPerPixelLighting = true;

                // The world matrix can be used to position, rotate
                // or resize (scale) the model. Identity means that
                // the model is unrotated, drawn at the origin, and
                // its size is unchanged from the loaded content file.
                effect.World = Matrix.Identity;

                // Move the camera 8 units away from the origin:
                var cameraPosition = new Vector3 (0, 8, 0);
                // Tell the camera to look at the origin:
                var cameraLookAtVector = Vector3.Zero;
                // Tell the camera that positive Z is up
                var cameraUpVector = Vector3.UnitZ;

                effect.View = Matrix.CreateLookAt (
                    cameraPosition, cameraLookAtVector, cameraUpVector);

                // We want the aspect ratio of our display to match
                // the entire screen's aspect ratio:
                float aspectRatio = 
                    graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
                // Field of view measures how wide of a view our camera has.
                // Increasing this value means it has a wider view, making everything
                // on screen smaller. This is conceptually the same as "zooming out".
                // It also 
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                // Anything closer than this will not be drawn (will be clipped)
                float nearClipPlane = 1;
                // Anything further than this will not be drawn (will be clipped)
                float farClipPlane = 200;

                effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            }

            // Now that we've assigned our properties on the effects we can
            // draw the entire mesh
            mesh.Draw ();
        }
        base.Draw(gameTime);
    }
}
```

當我們執行此程式碼我們會在螢幕上看到模型：

![模型顯示於螢幕上](part1-images/image8.png "會顯示於螢幕上執行此程式碼時，如果模型")

### <a name="model-class"></a>模型類別

`Model`類別是執行 （例如.fbx 檔案） 的內容檔案從 3D 轉譯的核心類別。 它包含所有呈現，包括 3D 幾何紋理參考所需的資訊和`BasicEffect`控制位置、 光線和相機值的執行個體。

`Model`類別本身並未直接擁有的定位因為多個位置，可以轉譯單一模型執行個體，因為我們將稍後在本指南示範的變數。

每個`Model`是由一或多個`ModelMesh`公開會透過執行個體`Meshes`屬性。 雖然我們可能會考慮`Model`為單一遊戲物件 （例如機器人或一輛車），每個`ModelMesh`可以繪製不同`BasicEffect`值。 比方說，個別的網狀結構組件可能代表機器人或汽車，車輪腳和我們可能會指派`BasicEffect`滾輪微調或腳值移動。 

### <a name="basiceffect-class"></a>BasicEffect 類別

`BasicEffect`類別提供控制呈現選項屬性。 我們對第一次修改`BasicEffect`是呼叫`EnableDefaultLighting`方法。 顧名思義，這可讓驗證，非常好用的預設光源`Model`遊戲中如預期般隨即出現。 如果我們標記為註解`EnableDefaultLighting`呼叫，則我們會看到呈現具有只紋理，但沒有陰影或反射光暈的模型：

```csharp
//effect.EnableDefaultLighting ();
```

![呈現具有只紋理，但沒有陰影或反射光暈模型](part1-images/image9.png "呈現具有只紋理，但沒有陰影或反射光暈的模型")

`World`屬性可以用來調整位置、 旋轉和模型的小數位數。 使用上方的程式碼`Matrix.Identity`值，表示`Model`會轉譯遊戲中指定的完全相同.fbx 檔案中。 我們將矩陣和更詳細地 3D 座標涵蓋[第 3 部分](~/graphics-games/monogame/3d/part3.md)，做為範例中，我們可以變更的位置，但`Model`藉由變更`World`屬性，如下所示：

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

此程式碼會產生 3 的自然單位會移動的物件：

![此程式碼會產生 3 的自然單位會移動的物件](part1-images/image10.png "此程式碼會產生 3 的自然單位會移動的物件")

最後兩個屬性上指派`BasicEffect`是`View`和`Projection`。 我們將涵蓋在 3D 相機[第 3 部分](~/graphics-games/monogame/3d/part3.md)，例如，我們可以透過變更本機修改相機的位置，但`cameraPosition`變數：

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

我們可以看到相機已經移動進一步後，導致`Model`出現較小，因為檢視方塊：

![相機已經移動進一步後，導致出現較小，因為檢視方塊的模型](part1-images/image11.png "相機已經移動進一步後，導致出現較小，因為檢視方塊的模型")

## <a name="rendering-multiple-models"></a>轉譯多個模型

如上所述，單一`Model`可以繪製多次。 若要簡化此我們會移動`Model`繪製到其本身會採用所需的方法的程式碼`Model`做為參數的位置。 一次完成時，我們`Draw`和`DrawModel`方法看起來像：


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

這會導致機器人繪製六倍的模型：

![這會導致機器人繪製六倍的模型](part1-images/image1.png "這會導致機器人繪製六倍的模型")

## <a name="summary"></a>總結

這個逐步解說中導入 MonoGame 的`Model`類別。 它涵蓋了將.fbx 檔案轉換成.xnb，這可以依次載入`Model`類別。 它也會示範如何修改`BasicEffect`執行個體可能會影響`Model`繪圖。

## <a name="related-links"></a>相關連結

- [MonoGame 模型參考](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [已完成的專案 （範例）](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/)
