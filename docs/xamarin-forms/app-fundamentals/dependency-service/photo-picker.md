---
title: 從 圖片庫挑選相片
description: 這篇文章說明如何使用 Xamarin.Forms DependencyService 類別，以從手機上的圖片庫中挑選相片。
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: b593815df9ce942a98496806116bacfa63e2a2d9
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38999058"
---
# <a name="picking-a-photo-from-the-picture-library"></a>從 圖片庫挑選相片

這篇文章會逐步建立可讓使用者能夠從手機上的圖片庫選取相片的應用程式。 因為 Xamarin.Forms 不包含這項功能，就必須使用[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)存取每個平台的原生 Api。  本文將介紹下列步驟使用`DependencyService`這項工作：

- **[建立介面](#Creating_the_Interface)** &ndash;了解如何建立共用程式碼中的介面。
- **[iOS 實作](#iOS_Implementation)** &ndash;了解如何在適用於 iOS 的原生程式碼中實作的介面。
- **[Android 的實作](#Android_Implementation)** &ndash;了解如何在原生程式碼中實作的介面，適用於 Android。
- **[通用 Windows 平台實作](#UWP_Implementation)** &ndash;了解如何在原生程式碼適用於通用 Windows 平台 (UWP) 中實作介面。
- **[在共用程式碼中實作](#Implementing_in_Shared_Code)** &ndash;了解如何使用`DependencyService`呼叫原生實作共用的程式碼。

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>建立介面

首先，表示所需的功能的共用程式碼中建立的介面。 在相片挑選應用程式的情況下只有一個方法則是必要項目。 這定義於[ `IPicturePicker` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs)介面中的範例程式碼的.NET Standard 程式庫：

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

`GetImageStreamAsync`方法會定義為非同步，因為此方法必須傳回快速，但它不能傳回`Stream`所選的相片，直到使用者瀏覽圖片庫並選取其中一個物件。

這個介面被實作在所有平台上使用平台特定程式碼。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 的實作

IOS 實作`IPicturePicker`介面會使用[ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/)中所述[**從資源庫選擇相片**](https://developer.xamarin.com/recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery/)配方和[範例程式碼](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)。

IOS 實作包含在[ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) iOS 專案中的範例程式碼的類別。 若要讓這個類別看見`DependencyService`管理員中，類別必須用來識別 [`assembly`] 類型的屬性`Dependency`，類別必須是公用的並明確地實作`IPicturePicker`介面：

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

`GetImageStreamAsync`方法會建立`UIImagePickerController`並初始化，以從相片庫選取映像。 兩個事件處理常式所需： 一個用於當使用者選取相片和另一個則用於當使用者取消的相片媒體櫃顯示。 `PresentModalViewController`然後向使用者顯示的相片媒體櫃。

在此時`GetImageStreamAsync`方法必須傳回`Task<Stream>`正在呼叫它的程式碼的物件。 只有在使用者完成互動的相片媒體櫃，並呼叫其中一個事件處理常式時，會完成這項工作。 針對這種情況下[ `TaskCompletionSource` ](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx)類別是不可或缺。 這個類別會提供`Task`物件的正確泛型型別，若要從傳回`GetImageStreamAsync`方法和類別可以稍後再接收信號，當工作完成。

`FinishedPickingMedia`當使用者選取一張圖片時，會呼叫事件處理常式。 不過，處理常式會提供`UIImage`物件和`Task`必須傳回.NET`Stream`物件。 這是在兩個步驟：`UIImage`物件會先轉換為 JPEG 檔案儲存在記憶體中`NSData`物件，然後`NSData`物件轉換成.NET`Stream`物件。 呼叫`SetResult`方法`TaskCompletionSource`物件提供完成工作`Stream`物件：

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

IOS 應用程式需要從使用者的權限存取手機上的相片媒體櫃。 將下列內容加入`dict`Info.plist 檔案的區段：

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```


<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 的實作

Android 的實作會使用所述的技巧[**選取映像**](https://developer.xamarin.com/recipes/android/other_ux/pick_image/)配方和[程式碼範例](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)。 不過，當使用者從圖片庫選取映像時所呼叫的方法是`OnActivityResult`衍生自的類別中覆寫`Activity`。 基於這個理由，法線[ `MainActivity` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) Android 專案中的類別具有已增添欄位、 屬性，並覆寫`OnActivityResult`方法：

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

`OnActivityResult`覆寫表示選取的圖片檔案，android`Uri`物件，但轉換成.NET，這可能`Stream`藉由呼叫物件`OpenInputStream`方法`ContentResolver`資料取自於的物件活動的`ContentResolver`屬性。

IOS 的實作中，像是 Android 的實作會使用`TaskCompletionSource`來表示當工作完成為止。 這`TaskCompletionSource`物件會定義為中的公用屬性`MainActivity`類別。 這可讓在中參考的屬性[ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) Android 專案中的類別。 這是具有類別`GetImageStreamAsync`方法：

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

這個方法會存取`MainActivity`類別有許多用途： 為`Instance`屬性，如`PickImageId`欄位，如`TaskCompletionSource`屬性，並呼叫`StartActivityForResult`。 這個方法會定義所`FormsAppCompatActivity`類別，這是基底類別的`MainActivity`。

<a name="UWP_Implementation" />

## <a name="uwp-implementation"></a>UWP 實作

不同於 iOS 和 Android 的實作，相片選擇器的實作，適用於通用 Windows 平台不需要`TaskCompletionSource`類別。 [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs)類別會使用[ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/)類別，以取得存取權的相片媒體櫃。 因為`PickSingleFileAsync`方法`FileOpenPicker`本身是非同步`GetImageStreamAsync`方法可以直接使用`await`該方法 （和其他非同步方法），並傳回`Stream`物件：


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

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共用程式碼中實作

現在，每個平台已實作的介面，.NET Standard 程式庫中的應用程式可以利用它。

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

`Clicked`處理常式會使用`DependencyService`類別來呼叫`GetImageStreamAsync`。 這會導致平台專案中的呼叫。 如果此方法會傳回`Stream`物件，則處理常式會建立`Image`項目與該圖片`TabGestureRecognizer`，並取代`StackLayout`該頁面上`Image`:

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

點選`Image`項目頁面會恢復正常。


## <a name="related-links"></a>相關連結

- [從資源庫 (iOS) 中選擇相片](https://developer.xamarin.com/recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery/)
- [選取映像 (Android)](https://developer.xamarin.com/recipes/android/other_ux/pick_image/)
- [DependencyService （範例）](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
