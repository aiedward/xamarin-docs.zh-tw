---
title: 播放網頁視訊
description: 這篇文章說明如何使用 Xamarin.Forms 的視訊播放器應用程式中播放網頁視訊。
ms.prod: xamarin
ms.assetid: 75781A10-865D-4BA8-8D6B-E3DA012922BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 566f056bd616c918ce274b9c7406d94fdc265ea2
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994555"
---
# <a name="playing-a-web-video"></a>播放網頁視訊

`VideoPlayer`類別會定義`Source`屬性，用來指定來源的視訊檔，以及`AutoPlay`屬性。 `AutoPlay` 具有預設值為`true`，這表示影片應該開始播放之後自動`Source`已設定：

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

`Source`屬性的類型是`VideoSource`，它模仿 Xamarin.Forms [ `ImageSource` ](xref:Xamarin.Forms.ImageSource)抽象類別，而其三個衍生項目[ `UriImageSource` ](xref:Xamarin.Forms.UriImageSource)， [`FileImageSource` ](xref:Xamarin.Forms.FileImageSource)，並[ `StreamImageSource` ](xref:Xamarin.Forms.StreamImageSource)。 沒有資料流的選項可供`VideoPlayer`不過，因為 iOS 和 Android 不支援播放視訊，以從資料流。

## <a name="video-sources"></a>影片來源

抽象`VideoSource`類別僅包含三個具現化衍生自的三個類別的靜態方法`VideoSource`:

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

處理型別的物件`UriVideoSource`如下所述。

`ResourceVideoSource`類別用來存取儲存為內嵌資源的平台應用程式，也會使用指定的視訊檔案`string`屬性：

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

處理物件型別的`ResourceVideoSource`一文所述[載入應用程式資源影片](loading-resources.md)。 `VideoPlayer`類別有任何設備可載入視訊檔案儲存為.NET Standard 程式庫中的資源。

`FileVideoSource`類別用來從裝置的視訊媒體櫃存取視訊檔案。 單一的屬性也屬於型別`string`:

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

處理物件型別的`FileVideoSource`一文所述[存取裝置的視訊媒體櫃](accessing-library.md)。

`VideoSource`類別包含`TypeConverter`參考的屬性`VideoSourceConverter`:

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

這個型別轉換子會叫用時`Source`屬性設定為在 XAML 中的字串。 以下是`VideoSourceConverter`類別：

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

`ConvertFromInvariantString`方法會嘗試將字串轉換為`Uri`物件。 如果這個動作成功，而且配置不是`file:`，則方法會傳回`UriVideoSource`。 否則，它會傳回`ResourceVideoSource`。

## <a name="setting-the-video-source"></a>設定影片來源

所有其他邏輯涉及影片來源是在個別的平台轉譯器中實作。 下列各節顯示如何平台轉譯器播放視訊時`Source`屬性設定為`UriVideoSource`物件。

### <a name="the-ios-video-source"></a>IOS 視訊來源

兩個區段`VideoPlayerRenderer`涉及設定視訊來源的影片播放程式。 當 Xamarin.Forms 會先建立類型的物件`VideoPlayer`，則`OnElementChanged`方法呼叫`NewElement`引數物件的屬性設定為， `VideoPlayer`。 `OnElementChanged`方法呼叫`SetSource`:

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

更新版本上，當`Source`屬性變更時，`OnElementPropertyChanged`方法呼叫`PropertyName`屬性的 「 來源 」，和`SetSource`會再次呼叫。

