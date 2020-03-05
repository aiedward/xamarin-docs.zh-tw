---
title: Xamarin. Android TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2017
ms.openlocfilehash: 2ffa544789e0d605a241c8e038c790650a7fc6a3
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292867"
---
# <a name="xamarinandroid-textureview"></a>Xamarin. Android TextureView

`TextureView` 類別是使用硬體加速2D 轉譯的視圖，可讓您顯示影片或 OpenGL 內容串流。 例如，下列螢幕擷取畫面顯示從裝置相機顯示即時摘要的 `TextureView`：

[從裝置相機 ![即時影像的範例螢幕擷取畫面](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

不同于 `SurfaceView` 類別（也可以用來顯示 OpenGL 或影片內容），TextureView 不會轉譯成另一個視窗。
因此，`TextureView` 可以像任何其他視圖一樣支援視圖轉換。 例如，您可以藉由設定其 [`Rotation`] 屬性、其透明度 `Alpha` 屬性等等來完成旋轉 `TextureView`。

因此，在 `TextureView`，我們現在可以執行像是從相機顯示即時串流並加以轉換之類的動作，如下列程式碼所示：

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

上述程式碼會在活動的 `OnCreate` 方法中建立 `TextureView` 實例，並將活動設定為 `TextureView`的 `SurfaceTextureListener`。 為 `SurfaceTextureListener`，活動會執行 `TextureView.ISurfaceTextureListener` 介面。 當 `SurfaceTexture` 已準備好可供使用時，系統將會呼叫 `OnSurfaceTextAvailable` 方法。 在此方法中，我們會採用傳入的 `SurfaceTexture`，並將它設定為相機的預覽材質。 然後，我們可以自由地執行以視圖為基礎的一般作業，例如設定 `Rotation` 和 `Alpha`，如上述範例所示。 產生的應用程式會在裝置上執行，如下所示：

[在裝置上執行之應用程式的 ![範例，顯示影像](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

若要使用 `TextureView`，必須啟用硬體加速，其預設會是 API 層級14。 此外，由於此範例使用相機，因此必須在**androidmanifest.xml**中設定 `android.permission.CAMERA` 許可權和 `android.hardware.camera` 功能。

## <a name="related-links"></a>相關連結

- [TextureViewDemo （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)/）
