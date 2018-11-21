---
title: 建立平台影片播放程式
description: 這篇文章說明如何實作每個平台上，使用 Xamarin.Forms 的視訊播放程式的自訂轉譯器。
ms.prod: xamarin
ms.assetid: EEE2FB9B-EB73-4A3F-A859-7A1D4808E149
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 0090ec798e8d7b1dfb9bd8e25f09d71ec0353b45
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171907"
---
# <a name="creating-the-platform-video-players"></a>建立平台影片播放程式

[ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)解決方案包含所有的程式碼實作適用於 Xamarin.Forms 的視訊播放器。 它也包含一系列將示範如何使用視訊的播放器應用程式內的頁面。 所有`VideoPlayer`程式碼和其平台轉譯器位於命名的專案資料夾`FormsVideoLibrary`，也可以使用命名空間`FormsVideoLibrary`。 這應該讓您輕鬆地將檔案複製到您自己的應用程式和參考的類別。

## <a name="the-video-player"></a>視訊播放程式

[ `VideoPlayer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/VideoPlayer.cs)類別是一部分**VideoPlayerDemos**平台之間共用的.NET Standard 程式庫。 它衍生自`View`:

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

每個平台包含類別，名為`VideoPlayerRenderer`包含實作視訊播放程式的平台特定程式碼。 此轉譯器的主要工作是建立該平台的視訊播放器。

### <a name="the-ios-player-view-controller"></a>IOS 播放程式檢視控制器

實作影片播放程式在 iOS 中時，會牽涉到數個類別。 應用程式會先建立[ `AVPlayerViewController` ](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) ，然後設定[ `Player` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.Player/)類型的物件屬性[ `AVPlayer` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayer/)。 播放程式指派的視訊來源時，不需要額外的類別。

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

設定平台控制項的轉譯器通常衍生自[ `ViewRenderer<View, NativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs)類別，其中`View`是 Xamarin.Forms`View`衍生項目 (在此情況下， `VideoPlayer`) 和`NativeView`是 iOS`UIView`衍生轉譯器類別。 此轉譯器，該泛型引數只是設定為`UIView`，您很快會看到的原因。

當轉譯器為基礎`UIViewController`衍生項目 （與此項為），則類別應該覆寫`ViewController`屬性，並傳回檢視控制器，在此情況下`AVPlayerViewController`。 也就是目的`_playerViewController`欄位：

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

主要是負責`OnElementChanged`檢查是否可覆寫`Control`屬性是`null`，而且，若是如此，建立平台控制項，並將它傳遞給`SetNativeControl`方法。 在此情況下，才可從該物件`View`屬性`AVPlayerViewController`。 該`UIView`剛好是名為私用類別衍生`AVPlayerView`，但因為它是私用，不能在做為第二個泛型引數明確指定`ViewRenderer`。

通常`Control`轉譯器類別的屬性之後指`UIView`用來實作的轉譯器，但在此情況下`Control`屬性不會用於其他位置。

### <a name="the-android-video-view"></a>Android 的視訊檢視

Android 的轉譯器，如`VideoPlayer`為基礎的 Android [ `VideoView` ](https://developer.xamarin.com/api/type/Android.Widget.VideoView/)類別。 不過，如果`VideoView`用來播放視訊 Xamarin.Forms 應用程式中，視訊會填滿區域分配給`VideoPlayer`而不需要維護正確的外觀比例。 這個原因 （如稍後所見），`VideoView`進行 Android 的子系`RelativeLayout`。 A`using`指示詞定義`ARelativeLayout`區別 Xamarin.Forms `RelativeLayout`，這第二個泛型引數`ViewRenderer`:

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

開始在 Xamarin.Forms 2.5，Android 的轉譯器應包含的建構函式`Context`引數。

`OnElementChanged`覆寫會同時建立`VideoView`並`RelativeLayout`和設定的版面配置參數`VideoView`以將它在置`RelativeLayout`。


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

處理常式`Prepared`事件會附加在這個方法，並在卸離`Dispose`方法。 此事件引發時`VideoView`有足夠的資訊，開始播放視訊檔案。

### <a name="the-uwp-media-element"></a>UWP 媒體項目

通用 Windows 平台 (UWP) 中，最常見的視訊播放程式會[ `MediaElement` ](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/)。 文件記載`MediaElement`指出[ `MediaPlayerElement` ](/uwp/api/windows.ui.xaml.controls.mediaplayerelement/)時，才需要支援版本的 Windows 10 組建 1607年的開頭應該改為使用。

`OnElementChanged`覆寫建立所需`MediaElement`、 設定幾個事件處理常式，並傳遞`MediaElement`物件`SetNativeControl`:

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

兩個事件處理常式會在中斷連結`Dispose`轉譯器的事件。

## <a name="showing-the-transport-controls"></a>顯示傳輸控制項

包含在平台的所有視訊播放程式支援一組預設的傳輸控制包含播放和暫停和列，以指出目前的位置，在影片中，並移至新位置的按鈕。

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

雖然這個屬性同時具有`set`和`get`存取子，轉譯器都有要處理的情況下，只有當屬性設定。 `get`存取子只會傳回屬性的目前值。

屬性，例如`AreTransportControlsEnabled`處理平台的轉譯器有兩種：

- Xamarin.Forms 建立時，第一次`VideoPlayer`項目。 這所示`OnElementChanged`轉譯器的覆寫時`NewElement`屬性不是`null`。 在此階段中，可以設定的轉譯器是定義於自己的平台影片播放程式，從屬性的初始值`VideoPlayer`。

- 如果中的屬性`VideoPlayer`稍後變更，則`OnElementPropertyChanged`呼叫轉譯器的方法。 這可讓更新新的屬性設定為基礎的平台影片播放程式的轉譯器。

下列各節將討論如何`AreTransportControlsEnabled`屬性在每個平台上處理。

### <a name="ios-playback-controls"></a>iOS 播放控制項

IOS 的屬性`AVPlayerViewController`管理顯示控制項是傳輸[ `ShowsPlaybackControls` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.ShowsPlaybackControls/)。 以下是如何在 iOS 中設定該屬性`VideoViewRenderer`:

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

`Element`轉譯器的屬性會參考`VideoPlayer`類別。

### <a name="the-android-media-controller"></a>Android 媒體控制器

在 Android 中，顯示傳輸控制項需要建立[ `MediaController` ](https://developer.xamarin.com/api/type/Android.Widget.MediaController/)物件，並將它與`VideoView`物件。 機制會示範在`SetAreTransportControlsEnabled`方法：

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

### <a name="the-uwp-transport-controls-property"></a>UWP 闇熁勂屬性

UWP`MediaElement`會定義名為的屬性[ `AreTransportControlsEnabled` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_AreTransportControlsEnabled)，以便設定從`VideoPlayer`相同名稱的屬性：

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

一個屬性，才能開始播放影片： 這是重要`Source`參考視訊檔案的屬性。 實作`Source`屬性會在下一步 文章中，所述[播放網頁視訊](web-videos.md)。


## <a name="related-links"></a>相關連結

- [示範影片播放程式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
