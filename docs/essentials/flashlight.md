---
title: Xamarin.Essentials： 手電筒
description: 本文件說明在 Xamarin.Essentials，都能夠開啟或關閉裝置的相機轉變為手電筒快閃手電筒類別。
ms.assetid: 06A03553-D212-43A2-9E6E-C2D2D93EB136
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 8c471f64c14a2e41693c450e02f89e7ac845d060
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353356"
---
# <a name="xamarinessentials-flashlight"></a>Xamarin.Essentials： 手電筒

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**手電筒**類別都能夠開啟或關閉裝置的相機轉變為手電筒 flash。

## <a name="getting-started"></a>快速入門

若要存取**手電筒**須有下列的平台特定設定的功能。

# <a name="androidtabandroid"></a>[Android](#tab/android)

手電筒和數位相機的權限所需，且必須在 Android 專案中設定。 這可以透過下列方式新增：

開啟**AssemblyInfo.cs**下方的檔案**屬性**資料夾，並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

或更新 Android 資訊清單：

開啟**AndroidManifest.xml**下方檔案**屬性**資料夾，並新增下列內**資訊清單**節點。

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

或以滑鼠右鍵按一下 Android 專案，並開啟專案的內容。 底下**Android 資訊清單**尋找**必要權限：** 區域，並檢查**手電筒**並**相機**權限。 這樣會自動更新**AndroidManifest.xml**檔案。

藉由新增這些權限[Google Play 將會自動篩選掉裝置](http://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features)而不需要特定的硬體。 您可以將下列內容新增至您在您的 Android 專案的 AssemblyInfo.cs 檔案，取得解決這個問題：

```csharp
[assembly: UsesFeature("android.hardware.camera", Required = false)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = false)]
```

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要其他設定。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

不需要其他設定。

-----

## <a name="using-flashlight"></a>使用手電筒

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

手電筒可以開啟或關閉透過`TurnOnAsync`和`TurnOffAsync`方法：

```csharp
try
{
    // Turn On
    await Flashlight.TurnOnAsync();

    // Turn Off
    await Flashlight.TurnOffAsync();
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to turn on/off flashlight
}
```

## <a name="platform-implementation-specifics"></a>平台實作的特性

### <a name="androidtabandroid"></a>[Android](#tab/android)

手電筒類別已最佳化並根據裝置的作業系統。

#### <a name="api-level-23-and-higher"></a>API 層級 23 和更新版本

在較新的 API 層級[Torch 模式](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode)將用來開啟或關閉裝置 flash 單位。

#### <a name="api-level-22-and-lower"></a>API 層級 22 和較低

若要開啟或關閉建立相機表面的材質`FlashMode`相機單位。 

### <a name="iostabios"></a>[iOS](#tab/ios)

[AVCaptureDevice](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureDevice/)用來開啟和關閉 Torch 和裝置的模式。

### <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[Lamp](https://docs.microsoft.com/en-us/uwp/api/windows.devices.lights.lamp)可用來偵測第一個 lamp，若要開啟或關閉裝置的背面。

-----

## <a name="api"></a>API

- [手電筒原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Flashlight)
- [手電筒 API 文件](xref:Xamarin.Essentials.Flashlight)
