---
title: "使用 UrhoSharp ARKit"
ms.topic: article
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/01/2016
ms.openlocfilehash: 4c6928af364870541581b784b7c4e9e7717eb2db
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="using-arkit-with-urhosharp"></a>使用 UrhoSharp ARKit

透過推出[ARKit](https://developer.apple.com/arkit/)，Apple，變成開發人員可以建立擴增的實境的應用程式。 ARKit 可以追蹤您的裝置的確切位置，並偵測世界上的各種介面，並由開發人員可以 blend 凸出 ARKit 插入程式碼中的資料。

[UrhoSharp](~/graphics-games/urhosharp/index.md)提供完整且更容易使用的 3D API 可讓您建立 3D 應用程式。   這兩種可以混合在一起，ARKit 提供世界中，實體的資訊和 Urho 來呈現結果。

此頁面說明如何連接這些兩者一起以建立絕佳的擴增的實境應用程式。


## <a name="the-basics"></a>基本概念

偵測到的 iPhone，我們想要做什麼是世界上的呈現 3D 內容。   這個概念是將 blend 來自電話的攝影機與 3D 內容，內容，當使用者的電話上移動聊天室，確保 3D 物件行為，因為是在該空間的一部分-這由錨定至這個世界的物件。

![在 ARKit 動畫的圖](urhosharp-images/image1.gif)


我們將載入 3D 的資產，並將它們放置在世界中，使用 Urho 程式庫和，我們將使用 ARKit 取得視訊資料流來自相機與世界各地的電話的位置。   使用者移動具有其電話時，我們將使用的位置所做的變更來更新 Urho 引擎正在顯示之座標系統。

如此一來，當您將物件放在 3D 空間中，使用者移動，3D 物件的位置會反映和放置位置的位置。

## <a name="setting-up-your-application"></a>設定您的應用程式

### <a name="ios-application-launch"></a>iOS 應用程式啟動

您的 iOS 應用程式需求來建立並啟動 3D 內容，您可以建立實作的子類別[ `Urho.Application` ](https://developer.xamarin.com/api/type/Urho.Application/) ，並提供您安裝的程式碼覆寫`Start`方法。  這是其中場景取得填入資料，事件處理常式會安裝，依此類推。

我們已引入`Urho.ArkitApp`類別的子類別`Urho.Application`然後在其`Start`方法會執行大部分的工作。   您只需要執行應用程式是您現有 Urho 變更類型的基底類別`Urho.ArkitApp`，並可將執行您 urho 場景世界的應用程式。

### <a name="the-arkitapp-class"></a>ArkitApp 類別

由作業系統傳遞時，這個類別會提供一組方便的預設值，這兩個場景以某些索引鍵的物件以及 ARKit 事件的處理。

安裝程式會發生`Start`虛擬方法。   當您覆寫這個方法在子類別上時，您必須確定鏈結至您的家長使用`base.Start()`上您自己的實作。

`Start`方法設定場景、 檢視區、 相機和方向的光線，並且呈現以公用屬性：

- [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene/)來保存您的物件
- 方向[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/)具有陰影，和其位置已透過使用`LightNode`屬性
- [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/) ARKit 會傳遞至應用程式的更新時就會更新元件和
- [ `ViewPort` ](https://developer.xamarin.com/api/type/Urho.Viewport/)顯示結果。


### <a name="your-code"></a>您的程式碼

您接著需要子類別化`ArkitApp`類別並覆寫`Start`方法。   您的方法應該執行的第一件事是鏈結最多`ArkitApp.Start`藉由呼叫`base.Start()`。  之後，您可以使用的任何屬性設定由 ArkitApp 若要將物件加入至場景，自訂燈、 shadows 或您想要處理的事件。

ARKit/UrhoSharp 範例載入材質動畫的字元，並播放的動畫，具有下列實作：

    ```csharp
    public class MutantDemo : ArkitApp
    {
        [Preserve]
        public MutantDemo(ApplicationOptions opts) : base(opts) { }

        Node mutantNode;

        protected override void Start()
        {
            base.Start ();

            // Mutant
            mutantNode = Scene.CreateChild();
            mutantNode.Rotation = new Quaternion(x: 0, y:15, z:0);
            mutantNode.Position = new Vector3(0, -1f, 2f); /*two meters away*/
            mutantNode.SetScale(0.5f);

            var mutant = mutantNode.CreateComponent<AnimatedModel>();
            mutant.Model = ResourceCache.GetModel("Models/Mutant.mdl");
            mutant.Material = ResourceCache.GetMaterial("Materials/mutant_M.xml");

            var animation = mutantNode.CreateComponent<AnimationController>();
            animation.Play("Animations/Mutant_HipHop1.ani", 0, true, 0.2f);
        }
    }
    ```

而這是真的，您只需要在此時有 3D 內容擴增實境方式顯示。

Urho 3D 模型和動畫，對使用自訂格式，因此您需要為此格式匯出您的資產。   您可以使用類似的工具[Urho3D Blender 增益集](https://github.com/reattiva/Urho3D-Blender)和[UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) ，可以將轉換這些資產 DBX、 DAE、 OBJ、 Blend，等常見的格式從 Urho 所需的 3D 最大的格式。

若要了解有關建立使用 Urho 3D 應用程式的詳細資訊，請造訪[簡介 UrhoSharp](~/graphics-games/urhosharp/introduction.md)指南。

## <a name="arkitapp-in-depth"></a>ArkitApp 深度

> [!NOTE]
> 本節適用於開發人員想要自訂預設經驗 UrhoSharp 和 ARKit，或者想要獲得更深層的了解整合如何運作。   您不需要在閱讀本節。

ARKit API 是很簡單，您建立及設定[ARSession](https://developer.apple.com/documentation/arkit/arsession)的物件，然後啟動傳遞[ARFrame](https://developer.apple.com/documentation/arkit/arframe)物件。   其中包含這兩個映像擷取的相機，以及裝置的估計的實際位置。

我們會撰寫一同由相機我們傳遞具有我們 3D 內容、 影像及調整攝影機中 UrhoSharp 要比對中的裝置位置和位置的機會。

下圖顯示項目正在進行`ArkitApp`類別：

[ ![類別和 ArkitApp 螢幕的圖表](urhosharp-images/image2.png)](urhosharp-images/image2.png)

### <a name="rendering-the-frames"></a>轉譯畫面格

這個概念很簡單，結合來自相機與我們 3D 圖形產生結合的映像的視訊。     我們將會在順序中，取得一系列的這些擷取的映像，我們會混合這項輸入與 Urho 場景。

簡單的方法會插入[ `RenderPathCommand` ](https://developer.xamarin.com/api/type/Urho.RenderPathCommand/)到主[ `RenderPath` ](https://developer.xamarin.com/api/type/Urho.RenderPath/)。  這是一組繪製單一畫面格時所執行的命令。  此命令將會填滿任何紋理，我們將傳遞給它與檢視區。    我們設定此項目是程序，在第一個框架上和實際的定義是在第**ARRenderPath.xml**此時載入檔案。

不過，我們正面臨著這些兩者混合在一起的兩個問題：


1. 在 iOS、 GPU 紋理必須是 2 的乘冪，解析，但我們會相機中的畫面格沒有解決方式是 2 的乘冪，例如： 1280 x 720。
2. 框架會以編碼[YUV](https://en.wikipedia.org/wiki/YUV)格式，由兩個映像-luma 及色度。

YUV 框架有兩個不同的解決方法。  代表亮度 （基本上灰階映像） 和多小 640x360 色度元件 1280 x 720 映像：

![示範結合 Y 和 UV 元件的映像](urhosharp-images/image3.png)


若要繪製使用 OpenGL ES 完整彩色的影像我們必須撰寫採用明亮 （Y 元件） 和色度 （UV 平面） 從紋理位置的小型著色器。  在 UrhoSharp 它們有名稱為"sDiffMap"和"sNormalMap 」，並將它們轉換成 RGB 格式：

```csharp
mat4 ycbcrToRGBTransform = mat4(
    vec4(+1.0000, +1.0000, +1.0000, +0.0000),
    vec4(+0.0000, -0.3441, +1.7720, +0.0000),
    vec4(+1.4020, -0.7141, +0.0000, +0.0000),
    vec4(-0.7010, +0.5291, -0.8860, +1.0000));

vec4 ycbcr = vec4(texture2D(sDiffMap, vTexCoord).r,
                    texture2D(sNormalMap, vTexCoord).ra, 1.0);
gl_FragColor = ycbcrToRGBTransform * ycbcr;
```

若要呈現紋理沒有兩個解析的乘冪，我們必須定義 Texture2D 具備下列參數：

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

因此，我們就能夠呈現擷取的映像作為背景和類似的嚇人 mutant 呈現其上方任何場景。

### <a name="adjusting-the-camera"></a>調整相機

`ARFrame`物件也包含估計的裝置位置。  我們現在我們要移動遊戲相機 ARFrame-ARKit 之前不是追蹤裝置方向 （向前復原、 音高和繞） 及呈現釘選在視訊頂端防偽-但如果您的位元移動您的裝置-非同小可粗製濫造會漂移。

發生這種情況，所以例如迴轉儀的內建感應器沒有能夠追蹤動作，他們可以只加速。  ARKit 分析每個框架和擷取功能點追蹤，因此能夠提供正確的轉換矩陣包含移動和旋轉的資料。

比方說，這是我們要如何取得目前的位置：

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

我們使用`-row.Z`因為 ARKit 使用慣用右手座標系統。


### <a name="plane-detection"></a>平面偵測

ARKit 能夠偵測到水平平面和這項功能可讓您與真實世界互動，例如，我們可以置於 mutant 真正的資料表或樓層。 若要這樣做，最簡單的方式是使用 HitTest 方法 (raycasting)。 轉換螢幕座標 (0.5，0.5 是中心) 為真實世界座標 (0，0，0 是第一個畫面格的位置)。

```chsarp
protected Vector3? HitTest(float screenX = 0.5f, float screenY = 0.5f)
{
    var result = ARSession.CurrentFrame.HitTest(new CGPoint(screenX, screenY),
        ARHitTestResultType.ExistingPlaneUsingExtent)?.FirstOrDefault();
    if (result != null)
    {
        var row = result.WorldTransform.Row3;
        return new Vector3(row.X, row.Y, -row.Z);
    }
    return null;
}
```

現在我們可以根據 where 我們點選至裝置螢幕上水平的介面上放置 mutant:

```chsarp
void OnTouchEnd(TouchEndEventArgs e)
{
    float x = e.X / (float)Graphics.Width;
    float y = e.Y / (float)Graphics.Height;
    var pos = HitTest(x, y);
    if (pos != null)
    mutantNode.Position = pos.Value;
}
```

![與檢視移動平面變更動畫的圖](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>實際的光源

根據真實世界光線條件中，虛擬場景應該深淺以更符合隔離。 ARFrame 包含 LightEstimate 屬性可讓我們調整 Urho 環境光線，這是以這種方式：


    var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
    var zone = Scene.GetComponent<Zone>();
    zone.AmbientColor = Color.White * ambientIntensity;


### <a name="beyond-ios---hololens"></a>IOS-HoloLens 之外

UrhoSharp[所有主要的作業系統上執行](~/graphics-games/urhosharp/platform/index.md)，因此您可以重複使用現有的程式碼其他位置。

HoloLens 是其中一個最令人興奮的平台執行。   這表示，您可以輕鬆地切換 iOS 和 HoloLens 建置使用 UrhoSharp 臻夾帶現實應用程式。

您可以找到 MutantDemo 來源的[github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo)。


## <a name="related-links"></a>相關連結

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [（含 UrhoSharp) ARKitXamarinDemo （範例）](https://github.com/EgorBo/ARKitXamarinDemo)
