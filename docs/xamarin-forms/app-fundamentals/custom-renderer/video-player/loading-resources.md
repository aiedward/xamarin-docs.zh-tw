---
title: 正在載入應用程式資源影片
description: 本文說明如何載入視訊儲存為影片播放器應用程式，使用 Xamarin.Forms 中的應用程式資源。
ms.prod: xamarin
ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: f28b0dc8e25cb2e498f4101175005f05a5c5a6ef
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241028"
---
# <a name="loading-application-resource-videos"></a>正在載入應用程式資源影片

自訂轉譯器，如`VideoPlayer`檢視都能播放視訊檔已內嵌在個別的平台專案中，為應用程式資源。 不過，目前版本的`VideoPlayer`無法存取的標準.NET 程式庫中內嵌資源。

若要載入這些資源，建立的執行個體`ResourceVideoSource`藉由設定`Path`檔名 （或資料夾和檔案名稱） 之資源的屬性。 或者，您可以呼叫靜態`VideoSource.FromResource`參考這個資源的方法。 然後，設定`ResourceVideoSource`物件`Source`屬性`VideoPlayer`。

## <a name="storing-the-video-files"></a>儲存視訊檔案

不同的三個平台為平台專案中儲存視訊檔案：

### <a name="ios-video-resources"></a>iOS 視訊資源

在 iOS 專案中，您可以儲存在視訊**資源**資料夾或子資料夾的**資源**資料夾。 視訊檔案必須具有`Build Action`的`BundleResource`。 設定`Path`屬性`ResourceVideoSource`為檔案名稱，例如**MyFile.mp4**中檔案**資源**資料夾，或**MyFolder/MyFile.mp4**，其中**MyFolder**是子資料夾的**資源**。

在**VideoPlayerDemos**方案， **VideoPlayerDemos.iOS**專案包含的子資料夾**資源**名為**視訊**包含名為**iOSApiVideo.mp4**。 這段簡短的影片示範如何使用 Xamarin 網站上找出適用於 iOS 的文件`AVPlayerViewController`類別。

### <a name="android-video-resources"></a>Android 的視訊資源

在 Android 專案中，影片必須儲存在子資料夾**資源**名為**原始**。 **原始**資料夾不能包含子資料夾。 授與視訊檔案`Build Action`的`AndroidResource`。 設定`Path`屬性`ResourceVideoSource`為檔案名稱，例如**MyFile.mp4**。

**VideoPlayerDemos.Android**專案包含的子資料夾**資源**名為**原始**，其中包含名為**AndroidApiVideo.mp4**.

### <a name="uwp-video-resources"></a>UWP 視訊資源

在通用 Windows 平台專案中，您可以儲存在專案中的任何資料夾中的視訊。 提供檔案`Build Action`的`Content`。 設定`Path`屬性`ResourceVideoSource`資料夾和檔案名稱，例如**MyFolder/MyVideo.mp4**。

**VideoPlayerDemos.UWP**專案包含名為的資料夾**視訊**檔案**UWPApiVideo.mp4**。

## <a name="loading-the-video-files"></a>載入的視訊檔案

每個平台轉譯器類別包含程式碼，在其`SetSource`載入視訊檔案儲存成資源的方法。

### <a name="ios-resource-loading"></a>Io 資源載入

IOS 版本`VideoPlayerRenderer`使用`GetUrlForResource`方法`NSBundle`載入資源。 完整路徑必須分成檔名、 延伸模組和目錄。 程式碼會使用`Path`中.NET 類別`System.IO`分配到這些元件的檔案路徑的命名空間：

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

### <a name="android-resource-loading"></a>Android 的資源載入

在 Android`VideoPlayerRenderer`用來建構檔案名稱和封裝名稱`Uri`物件。 封裝名稱是應用程式名稱在此情況下**VideoPlayerDemos.Android**，這可以取自靜態`Context.PackageName`屬性。 產生`Uri`物件隨後會傳遞至`SetVideoURI`方法`VideoView`:

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

UWP`VideoPlayerRenderer`建構`Uri`路徑的物件並將其設`Source`屬性`MediaElement`:

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

**播放的視訊資源**頁面**VideoPlayerDemos**解決方案會使用`OnPlatform`類別，以指定每個平台的視訊檔案：

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

如果 iOS 資源儲存在**資源**資料夾中，如果 UWP 資源儲存在專案的根資料夾中，您可以使用相同的檔名三個平台。 如果是這樣，則您可以直接設定該名稱`Source`屬性`VideoPlayer`。

以下是三個平台上執行該頁面：

[![播放視訊資源](loading-resources-images/playvideoresource-small.png "播放視訊資源")](loading-resources-images/playvideoresource-large.png#lightbox "播放視訊的資源")

您現在已經瞭解如何[從 Web URI 載入視訊](web-videos.md)以及如何播放內嵌的資源。 此外，您可以[載入從裝置的視訊媒體櫃的影片](accessing-library.md)。


## <a name="related-links"></a>相關連結

- [視訊播放程式示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
