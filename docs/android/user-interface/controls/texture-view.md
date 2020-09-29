---
title: Xamarin. Android TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2017
ms.openlocfilehash: 3085131e251a787965c91216106becb6c954da85
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457701"
---
# <a name="xamarinandroid-textureview"></a>Xamarin. Android TextureView

`TextureView`類別是使用硬體加速2d 轉譯的視圖，可讓影片或 OpenGL 內容資料流程顯示。 例如，下列螢幕擷取畫面顯示的是 `TextureView` 顯示來自裝置相機的實況饋送：

[![裝置相機中的即時影像範例螢幕擷取畫面](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

不同于 `SurfaceView` 也可用來顯示 OpenGL 或影片內容的類別，TextureView 不會轉譯成不同的視窗。
因此， `TextureView` 能夠支援像任何其他視圖一樣的視圖轉換。 例如，您 `TextureView` 只要設定 `Rotation` 屬性（property）、設定其 `Alpha` 屬性（property），依此類推，就可以完成輪替。

因此， `TextureView` 我們現在可以進行一些動作，像是顯示相機中的即時串流並進行轉換，如下列程式碼所示：

```csharp
public class TextureViewActivity : Activity,
    TextureView.ISurfaceTextureListener
{
    Camera _camera;
    TextureView _textureView;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        _textureView = new TextureView (this);
        _textureView.SurfaceTextureListener = this;

        SetContentView (_textureView);
    }

    public void OnSurfaceTextureAvailable (
        Android.Graphics.SurfaceTexture surface,
        int width, int height)
    {
        _camera = Camera.Open ();
        var previewSize = _camera.GetParameters ().PreviewSize;
        _textureView.LayoutParameters =
            new FrameLayout.LayoutParams (previewSize.Width,
                previewSize.Height, (int)GravityFlags.Center);

        try {
            _camera.SetPreviewTexture (surface);
            _camera.StartPreview ();
        } catch (Java.IO.IOException ex) {
            Console.WriteLine (ex.Message);
        }

        // this is the sort of thing TextureView enables
        _textureView.Rotation = 45.0f;
        _textureView.Alpha = 0.5f;
    }
    …
}
```

上述程式碼會 `TextureView` 在活動的方法中建立實例 `OnCreate` ，並將活動設定為 `TextureView` 的 `SurfaceTextureListener` 。 為，活動會實作為 `SurfaceTextureListener` `TextureView.ISurfaceTextureListener` 介面。 `OnSurfaceTextAvailable`當已備妥可供使用時，系統會呼叫方法 `SurfaceTexture` 。 在此方法中，我們會採用傳入的， `SurfaceTexture` 並將它設定為相機的預覽材質。 然後，我們可以自由執行以視圖為基礎的一般作業，例如設定 `Rotation` 和 `Alpha` ，如上述範例所示。 產生的應用程式會在裝置上執行，如下所示：

[![在裝置上執行的應用程式範例，顯示影像](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

若要使用 `TextureView` ，您必須啟用硬體加速，其預設會是 API 層級14。 此外，因為此範例使用相機，所以 `android.permission.CAMERA` `android.hardware.camera` 必須在 **AndroidManifest.xml**中設定許可權和功能。

## <a name="related-links"></a>相關連結

- [TextureViewDemo (範例) ](/samples/xamarin/monodroid-samples/textureviewdemo)/) 