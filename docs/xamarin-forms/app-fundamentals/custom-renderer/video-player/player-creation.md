---
title: 建立平台的視訊播放程式
description: 本文說明如何實作影片播放器自訂轉譯器，每個平台上，使用 Xamarin.Forms。
ms.prod: xamarin
ms.assetid: EEE2FB9B-EB73-4A3F-A859-7A1D4808E149
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 205adf802bc0fc496d79e2b9df4a4360e6c27dc0
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241120"
---
# <a name="creating-the-platform-video-players"></a>建立平台的視訊播放程式

[ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)方案包含所有實作的程式碼的 Xamarin.Forms 影片播放器。 它也示範如何使用影片播放器應用程式內一系列的網頁。 所有`VideoPlayer`程式碼和其平台轉譯器位於專案資料夾，名為`FormsVideoLibrary`，也使用的命名空間和`FormsVideoLibrary`。 這應該讓您輕鬆地將檔案複製到您自己的應用程式和參考類別。

## <a name="the-video-player"></a>此視訊播放程式

[ `VideoPlayer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/VideoPlayer.cs)類別是一部分**VideoPlayerDemos** .NET 標準平台間共用的文件庫。 它衍生自`View`:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
    }
}
```

這個類別的成員 (而`IVideoPlayerController`介面)，請依照下列文章中所述。

三個平台的每個包含類別，名為`VideoPlayerRenderer`，其中包含實作視訊播放程式的平台專屬程式碼。 此轉譯器的主要工作是建立該平台的視訊播放程式。

### <a name="the-ios-player-view-controller"></a>IOS 的播放程式檢視控制器

在 iOS 中實作影片播放器時，會需要數個類別。 應用程式會先建立[ `AVPlayerViewController` ](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) ，然後設定[ `Player` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.Player/)屬性型別的物件[ `AVPlayer` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayer/)。 當播放程式指派的視訊來源需要額外的類別。

像所有轉譯器而言，iOS [ `VideoPlayerRenderer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos.iOS/VideoPlayerRenderer.cs)包含`ExportRenderer`屬性，可識別`VideoPlayer`產生器檢視：

```csharp
using System;
using System.ComponentModel;
using System.IO;

using AVFoundation;
using AVKit;
using CoreMedia;
using Foundation;
using UIKit;

using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.iOS.VideoPlayerRenderer))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
    }
}
```

平台控制項設定的轉譯器通常衍生自[ `ViewRenderer<View, NativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs)類別，其中`View`方法是透過 Xamarin.Forms`View`衍生項目 (在此情況下， `VideoPlayer`) 和`NativeView`是 iOS`UIView`轉譯器類別衍生。 此轉譯器，該泛型引數只設定為`UIView`，稍後就會看到的原因。

當轉譯器為基礎`UIViewController`衍生項目 （與此項是），則類別應該覆寫`ViewController`屬性和傳回檢視控制器，在此情況下`AVPlayerViewController`。 也就是目的`_playerViewController`欄位：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Create AVPlayerViewController
                    _playerViewController = new AVPlayerViewController();

                    // Set Player property to AVPlayer
                    player = new AVPlayer();
                    _playerViewController.Player = player;

                    // Use the View from the controller as the native control
                    SetNativeControl(_playerViewController.View);
                }
                ···
            }
        }
        ···
    }
}
```

主要責任`OnElementChanged`覆寫是進行檢查，如果`Control`屬性是`null`而且，如果是，建立平台控制項，並將它傳遞給`SetNativeControl`方法。 在此情況下，才可從該物件`View`屬性`AVPlayerViewController`。 確認`UIView`衍生項目剛好是名為私用類別`AVPlayerView`，但它是私用，因為它不能明確地指定做為第二個泛型引數`ViewRenderer`。

通常`Control`轉譯器類別的屬性之後指`UIView`用來實作轉譯器，但在此情況下`Control`屬性不使用其他位置。

### <a name="the-android-video-view"></a>Android 的視訊檢視

用於 Android 的轉譯器`VideoPlayer`為基礎的 Android [ `VideoView` ](https://developer.xamarin.com/api/type/Android.Widget.VideoView/)類別。 不過，如果`VideoView`單獨用於播放視訊中的 Xamarin.Forms 應用程式，視訊的填滿的區域分配`VideoPlayer`而不會維護正確的外觀比例。 這個原因 （如您會看到），`VideoView`進行 Android 的子系`RelativeLayout`。 A`using`指示詞定義`ARelativeLayout`區別 Xamarin.Forms `RelativeLayout`，位於第二個泛型引數和`ViewRenderer`:

```csharp
using System;
using System.ComponentModel;
using System.IO;

using Android.Content;
using Android.Media;
using Android.Widget;
using ARelativeLayout = Android.Widget.RelativeLayout;

