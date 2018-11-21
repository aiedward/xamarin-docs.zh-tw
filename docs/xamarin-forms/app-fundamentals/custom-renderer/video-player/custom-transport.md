---
title: 自訂影片傳輸控制項
description: 這篇文章說明如何使用 Xamarin.Forms 的視訊播放器應用程式中實作自訂傳輸控制項。
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 3397c931dcb23a29b0682699512a5b4c9018de38
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171062"
---
# <a name="custom-video-transport-controls"></a>自訂影片傳輸控制項

傳輸控制的影片播放程式包含執行的函式的按鈕**播放**，**暫停**，並**停止**。 這些按鈕的識別一般是使用熟悉的圖示，而不是文字，而**播放**並**暫停**函式通常會結合成一個按鈕。

根據預設，`VideoPlayer`顯示傳輸每個平台所支援的控制項。 當您設定`AreTransportControlsEnabled`屬性設`false`，會隱藏這些控制項。 然後您可以控制`VideoPlayer`以程式設計方式或提供您自己的傳輸控制項。

## <a name="the-play-pause-and-stop-methods"></a>播放、 暫停和停止方法

`VideoPlayer`類別會定義三個方法，名為`Play`， `Pause`，和`Stop`實作藉由引發事件：

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

IOS 版本`VideoPlayerRenderer`會使用`OnElementChanged`方法來設定這三個事件處理常式時`NewElement`屬性不是`null`，並卸離的事件處理常式時`OldElement`不是`null`:

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

事件處理常式實作上呼叫方法`AVPlayer`物件。 沒有任何`Stop`方法`AVPlayer`，因此它模擬暫停影片，並將位置移至開頭。

### <a name="android-transport-implementations"></a>Android 的傳輸實作

Android 的實作是類似於 iOS 實作。 三個函式的處理常式時，會設定`NewElement`不是`null`卸離時以及`OldElement`不是`null`:

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

UWP 的實作三個傳輸函式是非常類似於 iOS 和 Android 的實作：

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

## <a name="the-video-player-status"></a>視訊播放器狀態

實作**播放**，**暫停**，並**停止**函式是不夠的支援傳輸控制項。 通常**播放**並**暫停**命令都實作與同一個按鈕，變更其外觀，指出是否影片目前正在播放或暫停。 此外，如果影片尚未載入，不應該甚至啟用 按鈕。

這些需求表示，視訊播放程式必須提供目前狀態，指出它是播放或暫停，則它尚未準備好播放視訊。 (每個平台也支援的屬性，指出是否影片可以暫停，或可以移至新位置，但這些屬性是適用於串流視訊，而不是視訊檔案，因此它們不支援在`VideoPlayer`此處所述。)

**VideoPlayerDemos**專案包含`VideoStatus`列舉有三個成員：

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

`VideoPlayer`類別會定義名為僅限真實的可繫結屬性`Status`型別的`VideoStatus`。 這個屬性被定義為唯讀，因為它應該只從平台轉譯器設定：

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

通常，唯讀的可繫結屬性會有私用`set`存取子上的`Status`允許它在類別中，從設定的屬性。 針對`View`衍生項目轉譯器，不過，支援的屬性必須設定從類別之外，但只能由平台轉譯器。

基於這個理由，另一個屬性定義名稱`IVideoPlayerController.Status`。 這是明確介面實作，並可透過`IVideoPlayerController`介面，`VideoPlayer`類別會實作：

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

這是類似於如何[ `WebView` ](xref:Xamarin.Forms.WebView)控制項會使用[ `IWebViewController` ](xref:Xamarin.Forms.IWebViewController)實作的介面`CanGoBack`和`CanGoForward`屬性。 (請參閱原始程式碼[ `WebView` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/WebView.cs)和其轉譯器，如需詳細資訊。)

這可讓類別外部`VideoPlayer`來設定`Status`屬性，藉由參考`IVideoPlayerController`介面。 （您很快將程式碼。）屬性可以設定從其他類別，但它不太可能會不小心設定。 最重要的是，`Status`無法透過資料繫結設定屬性。

為了協助保持此轉譯器`Status`更新的屬性`VideoPlayer`類別會定義`UpdateStatus`事件觸發的每個十分之一秒：

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

IOS`VideoPlayerRenderer`設定的處理常式`UpdateStatus`事件 (並卸離該處理常式時的基礎`VideoPlayer`項目不存在)，然後使用處理常式來設定`Status`屬性：

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