若要播放視訊檔，在 iOS 中，類型的物件[ `AVAsset` ](https://developer.xamarin.com/api/type/AVFoundation.AVAsset/)初次建立封裝的視訊檔案，以及用來建立[ `AVPlayerItem` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/)，這再交給`AVPlayer`物件。 以下是如何`SetSource`方法會處理`Source`屬性的型別時`UriVideoSource`:

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

`AutoPlay`屬性會有在 iOS 視訊類別中，任何的類比因此屬性會檢查在結尾`SetSource`方法來呼叫`Play`方法`AVPlayer`物件。

在某些情況下，影片會繼續播放後的頁面`VideoPlayer`瀏覽回到首頁。 若要停止視訊，請`ReplaceCurrentItemWithPlayerItem`也會設定`Dispose`覆寫：

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

Android`VideoPlayerRenderer`必須設定影片來源的播放程式時`VideoPlayer`會先建立和更新版本時`Source`屬性變更：

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

`SetSource`方法會處理類型的物件`UriVideoSource`藉由呼叫`SetVideoUri`上`VideoView`android`Uri`從字串的 URI 建立的物件。 `Uri`類別完整這裡以區分它與.NET`Uri`類別：

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

Android`VideoView`沒有相對應`AutoPlay`屬性，因此`Start`會呼叫方法，如果您已設定新的影片。

如果有為行為的 iOS 和 Android 的轉譯器之間的差異`Source`屬性`VideoPlayer`設為`null`，或如果`Uri`屬性`UriVideoSource`設為`null`或空白字串。 如果 iOS 視訊播放器目前正在播放影片，並`Source`設為`null`(或字串`null`或空白)，`ReplaceCurrentItemWithPlayerItem`呼叫`null`值。 目前的視訊會被取代，並停止播放。

Android 不支援類似的設備。 如果`Source`屬性設定為`null`，則`SetSource`方法只會忽略它，而目前的視訊會繼續播放。

### <a name="the-uwp-video-source"></a>UWP 視訊來源

UWP`MediaElement`定義`AutoPlay`屬性，如同任何其他屬性轉譯器會處理：

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

`SetSource`屬性控制代碼`UriVideoSource`藉由設定物件`Source`屬性`MediaElement`.net`Uri`的值，或`null`如果`Source`屬性`VideoPlayer`設為`null`:

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

使用這些屬性在三個轉譯器中實作時，就可以播放視訊來源的 URL。 **播放網頁視訊**頁面[ **VideoPlayDemos** ]( https://developer.xamarin.com/samples/xamarin-forms/customrenderers/videoplayerdemos/index.md)程式由下列的 XAML 檔案中定義：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayWebVideoPage"
             Title="Play Web Video">

    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

</ContentPage>
```

`VideoSourceConverter`類別將字串轉換成`UriVideoSource`。 當您瀏覽至**播放網頁視訊** 頁面上，視訊會開始載入及播放已下載並緩衝處理足夠數量的資料時啟動。 影片是約 10 分鐘的長度：

[![播放網頁視訊](web-videos-images/playwebvideo-small.png "播放網頁視訊")](web-videos-images/playwebvideo-large.png#lightbox "播放網頁視訊")

在每個三個平台上傳輸控制淡出如果它們不會被使用，但可以還原成藉由點選影片的檢視。

您可以防止自動啟動設定視訊`AutoPlay`屬性設`false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AutoPlay="false" />
```

您必須按下**播放**按鈕以啟動影片。

同樣地，您就可以隱藏傳輸控制項的顯示設定`AreTransportControlsEnabled`屬性設`false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AreTransportControlsEnabled="False" />
```

如果您將這兩個屬性設定為`false`，然後影片不會開始播放，並會有沒有辦法加以啟動 ！ 您必須呼叫`Play`從程式碼後置檔案，或文件中所述，建立您自己的傳輸控制[實作自訂影片傳輸控制項](custom-transport.md)。

**App.xaml**檔案包含的兩個其他影片的資源：

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

這些其他影片的其中一個的參考，您可以明確中的 URL 取代**PlayWebVideo.xaml**檔案並`StaticResource`標記延伸，在此情況下`VideoSourceConverter`不需要建立`UriVideoSource`物件：

```xaml
<video:VideoPlayer Source="{StaticResource ElephantsDream}" />
```

或者，您可以設定`Source`從視訊檔案中的屬性`ListView`所述，在下一篇文章中，[繫結到播放程式的視訊來源](source-bindings.md)。





## <a name="related-links"></a>相關連結

- [示範影片播放程式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
