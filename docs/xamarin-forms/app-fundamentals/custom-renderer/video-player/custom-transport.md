---
title: "自訂的視訊傳輸控制"
ms.topic: article
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: d37c2c9dbb12668680b5813e9e083d2ebb184c66
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="custom-video-transport-controls"></a>自訂的視訊傳輸控制

影片播放器的傳輸控制項包含可執行函式的按鈕**播放**，**暫停**，和**停止**。 這些按鈕通常都會使用熟悉的圖示，而不是文字，而**播放**和**暫停**函式通常會結合為一個 按鈕。

根據預設，`VideoPlayer`顯示傳輸每個平台所支援的控制項。 當您將`AreTransportControlsEnabled`屬性`false`，這些控制項都會被隱藏。 然後您可以控制`VideoPlayer`以程式設計方式或提供您自己的傳輸控制項。

## <a name="the-play-pause-and-stop-methods"></a>播放、 暫停和停止方法

`VideoPlayer`類別會定義三個方法，名為`Play`， `Pause`，和`Stop`由引發事件實作：

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        public event EventHandler PlayRequested;

        public void Play()
        {
            PlayRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler PauseRequested;

        public void Pause()
        {
            PauseRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler StopRequested;

        public void Stop()
        {
            StopRequested?.Invoke(this, EventArgs.Empty);
        }
    }
}
```

這些事件的事件處理常式由設定`VideoPlayerRenderer`類別中每個平台，如下所示：

### <a name="ios-transport-implementations"></a>iOS 傳輸實作

IOS 版本`VideoPlayerRenderer`使用`OnElementChanged`方法來設定這三個事件處理常式時`NewElement`屬性不是`null`並卸離的事件處理常式時`OldElement`不`null`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            player.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            player.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            player.Pause();
            player.Seek(new CMTime(0, 1));
        }
    }
}
```

事件處理常式實作上呼叫方法`AVPlayer`物件。 沒有任何`Stop`方法`AVPlayer`，因此它會模擬暫停視訊，並將位置移到開頭。

### <a name="android-transport-implementations"></a>Android 的傳輸實作

Android 的實作會類似於 iOS 實作的。 三個函式的處理常式時，會設定`NewElement`不`null`和卸離時`OldElement`不`null`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        void OnPlayRequested(object sender, EventArgs args)
        {
            videoView.Start();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            videoView.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            videoView.StopPlayback();
        }
    }
}
```

三個函式呼叫所定義的方法`VideoView`。

### <a name="uwp-transport-implementations"></a>UWP 傳輸實作

UWP 實作三個傳輸函式是非常類似於 iOS 和 Android 的實作：

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
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            Control.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            Control.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            Control.Stop();
        }
    }
}
```

## <a name="the-video-player-status"></a>影片播放器狀態

實作**播放**，**暫停**，和**停止**函式是不夠的支援傳輸控制項。 通常**播放**和**暫停**命令會變更其外觀，指出是否將視訊目前正在播放或暫停的相同按鈕來實作。 此外，如果視訊尚未載入，不應該即使啟用按鈕。

這些需求表示視訊播放程式必須提供目前的狀態指出如果播放或暫停，或如果它尚未準備好要播放的視訊。 (三個平台也支援屬性，指出是否視訊可以暫停，或可以移至新位置，但這些屬性是適用於串流處理視訊，而不是視訊檔案，因此它們不支援在`VideoPlayer`此處所述。)

**VideoPlayerDemos**專案包含`VideoStatus`列舉型別具有三個成員：

```csharp
namespace FormsVideoLibrary
{
    public enum VideoStatus
    {
        NotReady,
        Playing,
        Paused
    }
}
```

`VideoPlayer`類別會定義名為的真實只可繫結屬性`Status`型別的`VideoStatus`。 這個屬性被定義為唯讀，因為它應該只設定從平台轉譯器：

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Status read-only property
        private static readonly BindablePropertyKey StatusPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Status), typeof(VideoStatus), typeof(VideoPlayer), VideoStatus.NotReady);

        public static readonly BindableProperty StatusProperty = StatusPropertyKey.BindableProperty;

        public VideoStatus Status
        {
            get { return (VideoStatus)GetValue(StatusProperty); }
        }

        VideoStatus IVideoPlayerController.Status
        {
            set { SetValue(StatusPropertyKey, value); }
            get { return Status; }
        }
        ···
    }
}
```

唯讀的可繫結屬性通常會有私用`set`存取子上的`Status`以允許從設定類別內的屬性。 如`View`衍生物受到轉譯器，不過，屬性必須設定從類別外部的但只能由平台轉譯器。

基於這個理由，定義另一個屬性同名`IVideoPlayerController.Status`。 這是明確介面實作，而且會透過`IVideoPlayerController`介面`VideoPlayer`類別會實作：

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPlayerController
    {
        VideoStatus Status { set; get; }

        TimeSpan Duration { set; get; }
    }
}
```

