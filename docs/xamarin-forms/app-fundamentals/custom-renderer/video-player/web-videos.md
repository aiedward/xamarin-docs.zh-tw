---
title: 播放 Web 視訊
description: 本文說明如何使用 Xamarin.Forms 實作影片播放程式應用程式。
ms.prod: xamarin
ms.assetid: 75781A10-865D-4BA8-8D6B-E3DA012922BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 7f40d0d11fc932121b4ff7789969bbb1e354024c
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172206"
---
# <a name="playing-a-web-video"></a>播放 Web 視訊

`VideoPlayer` 類別會定義 `Source` 屬性，用於指定視訊檔案的來源，以及 `AutoPlay` 屬性。 `AutoPlay` 的預設設定為 `true`，表示視訊會在設定 `Source` 之後自動開始播放：

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Source property
        public static readonly BindableProperty SourceProperty =
            BindableProperty.Create(nameof(Source), typeof(VideoSource), typeof(VideoPlayer), null);

        [TypeConverter(typeof(VideoSourceConverter))]
        public VideoSource Source
        {
            set { SetValue(SourceProperty, value); }
            get { return (VideoSource)GetValue(SourceProperty); }
        }

        // AutoPlay property
        public static readonly BindableProperty AutoPlayProperty =
            BindableProperty.Create(nameof(AutoPlay), typeof(bool), typeof(VideoPlayer), true);

        public bool AutoPlay
        {
            set { SetValue(AutoPlayProperty, value); }
            get { return (bool)GetValue(AutoPlayProperty); }
        }
        ···
    }
}
```

`Source` 屬性的類型為 `VideoSource`，其模式是根據 Xamarin.Forms [`ImageSource`](xref:Xamarin.Forms.ImageSource) 抽象類別建立，且具有三個衍生類別：[`UriImageSource`](xref:Xamarin.Forms.UriImageSource)、[`FileImageSource`](xref:Xamarin.Forms.FileImageSource)，及 [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource)。 但是，`VideoPlayer` 沒有任何可用的串流選項，因為 iOS 和 Android 不支援播放來自串流的視訊。

## <a name="video-sources"></a>視訊來源

抽象 `VideoSource` 類別僅由三個靜態方法組成，這三個方法會具現化三個從 `VideoSource` 衍生的類別：

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        public static VideoSource FromUri(string uri)
        {
            return new UriVideoSource() { Uri = uri };
        }

        public static VideoSource FromFile(string file)
        {
            return new FileVideoSource() { File = file };
        }

        public static VideoSource FromResource(string path)
        {
            return new ResourceVideoSource() { Path = path };
        }
    }
}
```

`UriVideoSource` 類別會用於使用 URI 指定可下載的視訊檔案。 它會定義一個 `string` 類型的單一屬性：

```csharp
namespace FormsVideoLibrary
{
    public class UriVideoSource : VideoSource
    {
        public static readonly BindableProperty UriProperty =
            BindableProperty.Create(nameof(Uri), typeof(string), typeof(UriVideoSource));

        public string Uri
        {
            set { SetValue(UriProperty, value); }
            get { return (string)GetValue(UriProperty); }
        }
    }
}
```

處理 `UriVideoSource` 類型物件的流程會在下方描述。

`ResourceVideoSource` 類別會用於存取以內嵌資源儲存在平台應用程式中的視訊檔案，且視訊檔案也是透過 `string` 屬性指定：

```csharp
namespace FormsVideoLibrary
{
    public class ResourceVideoSource : VideoSource
    {
        public static readonly BindableProperty PathProperty =
            BindableProperty.Create(nameof(Path), typeof(string), typeof(ResourceVideoSource));

        public string Path
        {
            set { SetValue(PathProperty, value); }
            get { return (string)GetValue(PathProperty); }
        }
    }
}
```

處理 `ResourceVideoSource` 類型物件的流程會在[載入應用程式資源視訊](loading-resources.md)一文中描述。 `VideoPlayer` 類別沒有任何設施可載入以在 .NET Standard 程式庫中儲存為資源的視訊檔案。

`FileVideoSource` 類別會用於存取來自裝置影片庫的視訊檔案。 其單一屬性的類型也是 `string`：

```csharp
namespace FormsVideoLibrary
{
    public class FileVideoSource : VideoSource
    {
        public static readonly BindableProperty FileProperty =
                  BindableProperty.Create(nameof(File), typeof(string), typeof(FileVideoSource));

        public string File
        {
            set { SetValue(FileProperty, value); }
            get { return (string)GetValue(FileProperty); }
        }
    }
}
```

處理 `FileVideoSource` 類型物件的流程會在[存取裝置的影片庫](accessing-library.md)一文中描述。

