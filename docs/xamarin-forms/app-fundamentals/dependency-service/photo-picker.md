---
title: 從圖片媒體櫃挑選相片
description: 此文章說明如何使用 Xamarin.Forms DependencyService 類別從手機的圖片媒體櫃挑選相片。
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 36601b02bb2984d9350166dedac0d650d9642f91
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650650"
---
# <a name="picking-a-photo-from-the-picture-library"></a>從圖片媒體櫃挑選相片

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)

此文章會逐步說明如何建立可讓使用者從手機圖片媒體櫃挑選相片的應用程式。 由於 Xamarin.Forms 不包含這項功能，因此您必須使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 存取每個平台的原生 API。

## <a name="creating-the-interface"></a>建立介面

首先，在共用程式碼中建立介面以表示所需的功能。 如果是相片挑選應用程式，則只有一個方法是必要的。 這定義於範例程式碼 .NET Standard 程式庫中的 [`IPicturePicker`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs) 介面：

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

`GetImageStreamAsync` 方法會定義為非同步，因為此方法雖然必須快速傳回，但要等到使用者瀏覽圖片媒體櫃並選取其中一張相片時才能傳回所選相片的 `Stream` 物件。

這個介面會在所有平台上使用平台特定程式碼來實作。

## <a name="ios-implementation"></a>iOS 實作

`IPicturePicker` 介面的 iOS 實作會使用 [`UIImagePickerController`](xref:UIKit.UIImagePickerController)，如 [**Choose a Photo from the Gallery**](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery) (從資源庫選擇相片) 配方和[範例程式碼](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)中所述。

範例程式碼 iOS 專案中的 [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) 類別包含 iOS 實作。 若要讓 `DependencyService` 管理員看得見此類別，該類別必須使用 `Dependency` 類型的 [`assembly`] 屬性來識別，且該類別必須為公用且明確地實作 `IPicturePicker` 介面：

```csharp
[assembly: Dependency (typeof (PicturePickerImplementation))]

namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<Stream> GetImageStreamAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.PhotoLibrary,
                MediaTypes = UIImagePickerController.AvailableMediaTypes(UIImagePickerControllerSourceType.PhotoLibrary)
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<Stream>();
            return taskCompletionSource.Task;
        }
        ...
    }
}

```

`GetImageStreamAsync` 方法會建立 `UIImagePickerController` 並將其初始化，以從相片媒體櫃選取影像。 需要兩個事件處理常式：一個在使用者選取相片時使用；另一個在使用者取消顯示相片媒體櫃時使用。 接著，`PresentModalViewController` 即會向使用者顯示相片媒體櫃。

此時，`GetImageStreamAsync` 方法必須將 `Task<Stream>` 物件傳回給呼叫它的程式碼。 這項工作只有在使用者完成與相片媒體櫃的互動，並呼叫其中一個事件處理常式時才會完成。 針對這種情況，[`TaskCompletionSource`](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx) 類別是不可或缺的。 這個類別會提供適當泛型型別的 `Task` 物件以從 `GetImageStreamAsync` 方法傳回，該類別即可稍後於工作完成時收到通知。

當使用者選取圖片時，即會呼叫 `FinishedPickingMedia` 事件處理常式。 不過，處理常式會提供 `UIImage` 物件，且 `Task` 必須傳回 .NET `Stream` 物件。 這是透過下列兩個步驟達成：`UIImage` 物件會先轉換為儲存在 `NSData` 物件記憶體中的 JPEG 檔案，然後 `NSData` 物件會轉換成 .NET `Stream` 物件。 呼叫 `TaskCompletionSource` 物件的 `SetResult` 方法時，其可提供 `Stream` 物件以完成工作：

```csharp
namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;
        ...
        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            UIImage image = args.EditedImage ?? args.OriginalImage;

            if (image != null)
            {
                // Convert UIImage to .NET Stream object
                NSData data = image.AsJPEG(1);
                Stream stream = data.AsStream();

                UnregisterEventHandlers();

                // Set the Stream as the completion of the Task
                taskCompletionSource.SetResult(stream);
            }
            else
            {
                UnregisterEventHandlers();
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            UnregisterEventHandlers();
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }

        void UnregisterEventHandlers()
        {
            imagePicker.FinishedPickingMedia -= OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled -= OnImagePickerCancelled;
        }
    }
}

```

iOS 應用程式需要使用者的權限，以存取手機上的相片媒體櫃。 將下列內容新增至 Info.plist 檔案的 `dict` 區段：

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```

## <a name="android-implementation"></a>Android 實作

Android 實作會使用 [**Select an Image**](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image) (選取影像) 配方和[程式碼範例](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)中所述的技巧。 不過，當使用者從圖片媒體櫃選取影像時，所呼叫的方法是衍生自 `Activity` 之類別中的 `OnActivityResult` 覆寫。 基於這個理由，Android 專案中的一般 [`MainActivity`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) 類別已補充一個欄位、屬性，以及 `OnActivityResult` 方法的覆寫：

```csharp
public class MainActivity : FormsAppCompatActivity
{
    ...
    // Field, property, and method for Picture Picker
    public static readonly int PickImageId = 1000;

