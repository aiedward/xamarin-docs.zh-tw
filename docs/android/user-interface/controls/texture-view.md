---
title: TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2017
ms.openlocfilehash: f43147d98599d36aa136e4ddc2975205e0e69e26
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122838"
---
# <a name="textureview"></a>TextureView

`TextureView`類別是使用硬體加速的 2D 轉譯，若要啟用的影片或 OpenGL 要顯示的內容資料流的檢視。 例如，下列螢幕擷取畫面顯示`TextureView`顯示從裝置的相機的即時摘要：

[![從裝置的相機的即時映像的範例螢幕擷取畫面](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

不同於`SurfaceView`類別，也可以用來顯示 OpenGL 或視訊內容，TextureView 不會轉譯成另一個視窗。
因此，`TextureView`能夠支援檢視轉換任何其他檢視一樣。 比方說，旋轉`TextureView`可藉由直接設定其`Rotation`屬性，藉由設定其透明度其`Alpha`屬性，並依此類推。

因此，使用`TextureView`我們現在可以執行下列動作顯示來自相機的即時資料流，並轉換，如下列程式碼所示：

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

上述程式碼會建立`TextureView`中的活動執行個體`OnCreate`方法，並設定為活動`TextureView`的`SurfaceTextureListener`。 要`SurfaceTextureListener`，活動會實作`TextureView.ISurfaceTextureListener`介面。 系統會呼叫`OnSurfaceTextAvailable`方法時`SurfaceTexture`可供使用。 在這種方法，我們會`SurfaceTexture`，會傳入，並將它設相機的預覽的紋理。 然後，就可以執行一般檢視為基礎的作業，例如設定`Rotation`和`Alpha`，如上述範例所示。 產生的應用程式，並在裝置上，執行如下所示：

[![在顯示影像的裝置上執行應用程式範例](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

若要使用`TextureView`，硬體加速功能必須啟用，它會根據預設，從 API 層級 14 開始。 也，因為此範例會使用相機上，同時`android.permission.CAMERA`權限和`android.hardware.camera`必須設定功能**AndroidManifest.xml**。



## <a name="related-links"></a>相關連結

- [TextureViewDemo （範例）](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [簡介 Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
