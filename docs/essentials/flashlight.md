---
title: Xamarin.Essentials 手電筒
description: 手電筒類別有開啟或關閉裝置的相機 flash 變成手電筒的能力。
ms.assetid: 06A03553-D212-43A2-9E6E-C2D2D93EB136
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3d867d742314f2677eeab35bbc354f696c99bf75
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-flashlight"></a>Xamarin.Essentials 手電筒

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**手電筒**類別具有開啟或關閉裝置的相機 flash 變成手電筒的能力。

## <a name="getting-started"></a>快速入門

若要存取**手電筒**還需要下列平台的特定安裝程式的功能。

# <a name="androidtabandroid"></a>[Android](#tab/android)

手電筒和數位相機的權限所需，而且必須設定的 Android 專案中。 這可以透過下列方式加入：

開啟**AssemblyInfo.cs**底下**屬性**資料夾並加入：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

或更新 Android 資訊清單：

開啟**AndroidManifest.xml**底下**屬性**資料夾，然後將下列內部**資訊清單**節點。

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

或 Anroid 專案上按一下滑鼠右鍵，然後開啟專案的屬性。 在下**Android 資訊清單**尋找**必要的權限：**區域，然後核取**手電筒**和**相機**權限。 這會自動更新**AndroidManifest.xml**檔案。

加入這些權限[Google Play 將會自動篩選掉裝置](http://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features)沒有特定的硬體。 您可以取得解決這個問題，您在您的 Android 專案的 AssemblyInfo.cs 檔案中加入下列：

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

在您類別中加入 Xamarin.Essentials 的參考：

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

### <a name="androidtabandroid-specifics"></a>[Android](#tab/android-specifics)

手電筒類別已被 optmized 根據裝置的作業系統。

#### <a name="api-level-23-and-higher"></a>應用程式開發介面層級 23 及更高版本

在較新的應用程式開發介面層級[燒模式](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode)將用來開啟或關閉快閃裝置單位。

#### <a name="api-level-22-and-lower"></a>應用程式開發介面層級 22 和較低

相機表面的材質是用來開啟或關閉`FlashMode`相機單位。 

### <a name="iostabios-specifics"></a>[iOS](#tab/ios-specifics)

[AVCaptureDevice](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureDevice/)用來開啟和關閉攜帶和裝置的模式。

### <a name="uwptabuwp-specifics"></a>[UWP](#tab/uwp-specifics)

[Lamp](https://docs.microsoft.com/en-us/uwp/api/windows.devices.lights.lamp)用來偵測來開啟或關閉裝置背面的第一個燈。

-----

## <a name="api"></a>API

- [手電筒原始程式碼](https://github.com/xamarin/Essentials/tree/master/Essentials/Flashlight)
- [手電筒 API 文件](xref:Xamarin.Essentials.Flashlight)
