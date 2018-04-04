---
title: 從 圖片庫挑選相片
description: 使用 DependencyService 挑選從手機上的圖片媒體櫃的相片
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 5b4e3ab12a74b0f70866dc9f41593bfd5bcec0e8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="picking-a-photo-from-the-picture-library"></a>從 圖片庫挑選相片

這篇文章會引導，可讓使用者能夠從手機上的圖片庫選取相片應用程式的建立。 因為 Xamarin.Forms 不包含這項功能，所以需要使用[ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/)存取每個平台上的原生 Api。  本文將說明如何使用下列步驟`DependencyService`這項工作：

- **[建立介面](#Creating_the_Interface)** &ndash;了解如何建立共用的程式碼中的介面。
- **[iOS 實作](#iOS_Implementation)** &ndash;了解如何在原生 iOS 程式碼中實作介面。
- **[Android 實作](#Android_Implementation)** &ndash;了解如何在原生程式碼中實作的介面，適用於 Android。
- **[通用 Windows 平台實作](#UWP_Implementation)** &ndash;深入了解如何在原生程式碼中的通用 Windows 平台 (UWP) 上實作介面。
- **[Windows Phone 實作](#Windows_Phone_Implementation)** &ndash;了解如何在原生程式碼中實作的介面，適用於 Windows Phone 8.1。
- **[在共用程式碼中實作](#Implementing_in_Shared_Code)** &ndash;了解如何使用`DependencyService`來呼叫原生實作共用的程式碼。

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>建立介面

首先，建立共用的程式碼表示所需的功能中的介面。 如果相片挑選應用程式中，只有一個方法則是必要項目。 這定義在[ `IPicturePicker` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs)介面中的範例程式碼的可攜式類別程式庫：

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

`GetImageStreamAsync`方法會定義為非同步，因為此方法必須傳回快速，但它不能傳回`Stream`除非使用者已瀏覽圖片庫並選取其中一個所選相片的物件。

使用平台專屬程式碼的平台被實作這個介面。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 實作

IOS 實作`IPicturePicker`介面使用[ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/)中所述[**從資源庫選擇相片**](https://developer.xamarin.com/recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery/)配方和[範例程式碼](https://github.com/xamarin/recipes/tree/master/ios/media/video_and_photos/choose_a_photo_from_the_gallery)。

IOS 實作則包含在[ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) iOS 專案中的範例程式碼的類別。 若要讓這個類別看見`DependencyService`管理員中，類別必須用來識別 [`assembly`] 類型的屬性`Dependency`，類別必須是公用的並明確地實作`IPicturePicker`介面：

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

`GetImageStreamAsync`方法會建立`UIImagePickerController`並初始化，以從相片圖庫選取映像。 兩個事件處理常式所需： 一個用於當使用者選取相片與另一個則用於當使用者取消的相片程式庫顯示。 `PresentModalViewController`然後向使用者顯示相片的程式庫。

此時，`GetImageStreamAsync`方法必須傳回`Task<Stream>`正在呼叫的程式碼的物件。 使用者已完成與相片程式庫互動，並在呼叫其中一個事件處理常式時，才會完成此工作。 情況下，像這樣， [ `TaskCompletionSource` ](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx)類別非常重要。 這個類別會提供`Task`從傳回的正確泛型型別物件`GetImageStreamAsync`方法和類別可以稍後再接收信號，工作完成。

`FinishedPickingMedia`當使用者選取一張圖片時，會呼叫事件處理常式。 不過，此處理常式提供`UIImage`物件和`Task`必須傳回.NET`Stream`物件。 這是兩個步驟：`UIImage`物件先轉換成 JPEG 檔儲存在記憶體中`NSData`物件，然後`NSData`物件轉換成.NET`Stream`物件。 呼叫`SetResult`方法`TaskComkpletionSource`物件完成工作，藉由提供`Stream`物件：

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

                // Set the Stream as the completion of the Task
                taskCompletionSource.SetResult(stream);
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

IOS 應用程式需要從使用者的權限存取手機上的相片的媒體櫃。 將下列內容加入`dict`Info.plist 檔區段：

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```


<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 的實作

Android 的實作會使用所述的技巧[**選取映像**](https://developer.xamarin.com/recipes/android/other_ux/pick_image/)配方和[範例程式碼](https://github.com/xamarin/recipes/tree/master/android/other_ux/pick_image)。 不過，當使用者從圖片庫選取映像時所呼叫的方法是`OnActivityResult`衍生自的類別中覆寫`Activity`。 基於這個原因，一般[ `MainActivity` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) Android 專案中的類別已被增添欄位、 屬性和的覆寫`OnActivityResult`方法：

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
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

`OnActivityResult`覆寫表示選取的圖片檔案，與 Android`Uri`物件，但可能會轉換成.NET`Stream`藉由呼叫物件`OpenInputStream`方法`ContentResolver`已經從取得的物件活動的`ContentResolver`屬性。

IOS 實作中，像是 Android 的實作會使用`TaskCompletionSource`來表示工作已完成。 這`TaskCompletionSource`物件定義中的公用屬性`MainActivity`類別。 這可讓屬性中參考[ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) Android 專案中的類別。 這是具有類別`GetImageStreamAsync`方法：

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

這個方法會存取`MainActivity`多種用途的類別： 針對`Instance`屬性，如`PickImageId`欄位，以供`TaskCompletionSource`屬性，並呼叫`StartActivityForResult`。 這個方法由定義`FormsApplicationActivity`也就是類別的基底類別`MainActivity`。

<a name="UWP_Implementation" />

## <a name="uwp-implementation"></a>UWP 實作

不同於 iOS 和 Android 的實作中，針對通用 Windows 平台相片選擇器的實作不需要`TaskCompletionSource`類別。 [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs)類別會使用[ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/)類別，以取得存取權的相片程式庫。 因為`PickSingleFileAsync`方法`FileOpenPicker`本身是非同步，`GetImageStreamAsync`方法可以直接使用`await`利用該方法 （和其他非同步方法），並傳回`Stream`物件：


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

<a name="Windows_Phone_Implementation" />

## <a name="windows-phone-81-implementation"></a>Windows Phone 8.1 實作

Windows Phone 8.1 的實作會類似於 UWP 實作，但有重大影響的一大差異除外：`PickSingleFileAsync`方法`FileOpenPicker`不提供。 相反地，`GetImageStreamAsync`方法必須呼叫`PickSingleFileAndContinue`。 這個方法會傳回相片程式庫會顯示給使用者，但使用者的選取項目之相片的呼叫所收到信號時`OnActivated`方法`App`類別。

基於這個理由， [ `App` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/WinPhone/App.xaml.cs) Xamarin.Forms 專案範本，在 Windows Phone 8.1 專案中所建立的類別已增添型別的屬性`TaskCompletionSource`和的覆寫`OnActivated`方法：

```csharp
namespace DependencyServiceSample.WinPhone
{
    public sealed partial class App : Application
    {
        ...
        public TaskCompletionSource<Stream> TaskCompletionSource { set; get; }

        protected async override void OnActivated(IActivatedEventArgs args)
        {
            base.OnActivated(args);

            IContinuationActivatedEventArgs continuationArgs = args as IContinuationActivatedEventArgs;

            if (continuationArgs != null &&
                continuationArgs.Kind == ActivationKind.PickFileContinuation)
            {
                FileOpenPickerContinuationEventArgs pickerArgs = args as FileOpenPickerContinuationEventArgs;

                if (pickerArgs.Files.Count > 0)
                {
                    // Get the file and a Stream
                    StorageFile storageFile = pickerArgs.Files[0];
                    IRandomAccessStreamWithContentType raStream = await storageFile.OpenReadAsync();
                    Stream stream = raStream.AsStreamForRead();

                    // Set the completion of the Task
                    TaskCompletionSource.SetResult(stream);
                }
                else
                {
                    TaskCompletionSource.SetResult(null);
                }
            }
        }
    }
}
```

`OnActivated`數個原因，包括應用程式啟動時，可能會呼叫方法。 程式碼只能使用只將這些呼叫當檔案開啟選擇器已完成，並且接著取得`Stream`物件從`StorageFile`。

[ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/WinPhone/PicturePickerImplementation.cs)類別包含`GetImageStreamAsync`建立方法`FileOpenPicker`和呼叫`PickSingleFileAndContainue`:

```csharp
[assembly: Xamarin.Forms.Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.WinPhone
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public Task<Stream> GetImageStreamAsync()
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

            // Display the picker for a single file (resumes in OnActivated in App.xaml.cs)
            openPicker.PickSingleFileAndContinue();

            // Create a TaskCompletionSource stored in App.xaml.cs
            TaskCompletionSource<Stream> taskCompletionSource = new TaskCompletionSource<Stream>();
            (Application.Current as App).TaskCompletionSource = taskCompletionSource;

            // Return the Task object
            return taskCompletionSource.Task;
        }
    }
}

```

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共用程式碼中實作

現在，每個平台已實作的介面，常見的可攜式類別庫中的應用程式可以利用它。

[ `App` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs)類別會建立`Button`挑選相片：

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

`Clicked`處理常式會使用`DependencyService`類別來呼叫`GetImageStreamAsync`。 這會導致的平台專案中呼叫。 如果此方法會傳回`Stream`物件，然後此處理常式建立`Image`項目與該圖片`TabGestureRecognizer`，並取代`StackLayout`與頁面上`Image`:

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

點選`Image`項目會傳回頁面為 normal。


## <a name="related-links"></a>相關連結

- [選擇相片圖庫 (iOS)](https://developer.xamarin.com/recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery/)
- [選取映像 (Android)](https://developer.xamarin.com/recipes/android/other_ux/pick_image/)
- [DependencyService （範例）](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
