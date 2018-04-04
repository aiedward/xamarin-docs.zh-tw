---
title: 播放網頁視訊
ms.prod: xamarin
ms.assetid: 75781A10-865D-4BA8-8D6B-E3DA012922BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 8326c142207e90f9b7d4bced7effd88ec88d8fa8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="playing-a-web-video"></a>播放網頁視訊

`VideoPlayer`類別會定義`Source`屬性用來指定來源的視訊檔，以及`AutoPlay`屬性。 `AutoPlay` 具有預設值為`true`，這表示視訊應該開始播放之後自動`Source`已設定：

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

`Source`屬性屬於型別`VideoSource`，這模仿 Xamarin.Forms [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/)抽象類別，而它的三個蠻[ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/)， [`FileImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FileImageSource/)，和[ `StreamImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StreamImageSource/)。 沒有資料流選項僅適用於`VideoPlayer`不過，因為 iOS 和 Android 不支援播放視訊從資料流。

## <a name="video-sources"></a>視訊來源

抽象`VideoSource`類別只包含三個具現化衍生自的三個類別的靜態方法`VideoSource`:

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

`UriVideoSource`類別用來指定可下載的視訊檔案的 uri。 它會定義單一屬性的型別`string`:

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

處理類型的物件`UriVideoSource`如下所述。

`ResourceVideoSource`類別用來存取儲存為內嵌的資源，也指定使用的平台應用程式中的視訊檔案`string`屬性：

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

處理類型的物件`ResourceVideoSource`文件所述[載入應用程式資源視訊](loading-resources.md)。 `VideoPlayer`類別具有功能載入視訊檔案儲存為可攜式類別庫中的資源。

`FileVideoSource`類別用來存取裝置的視訊媒體櫃從視訊檔案。 單一的屬性也屬於型別`string`:

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

處理類型的物件`FileVideoSource`文件所述[存取裝置的視訊媒體櫃](accessing-library.md)。

`VideoSource`類別包含`TypeConverter`屬性必須參考`VideoSourceConverter`:

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

這個型別轉換子就叫用時`Source`屬性設定為在 XAML 中的字串。 以下是`VideoSourceConverter`類別：

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

`ConvertFromInvariantString`方法會嘗試將字串轉換為`Uri`物件。 如果成功，且不是`file:`，則方法會傳回`UriVideoSource`。 否則，它會傳回`ResourceVideoSource`。

## <a name="setting-the-video-source"></a>設定視訊來源

個別的平台轉譯器被實作的所有其他邏輯涉及視訊來源。 下列章節將示範如何平台轉譯器中播放視訊時`Source`屬性設定為`UriVideoSource`物件。

### <a name="the-ios-video-source"></a>IOS 視訊來源

兩個區段`VideoPlayerRenderer`時牽涉到將視訊來源視訊播放程式的設定。 Xamarin.Forms 先建立型別的物件`VideoPlayer`、`OnElementChanged`方法呼叫`NewElement`引數物件的屬性設定為， `VideoPlayer`。 `OnElementChanged`方法呼叫`SetSource`:

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

更新版本上，當`Source`屬性變更，`OnElementPropertyChanged`方法呼叫`PropertyName`屬性的 「 來源 」，和`SetSource`會再次呼叫。

