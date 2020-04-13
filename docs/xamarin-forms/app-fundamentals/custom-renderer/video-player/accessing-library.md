---
title: 存取裝置的影片庫
description: 本文說明如何使用 Xamarin.Forms 存取裝置影片播放程式應用程式中的影片庫。
ms.prod: xamarin
ms.assetid: 364C1D43-EAAE-45B9-BE24-0DA5AE74C4D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 2f9de881621b6634b95bdca56a0aa9e7b9f2bb98
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771858"
---
# <a name="accessing-the-devices-video-library"></a>存取裝置的影片庫

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

現今多數行動裝置和桌上型電腦都能使用裝置的相機錄影。 然後使用者建立的影片會以檔案的形式儲存在裝置上。 這些檔案可以從影像庫中擷取，並透過 `VideoPlayer` 類別進行播放，與其他影片無異。

## <a name="the-photo-picker-dependency-service"></a>相片選擇器相依性服務

每個平台都含有讓使用者能從裝置影像庫中選取相片或影片的設備。 要從裝置的影像庫播放影片，首先要建立叫用各個平台上影像選擇器的相依性服務。 下述相依性服務與[**從圖片庫挑選相片**](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)一文所定義的相依性服務非常類似，不同之處在於影片選擇器會傳回檔案名稱，而非 `Stream` 物件。

.NET Standard 程式庫專案會為相依性服務定義 `IVideoPicker` 介面：

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPicker
    {
        Task<string> GetVideoFileAsync();
    }
}
```

每個平台均包含實作這個介面的 `VideoPicker` 類別。

### <a name="the-ios-video-picker"></a>iOS 影片選擇器

iOS`VideoPicker`使用[`UIImagePickerController`](xref:UIKit.UIImagePickerController)iOS 訪問圖像庫,指定應將其限制`MediaType`在 iOS 屬性中的視頻(稱為"電影")。 請注意，`VideoPicker` 會明確實作 `IVideoPicker` 介面。 另外也請注意 `Dependency` 屬性會將此類別識別為相依性服務。 以下是可讓 Xamarin.Forms 尋找平台專案中相依性服務的兩個需求：

```csharp
using System;
using System.Threading.Tasks;
using UIKit;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.iOS.VideoPicker))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPicker : IVideoPicker
    {
        TaskCompletionSource<string> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<string> GetVideoFileAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.SavedPhotosAlbum,
                MediaTypes = new string[] { "public.movie" }
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<string>();
            return taskCompletionSource.Task;
        }

        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            if (args.MediaType == "public.movie")
            {
                taskCompletionSource.SetResult(args.MediaUrl.AbsoluteString);
            }
            else
            {
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }
    }
}
```

### <a name="the-android-video-picker"></a>Android 影片選擇器

`IVideoPicker` 的 Android 實作需要作為應用程式活動一部分的回呼方法。 因此，`MainActivity` 類別會定義兩個屬性、一個欄位和一個回呼方法：

```csharp
namespace VideoPlayerDemos.Droid
{
    ···
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            Current = this;
            ···
        }

        // Field, properties, and method for Video Picker
        public static MainActivity Current { private set; get; }

        public static readonly int PickImageId = 1000;

        public TaskCompletionSource<string> PickImageTaskCompletionSource { set; get; }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);

            if (requestCode == PickImageId)
            {
                if ((resultCode == Result.Ok) && (data != null))
                {
                    // Set the filename as the completion of the Task
                    PickImageTaskCompletionSource.SetResult(data.DataString);
                }
                else
                {
                    PickImageTaskCompletionSource.SetResult(null);
                }
            }
        }
    }
}
```

`MainActivity` 中的 `OnCreate` 方法會將自己的執行個體儲存在靜態 `Current` 屬性中。 這讓 `IVideoPicker` 的實作可取得 `MainActivity` 執行個體，以啟動 **Select Video** 選擇器：

```csharp
using System;
using System.Threading.Tasks;
using Android.Content;
using Xamarin.Forms;

// Need application's MainActivity
using VideoPlayerDemos.Droid;

