---
標題：「建立平臺影片播放程式」描述：「本文說明如何使用，在每個平臺上執行影片播放程式自訂轉譯器 Xamarin.Forms 。」
assetid： EEE2FB9B-EB73-4A3F-A859-7A1D4808E149 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：02/12/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="creating-the-platform-video-players"></a>建立平台視訊播放程式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

[**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)解決方案包含可為執行影片播放程式的所有程式碼 Xamarin.Forms 。 它也包含一系列頁面，示範如何在應用程式中使用視訊播放程式。 所有 `VideoPlayer` 及其平台轉譯器都位於名為 `FormsVideoLibrary` 的專案資料夾內，且使用 `FormsVideoLibrary` 命名空間。 這應該可讓您輕鬆地將檔案複製到您的應用程式並參考類別。

## <a name="the-video-player"></a>視訊播放程式

[`VideoPlayer`](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/FormsVideoLibrary/VideoPlayer.cs)類別是**VideoPlayerDemos** .NET Standard 程式庫的一部分，可在平臺之間共用。 它衍生自 `View`：

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

此類別的成員 (及 `IVideoPlayerController` 介面) 都會在下列文章中描述。

每個平台都包含名為 `VideoPlayerRenderer` 的類別，其中包含實作視訊播放程式的平台特定程式碼。 此轉譯器的主要工作是建立該平台的視訊播放程式。

### <a name="the-ios-player-view-controller"></a>iOS 播放程式檢視控制器

在 iOS 中實作視訊播放程式會涉及數個類別。 應用程式會先建立 [`AVPlayerViewController`](xref:AVKit.AVPlayerViewController) ，然後將 [`Player`](xref:AVKit.AVPlayerViewController.Player*) 屬性設定為類型的物件 [`AVPlayer`](xref:AVFoundation.AVPlayer) 。 將視訊來源指派給播放程式時，還需要其他類別。

如同所有轉譯器，iOS [`VideoPlayerRenderer`](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos.iOS/FormsVideoLibrary/VideoPlayerRenderer.csVideoPlayerRenderer.cs) 包含一個 `ExportRenderer` 屬性，可使用轉譯器來識別此 `VideoPlayer` 視圖：

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

