---
title: 正在載入應用程式資源影片
description: 這篇文章說明如何將影片儲存視訊播放器應用程式，使用 Xamarin.Forms 中的應用程式資源載入。
ms.prod: xamarin
ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 17e9e7061e4329431a0f34abdbbb616a1aff1b43
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171322"
---
# <a name="loading-application-resource-videos"></a>正在載入應用程式資源影片

自訂轉譯器，如`VideoPlayer`檢視都能播放的視訊檔案已內嵌在個別的平台專案中，為應用程式資源。 不過，目前版本的`VideoPlayer`無法存取內嵌於.NET Standard 程式庫中的資源。

若要載入這些資源，建立的執行個體`ResourceVideoSource`藉由設定`Path`檔名 （或資料夾與檔案名稱） 之資源的屬性。 或者，您可以呼叫靜態`VideoSource.FromResource`參考這個資源的方法。 然後，設定`ResourceVideoSource`物件至`Source`屬性`VideoPlayer`。

## <a name="storing-the-video-files"></a>儲存視訊檔案

將視訊檔儲存在平台專案是每個平台不同。

### <a name="ios-video-resources"></a>iOS 視訊資源

在 iOS 專案中，您可以儲存中的影片**資源**資料夾或子資料夾**資源**資料夾。 視訊檔案必須具有`Build Action`的`BundleResource`。 設定`Path`屬性`ResourceVideoSource`到檔案名稱，例如**MyFile.mp4**中的檔案**資源**資料夾，或**MyFolder/MyFile.mp4**，何處**MyFolder**是子資料夾的**資源**。

在  **VideoPlayerDemos**方案， **VideoPlayerDemos.iOS**專案包含的子資料夾**資源**名為**影片**包含名為的檔案**iOSApiVideo.mp4**。 這是一部簡短影片會示範如何使用 Xamarin 的網站尋找適用於 iOS 的文件`AVPlayerViewController`類別。

### <a name="android-video-resources"></a>Android 的影片資源

在 Android 專案中，必須儲存影片中的子資料夾**資源**名為**原始**。 **原始**資料夾不能包含子資料夾。 提供的視訊檔案`Build Action`的`AndroidResource`。 設定`Path`的屬性`ResourceVideoSource`到檔案名稱，例如**MyFile.mp4**。

**VideoPlayerDemos.Android**專案中包含的子資料夾**資源**名為**原始**，其中包含名為**AndroidApiVideo.mp4**.

### <a name="uwp-video-resources"></a>UWP 影片資源

在通用 Windows 平台專案中，您可以儲存在專案中的任何資料夾中的影片。 請將檔案`Build Action`的`Content`。 設定`Path`的屬性`ResourceVideoSource`資料夾和檔案名稱，例如**MyFolder/MyVideo.mp4**。

**VideoPlayerDemos.UWP**專案包含名為的資料夾**影片**檔案**UWPApiVideo.mp4**。

## <a name="loading-the-video-files"></a>正在載入的視訊檔案

每個平台的轉譯器類別包含程式碼，在其`SetSource`載入影片的檔案儲存為資源的方法。

### <a name="ios-resource-loading"></a>iOS 資源載入

IOS 版本`VideoPlayerRenderer`會使用`GetUrlForResource`方法`NSBundle`載入資源。 完整路徑必須分成檔案名稱、 延伸與目錄。 程式碼會使用`Path`類別在.NET 中的`System.IO`分配到這些元件的檔案路徑的命名空間：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void SetSource()
        {
            AVAsset asset = null;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhitespace(path))
                {
                    string directory = Path.GetDirectoryName(path);
                    string filename = Path.GetFileNameWithoutExtension(path);
                    string extension = Path.GetExtension(path).Substring(1);
                    NSUrl url = NSBundle.MainBundle.GetUrlForResource(filename, extension, directory);
                    asset = AVAsset.FromUrl(url);
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="android-resource-loading"></a>Android 資源載入

Android`VideoPlayerRenderer`使用的檔案名稱和封裝名稱來建構`Uri`物件。 封裝名稱是應用程式名稱在此情況下**VideoPlayerDemos.Android**，其可從此靜態`Context.PackageName`屬性。 結果`Uri`物件會傳遞至`SetVideoURI`方法`VideoView`:

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
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string package = Context.PackageName;
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    string filename = Path.GetFileNameWithoutExtension(path).ToLowerInvariant();
                    string uri = "android.resource://" + package + "/raw/" + filename;
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="uwp-resource-loading"></a>UWP 資源載入

UWP`VideoPlayerRenderer`建構`Uri`路徑物件並將它設定為`Source`屬性`MediaElement`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = "ms-appx:///" + (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    Control.Source = new Uri(path);
                    hasSetSource = true;
                }
            }
        }
        ···
    }
}
```

## <a name="playing-the-resource-file"></a>播放資源檔

**播放視訊的資源**頁面**VideoPlayerDemos**解決方案會使用`OnPlatform`類別，以指定每個平台的視訊檔案：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayVideoResourcePage"
             Title="Play Video Resource">
    <video:VideoPlayer>
        <video:VideoPlayer.Source>
            <video:ResourceVideoSource>
                <video:ResourceVideoSource.Path>
                    <OnPlatform x:TypeArguments="x:String">
                        <On Platform="iOS" Value="Videos/iOSApiVideo.mp4" />
                        <On Platform="Android" Value="AndroidApiVideo.mp4" />
                        <On Platform="UWP" Value="Videos/UWPApiVideo.mp4" />
                    </OnPlatform>
                </video:ResourceVideoSource.Path>
            </video:ResourceVideoSource>
        </video:VideoPlayer.Source>
    </video:VideoPlayer>
</ContentPage>
```

如果 iOS 資源會儲存在**資源**資料夾，和 UWP 資源儲存在專案的根資料夾中，如果您還可以使用相同的檔案名稱，每個平台。 如果這種情況，則您可以設定該名稱直接進入`Source`屬性`VideoPlayer`。

以下是執行該頁面：

[![播放視訊的資源](loading-resources-images/playvideoresource-small.png "播放視訊的資源")](loading-resources-images/playvideoresource-large.png#lightbox "播放視訊的資源")

您現在已了解如何[從 Web URI 載入影片](web-videos.md)，以及如何播放內嵌的資源。 此外，您可以[將影片載入從裝置的視訊媒體櫃](accessing-library.md)。


## <a name="related-links"></a>相關連結

- [示範影片播放程式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