`VideoSource` 類別包含一個 `TypeConverter` 屬性，該屬性會參考 `VideoSourceConverter`：

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        ···
    }
}
```

此類型轉換器會在將 `Source` 屬性設為 XAML 中的字串時叫用。 以下是 `VideoSourceConverter` 類別：

```csharp
namespace FormsVideoLibrary
{
    public class VideoSourceConverter : TypeConverter
    {
        public override object ConvertFromInvariantString(string value)
        {
            if (!String.IsNullOrWhiteSpace(value))
            {
                Uri uri;
                return Uri.TryCreate(value, UriKind.Absolute, out uri) && uri.Scheme != "file" ?
                                VideoSource.FromUri(value) : VideoSource.FromResource(value);
            }

            throw new InvalidOperationException("Cannot convert null or whitespace to ImageSource");
        }
    }
}
```

`ConvertFromInvariantString` 方法會嘗試將字串轉換成 `Uri` 物件。 若轉換成功，且配置非為 `file:`，則方法會傳回一個 `UriVideoSource`。 否則，它會傳回 `ResourceVideoSource`。

## <a name="setting-the-video-source"></a>設定視訊來源

所有其他涉及視訊來源的邏輯都會在個別平台轉譯器中實作。 下列各節會顯示平台轉譯器如何在將 `Source` 屬性設為 `UriVideoSource` 物件時播放視訊。

### <a name="the-ios-video-source"></a>iOS 視訊來源

設定視訊播放程式的視訊來源會涉及 `VideoPlayerRenderer` 的兩個區段。 當 Xamarin.Forms 首先建立 `VideoPlayer` 類型的物件時，便會呼叫 `OnElementChanged` 方法，並將引數物件的 `NewElement` 屬性設為 `VideoPlayer`。 `OnElementChanged` 方法會呼叫 `SetSource`：

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
                SetSource();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

之後，當 `Source` 屬性變更時，便會使用 "Source" 的 `PropertyName` 屬性呼叫 `OnElementPropertyChanged` 方法，並再次呼叫 `SetSource`。

若要在 iOS 上播放視訊檔案，會先建立 [`AVAsset`](https://developer.xamarin.com/api/type/AVFoundation.AVAsset/) 類型的物件來封裝視訊檔案，然後用它來建立 [`AVPlayerItem`](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/)，之後再交給 `AVPlayer` 物件。 以下是 `SetSource` 方法處理 `Source` 屬性的方式 (當其類型為 `UriVideoSource` 時)：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        ···
        void SetSource()
        {
            AVAsset asset = null;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
            if (asset != null)
            {
                playerItem = new AVPlayerItem(asset);
            }
            else
            {
                playerItem = null;
            }

            player.ReplaceCurrentItemWithPlayerItem(playerItem);

            if (playerItem != null && Element.AutoPlay)
            {
                player.Play();
            }
        }
        ···
    }
}
```

`AutoPlay` 屬性在 iOS 視訊類別中沒有任何類別項目，因此會在 `SetSource` 方法的結尾檢查該屬性，以在 `AVPlayer` 物件上呼叫 `Play` 方法。

在某些情況下，當具備 `VideoPlayer` 的網頁巡覽回首頁後，視訊仍會繼續播放。 為了停止視訊，`Dispose` 覆寫中也設定了 `ReplaceCurrentItemWithPlayerItem`：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void Dispose(bool disposing)
        {
            base.Dispose(disposing);

            if (player != null)
            {
                player.ReplaceCurrentItemWithPlayerItem(null);
            }
        }
        ···
    }
}
```

### <a name="the-android-video-source"></a>Android 視訊來源

首次建立 `VideoPlayer`，以及在稍後當 `Source` 屬性變更時，Android `VideoPlayerRenderer` 需要設定播放程式的視訊來源：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetSource();
                ···
            }
        }
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

`SetSource` 方法會透過使用從字串 URI 建立的 Android `Uri` 物件，在 `VideoView` 上呼叫 `SetVideoUri` 來處理 `UriVideoSource` 類型的物件。 此處的 `Uri` 類別使用完整名稱，以和 .NET `Uri` 類別區別：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void SetSource()
        {
            isPrepared = false;
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···

            if (hasSetSource && Element.AutoPlay)
            {
                videoView.Start();
            }
        }
        ···
    }
}
```

Android `VideoView` 沒有對應的 `AutoPlay` 屬性，因此當設定新視訊時，便會呼叫 `Start` 方法。