using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.Droid.VideoPlayerRenderer))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        public VideoPlayerRenderer(Context context) : base(context)
        {
        }
        ···
    }
}
```

從開始，在 Xamarin.Forms 2.5，Android 轉譯器應該包含的建構函式`Context`引數。

`OnElementChanged`覆寫會建立兩者`VideoView`和`RelativeLayout`和設定的版面配置參數`VideoView`為置中在`RelativeLayout`。


```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Save the VideoView for future reference
                    videoView = new VideoView(Context);

                    // Put the VideoView in a RelativeLayout
                    ARelativeLayout relativeLayout = new ARelativeLayout(Context);
                    relativeLayout.AddView(videoView);

                    // Center the VideoView in the RelativeLayout
                    ARelativeLayout.LayoutParams layoutParams =
                        new ARelativeLayout.LayoutParams(LayoutParams.MatchParent, LayoutParams.MatchParent);
                    layoutParams.AddRule(LayoutRules.CenterInParent);
                    videoView.LayoutParameters = layoutParams;

                    // Handle a VideoView event
                    videoView.Prepared += OnVideoViewPrepared;

                    // Use the RelativeLayout as the native control
                    SetNativeControl(relativeLayout);
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            base.Dispose(disposing);
        }

        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
    }
}
```

處理常式`Prepared`事件附加在這個方法，且在卸離`Dispose`方法。 這個事件一經引發時`VideoView`有足夠的資訊來開始播放視訊檔案。

### <a name="the-uwp-media-element"></a>UWP 媒體項目

在通用 Windows 平台 (UWP)，最常見的視訊播放程式是[ `MediaElement` ](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/)。 這份文件的`MediaElement`表示[ `MediaPlayerElement` ](/uwp/api/windows.ui.xaml.controls.mediaplayerelement/)應該改用時，才需要支援的 Windows 10 開頭 1607年組建版本。

`OnElementChanged`覆寫，就需要建立`MediaElement`、 設定幾個事件處理常式，並傳遞`MediaElement`物件`SetNativeControl`:

```csharp
using System;
using System.ComponentModel;

using Windows.Storage;
using Windows.Storage.Streams;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.UWP.VideoPlayerRenderer))]

namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    MediaElement mediaElement = new MediaElement();
                    SetNativeControl(mediaElement);

                    mediaElement.MediaOpened += OnMediaElementMediaOpened;
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                Control.MediaOpened -= OnMediaElementMediaOpened;
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }        
        ···
    }
}
```

在卸離的兩個事件處理常式`Dispose`轉譯器的事件。

## <a name="showing-the-transport-controls"></a>顯示傳輸控制

包含在三個平台的所有視訊播放程式支援一組預設的傳輸控制包含按鈕，以播放和暫停，並表示視訊中目前的位置，並移至新位置的列。

`VideoPlayer`類別會定義名為的屬性`AreTransportControlsEnabled`並設定預設值為`true`:


```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // AreTransportControlsEnabled property
        public static readonly BindableProperty AreTransportControlsEnabledProperty =
            BindableProperty.Create(nameof(AreTransportControlsEnabled), typeof(bool), typeof(VideoPlayer), true);

        public bool AreTransportControlsEnabled
        {
            set { SetValue(AreTransportControlsEnabledProperty, value); }
            get { return (bool)GetValue(AreTransportControlsEnabledProperty); }
        }
        ···
    }
}
```

雖然此屬性同時具有`set`和`get`存取子中，必須處理的情況下，設定屬性時，才轉譯器。 `get`存取子只會傳回屬性的目前值。

屬性，例如`AreTransportControlsEnabled`處理平台轉譯器有兩種：

- 第一次時，才能建立 Xamarin.Forms`VideoPlayer`項目。 這表示在`OnElementChanged`轉譯器的覆寫時`NewElement`屬性不是`null`。 在這個階段中，可以設定的轉譯器是定義在自己的平台影片播放器從屬性的初始值`VideoPlayer`。

- 如果在屬性`VideoPlayer`稍後變更，然後在`OnElementPropertyChanged`轉譯器會呼叫。 這可讓更新新的屬性設定為基礎的平台影片播放器的轉譯器。

以下是如何`AreTransportControlsEnabled`屬性處理的三個平台：

### <a name="ios-playback-controls"></a>iOS 的播放控制項

IOS 屬性`AVPlayerViewController`，可控制顯示傳輸控制項是[ `ShowsPlaybackControls` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.ShowsPlaybackControls/)。 以下是該屬性在 iOS 中的設定方式`VideoViewRenderer`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            ((AVPlayerViewController)ViewController).ShowsPlaybackControls = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

`Element`轉譯器的屬性參考到`VideoPlayer`類別。

### <a name="the-android-media-controller"></a>Android 媒體控制站

在 Android 中，顯示傳輸控制項需要建立[ `MediaController` ](https://developer.xamarin.com/api/type/Android.Widget.MediaController/)物件並將它與`VideoView`物件。 中會示範機制`SetAreTransportControlsEnabled`方法：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            if (Element.AreTransportControlsEnabled)
            {
                mediaController = new MediaController(Context);
                mediaController.SetMediaPlayer(videoView);
                videoView.SetMediaController(mediaController);
            }
            else
            {
                videoView.SetMediaController(null);

                if (mediaController != null)
                {
                    mediaController.SetMediaPlayer(null);
                    mediaController = null;
                }
            }
        }
        ···
    }
}
```

### <a name="the-uwp-transport-controls-property"></a>UWP 傳輸控制項屬性

UWP`MediaElement`定義屬性，名為[ `AreTransportControlsEnabled` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_AreTransportControlsEnabled)，如此一來，屬性會設定從`VideoPlayer`相同名稱的屬性：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            Control.AreTransportControlsEnabled = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

一個屬性，才能開始播放視訊： 這很重要`Source`參考視訊檔案的屬性。 實作`Source`屬性所描述的下一個文件中[播放 Web 視訊](web-videos.md)。


## <a name="related-links"></a>相關連結

- [視訊播放程式示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