若要播放視訊檔案在 iOS 中，類型的物件[ `AVAsset` ](https://developer.xamarin.com/api/type/AVFoundation.AVAsset/)初次建立封裝的視訊檔案，而且用來建立[ `AVPlayerItem` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/)，這會遞交給`AVPlayer`物件。 以下是如何`SetSource`方法會處理`Source`屬性的型別時`UriVideoSource`:

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

`AutoPlay`屬性沒有類比類別中有 iOS 視訊，因此屬性會進行檢查的結尾`SetSource`方法呼叫`Play`方法`AVPlayer`物件。

在某些情況下，視訊會繼續播放後的頁面`VideoPlayer`向後巡覽至首頁。 若要停止視訊，`ReplaceCurrentItemWithPlayerItem`在`Dispose`覆寫：

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

### <a name="the-android-video-source"></a>Android 的視訊來源

在 Android `VideoPlayerRenderer` ，必須先設定視訊播放程式的來源時`VideoPlayer`的第一個建立和更新版本時`Source`屬性變更：

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

`SetSource`方法會處理類型的物件`UriVideoSource`藉由呼叫`SetVideoUri`上`VideoView`android`Uri`所建立的 URI 的字串物件。 `Uri`類別完整這裡加以區別.NET`Uri`類別：

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

在 Android`VideoView`沒有對應的`AutoPlay`屬性，所以`Start`呼叫方法時若已設定新的視訊。

沒有行為的 ios 和 Android 的轉譯器之間的差異如果`Source`屬性`VideoPlayer`設為`null`，或如果`Uri`屬性`UriVideoSource`設`null`或空白字串。 如果 iOS 影片播放器目前正在播放視訊，和`Source`設`null`(或字串是`null`或空白)，`ReplaceCurrentItemWithPlayerItem`呼叫`null`值。 目前的視訊會被取代，並停止播放。

Android 不支援類似的功能。 如果`Source`屬性設定為`null`、`SetSource`方法只會予以忽略，而目前的視訊會繼續播放。

### <a name="the-uwp-video-source"></a>UWP 視訊來源

UWP`MediaElement`定義`AutoPlay`屬性，會在類似任何其他內容轉譯器中處理：

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

`SetSource`屬性處理常式`UriVideoSource`物件藉由設定`Source`屬性`MediaElement`的.net`Uri`值，或`null`如果`Source`屬性`VideoPlayer`設`null`:

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

## <a name="setting-a-url-source"></a>URL 來源設定

有三個轉譯器中的這些屬性的實作，就可能要播放的視訊來源的 URL。 **播放 Web 視訊**頁面[ **VideoPlayDemos** ]( https://developer.xamarin.com/samples/xamarin-forms/customrenderers/videoplayerdemos/index.md)程式由下列 XAML 檔案所定義：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayWebVideoPage"
             Title="Play Web Video">

    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

</ContentPage>
```

`VideoSourceConverter`類別將字串轉換成`UriVideoSource`。 當您瀏覽至**播放 Web 視訊** 頁面上，視訊會開始載入和啟動播放已下載並緩衝處理足夠數量的資料。 視訊是長度約 10 分鐘：

[![播放網頁的視訊](web-videos-images/playwebvideo-small.png "播放的視訊 Web")](web-videos-images/playwebvideo-large.png#lightbox "播放網頁的視訊")

每個三個平台上傳輸控制淡出，如果它們被使用，但是可以藉由點選視訊檢視還原。

您可以防止自動啟動設定視訊`AutoPlay`屬性`false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AutoPlay="false" />
```

您必須按**播放**按鈕以啟動視訊。

同樣地，您可以隱藏傳輸控制項的顯示設定`AreTransportControlsEnabled`屬性`false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AreTransportControlsEnabled="False" />
```

如果您將這兩個屬性設定為`false`，然後視訊時才會開始播放，並會有沒有方法可以將它啟動 ！ 您必須呼叫`Play`從程式碼後置檔案，或文件中所述，建立您自己的傳輸控制[實作自訂視訊傳輸控制](custom-transport.md)。 

**App.xaml**檔案包含兩個額外的視訊資源：

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

這些其他影片的其中一個參考，您可以取代中的明確 URL **PlayWebVideo.xaml**檔案搭配`StaticResource`標記延伸，在此情況下`VideoSourceConverter`不需要建立`UriVideoSource`物件：

```xaml
<video:VideoPlayer Source="{StaticResource ElephantsDream}" />
```

或者，您可以設定`Source`屬性中的視訊檔案從`ListView`文章所述，[繫結到播放程式的視訊來源](source-bindings.md)。





## <a name="related-links"></a>相關連結

- [視訊播放程式示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