這是類似於如何[ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)控制使用[ `IWebViewController` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IWebViewController/)介面才能實作`CanGoBack`和`CanGoForward`屬性。 (請參閱的原始程式碼[ `WebView` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/WebView.cs)和其轉譯器，如需詳細資訊。)

這可讓類別外部`VideoPlayer`設定`Status`屬性參考`IVideoPlayerController`介面。 （您會看到此程式碼很快。）這個屬性可以設定從其他類別，但不是太可能不小心設定。 最重要的是，`Status`屬性不能透過資料繫結設定。

為了協助保持此轉譯器`Status`屬性更新，`VideoPlayer`類別會定義`UpdateStatus`事件所觸發的每個十分之一秒：

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        public event EventHandler UpdateStatus;

        public VideoPlayer()
        {
            Device.StartTimer(TimeSpan.FromMilliseconds(100), () =>
            {
                UpdateStatus?.Invoke(this, EventArgs.Empty);
                return true;
            });
        }
        ···
    }
}
```

### <a name="the-ios-status-setting"></a>IOS 狀態設定

IOS`VideoPlayerRenderer`設定的處理常式`UpdateStatus`事件 (並卸離該處理常式時基礎`VideoPlayer`項目不存在)，使用此處理常式，將設定`Status`屬性：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (player.Status)
            {
                case AVPlayerStatus.ReadyToPlay:
                    switch (player.TimeControlStatus)
                    {
                        case AVPlayerTimeControlStatus.Playing:
                            videoStatus = VideoStatus.Playing;
                            break;

                        case AVPlayerTimeControlStatus.Paused:
                            videoStatus = VideoStatus.Paused;
                            break;
                    }
                    break;
                }
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
            ···
        }
        ···
    }
}
```

兩個屬性`AVPlayer`必須存取： [ `Status` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.Status/)型別的屬性`AVPlayerStatus`和[ `TimeControlStatus` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.TimeControlStatus/)型別的屬性`AVPlayerTimeControlStatus`。 請注意，`Element`屬性 (也就是`VideoPlayer`) 必須轉換成`IVideoPlayerController`設定`Status`屬性。

### <a name="the-android-status-setting"></a>Android 的狀態設定

[ `IsPlaying` ](https://developer.xamarin.com/api/property/Android.Widget.VideoView.IsPlaying/)屬性 Android`VideoView`是布林值，只表示如果視訊播放或暫停。 若要判斷是否`VideoView`兩 play 也無法暫停視訊尚未，`Prepared`事件`VideoView`必須處理。 這些兩個處理常式中設定`OnElementChanged`方法，並卸離期間`Dispose`覆寫：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    ···
                    videoView.Prepared += OnVideoViewPrepared;
                    ···
                }
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }

        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            if (Element != null)
            {
                Element.UpdateStatus -= OnUpdateStatus;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

`UpdateStatus`處理常式會使用`isPrepared`欄位 (在中設定`Prepared`處理常式) 和`IsPlaying`屬性來設定`Status`屬性：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus status = VideoStatus.NotReady;

            if (isPrepared)
            {
                status = videoView.IsPlaying ? VideoStatus.Playing : VideoStatus.Paused;
            }
            ···
        }
        ···
    }
}
```

### <a name="the-uwp-status-setting"></a>UWP 狀態設定

UWP`VideoPlayerRenderer`利用`UpdateStatus`事件，但是它不需要它的設定`Status`屬性。 `MediaElement`定義[ `CurrentStateChanged` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentStateChanged)允許轉譯器的事件時通知[ `CurrentState` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentState)屬性已變更。 屬性會遭到卸離`Dispose`覆寫：

```csharp
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
                    ···
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                };
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                ···
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