兩個屬性`AVPlayer`必須存取： [ `Status` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.Status/)型別的屬性`AVPlayerStatus`而[ `TimeControlStatus` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.TimeControlStatus/)型別的屬性`AVPlayerTimeControlStatus`。 請注意，`Element`屬性 (也就是`VideoPlayer`) 必須轉換成`IVideoPlayerController`若要設定`Status`屬性。

### <a name="the-android-status-setting"></a>Android 的狀態設定

[ `IsPlaying` ](https://developer.xamarin.com/api/property/Android.Widget.VideoView.IsPlaying/) Android 屬性`VideoView`是布林值，只表示如果視訊播放或暫停。 若要判斷是否`VideoView`可以不播放或暫停影片，`Prepared`事件的`VideoView`必須處理。 這些兩個處理常式中設定`OnElementChanged`方法，並卸離期間`Dispose`覆寫：

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

UWP`VideoPlayerRenderer`利用`UpdateStatus`事件，但是它不需要它的設定`Status`屬性。 `MediaElement`定義[ `CurrentStateChanged` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentStateChanged)允許轉譯器的事件時收到通知[ `CurrentState` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentState)屬性已變更。 在卸離屬性`Dispose`覆寫：

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

`CurrentState`屬性的類型是[ `MediaElementState` ](/uwp/api/windows.ui.xaml.media.mediaelementstate)，並輕鬆地對應到`VideoStatus`:

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

使用 Unicode 字元的符號**播放**，**暫停**，並**停止**映像會造成問題。 [其他技術](https://unicode-table.com/en/blocks/miscellaneous-technical/)區段 Unicode 標準會定義三個似乎適用於此用途的符號字元。 這些是：

- 0x23F5 （黑色中型向右三角形） 或&#x23F5;針對**播放**
- 0x23F8 （雙垂直線） 或&#x23F8;針對**暫停**
- 0x23F9 （黑色方塊） 或&#x23F9;針對**停止**

不論如何這些符號出現在您的瀏覽器 （和不同的瀏覽器則會以不同方式處理它們），它們不會顯示以一致的方式在 Xamarin.Forms 所支援的平台。 IOS 和 UWP 裝置上**暫停**並**停止**字元具有圖形化的外觀，以藍色的 3D 背景及白色的前景。 這不是在 Android 上，其中的符號就是只是藍色的情況。 不過，0x23F5 字碼指標**播放**沒有相同的外觀，UWP，和它甚至不受支援 iOS 和 Android 上。

基於這個理由，0x23F5 字碼指標無法用於**播放**。 不錯替代方式是：

- 0x25B6 （黑色向右三角形） 或&#x25B6;針對**播放**

這是每個平台支援的不同之處在於它是一般的黑色三角形不像的 3D 外觀**暫停**並**停止**。 其中一個可能性是遵循 0x25B6 字碼指標，以變數的程式碼：

- 0x25B6 後面 0xFE0F （variant 型別 16） 或&#x25B6; &#xFE0F; for**播放**

這是用於下方所顯示的標記。 在 iOS 上，它可讓**播放**符號做為相同的 3D 外觀**暫停**並**停止**按鈕，但在 variant 不作用於 Android 和 UWP。

**自訂傳輸**頁面上設定**AreTransportControlsEnabled**屬性設**false** ，並包含`ActivityIndicator`載入影片時, 顯示，另外兩個按鈕。 `DataTrigger` 物件用來啟用和停用`ActivityIndicator`和按鈕，以及切換的第一個按鈕之間**播放**並**暫停**:

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

一文中詳細說明資料觸發程序[資料觸發程序](~/xamarin-forms/app-fundamentals/triggers.md#data)。

程式碼後置檔案具有按鈕的處理常式`Clicked`事件：

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

因為`AutoPlay`設定為`false`中**CustomTransport.xaml**檔案中，您必須按下**播放**按鈕時若要開始影片變成啟用狀態。 按鈕的定義，因此上面所討論的 Unicode 字元都會伴隨其文字對等項目。 當播放視訊的按鈕在每個平台上有一致的外觀：

[![自訂傳輸播放](custom-transport-images/customtransportplaying-small.png "自訂傳輸播放")](custom-transport-images/customtransportplaying-large.png#lightbox "自訂傳輸播放")

但在 Android 和 UWP**播放**暫停視訊時按鈕看起來非常不同：

[![自訂傳輸暫停](custom-transport-images/customtransportpaused-small.png "自訂傳輸已暫停")](custom-transport-images/customtransportpaused-large.png#lightbox "自訂傳輸已暫停")

在生產環境應用程式中，您可能要使用您自己的點陣圖影像的按鈕以達到視覺一致性。


## <a name="related-links"></a>相關連結

- [示範影片播放程式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
