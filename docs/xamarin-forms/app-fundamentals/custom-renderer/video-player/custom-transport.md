---
title: 自訂影片傳輸控制項
description: 本文說明如何使用 Xamarin.Forms，在影片播放程式應用程式中實作自訂傳輸控制項。
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: c6aa4aed134667f25b3822c7604b85e27a404a3a
ms.sourcegitcommit: 91a4fcb715506e18e8070bc89bf2cb14d079ad32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2019
ms.locfileid: "58870153"
---
# <a name="custom-video-transport-controls"></a>自訂影片傳輸控制項

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)

影片播放程式的傳輸控制項包括執行 [播放]、[暫停] 和 [停止] 功能的按鈕。 這些按鈕通常會以熟悉的圖示而非文字呈現，且 [播放] 及 [暫停] 功能常會合併為一個按鈕。

根據預設，`VideoPlayer` 會顯示由各平台支援的傳輸控制項。 當您將 `AreTransportControlsEnabled` 屬性設定為 `false` 時，就會隱藏這些控制項。 接著可以用程式設計的方式來控制 `VideoPlayer` 或支援您本身的傳輸控制項。

## <a name="the-play-pause-and-stop-methods"></a>Play、Pause 和 Stop 方法

`VideoPlayer` 類別會定義名為 `Play`、`Pause`、`Stop`，且由引發事件實作的三個方法：

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

這些事件的事件處理常式會由各平台的 `VideoPlayerRenderer` 類別設定，如下所示：

### <a name="ios-transport-implementations"></a>iOS 傳輸實作

當 `NewElement` 屬性不是 `null` 時，iOS 版的 `VideoPlayerRenderer` 會使用 `OnElementChanged` 方法設定這三個事件的處理常式，並在 `OldElement` 不是 `null` 時，將事件處理常式中斷連結：

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

事件處理常式是透過呼叫 `AVPlayer` 物件上的方法實作。 因為沒有適用於 `AVPlayer` 的 `Stop` 方法，所以它會以暫停影片並將位置移至開頭來進行模擬。

### <a name="android-transport-implementations"></a>Android 傳輸實作

Android 實作近似於 iOS 實作。 當 `NewElement` 不是 `null` 時，會設定這三個功能的處理常式，並在 `OldElement` 不是 `null` 時，中斷連結：

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

這三個功能會呼叫由 `VideoView` 定義的方法。

### <a name="uwp-transport-implementations"></a>UWP 傳輸實作

這三個傳輸功能的 UWP 實作非常近似於 iOS 和 Android 實作：

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

## <a name="the-video-player-status"></a>影片播放程式狀態

實作**播放**、**暫停**、和**停止**功能並不足以支援傳輸控制項。 **播放**和**暫停**命令通常會以相同的按鈕實作，且該按鈕會指出影片正在播放或暫停。 此外，若影片尚未載入，就不應啟用按鈕。

這些需求指出影片播放程式需要提供目前狀態，指出其正在播放、暫停或仍未準備好播放影片。 (各平台也支援指出影片可以暫停或移至新位置的屬性，但這些屬性適用於串流影片而非影片檔案，所以此處描述的 `VideoPlayer` 中不支援這些屬性。)

**VideoPlayerDemos** 專案包含具有三個成員的 `VideoStatus` 列舉：

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

`VideoPlayer` 類別可定義類型為 `VideoStatus` 且名為 `Status` 的唯讀可繫結屬性。 因為此屬性應只從平台轉譯器設定，所以其定義為唯讀：

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

通常，唯讀可繫結屬性擁有 `Status` 屬性上專用的 `set` 存取子，使其可以從類別內設定。 不過，對於轉譯器支援的 `View` 導數，則必須從類別外設定屬性，且只能由平台轉譯器進行設定。

因此，會為另一個屬性定義名稱 `IVideoPlayerController.Status`。 此為明確介面實作，且 `VideoPlayer` 類別實作的 `IVideoPlayerController` 介面使其變得可行：

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

這近似於 [`WebView`](xref:Xamarin.Forms.WebView) 控制項使用 [`IWebViewController`](xref:Xamarin.Forms.IWebViewController) 介面實作 `CanGoBack` 和 `CanGoForward` 屬性的方式。 (如需詳細資料，請參閱 [`WebView`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/WebView.cs) 原始程式碼及其轉譯器。)

這讓 `VideoPlayer` 之外的類別可以藉由參考 `IVideoPlayerController` 介面設定 `Status` 屬性。 (您很快就可以看到程式碼。)屬性雖然也可以從其他類別設定，但不太可能發生不小心設定的情形。 最重要的是，`Status` 屬性無法透過資料繫結設定。

為了讓轉譯器持續更新此 `Status` 屬性，`VideoPlayer` 類別會定義每十分之一秒就觸發一次的 `UpdateStatus` 事件：

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

### <a name="the-ios-status-setting"></a>iOS 狀態設定

iOS `VideoPlayerRenderer` 會設定 `UpdateStatus` 事件的處理常式 (且會在缺少基礎 `VideoPlayer` 元素時，將該處理常式中斷連結)，並使用處理常式設定 `Status` 屬性：

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

必須存取 `AVPlayer` 的兩個屬性：類型為 `AVPlayerStatus` 的 [`Status`](xref:AVFoundation.AVPlayer.Status*) 屬性和類型為 `AVPlayerTimeControlStatus` 的 [`TimeControlStatus`](xref:AVFoundation.AVPlayer.TimeControlStatus*) 屬性。 請注意，必須將 `Element` 屬性 (即為 `VideoPlayer`) 轉換為 `IVideoPlayerController` 以設定 `Status` 屬性。