通常是設定平臺控制項衍生自類別的轉譯器 [`ViewRenderer<View, NativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs) ，其中 `View` 是衍生的 Xamarin.Forms `View` （在此案例中為 `VideoPlayer` ），而是轉譯器類別的 `NativeView` iOS `UIView` 衍生。 針對此轉譯器，泛型引數會直接設為 `UIView`；您會在稍後了解原因。

當轉譯器是以 `UIViewController` 衍生類別 (例如此物件) 為基礎時，類別應覆寫 `ViewController` 屬性，並傳回檢視控制器 (在此案例中為 `AVPlayerViewController`)。 這即為 `_playerViewController` 欄位的用途：

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

`OnElementChanged` 覆寫的主要責任是檢查 `Control` 屬性是否為 `null`；若為 null，則建立平台控制項，並將它傳遞給 `SetNativeControl` 方法。 在此情況下，該物件只能透過 `AVPlayerViewController` 的 `View` 屬性使用。 該 `UIView` 衍生類別即是名為 `AVPlayerView` 的 Private 類別，但因為它是 Private，所以無法明確指定為傳遞給 `ViewRenderer` 的第二個泛型引數。

一般而言，轉譯器類別的 `Control` 屬性在這之後便會指向用於實作轉譯器的 `UIView`，但在此案例中，`Control` 屬性並未在其他位置使用。

### <a name="the-android-video-view"></a>Android 視訊檢視

的 Android 轉譯器 `VideoPlayer` 是以 android 類別為基礎 [`VideoView`](xref:Android.Widget.VideoView) 。 不過，如果 `VideoView` 單獨使用來播放應用程式中的影片 Xamarin.Forms ，影片就會填滿的區域分配， `VideoPlayer` 而不會維持正確的外觀比例。 基於此原因 (您會在稍後了解)，`VideoView` 會成為 Android `RelativeLayout` 的子系。 `using` `ARelativeLayout` 指示詞會定義來區別它與 Xamarin.Forms `RelativeLayout` ，這是中的第二個泛型引數 `ViewRenderer` ：

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

從 Xamarin.Forms 2.5 開始，Android 轉譯器應包含具有引數的函式 `Context` 。

`OnElementChanged` 覆寫會建立 `VideoView` 和 `RelativeLayout`，並設定 `VideoView` 的配置參數來將其置於 `RelativeLayout` 內的中央。

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

`Prepared` 事件的處理常式會附加在此方法中，且會在 `Dispose` 方法內中斷連結。 此事件會在 `VideoView` 具備充足資訊，足以開始播放視訊檔案時引發。

### <a name="the-uwp-media-element"></a>UWP 媒體項目

在通用 Windows 平臺（UWP）中，最常見的影片播放方式是 [`MediaElement`](xref:Windows.UI.Xaml.Controls.MediaElement) 。 的檔 `MediaElement` 指出， [`MediaPlayerElement`](xref:Windows.UI.Xaml.Controls.MediaPlayerElement) 只有在只需要支援從組建1607開始的 Windows 10 版本時，才應改用。

`OnElementChanged` 覆寫需要建立 `MediaElement`、設定幾個事件處理常式，並將 `MediaElement` 物件傳遞給 `SetNativeControl`：

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

兩個事件處理常式會在轉譯器的 `Dispose` 事件中中斷連結。

## <a name="showing-the-transport-controls"></a>顯示傳輸控制項

所有包含在平台內的視訊播放程式都支援一組預設傳輸控制項，其中包含用於播放與暫停的按鈕、指出視訊中目前位置及移動到新位置的橫條。

`VideoPlayer` 類別會定義名為 `AreTransportControlsEnabled` 的屬性，並將預設值設為 `true`：

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

雖然此屬性同時擁有 `set` 和 `get` 存取子，但轉譯器只需在設定屬性時處理案例。 `get` 存取子只會傳回屬性目前的值。

例如 `AreTransportControlsEnabled` 這類屬性會由平台轉譯器以兩種方式處理：

- 第一次是在 Xamarin.Forms 建立專案時 `VideoPlayer` 。 這會在 `NewElement` 屬性並非 `null` 時，由轉譯器的 `OnElementChanged` 覆寫指出。 此時，轉譯器可從屬性的初始值設定其自身的平台視訊播放程式，如 `VideoPlayer` 中所定義。

- 若 `VideoPlayer` 中的屬性稍後發生變更，則會呼叫轉譯器中的 `OnElementPropertyChanged` 方法。 這可讓轉譯器根據新的屬性設定來更新平台視訊播放程式。

下列各節會討論 `AreTransportControlsEnabled` 屬性在每個平台上的處理方式。

### <a name="ios-playback-controls"></a>iOS 播放控制項

`AVPlayerViewController`控制傳輸控制項顯示之 iOS 的屬性為 [`ShowsPlaybackControls`](xref:AVKit.AVPlayerViewController.ShowsPlaybackControls*) 。 以下是該屬性在 iOS `VideoViewRenderer` 中的設定方式：

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

轉譯器的 `Element` 屬性會指向 `VideoPlayer` 類別。

### <a name="the-android-media-controller"></a>Android 媒體控制器

在 Android 中，顯示傳輸控制項需要建立 [`MediaController`](xref:Android.Widget.MediaController) 物件，並將它與物件產生關聯 `VideoView` 。 其機制會在 `SetAreTransportControlsEnabled` 方法中示範：

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

UWP `MediaElement` 會定義名為的屬性 [`AreTransportControlsEnabled`](xref:Windows.UI.Xaml.Controls.MediaElement.AreTransportControlsEnabled*) ，以便從 `VideoPlayer` 相同名稱的屬性設定屬性：

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

要開始播放視訊還需要一個屬性，即參考視訊檔案的重要 `Source` 屬性。 實作 `Source` 屬性的過程會在下一篇文章中描述：[播放 Web 視訊](web-videos.md)。

## <a name="related-links"></a>相關連結

- [影片播放程式示範 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
