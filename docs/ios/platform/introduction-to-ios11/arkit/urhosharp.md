---
title: ARKit 使用 UrhoSharp 在 Xamarin.iOS 中
description: 本文件說明如何設定 Xamarin.iOS，ARKit 應用程式，然後查看畫面格呈現方式，如何調整相機、 如何偵測平面，如何使用光線和更多功能。 此外，它也會討論 UrhoSharp 和撰寫程式碼的 HoloLens。
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/01/2017
ms.openlocfilehash: b02ecc8a40f6ff8a1862d50202439d369003a53d
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2019
ms.locfileid: "57981597"
---
# <a name="using-arkit-with-urhosharp-in-xamarinios"></a>ARKit 使用 UrhoSharp 在 Xamarin.iOS 中

引進[ARKit](https://developer.apple.com/arkit/)，Apple 已將結構簡化的開發人員建立擴增的實境應用程式。 ARKit 可以追蹤您的裝置的確切位置，並偵測在世界上的各種介面，然後是由開發人員，blend 超出 ARKit 進入您的程式碼的資料。

[UrhoSharp](~/graphics-games/urhosharp/index.md)提供全方位且容易使用的 3D API，可用來建立 3D 應用程式。   這兩種都可以是混合在一起，，來提供實體世界的相關資訊的 ARKit 和 Urho 來呈現結果。

此頁面說明如何連接在一起，建立絕佳的擴增的實境應用程式的這兩個世界。


## <a name="the-basics"></a>基本概念

IPhone/iPad 所見，我們要做什麼是世界上的呈現 3D 內容。   這個構想是將 blend 來自裝置的相機使用 3D 內容，內容，以確保裝置的使用者移動房間周圍的 3D 物件的行為與其屬於它們的這個聊天室-這是藉由錨定到此世界的物件。

![ARKit 中的動畫的圖形](urhosharp-images/image1.gif)


我們將載入 3D 資產，並將它們放置在世界中，使用 Urho 程式庫，而且我們將使用 ARKit 取得來自相機，以及位置的世界中的電話的視訊資料流。   當使用者移動使用他的電話，我們將使用位置中所做的變更來更新顯示 Urho 引擎座標系統。

如此一來，當您放置在 3D 空間中的物件，並在使用者移動，3D 物件的位置會反映的位置和放置位置的位置。

## <a name="setting-up-your-application"></a>設定您的應用程式

### <a name="ios-application-launch"></a>iOS 應用程式啟動

您的 iOS 應用程式，就必須建立並啟動您的 3D 內容，您可以建立實作的子類別[ `Urho.Application` ](https://developer.xamarin.com/api/type/Urho.Application/) ，並提供您的安裝程式碼，藉由覆寫`Start`方法。  這是其中場景取得填入資料，事件處理常式會設定等等。

我們引進了`Urho.ArkitApp`類別子類別化`Urho.Application`並在其`Start`方法會執行繁重的工作。   您只需要執行應用程式是您現有 Urho 變更類型的基底類別`Urho.ArkitApp`，而且您有會執行您 urho 場景世界中的應用程式。

### <a name="the-arkitapp-class"></a>ArkitApp 類別

由作業系統傳遞時，這個類別會提供一組方便的預設值，這兩個場景中填入某些索引鍵的物件以及 ARKit 事件的處理。

安裝程式會發生在`Start`虛擬方法。   當您覆寫此方法，在您的子類別上時，您必須先確定鏈結至您的家長使用`base.Start()`上您自己的實作。

`Start`方法設定場景、 檢視區、 相機和方向的光線，並呈現這些為公用屬性：

- [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene/)來保存您的物件，
- 方向性[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/)陰影及的位置均可透過使用`LightNode`屬性
- [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/) ARKit 提供給應用程式的更新時，會更新其元件和
- [ `ViewPort` ](https://developer.xamarin.com/api/type/Urho.Viewport/)顯示結果。


### <a name="your-code"></a>您的程式碼

您接著需要子類別`ArkitApp`類別並覆寫`Start`方法。   您的方法應該做的第一件事是鏈結最多`ArkitApp.Start`藉由呼叫`base.Start()`。  在那之後，您可以使用任何 ArkitApp 屬性設定若要將物件加入至場景，自訂的號誌、 shadows 或您想要處理的事件。

ARKit/UrhoSharp 範例載入紋理動畫的字元，並播放動畫，使用下列實作：

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

而這是真的，您只需要在此時有您在擴增實境中顯示的 3D 內容。

Urho 3D 模型和動畫，使用自訂的格式，因此您必須將您的資產匯出成此格式。   您可以使用工具，像是[Urho3D Blender 增益集](https://github.com/reattiva/Urho3D-Blender)並[UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) ，可以將轉換這些資產從熱門的格式，例如 DBX、 DAE、 OBJ、 Blend，Urho 所需的 3D-最大值的格式。

若要了解如何建立使用 Urho 的 3D 應用程式，請造訪[UrhoSharp 簡介](~/graphics-games/urhosharp/introduction.md)指南。

## <a name="arkitapp-in-depth"></a>深入了解 ArkitApp

> [!NOTE]
> 本節適用於開發人員想要自訂預設的經驗 UrhoSharp 和 ARKit，或者想要更深入的了解整合的運作方式。   您不需要在閱讀本節。

ARKit API 很簡單，您建立並設定[ARSession](https://developer.apple.com/documentation/arkit/arsession)的物件，然後開始傳遞[ARFrame](https://developer.apple.com/documentation/arkit/arframe)物件。   其中包含這兩個擷取的相機，以及裝置之估計的實際位置的映像。

我們將撰寫觀景窗我們傳遞 3D 內容，使用的映像和調整以符合在裝置的位置和位置的機會 UrhoSharp 觀景窗。

下圖顯示正在進行什麼`ArkitApp`類別：

[![類別和 ArkitApp 螢幕的圖表](urhosharp-images/image2.png)](urhosharp-images/image2.png#lightbox)

### <a name="rendering-the-frames"></a>轉譯畫面格

這個概念十分簡單，結合來自我們的 3D 圖形，以產生結合的映像使用相機的視訊。     我們將會依序出現一系列的這些擷取的映像，我們將使用 Urho 場景混用這項輸入。

簡單的方法是插入[ `RenderPathCommand` ](https://developer.xamarin.com/api/type/Urho.RenderPathCommand/)貼入 main [ `RenderPath` ](https://developer.xamarin.com/api/type/Urho.RenderPath/)。  這是一組繪製單一畫面格時所執行的命令。  此命令將會填滿與任何我們傳遞給它的紋理檢視區。    我們設定此功能的程序的第一個畫面上，且實際的定義在第進行**ARRenderPath.xml**目前載入的檔案。

不過，我們都面臨來混合這兩個世界在一起的兩個問題：


1. 在 iOS 上，GPU 紋理必須是二的次方的解析度，但我們將會收到來自相機的框架不需要解析的乘冪，例如：1280 x 720。
2. 畫面格會以編碼[YUV](https://en.wikipedia.org/wiki/YUV)格式，由兩個映像-luma 與色度。

YUV 框架有兩個不同的解析度。  1280 x 720 影像，表示明亮度 （基本上灰階映像） 和多小 640x360 色度元件：

![示範結合 Y 和 UV 元件的映像](urhosharp-images/image3.png)


若要繪製完整的彩色映像，以使用 OpenGL ES 我們必須撰寫小型的著色器材質的位置從採用明亮度 （Y 元件） 及色度 （UV 平面）。  在 UrhoSharp 它們有名稱為"sDiffMap"和"sNormalMap 」，並將它們轉換成 RGB 格式：

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

要呈現沒有兩個解析的乘冪的材質我們必須定義 Texture2D 具備下列參數：

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

因此，我們就能夠呈現擷取的影像作為背景和呈現任何場景上方的嚇人 mutant。

### <a name="adjusting-the-camera"></a>調整觀景窗

`ARFrame`物件還包含預估的裝置位置。  我們現在我們需要移動遊戲相機 ARFrame-ARKit 之前仍無法追蹤裝置方向 （向前復原，字幅和繞） 而轉譯已釘選在視訊頂端雷射-但如果您有點移動您的裝置-大不了全像投影會漂移。

因為陀螺儀等的內建感應器不能追蹤移動，所以發生此情況，他們可以只加速。  ARKit 分析每個框架和擷取功能來追蹤點，並因此可得到正確轉換包含資料移動和旋轉的矩陣。

比方說，這是我們要如何取得目前的位置：

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

我們使用`-row.Z`因為 ARKit 使用右手性座標系統。


### <a name="plane-detection"></a>平面偵測

ARKit 能夠偵測水平平面和這項功能可讓您與真實世界互動，例如，我們可以在這裡放置 mutant 實際的資料表上鋪設的地磚。 若要這樣做，最簡單的方式是使用 HitTest 方法 (raycasting)。 它會將轉換螢幕座標 (0.5，0.5 中心) 成實際的座標 (0，0; 0 是第一個畫面格位置)。

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

現在我們可以將 mutant 放在哪裡而定我們點選裝置畫面上水平的介面上：

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

![變更的動畫的圖平面檢視移](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>逼真的光源

依據真實世界光線的條件，較淺或較深，使其更符合其周圍環境，應該是虛擬的場景。 ARFrame 包含 LightEstimate 屬性，我們可以使用它來調整 Urho 環境光線，這是如下所示：

```csharp
var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
var zone = Scene.GetComponent<Zone>();
zone.AmbientColor = Color.White * ambientIntensity;
```

### <a name="beyond-ios---hololens"></a>IOS-HoloLens 之外

UrhoSharp[會在所有主流作業系統上執行](~/graphics-games/urhosharp/platform/index.md)，因此您可以重複使用現有的程式碼在其他地方。

HoloLens 是其中一個最令人興奮的平台執行。   這表示，您可以輕鬆地切換 iOS 和 HoloLens 建置使用 UrhoSharp awesome 擴增實境應用程式。

您可以找到在 MutantDemo 來源[github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo)。


## <a name="related-links"></a>相關連結

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [（使用 UrhoSharp) ARKitXamarinDemo （範例）](https://github.com/EgorBo/ARKitXamarinDemo)
