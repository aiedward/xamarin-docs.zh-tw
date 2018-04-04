---
title: TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/30/2017
ms.openlocfilehash: 1cd332894deac1e1fb076f647bb0120bda2306da
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="textureview"></a>TextureView

`TextureView`類別是會使用硬體加速的 2D 轉譯，讓視訊或 OpenGL 內容資料流所要顯示的檢視。 例如，下列螢幕擷取畫面顯示`TextureView`顯示裝置的相機中的即時摘要：

[![即時從裝置的相機映像的範例螢幕擷取畫面](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

不同於`SurfaceView`類別，也可以用來顯示 OpenGL 或視訊內容，TextureView 不會轉譯成另一個視窗。
因此，`TextureView`能夠支援任何其他檢視一樣檢視轉換。 比方說，旋轉`TextureView`可藉由只要設定其`Rotation`屬性，藉由設定其透明度其`Alpha`屬性，依此類推。

因此，與`TextureView`我們現在可以執行下列作業顯示來自相機的即時資料流，並轉換，如下列程式碼所示：

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

上述程式碼會建立`TextureView`中活動的執行個體`OnCreate`方法，並設定為活動`TextureView`的`SurfaceTextureListener`。 要`SurfaceTextureListener`，活動會實作`TextureView.ISurfaceTextureListener`介面。 系統會呼叫`OnSurfaceTextAvailable`方法時`SurfaceTexture`可供使用。 在此方法中，我們採用`SurfaceTexture`的是傳入，並將它設相機的預覽紋理。 然後，就可用來執行一般檢視為基礎的作業，例如設定`Rotation`和`Alpha`，如上述範例所示。 產生的應用程式，並在裝置上，執行如下所示：

[![顯示映像，在裝置上執行的應用程式的範例](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

若要使用`TextureView`，硬體加速必須啟用，它會根據預設，從應用程式開發介面層級 14 開始。 也，因為這個範例會使用相機上，同時`android.permission.CAMERA`權限和`android.hardware.camera`功能必須在設定**AndroidManifest.xml**。



## <a name="related-links"></a>相關連結

- [TextureViewDemo （範例）](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [介紹的冰淇淋三明治](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