[assembly: Dependency(typeof(FormsVideoLibrary.Droid.VideoPicker))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPicker : IVideoPicker
    {
        public Task<string> GetVideoFileAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("video/*");
            intent.SetAction(Intent.ActionGetContent);

            // Get the MainActivity instance
            MainActivity activity = MainActivity.Current;

            // Start the picture-picker activity (resumes in MainActivity.cs)
            activity.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Video"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            activity.PickImageTaskCompletionSource = new TaskCompletionSource<string>();

            // Return Task object
            return activity.PickImageTaskCompletionSource.Task;
        }
    }
}
```

對 `MainActivity` 物件新增的項目是 [**VideoPlayerDemos **](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) 解決方案中，唯一需要改變一般應用程式程式碼才能支援 `FormsVideoLibrary` 類別的程式碼。

### <a name="the-uwp-video-picker"></a>UWP 影片選擇器

`IVideoPicker`介面的 UWP 使用[`FileOpenPicker`](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/)UWP 。 它會先在圖片庫中搜尋檔案，並限制檔案類型為 MP4 和 WMV (Windows Media 視訊)：

```csharp
using System;
using System.Threading.Tasks;
using Windows.Storage;
using Windows.Storage.Pickers;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.UWP.VideoPicker))]

namespace FormsVideoLibrary.UWP
{
    public class VideoPicker : IVideoPicker
    {
        public async Task<string> GetVideoFileAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary
            };

            openPicker.FileTypeFilter.Add(".wmv");
            openPicker.FileTypeFilter.Add(".mp4");

            // Get a file and return the path
            StorageFile storageFile = await openPicker.PickSingleFileAsync();
            return storageFile?.Path;
        }
    }
}
```

## <a name="invoking-the-dependency-service"></a>叫用相依性服務

[**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) 程式的 **Play Library Video** 頁面示範如何使用影片選擇器相依性服務。 XAML 檔案包含 `VideoPlayer` 執行個體與標記 **Show Video Library** 的 `Button`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayLibraryVideoPage"
             Title="Play Library Video">
    <StackLayout>
        <video:VideoPlayer x:Name="videoPlayer"
                           VerticalOptions="FillAndExpand" />

        <Button Text="Show Video Library"
                Margin="10"
                HorizontalOptions="Center"
                Clicked="OnShowVideoLibraryClicked" />
    </StackLayout>
</ContentPage>
```

程式碼後置檔案包含 `Button` 的 `Clicked` 處理常式。 叫用該相依性服務需要呼叫 `DependencyService.Get`，以取得平台專案中 `IVideoPicker` 介面的實作。 然後會在該執行個體上呼叫 `GetVideoFileAsync` 方法：

```csharp
namespace VideoPlayerDemos
{
    public partial class PlayLibraryVideoPage : ContentPage
    {
        public PlayLibraryVideoPage()
        {
            InitializeComponent();
        }

       async void OnShowVideoLibraryClicked(object sender, EventArgs args)
        {
            Button btn = (Button)sender;
            btn.IsEnabled = false;

            string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();

            if (!String.IsNullOrWhiteSpace(filename))
            {
                videoPlayer.Source = new FileVideoSource
                {
                    File = filename
                };
            }

            btn.IsEnabled = true;
        }
    }
}
```

接著 `Clicked` 處理常式會使用該檔案名稱來建立 `FileVideoSource` 物件，並將其設定為 `VideoPlayer` 的 `Source` 屬性。

每個 `VideoPlayerRenderer` 類別的 `SetSource` 方法中都包含 `FileVideoSource` 類型之物件的程式碼。 如下所示：

### <a name="handling-ios-files"></a>處理 iOS 檔案

iOS 版本的 `VideoPlayerRenderer` 會使用含檔案名稱的靜態 `Asset.FromUrl` 方法，處理 `FileVideoSource` 物件。 這會建立代表裝置影像庫中檔案的 `AVAsset` 物件：

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
            else if (Element.Source is FileVideoSource)
            {
                string uri = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-android-files"></a>處理 Android 檔案

在處理 `FileVideoSource` 類型的物件時，`VideoPlayerRenderer` 的 Android 實作會使用 `VideoView` 的 `SetVideoPath` 方法來指定裝置影像庫中的檔案：

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
            else if (Element.Source is FileVideoSource)
            {
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    videoView.SetVideoPath(filename);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-uwp-files"></a>處理 UWP 檔案

在處理 `FileVideoSource` 類型的物件時，`SetSource` 方法的 UWP 實作需要建立 `StorageFile` 物件，開啟該檔案以讀取，再將資料流物件傳遞至 `MediaElement` 的 `SetSource` 方法：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is FileVideoSource)
            {
                // Code requires Pictures Library in Package.appxmanifest Capabilities to be enabled
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    StorageFile storageFile = await StorageFile.GetFileFromPathAsync(filename);
                    IRandomAccessStreamWithContentType stream = await storageFile.OpenReadAsync();
                    Control.SetSource(stream, storageFile.ContentType);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

對於每個平台而言，因為檔案位於裝置上，而且不需下載，所以當設定好影片來源之後，就會幾乎立即開始播放影片。

## <a name="related-links"></a>相關連結

- [影片播放程式示範 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
- [從圖片庫挑選相片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