    public TaskCompletionSource<Stream> PickImageTaskCompletionSource { set; get; }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent intent)
    {
        base.OnActivityResult(requestCode, resultCode, intent);

        if (requestCode == PickImageId)
        {
            if ((resultCode == Result.Ok) && (intent != null))
            {
                Android.Net.Uri uri = intent.Data;
                Stream stream = ContentResolver.OpenInputStream(uri);

                // Set the Stream as the completion of the Task
                PickImageTaskCompletionSource.SetResult(stream);
            }
            else
            {
                PickImageTaskCompletionSource.SetResult(null);
            }
        }
    }
}

```

`OnActivityResult` 覆寫表示選取的圖片檔案具有 Android `Uri` 物件，但其可藉由呼叫 `ContentResolver` 物件 (擷取自活動的 `ContentResolver` 屬性) 的 `OpenInputStream` 方法來轉換成 .NET `Stream` 物件。

如同 iOS 實作，Android 實作會使用 `TaskCompletionSource` 以在工作完成時發出通知。 這個 `TaskCompletionSource` 物件會定義為 `MainActivity` 類別中的公用屬性。 這可讓該屬性在 Android 專案的 [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) 類別中受到參考。 這是搭配 `GetImageStreamAsync` 方法的類別：

```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.Droid
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public Task<Stream> GetImageStreamAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("image/*");
            intent.SetAction(Intent.ActionGetContent);

            // Start the picture-picker activity (resumes in MainActivity.cs)
            MainActivity.Instance.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Picture"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            MainActivity.Instance.PickImageTaskCompletionSource = new TaskCompletionSource<Stream>();

            // Return Task object
            return MainActivity.Instance.PickImageTaskCompletionSource.Task;
        }
    }
}
```

這個方法可基於多種用途存取 `MainActivity` 類別：可用於 `Instance` 屬性、用於 `PickImageId` 欄位、用於 `TaskCompletionSource` 屬性，以及呼叫 `StartActivityForResult`。 這個方法是由 `FormsAppCompatActivity` 類別所定義，該類別為 `MainActivity` 的基底類別。

## <a name="uwp-implementation"></a>UWP 實作

不同於 iOS 和 Android 實作，通用 Windows 平台的相片選擇器實作不需要 `TaskCompletionSource` 類別。 [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs) 類別會使用 [`FileOpenPicker`](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) 類別，以取得相片媒體櫃的存取權。 因為 `FileOpenPicker` 的 `PickSingleFileAsync` 方法本身為非同步，所以 `GetImageStreamAsync` 方法可以直接使用 `await` 搭配該方法 (和其他非同步方法)，並傳回 `Stream` 物件：


```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.UWP
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public async Task<Stream> GetImageStreamAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary,
            };

            openPicker.FileTypeFilter.Add(".jpg");
            openPicker.FileTypeFilter.Add(".jpeg");
            openPicker.FileTypeFilter.Add(".png");

            // Get a file and return a Stream
            StorageFile storageFile = await openPicker.PickSingleFileAsync();

            if (storageFile == null)
            {
                return null;
            }

            IRandomAccessStreamWithContentType raStream = await storageFile.OpenReadAsync();
            return raStream.AsStreamForRead();
        }
    }
}
```

## <a name="implementing-in-shared-code"></a>在共用程式碼中實作

現在，每個平台均已實作介面，.NET Standard 程式庫中的應用程式即可加以利用。

[`App`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs) 類別會建立 `Button` 以挑選相片：

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

`Clicked` 處理常式會使用 `DependencyService` 類別來呼叫 `GetImageStreamAsync`。 這會在平台專案中產生呼叫。 如果方法傳回 `Stream` 物件，則處理常式會為含有 `TapGestureRecognizer` 的圖片建立 `Image` 項目，並將頁面上的 `StackLayout` 取代為該 `Image`：

```csharp
pickPictureButton.Clicked += async (sender, e) =>
{
    pickPictureButton.IsEnabled = false;
    Stream stream = await DependencyService.Get<IPicturePicker>().GetImageStreamAsync();

    if (stream != null)
    {
        Image image = new Image
        {
            Source = ImageSource.FromStream(() => stream),
            BackgroundColor = Color.Gray
        };

        TapGestureRecognizer recognizer = new TapGestureRecognizer();
        recognizer.Tapped += (sender2, args) =>
        {
            (MainPage as ContentPage).Content = stack;
            pickPictureButton.IsEnabled = true;
        };
        image.GestureRecognizers.Add(recognizer);

        (MainPage as ContentPage).Content = image;
    }
    else
    {
        pickPictureButton.IsEnabled = true;
    }
};
```

點選 `Image` 項目時，頁面即會恢復正常。

## <a name="related-links"></a>相關連結

- [從資源庫選擇相片 (iOS)](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)
- [選取影像 (Android)](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)
- [Dependency Service (Samples)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample) (相依性服務 (範例))
