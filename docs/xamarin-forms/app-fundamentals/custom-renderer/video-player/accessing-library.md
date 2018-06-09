---
title: 存取裝置的視訊媒體櫃
description: 本文說明如何存取裝置的影片播放器應用程式，使用 Xamarin.Forms 中的視訊媒體櫃。
ms.prod: xamarin
ms.assetid: 364C1D43-EAAE-45B9-BE24-0DA5AE74C4D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 7e9f7ad93ae8828155847b923cb2779b3146f63e
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240696"
---
# <a name="accessing-the-devices-video-library"></a>存取裝置的視訊媒體櫃

大多數最新型的行動裝置和桌面的電腦能夠使用裝置攝影機錄製影片。 然後，使用者建立的視訊會儲存為裝置上的檔案。 這些檔案可以從映像庫中擷取和播放`VideoPlayer`類別就像任何其他視訊。

## <a name="the-photo-picker-dependency-service"></a>相片選擇器相依性服務

每三個平台包含一種可讓使用者從裝置的映像庫中選取的相片或視訊的設備。 播放視訊從裝置的映像庫中的第一個步驟建置到叫用每個平台映像選擇器的相依性服務。 下面描述的相依性服務是非常類似中定義的其中一項[**挑選相片圖片庫從**](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)發行項，不同之處在於視訊選擇器傳回檔案名稱，而不是`Stream`物件。

標準.NET 程式庫專案會定義名為介面`IVideoPicker`相依性服務：

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPicker
    {
        Task<string> GetVideoFileAsync();
    }
}
```

三個平台的每個包含類別，名為`VideoPicker`它會實作這個介面。

### <a name="the-ios-video-picker"></a>IOS 視訊選擇器

IOS`VideoPicker`使用 iOS [ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/)存取映像庫中，指定它應該限制為視訊 （稱為 「 電影 」） iOS 中`MediaType`屬性。 請注意，`VideoPicker`明確實作`IVideoPicker`介面。 另請注意`Dependency`屬性，可識別這個類別做為相依性服務。 這些是允許 Xamarin.Forms 尋找相依性服務平台專案中的兩個需求：

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

### <a name="the-android-video-picker"></a>Android 的視訊選擇器

Android 的實作`IVideoPicker`需要屬於應用程式的活動的回呼方法。 基於這個原因，`MainActivity`類別會定義兩個屬性、 欄位和回呼方法：

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

`OnCreate`方法中的`MainActivity`將自己的執行個體儲存在靜態`Current`屬性。 這可讓實作`IVideoPicker`取得`MainActivity`啟動的執行個體**選取視訊**選擇器：

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

若要新增的項目`MainActivity`物件是唯一的程式碼中[ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)解決方案，亦即一般應用程式程式碼需要修改，才能支援`FormsVideoLibrary`類別。

### <a name="the-uwp-video-picker"></a>UWP 視訊選擇器

UWP 實作`IVideoPicker`介面使用 UWP [ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/)。 它會開始與圖片媒體櫃的檔案搜尋，並將 MP4 和 WMV （Windows Media 視訊） 限制的檔案類型：

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

**播放程式庫視訊**頁面[ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)程式會示範如何使用視訊選擇器相依性服務。 XAML 檔案包含`VideoPlayer`執行個體和`Button`標示為**顯示視訊媒體櫃**:

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

程式碼後置檔案包含`Clicked`處理常式`Button`。 叫用相依性服務需要呼叫`DependencyService.Get`取得的實作`IVideoPicker`平台專案中的介面。 `GetVideoFileAsync`然後該執行個體上呼叫方法：

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

`Clicked`處理常式然後使用該檔案名稱來建立`FileVideoSource`物件，並將它設定為`Source`屬性`VideoPlayer`。

每個`VideoPlayerRenderer`類別包含程式碼，在其`SetSource`方法類型的物件`FileVideoSource`。 這些如下所示：

### <a name="handling-ios-files"></a>處理 iOS 檔案

IOS 版本`VideoPlayerRenderer`處理程序`FileVideoSource`物件使用靜態`Asset.FromUrl`以檔案名稱的方法。 這個建立`AVAsset`物件，表示裝置的映像庫中的檔案：

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

### <a name="handling-android-files"></a>處理 Android 的檔案

處理類型的物件時`FileVideoSource`，Android 實作`VideoPlayerRenderer`使用`SetVideoPath`方法`VideoView`至指定裝置的映像庫中的檔案：

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

處理類型的物件時`FileVideoSource`，UWP 實作`SetSource`方法需要建立`StorageFile`物件、 開啟檔案進行讀取，並資料流物件傳遞至`SetSource`方法`MediaElement`:

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

對於所有的三個平台，視訊會開始播放視訊之後，幾乎立即因為裝置上的檔案，且不需要下載設定來源。



## <a name="related-links"></a>相關連結

- [視訊播放程式示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
- [從 圖片庫挑選相片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
