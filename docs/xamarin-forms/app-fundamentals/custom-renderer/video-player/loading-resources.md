---
title: 載入應用程式資源影片
description: 本文說明如何使用 Xamarin.Forms，載入儲存在視訊播放器應用程式中作為應用程式資源的影片。
ms.prod: xamarin
ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 0fb9ed06ef58c4350f479021f0c18e48c693cf7f
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059878"
---
# <a name="loading-application-resource-videos"></a>載入應用程式資源影片

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)

用於 `VideoPlayer` 檢視的自訂轉譯器，可以播放內嵌在個別平台專案中作為應用程式資源的視訊檔案。 不過，目前的 `VideoPlayer` 版本無法存取內嵌在 .NET Standard 程式庫中的資源。

若要載入這些資源，請將 `Path` 屬性設成資源的檔案名稱 (或資料夾與檔案名稱) 來建立 `ResourceVideoSource` 的執行個體。 或者，您也可以呼叫靜態的 `VideoSource.FromResource` 方法來參考這個資源。 然後，將 `ResourceVideoSource` 物件設為 `VideoPlayer` 的 `Source` 屬性。

## <a name="storing-the-video-files"></a>儲存視訊檔案

在平台專案中儲存視訊檔案，因平台而異。

### <a name="ios-video-resources"></a>iOS 影片資源

在 iOS 專案中，您可將影片儲存在 **Resources** 資料夾或 **Resources** 資料夾的子資料夾中。 視訊檔案必須具有 `BundleResource` 的 `Build Action`。 將 `ResourceVideoSource` 的 `Path` 屬性設成檔案名稱，例如 **Resources** 資料夾中的檔案為 **MyFile.mp4** 或 **MyFolder/MyFile.mp4**，其中 **MyFolder** 是 **Resources** 的子資料夾。

在 **VideoPlayerDemos** 解決方案中，**VideoPlayerDemos.iOS** 專案有個 **Resources** 的子資料夾名為 **Videos**，其包含名為 **iOSApiVideo.mp4** 的檔案。 這部短片會示範如何使用 Xamarin 網站尋找適用於 iOS `AVPlayerViewController` 類別的文件。

### <a name="android-video-resources"></a>Android 影片資源

在 Android 專案中，影片必須儲存在 **Resources** 的 **raw** 子資料夾中。 **raw** 資料夾不能包含子資料夾。 提供視訊檔案 `AndroidResource` 的 `Build Action`。 將 `ResourceVideoSource` 的 `Path` 屬性設成檔案名稱，例如 **MyFile.mp4**。

**VideoPlayerDemos.Android** 專案包含 **Resources** 的 **raw** 子資料夾，其包含名為 **AndroidApiVideo.mp4** 的檔案。

### <a name="uwp-video-resources"></a>UWP 影片資源

在通用 Windows 平台專案中，您可將影片儲存在專案中的任何資料夾中。 提供將檔案 `Content` 的 `Build Action`。 將 `ResourceVideoSource` 的 `Path` 屬性設成資料夾和檔案名稱，例如 **MyFolder/MyVideo.mp4**。

**VideoPlayerDemos.UWP** 專案包含名為 **Videos** 的資料夾，其具有 **UWPApiVideo.mp4** 檔案。

## <a name="loading-the-video-files"></a>載入視訊檔案

每個平台轉譯器類別在其 `SetSource` 方法中都包含程式碼，以載入儲存為資源的視訊檔案。

### <a name="ios-resource-loading"></a>iOS 資源載入

iOS 版 `VideoPlayerRenderer` 會使用 `NSBundle` 的 `GetUrlForResource` 方法載入資源。 完整路徑必須分割成檔案名稱、副檔名和目錄。 程式碼會在 .NET `System.IO` 命名空間中使用 `Path` 類別，將檔案路徑分割成這些元件：

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

                if (!String.IsNullOrWhiteSpace(path))
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

Android 版 `VideoPlayerRenderer` 會使用檔案名稱和套件名稱來建構 `Uri` 物件。 套件名稱是應用程式的名稱，在本案例中為 **VideoPlayerDemos.Android**，其可從靜態的 `Context.PackageName` 屬性中取得。 然後將結果 `Uri` 物件傳遞至 `VideoView` 的 `SetVideoURI` 方法：

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

UWP `VideoPlayerRenderer` 建構了路徑的 `Uri` 物件，並將它設定為 `MediaElement` 的 `Source` 屬性：

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

**VideoPlayerDemos** 解決方案中的 [Play Video Resource] \(播放視訊資源\) 頁面會使用 `OnPlatform` 類別指定每個平台的視訊檔案：

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

如果 iOS 資源儲存在 **Resources** 資料夾中，而 UWP 資源儲存在專案的根資料夾中，則每個平台可以使用相同的檔案名稱。 如果是這種情況，您就可以直接將該名稱設成 `VideoPlayer` 的 `Source` 屬性。

以下是正在執行的頁面：

[![播放視訊資源](loading-resources-images/playvideoresource-small.png "播放視訊資源")](loading-resources-images/playvideoresource-large.png#lightbox "播放視訊資源")

您現在已了解如何[從 Web URI 載入影片](web-videos.md)，以及如何播放內嵌的資源。 此外，您可以[從裝置的影片櫃載入影片](accessing-library.md)。


## <a name="related-links"></a>相關連結

- [Video Player Demos (Samples)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) (視訊播放程式示範 (範例))
