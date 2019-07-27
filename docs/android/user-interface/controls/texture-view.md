---
title: Xamarin. Android TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2017
ms.openlocfilehash: 589cf1787f5dc3090cbfb1165e91d8ef58df37a6
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510157"
---
# <a name="xamarinandroid-textureview"></a>Xamarin. Android TextureView

`TextureView`類別是使用硬體加速2d 轉譯的視圖, 可讓您顯示影片或 OpenGL 內容串流。 例如, 下列螢幕擷取畫面顯示`TextureView`從裝置相機顯示即時摘要:

[![來自裝置相機之即時影像的範例螢幕擷取畫面](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

不同于`SurfaceView`類別 (也可以用來顯示 OpenGL 或 video 內容), TextureView 不會轉譯成另一個視窗。
因此, `TextureView`可以像任何其他視圖一樣支援視圖轉換。 例如, 您`TextureView`可以藉由`Rotation`設定其屬性來完成旋轉, 方法是藉由設定`Alpha`其屬性, 依此類推。

因此, `TextureView`我們現在可以執行像是從相機顯示即時串流並加以轉換之類的動作, 如下列程式碼所示:

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

上述程式碼會在`TextureView`活動的`OnCreate`方法中建立實例, 並`TextureView`將活動設定為的`SurfaceTextureListener`。 為`SurfaceTextureListener`, 活動`TextureView.ISurfaceTextureListener`會執行介面。 當備妥可供`OnSurfaceTextAvailable`使用時,系統會呼叫方法。`SurfaceTexture` 在此方法中, 我們會`SurfaceTexture`採用傳入的, 並將它設定為相機的預覽材質。 然後, 我們可以自由地執行以視圖為基礎的一般作業, 例如`Rotation`設定`Alpha`和, 如上述範例所示。 產生的應用程式會在裝置上執行, 如下所示:

[![在裝置上執行的應用程式範例, 顯示影像](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

若要使用`TextureView`, 則必須啟用硬體加速, 其預設會是 API 層級14。 此外, 由於此範例使用相機, `android.permission.CAMERA`因此必須在**androidmanifest.xml**中設定許可權`android.hardware.camera`和功能。



## <a name="related-links"></a>相關連結

- [TextureViewDemo (範例)](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [霜淇淋三明治簡介](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平臺](https://developer.android.com/sdk/android-4.0.html)
