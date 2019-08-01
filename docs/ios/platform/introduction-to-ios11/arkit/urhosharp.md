---
title: 在 Xamarin 中搭配使用 ARKit 與 UrhoSharp
description: 本檔說明如何在 Xamarin 中設定 ARKit 應用程式, 然後查看畫面格的呈現方式、如何調整相機、如何偵測平面、如何使用光源等等。 它也會討論 UrhoSharp 和撰寫 HoloLens 的程式碼。
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/01/2017
ms.openlocfilehash: 69ddb42f6bf51ec78d9735346c44efa94fb9c418
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68508766"
---
# <a name="using-arkit-with-urhosharp-in-xamarinios"></a>在 Xamarin 中搭配使用 ARKit 與 UrhoSharp

隨著[ARKit](https://developer.apple.com/arkit/)的引進, Apple 讓開發人員輕鬆建立增強的現實應用程式。 ARKit 可以追蹤您裝置的確切位置, 並偵測世界上的各種表面, 然後由開發人員將 ARKit 的資料混合到您的程式碼中。

[UrhoSharp](~/graphics-games/urhosharp/index.md)提供完整且便於使用的 3d API, 可讓您用來建立3d 應用程式。   這兩種都可以混合在一起、ARKit 以提供有關世界的實體資訊, 以及 Urho 來呈現結果。

此頁面說明如何將這兩個世界連接在一起, 以建立絕佳的增強型現實應用程式。

## <a name="the-basics"></a>基本概念

我們想要做的是將3D 內容呈現在世界上, 如 iPhone/iPad 所見。   其概念是將來自裝置相機的內容與3D 內容結合, 並隨著裝置的使用者在房間周圍移動, 以確保3D 物件的行為就像是該房間的一部分, 這是藉由將物件錨定到這個世界來完成。

![ARKit 中的動畫圖形](urhosharp-images/image1.gif)

我們將使用 Urho 程式庫來載入我們的3D 資產, 並將其放在世界中, 而我們將使用 ARKit 來取得來自相機的影片串流, 以及手機在世界中的位置。   當使用者行動電話時, 我們會使用位置中的變更來更新 Urho 引擎所顯示的座標系統。

如此一來, 當您將物件放在3D 空間中並移動使用者時, 3D 物件的位置會反映其放置位置和位置。

## <a name="setting-up-your-application"></a>設定應用程式

### <a name="ios-application-launch"></a>iOS 應用程式啟動

您的 iOS 應用程式需要建立並啟動您的3d 內容, 方法是建立並執行的子類別`Urho.Application` , 並藉由覆`Start`寫方法來提供您的安裝程式碼。  這就是您的場景填入資料的位置, 事件處理常式已設定, 依此類推。

我們引進了一個`Urho.ArkitApp`類別, 其中`Urho.Application`子類別和`Start`其方法會執行繁重的工作。   您需要對現有的 Urho 應用程式執行的動作, 是將基類變更為類型`Urho.ArkitApp` , 而且您有一個應用程式會在世界中執行 Urho 場景。

### <a name="the-arkitapp-class"></a>ArkitApp 類別

這個類別會提供一組便利的預設值, 這兩個場景都有一些重要物件, 以及由作業系統傳遞的 ARKit 事件處理。

此設定會在`Start`虛擬方法中進行。   當您在子類別上覆寫此方法時, 您必須在自己的執行中使用`base.Start()` , 以確保與您的父系連結。

`Start`方法會設定場景、視口、攝影機和方向光線, 並將其呈現為公用屬性:

- 用`Scene`來保存物件的。
- 具有陰影`Light`的方向, 而且其位置可`LightNode`透過屬性取得
- , `Camera`其元件會在 ARKit 傳遞應用程式的更新時更新, 並
- 顯示`ViewPort`結果的。

### <a name="your-code"></a>您的程式碼

接著, 您必須將`ArkitApp`類別子類別化, 並覆`Start`寫方法。   您的方法應該執行的第一件事, 是藉`ArkitApp.Start`由呼叫`base.Start()`來連結至。  之後, 您可以使用 ArkitApp 設定的任何屬性, 將您的物件新增至場景, 自訂您要處理的燈、陰影或事件。

ARKit/UrhoSharp 範例會載入具有材質的動畫字元並播放動畫, 並具有下列實現:

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

這就是您現在必須執行的工作, 以便在增強的現實中顯示3D 內容。

Urho 會針對3D 模型和動畫使用自訂格式, 因此您需要將資產匯出為此格式。   您可以使用[Urho3D Blender 增益集](https://github.com/reattiva/Urho3D-Blender)和[UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter)之類的工具, 將這些資產從受歡迎的格式 (例如, .DBX、DAE、OBJ、Blend、3d-Max) 轉換成 Urho 所需的格式。

若要深入瞭解如何使用 Urho 建立3D 應用程式, 請造訪[UrhoSharp 簡介](~/graphics-games/urhosharp/introduction.md)指南。

## <a name="arkitapp-in-depth"></a>深入 ArkitApp

> [!NOTE]
> 本節適用于想要自訂 UrhoSharp 和 ARKit 預設體驗的開發人員, 或想要更深入瞭解整合的運作方式。   您不需要閱讀本節。

ARKit API 相當簡單, 您可以建立並設定[ARSession](https://developer.apple.com/documentation/arkit/arsession)物件, 然後開始傳遞[ARFrame](https://developer.apple.com/documentation/arkit/arframe)物件。   其中包含相機所捕獲的影像, 以及裝置的預估實際位置。

我們將使用我們的3D 內容來撰寫相機傳遞給我們的影像, 並在 UrhoSharp 中調整相機, 以符合裝置位置和位置的機會。

下圖顯示`ArkitApp`類別中發生的情況:

[![ArkitApp 中的類別和畫面圖表](urhosharp-images/image2.png)](urhosharp-images/image2.png#lightbox)

### <a name="rendering-the-frames"></a>呈現畫面格

概念很簡單, 請將相機中的影片與我們的3D 圖形結合, 以產生合併的影像。     我們會依序取得一系列這些已捕獲的映射, 而我們會將此輸入與 Urho 場景混搭。

若要這麼做, 最簡單的方法是`RenderPathCommand`將插入主要`RenderPath`。  這是一組用來繪製單一框架的命令。  此命令將會以我們傳遞給它的任何材質來填滿此區。    我們會在第一個要處理的框架上進行這項設定, 而實際的定義會在此時載入的**ARRenderPath**檔案中完成。

不過, 我們面臨兩個問題, 可以將這兩個世界混合在一起:


1. 在 iOS 上, GPU 材質的解析度必須是2的乘冪, 但我們從相機取得的畫面不會有2乘冪的解析度, 例如:1280x720.
2. 畫面格是以[YUV](https://en.wikipedia.org/wiki/YUV)格式編碼, 以兩個影像 (luma 和色度) 表示。

YUV 框架有兩個不同的解析度。  代表亮度的1280x720 影像 (基本上是灰階影像) 和色度元件的640x360 更小:

![示範如何結合 Y 和 UV 元件的影像](urhosharp-images/image3.png)


若要使用 OpenGL ES 繪製完整的彩色影像, 我們必須撰寫一個從材質位置取得亮度 (Y 元件) 和色度 (UV 平面) 的小型著色器。  在 UrhoSharp 中, 其名稱為 "sDiffMap" 和 "sNormalMap", 並將它們轉換成 RGB 格式:

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

若要轉譯沒有兩個解析度之乘冪的材質, 我們必須使用下列參數來定義 Texture2D:

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

因此, 我們可以將已捕捉的影像轉譯為背景, 並將其上的任何場景轉譯為類似的變化。

### <a name="adjusting-the-camera"></a>調整相機

`ARFrame`物件也包含估計的裝置位置。  我們現在需要移動遊戲攝影機 ARFrame-在 ARKit 之前, 不是追蹤裝置方向 (變換、音調和偏擺) 並在影片上轉譯釘選的全息影像, 但如果您移動裝置, 則會漂移。

發生這種情況是因為內建的感應器 (例如陀螺儀) 無法追蹤移動作業, 因此只能加速。  ARKit 會分析每個畫面格並解壓縮功能點以進行追蹤, 因此能夠為我們提供包含移動和旋轉資料的正確轉換矩陣。

例如, 這就是我們可以取得目前位置的方式:

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

我們使用`-row.Z` , 因為 ARKit 會使用右手座標系統。


### <a name="plane-detection"></a>平面偵測

ARKit 能夠偵測到水準平面, 而這種功能可讓您與現實世界互動, 例如, 我們可以將變化放在實際的資料表或樓層上。 若要這麼做, 最簡單的方法是使用 HitTest 方法 (raycasting)。 它會將螢幕座標 (0.5; 0.5 為中間) 轉換成真實全局座標 (0; 0; 0是第一個框架的位置)。

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

現在, 我們可以根據裝置螢幕上的位置, 將變化放在水準表面上:

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

![將平面變更為 view 移動的動畫圖形](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>實際光源

根據真實世界光源的情況, 虛擬場景應較淡或較暗, 以符合其周圍的效果。 ARFrame 包含 LightEstimate 屬性, 可供我們用來調整 Urho 的環境光線, 這會如下所示:

```csharp
var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
var zone = Scene.GetComponent<Zone>();
zone.AmbientColor = Color.White * ambientIntensity;
```

### <a name="beyond-ios---hololens"></a>超出 iOS-HoloLens

UrhoSharp 會[在所有主要作業系統上執行](~/graphics-games/urhosharp/platform/index.md), 因此您可以在其他地方重複使用現有的程式碼。

HoloLens 是它在其上執行的最令人興奮的平臺之一。   這表示您可以輕鬆地在 iOS 和 HoloLens 之間切換, 以使用 UrhoSharp 建立絕佳的擴充現實應用程式。

您可以在[github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo)找到 MutantDemo 來源。


## <a name="related-links"></a>相關連結

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [ARKitXamarinDemo (使用 UrhoSharp) (範例)](https://github.com/EgorBo/ARKitXamarinDemo)
