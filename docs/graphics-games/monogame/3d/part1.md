---
title: 使用模型類別
description: 模型類別可大幅簡化轉譯複雜的 3D 物件相較於傳統的方法，呈現 3D 圖形。 從允許的任何自訂程式碼的內容很容易整合的內容檔案會建立模型物件。
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: d9c73dfcee6321ecb314ca229db407c6d0438977
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342538"
---
# <a name="using-the-model-class"></a>使用模型類別

_模型類別可大幅簡化轉譯複雜的 3D 物件相較於傳統的方法，呈現 3D 圖形。從允許的任何自訂程式碼的內容很容易整合的內容檔案會建立模型物件。_

MonoGame API 包含`Model`類別可用來儲存資料載入的內容檔案，以及執行轉譯。 模型檔案可能很簡單 （例如穩固的彩色三角形），或可能包含複雜的呈現，包括紋理和光源處理的資訊。

本逐步解說會使用[機器人的 3D 模型](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)並涵蓋下列：

- 開始使用新的遊戲專案
- 建立模型和其紋理 XNBs
- 遊戲的專案中包含 XNBs
- 繪製 3D 模型
- 繪製多個模型

完成時，我們的專案將會出現，如下所示：

![顯示六個機器人的完成的範例](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>建立空白的遊戲專案

我們必須設定第一次呼叫 MonoGame3D 遊戲專案。 如需建立新的 MonoGame 專案的詳細資訊，請參閱[本逐步解說建立跨平台 Monogame 專案](~/graphics-games/monogame/introduction/part1.md)。

在繼續之前，我們應該驗證專案隨即開啟，並正確地部署。 一次部署我們應該會看到空白的藍色螢幕：

![空白的藍色遊戲畫面](part1-images/image2.png)


## <a name="including-the-xnbs-in-the-game-project"></a>遊戲的專案中包含 XNBs

.Xnb 檔案格式是標準的擴充內建的內容 (已建立的內容[MonoGame 管線工具](http://www.monogame.net/documentation/?page=Pipeline))。 所有的內建的內容具有原始程式檔 （也就是在我們的模型的情況下.fbx 檔案） 和目的地檔案 （.xnb 檔案）。 .Fbx 格式是一種常見的 3D 模型格式可以建立應用程式這類[Maya](http://www.autodesk.com/products/maya/overview)並[Blender](http://www.blender.org/)。 

`Model` .xnb 從磁碟載入檔案，其中包含 3D 幾何資料也可以建構類別。   .Xnb 會建立這個檔案的內容的專案。 Monogame 範本會自動包含我們內容的資料夾 （具有延伸模組.mgcp 中) 的內容專案。 如需 MonoGame 管線工具的詳細討論，請參閱 < [Content Pipeline 指南](~/graphics-games/cocossharp/content-pipeline/index.md)。

本指南中我們將會略過使用 MonoGame 管線工具，並會使用。此處包含的 XNB 檔案。 請注意，。XNB 檔案每個平台不同，因此請務必針對您正在使用任何平台使用一組正確的 XNB 檔案。

我們將會解壓縮[Content.zip 檔案](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)，讓我們可以在我們的遊戲中使用自主的.xnb 檔案。 如果使用 Android 專案，以滑鼠右鍵按一下**資產**中的資料夾**WalkingGame.Android**專案。 如果使用 iOS 專案，以滑鼠右鍵按一下**WalkingGame.iOS**專案。 選取**新增]-> [新增檔案...** ，然後選取您正在使用的平台的資料夾中的這兩個.xnb 檔案。

兩個檔案現在應該可以我們專案的一部分：

![方案總管 內容資料夾 xnb 檔案](part1-images/xnbsinxs.png)

Visual Studio for Mac 可能不會自動設定新加入的 XNBs 的建置動作。 適用於 iOS、 以滑鼠右鍵按一下每個檔案，然後選取**建置動作]-> [BundleResource**。 適用於 Android、 以滑鼠右鍵按一下每個檔案，然後選取**建置動作]-> [AndroidAsset**。

## <a name="rendering-a-3d-model"></a>轉譯 3D 模型

螢幕上查看模型所需的最後一個步驟是新增的載入和繪圖程式碼。 具體來說，我們會執行下列作業：

- 定義`Model`執行個體中我們`Game1`類別
- 正在載入`Model`中執行個體 `Game1.LoadContent`
- 繪製`Model`中執行個體 `Game1.Draw`

取代`Game1.cs`程式碼檔案 (位於**WalkingGame** PCL) 取代為下列：

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

如果我們執行此程式碼就會在螢幕上看到的模型：

![顯示於螢幕上的模型](part1-images/image8.png "如果執行此程式碼時，將螢幕上顯示模型")

### <a name="model-class"></a>模型類別

`Model`類別是從內容 （例如.fbx 檔案） 的檔案執行 3D 轉換的核心類別。 它包含所有的轉譯，包括 3D 幾何，也就是材質參考所需的資訊和`BasicEffect`控制定位、 光線和觀景窗值的執行個體。

`Model`類別本身並未直接擁有的定位的單一模型執行個體可以轉譯在多個位置，因為我們稍後在本指南將示範變數。

每個`Model`包含一或多個`ModelMesh`執行個體，會透過公開`Meshes`屬性。 雖然我們可能會考慮`Model`為單一的遊戲物件 （例如傀儡程式或一輛車），每個`ModelMesh`可以使用不同繪製`BasicEffect`值。 比方說，個別的網狀結構組件可能代表腿的機器人的輪子上一輛車，而我們可能會指派`BasicEffect`值來做出 wheel 微調或腿的移動。 

### <a name="basiceffect-class"></a>BasicEffect 類別

`BasicEffect`類別會提供屬性來控制呈現選項。 我們對第一次修改`BasicEffect`是呼叫`EnableDefaultLighting`方法。 如同名稱所暗示，這可讓預設的光線，這是非常好用的驗證`Model`會出現在遊戲中如預期般運作。 如果我們取消註解`EnableDefaultLighting`呼叫，則我們會看到轉譯只是紋理，但沒有陰影或反射光暈的模型：

```csharp
//effect.EnableDefaultLighting ();
```

![模型呈現只是紋理，但沒有陰影或反射光暈](part1-images/image9.png "呈現只是紋理，但沒有陰影或反射光暈的模型")

`World`屬性可以用來調整位置、 旋轉和模型的比例。 使用上述程式碼`Matrix.Identity`值，這表示`Model`會呈現遊戲中指定的完全相同.fbx 檔案中。 我們要探討矩陣和更詳細 3D 座標[第 3 部分](~/graphics-games/monogame/3d/part3.md)，做為範例中，我們可以變更的位置，但`Model`藉由變更`World`屬性，如下所示：

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

此程式碼會產生 3 個全局單位會移動的物件：

![此程式碼會產生 3 個全局單位會移動的物件](part1-images/image10.png "此程式碼會產生 3 個全局單位會移動的物件")

最後兩個屬性上指派`BasicEffect`都`View`和`Projection`。 我們要探討中的 3D 攝影機[第 3 部分](~/graphics-games/monogame/3d/part3.md)，但做為範例，我們可以修改觀景窗的位置，藉由變更本機`cameraPosition`變數：

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

我們可以看到相機已移進一步後，導致`Model`出現較小，因為檢視方塊：

![相機已移進一步後，產生在模型中不會出現較小，因為檢視方塊](part1-images/image11.png "相機已移進一步後，導致出現較小，因為檢視方塊的模型")

## <a name="rendering-multiple-models"></a>轉譯多個模型

如上所述，單一`Model`可以繪製多次。 為了簡化起見我們會移動`Model`繪製到其本身會採用所需的方法的程式碼`Model`做為參數的位置。 完成後，我們`Draw`和`DrawModel`方法看起來像：


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

這會導致機器人所繪製六倍的模型：

![這會導致機器人模型所繪製六倍](part1-images/image1.png "這會導致機器人所繪製六倍的模型")

## <a name="summary"></a>總結

本逐步解說中導入 MonoGame 的`Model`類別。 它涵蓋了將.fbx 檔案轉換成.xnb，這進而可載入`Model`類別。 它也會示範如何修改`BasicEffect`執行個體可能會影響`Model`繪圖。

## <a name="related-links"></a>相關連結

- [MonoGame 模型參考](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [已完成的專案 （範例）](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/)
