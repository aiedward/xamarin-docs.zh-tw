---
title: Xamarin.Essentials：媒體選擇器
description: 中的 MediaPicker 類別 Xamarin.Essentials 可讓使用者在裝置上挑選或拍攝相片或影片。
ms.assetid: 23460875-6cf9-4440-a97b-46c55b0bca69
author: jamesmontemagno
ms.author: jamont
ms.date: 01/04/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4d080ab13bb04b8502ad547234d5b9592af406e1
ms.sourcegitcommit: 995ee23d93e08dceb8754cc6c682cd2f4594345b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972249"
---
# <a name="no-locxamarinessentials-media-picker"></a>Xamarin.Essentials：媒體選擇器

**MediaPicker** 類別可讓使用者在裝置上挑選或拍攝相片或影片。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取 **MediaPicker** 功能，需要下列平臺特定設定。

# <a name="android"></a>[Android](#tab/android)

以下是必要的許可權，而且必須在 Android 專案中設定。 能以下列方式新增：

開啟 [Properties] 資料夾下的 **AssemblyInfo.cs** 檔案並新增：

```csharp
// Needed for Picking photo/video
[assembly: UsesPermission(Android.Manifest.Permission.ReadExternalStorage)]

// Needed for Taking photo/video
[assembly: UsesPermission(Android.Manifest.Permission.WriteExternalStorage)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]

// Add these properties if you would like to filter out devices that do not have cameras, or set to false to make them optional
[assembly: UsesFeature("android.hardware.camera", Required = true)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = true)]
```

或更新 Android 資訊清單：

開啟 [ **Properties** ] 資料夾底下的 **AndroidManifest.xml** 檔案，並在 **資訊清單** 節點內新增下列內容。

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.CAMERA" />
```

禍以滑鼠右鍵按一 Android 專案並開啟專案的屬性。 在 [ **Android 資訊清單** ] 下，尋找 [ **必要許可權：** ] 區域並檢查這些許可權。 這將會自動更新 **AndroidManifest.xml** 檔案。

# <a name="ios"></a>[iOS](#tab/ios)

在中 `Info.plist` ，新增下列機碼：

```xml
<key>NSCameraUsageDescription</key>
<string>This app needs access to the camera to take photos.</string>
<key>NSMicrophoneUsageDescription</key>
<string>This app needs access to microphone for taking videos.</string>
<key>NSPhotoLibraryAddUsageDescription</key>
<string>This app needs access to the photo gallery for picking photos and videos.</string>
<key>NSPhotoLibraryUsageDescription</key>
<string>This app needs access to photos gallery for picking photos and videos.</string>
```

請務必將 `<string>` 每個更新中的更新為應用程式的特定描述，因為它會向您的使用者顯示。

# <a name="uwp"></a>[UWP](#tab/uwp)

在 [ `Package.appxmanifest` **功能** ] 下， `Microphone` 確定 `Webcam` 已核取和功能。

-----

## <a name="using-media-picker"></a>使用媒體選擇器

`MediaPicker`類別具有下列方法，這些方法會傳回 `FileResult` 可用來取得檔案位置或將其讀取為的 `Stream` 。

* `PickPhotoAsync`：開啟媒體瀏覽器以選取相片。
* `CapturePhotoAsync`：開啟相機拍攝相片。
* `PickVideoAsync`：開啟媒體瀏覽器以選取影片。
* `CaptureVideoAsync`：開啟相機以拍攝影片。

每個方法都可選擇性地接受 `MediaPickerOptions` 參數， `Title` 以允許在顯示給使用者的某些作業系統上設定。

> [!TIP]
> 您必須在 UI 執行緒上呼叫所有方法，因為系統會自動處理許可權檢查和要求 Xamarin.Essentials 。

## <a name="general-usage"></a>一般使用方式

```csharp
async Task TakePhotoAsync()
{
    try
    {
        var photo = await MediaPicker.CapturePhotoAsync();
        await LoadPhotoAsync(photo);
        Console.WriteLine($"CapturePhotoAsync COMPLETED: {PhotoPath}");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"CapturePhotoAsync THREW: {ex.Message}");
    }
}

async Task LoadPhotoAsync(FileResult photo)
{
    // canceled
    if (photo == null)
    {
        PhotoPath = null;
        return;
    }
    // save the file into local storage
    var newFile = Path.Combine(FileSystem.CacheDirectory, photo.FileName);
    using (var stream = await photo.OpenReadAsync())
    using (var newStream = File.OpenWrite(newFile))
        await stream.CopyToAsync(newStream);

    PhotoPath = newFile;
}
```


## <a name="api"></a>API

- [MediaPicker 來源程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/MediaPicker)
- [MediaPicker API 檔](xref:Xamarin.Essentials.MediaPicker)