`CurrentState`屬性屬於型別[ `MediaElementState` ](/uwp/api/windows.ui.xaml.media.mediaelementstate)，並輕鬆地將對應至`VideoStatus`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementCurrentStateChanged(object sender, RoutedEventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (Control.CurrentState)
            {
                case MediaElementState.Playing:
                    videoStatus = VideoStatus.Playing;
                    break;

                case MediaElementState.Paused:
                case MediaElementState.Stopped:
                    videoStatus = VideoStatus.Paused;
                    break;
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
        }
        ···
    }
}
```

## <a name="play-pause-and-stop-buttons"></a>播放、 暫停和停止按鈕

使用 Unicode 字元符號**播放**，**暫停**，和**停止**映像會有問題。 [其他技術](https://unicode-table.com/en/blocks/miscellaneous-technical/)Unicode 標準區段可定義三個看似適用於此用途的符號字元。 這些是：

- 0x23F5 （黑色中型指向右方的三角形） 或 & #x23F5;如**播放**
- 0x23F8 （雙分隔號） 或 & #x23F8;如**暫停**
- 0x23F9 （黑色方格） 或 & #x23F9;如**停止**

不論如何這些符號出現在您的瀏覽器 （和不同的瀏覽器以不同方式處理這類），不會顯示以一致的方式支援 Xamarin.Forms 平台上。 IOS 和 UWP 裝置上**暫停**和**停止**字元有圖形的外觀，以藍色的 3D 背景及白色前景。 這不是在 Android 上，其中的符號就是只是藍色的情況。 不過，如 0x23F5 字碼指標**播放**沒有 iOS 和 Android 上，甚至不支援相同 UWP 和它的外觀。

基於這個原因，0x23F5 字碼指標無法用於**播放**。 是很好的替代：

- 0x25B6 （黑色指向右方的三角形） 或 & #x25B6;如**播放**

這所有三個平台支援不同之處在於它是不相似的 3D 外觀純黑色三角形**暫停**和**停止**。 一個可能的原因是遵循 0x25B6 字碼指標變數的程式碼：

- 0xFE0F （variant 型別 16） 後面接著 0x25B6 或 & #x25B6; & #xFE0F;如**播放**

這是用於標記如下所示。 在 iOS 上，它提供**播放**符號做為相同的 3D 外觀**暫停**和**停止**按鈕，但在變數無法在 Android 和 UWP 上運作。

**自訂傳輸**頁面上設定**AreTransportControlsEnabled**屬性**false**並包含`ActivityIndicator`顯示視訊載入時，且有兩個按鈕。 `DataTrigger` 物件用來啟用和停用`ActivityIndicator`和按鈕，並切換的第一個按鈕之間**播放**和**暫停**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomTransportPage"
             Title="Custom Transport">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AutoPlay="False"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource BigBuckBunny}" />

        <ActivityIndicator Grid.Row="0"
                           Color="Gray"
                           IsVisible="False">
            <ActivityIndicator.Triggers>
                <DataTrigger TargetType="ActivityIndicator"
                             Binding="{Binding Source={x:Reference videoPlayer},
                                               Path=Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsVisible" Value="True" />
                    <Setter Property="IsRunning" Value="True" />
                </DataTrigger>
            </ActivityIndicator.Triggers>
        </ActivityIndicator>

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="0, 10"
                     BindingContext="{x:Reference videoPlayer}">

            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.Playing}">
                        <Setter Property="Text" Value="&#x23F8; Pause" />
                    </DataTrigger>

                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>

            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

本文詳細說明資料觸發程序[資料觸發程序](~/xamarin-forms/app-fundamentals/triggers.md#data)。

在程式碼後置檔案中有按鈕處理常式`Clicked`事件：

```csharp
namespace VideoPlayerDemos
{
    public partial class CustomTransportPage : ContentPage
    {
        public CustomTransportPage()
        {
            InitializeComponent();
        }

        void OnPlayPauseButtonClicked(object sender, EventArgs args)
        {
            if (videoPlayer.Status == VideoStatus.Playing)
            {
                videoPlayer.Pause();
            }
            else if (videoPlayer.Status == VideoStatus.Paused)
            {
                videoPlayer.Play();
            }
        }

        void OnStopButtonClicked(object sender, EventArgs args)
        {
            videoPlayer.Stop();
        }
    }
}
```

因為`AutoPlay`設`false`中**CustomTransport.xaml**檔案中，您必須按**播放**按鈕時，它會變成啟用開始視訊。 按鈕的定義，因此上面所討論的 Unicode 字元就會隨附其文字對等用法。 當播放視訊的按鈕在每個平台上有一致的外觀：

[![自訂傳輸播放](custom-transport-images/customtransportplaying-small.png "自訂傳輸播放")](custom-transport-images/customtransportplaying-large.png "自訂傳輸播放")

但在 Android 和 UWP、**播放**暫停視訊時按鈕看起來非常不同：

[![自訂傳輸暫停](custom-transport-images/customtransportpaused-small.png "自訂傳輸暫停")](custom-transport-images/customtransportpaused-large.png "自訂傳輸暫停")

在實際執行應用程式中，您可能會想要達到一致的視覺化使用點陣圖影像的按鈕。


## <a name="related-links"></a>相關連結

- [視訊播放程式示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