若將 `VideoPlayer` 的 `Source` 屬性設為 `null`，或是將 `UriVideoSource` 的 `Uri` 屬性設為 `null` 或空白字串，則 iOS 和 Android 轉譯器的行為會有所差異。 若 iOS 視訊播放程式目前正在播放視訊，且已將 `Source` 設為 `null` (或是字串為 `null` 或空白)，則會使用 `null` 值呼叫 `ReplaceCurrentItemWithPlayerItem`。 目前的視訊會遭到取代並停止播放。

Android 則不支援相似的設施。 若將 `Source` 屬性設為 `null`，則 `SetSource` 方法會直接忽略它，且目前的視訊會繼續播放。

### <a name="the-uwp-video-source"></a>UWP 視訊來源

UWP `MediaElement` 定義了一個 `AutoPlay` 屬性，該屬性會在轉譯器中與其他任何屬性一樣進行處理：

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
                SetSource();
                SetAutoPlay();
                ···    
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            else if (args.PropertyName == VideoPlayer.AutoPlayProperty.PropertyName)
            {
                SetAutoPlay();
            }
            ···
        }
        ···
    }
}
```

`SetSource` 屬性會透過將 `MediaElement` 的 `Source` 屬性設為 .NET `Uri` 值，或是設為 `null` (若將 `VideoPlayer` 的 `Source` 屬性設為 `null`) 來處理 `UriVideoSource` 物件：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    Control.Source = new Uri(uri);
                    hasSetSource = true;
                }
            }
            ···
            if (!hasSetSource)
            {
                Control.Source = null;
            }
        }

        void SetAutoPlay()
        {
            Control.AutoPlay = Element.AutoPlay;
        }
        ···
    }
}
```

## <a name="setting-a-url-source"></a>設定 URL 來源

使用在三個轉譯器中這些屬性的實作，便可以播放來自 URL 來源的視訊。 [**VideoPlayDemos**]( https://developer.xamarin.com/samples/xamarin-forms/customrenderers/videoplayerdemos/index.md) 程式中的 [Play Web Video] \(播放 Web 視訊\) 頁面會由下列 XAML 檔案定義：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayWebVideoPage"
             Title="Play Web Video">

    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

</ContentPage>
```

`VideoSourceConverter` 類別會將字串轉換成 `UriVideoSource`。 當您巡覽到 [Play Web Video] \(播放 Web 視訊\) 頁面時，便會開始載入視訊，並在下載及緩衝的資料數量足夠時開始播放。 視訊的長度約 10 分鐘：

[![播放 Web 視訊](web-videos-images/playwebvideo-small.png "播放 Web 視訊")](web-videos-images/playwebvideo-large.png#lightbox "播放 Web 視訊")

在每個平台上，傳輸控制項會在未使用時淡出，但可以透過點選視訊來還原檢視。

您可以透過將 `AutoPlay` 屬性設為 `false`，來避免自動播放視訊：

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AutoPlay="false" />
```

您將需要按下 [播放] 按鈕來開始播放視訊。

同樣地，您可以透過將 `AreTransportControlsEnabled` 屬性設為 `false`，來隱藏顯示傳輸控制項：

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AreTransportControlsEnabled="False" />
```

若您將兩個屬性同時設為 `false`，則視訊將不會播放，且沒有任何方式可以啟動它！ 您將需要從程式碼後置檔案呼叫 `Play`，或是建立您自己的傳輸控制項，如[實作自訂視訊傳輸控制項](custom-transport.md)一文中所述。

**App.xaml** 檔案包含兩個其他視訊的資源：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.App">
    <Application.Resources>
        <ResourceDictionary>

            <video:UriVideoSource x:Key="ElephantsDream"
                                  Uri="https://archive.org/download/ElephantsDream/ed_hd_512kb.mp4" />

            <video:UriVideoSource x:Key="BigBuckBunny"
                                  Uri="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

            <video:UriVideoSource x:Key="Sintel"
                                  Uri="https://archive.org/download/Sintel/sintel-2048-stereo_512kb.mp4" />

        </ResourceDictionary>
    </Application.Resources>
</Application>
```

若要參考其中一個其他影片，您可以將 **PlayWebVideo.xaml** 檔案中的明確 URL 替換成 `StaticResource` 標記延伸；在此情況下，`VideoSourceConverter` 並非建立 `UriVideoSource` 物件的必要項目：

```xaml
<video:VideoPlayer Source="{StaticResource ElephantsDream}" />
```

或者，您可以在 `ListView` 中設定視訊檔案的 `Source` 屬性，如下一篇文章中所述：[將視訊來源繫結到播放程式](source-bindings.md)。





## <a name="related-links"></a>相關連結

- [Video Player Demos (Samples)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) (視訊播放程式示範 (範例))