### <a name="the-android-status-setting"></a>Android 狀態設定

Android `VideoView` 的 [`IsPlaying`](https://developer.xamarin.com/api/property/Android.Widget.VideoView.IsPlaying/) 屬性為布林值，只會指出影片是否正在播放或已暫停。 若要判斷 `VideoView` 是否尚無法播放及暫停影片，那麼必須處理 `VideoView` 的 `Prepared` 事件。 這兩個處理常式會在 `OnElementChanged` 方法中設定，以及在執行 `Dispose` 覆寫期間中斷連結：

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

`UpdateStatus` 處理常式會使用 `isPrepared` 欄位 (在 `Prepared` 處理常式中設定) 及 `IsPlaying` 屬性，以設定 `Status` 屬性：

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

UWP `VideoPlayerRenderer` 會使用 `UpdateStatus` 事件，但不需要該事件來設定 `Status` 屬性。 `MediaElement` 會定義 [`CurrentStateChanged`](xref:Windows.UI.Xaml.Controls.MediaElement.CurrentStateChanged) 事件，且該事件會於變更 [`CurrentState`](xref:Windows.UI.Xaml.Controls.MediaElement.CurrentState*) 屬性時，讓轉譯器可收到通知。 屬性會在 `Dispose` 覆寫內中斷連結：

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

`CurrentState` 屬性的類型為 [`MediaElementState`](/uwp/api/windows.ui.xaml.media.mediaelementstate)，且可以輕易地對應至 `VideoStatus`：

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

## <a name="play-pause-and-stop-buttons"></a>[播放]、[停止] 和 [暫停] 按鈕

使用 Unicode 字元來代表 [播放]、[暫停] 和 [停止] 的影像可能會有問題。 但 Unicode 標準的 [Miscellaneous Technical](https://unicode-table.com/en/blocks/miscellaneous-technical/) (其他專門符號) 一節判定這三個符號字元應該適合此用途。 這些是：

- 0x23F5 (指向右方的黑色中型三角形) 或 &#x23F5;，代表 [播放]
- 0x23F8 (雙垂直長條) 或 &#x23F8;，代表 [暫停]
- 0x23F9 (黑色正方形) 或 &#x23F9;，代表 [停止]

無論這些符號是如何出現在您的瀏覽器中 (不同的瀏覽器會以不同方式處理它們)，它們都不會在 Xamarin.Forms 支援的平台上持續地顯示。 在 iOS 及 UWP 裝置上，[暫停] 及 [停止] 字元擁有圖形化的外觀，且帶有藍色 3D 的背景以及白色的前景。 但這在 Android 上就不一樣了，其符號為單純的藍色。 不過，[播放] 的 0x23F5 字碼指標在 UWP 上沒有相同的外觀，且甚至在 iOS 和 Android 上都不支援。

基於此原因，0x23F5 字碼指標無法作為 [播放] 使用。 適合的替代字碼指標為：

- 0x25B6 (指向右方的黑色三角形) 或 &#x25B6;，代表 [播放]

除了其為全黑的三角形，且不像 3D 外觀的 [暫停] 及 [停止] 之外，各平台皆對其支援。 其中一種可能性為使用變體程式碼來追隨 0x25B6 字碼指標：

- 後面接著 0xFE0F (變體 16) 的 0x25B6 或 &#x25B6;&#xFE0F;，代表 [播放]

這是下方所示標記使用的項目。 在 iOS 上，它提供 [播放] 符號與 [暫停] 及 [停止] 按鈕相同的 3D 外觀，但變體在 Android 和 UWP 上無效。

**自訂傳輸**頁面會將 **AreTransportControlsEnabled** 屬性設定為 **false**，以及包含載入影片時顯示的 `ActivityIndicator` 和兩個按鈕。 `DataTrigger` 物件可用來啟用、停用 `ActivityIndicator` 和按鈕，以及切換在 [播放] 和 [暫停] 之間的第一個按鈕：

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

資料觸發程序在[資料觸發程序](~/xamarin-forms/app-fundamentals/triggers.md#data)一文中有詳盡的描述。

程式碼後置檔案具有按鈕 `Clicked` 事件的處理常式：

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

因為在 **CustomTransport.xaml** 檔案中 `AutoPlay` 設定為 `false`，所以您必須在其啟用時按 [播放] 按鈕以開始影片。 因為定義了這些按鈕，所以上面討論的 Unicode 字元都會附有其相對應文字。 當影片播放時，按鈕在各平台上都有一致的外觀：

[![自訂傳輸播放](custom-transport-images/customtransportplaying-small.png "自訂傳輸播放")](custom-transport-images/customtransportplaying-large.png#lightbox "自訂傳輸播放")

但在影片暫停時，Android 及 UWP 上的 [播放] 看起來大不相同：

[![自訂傳輸暫停](custom-transport-images/customtransportpaused-small.png "自訂傳輸暫停")](custom-transport-images/customtransportpaused-large.png#lightbox "自訂傳輸暫停")

在生產應用程式中，您可能會想要使用自己的點陣圖影像讓按鈕達成視覺上的一致性。


## <a name="related-links"></a>相關連結

- [Video Player Demos (Samples)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) (視訊播放程式示範 (範例))
