---
title： " Xamarin.Essentials ：閃光燈" 描述： "本檔描述中的閃光燈類別 Xamarin.Essentials ，它能夠開啟或關閉裝置的相機閃光燈，使其變成閃光燈。」
assetid： 06A03553-D212-43A2-9E6E-C2D2D93EB136 author： jamesmontemagno ms. custom： video ms. 作者： jamont ms. date： 11/04/2018 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinessentials-flashlight"></a>Xamarin.Essentials：上個

**手電筒**類別可以開啟或關閉裝置相機閃光燈，將其變為手電筒。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取**手電筒**功能，需要下列平台特定設定。

# <a name="android"></a>[Android](#tab/android)

需要手電筒和相機的權限，並且必須在 Android 專案中設定。 能以下列方式新增：

開啟 [Properties]**** 資料夾下的 **AssemblyInfo.cs** 檔案並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

或更新 Android 資訊清單：

開啟**Properties**資料夾底下的**androidmanifest.xml** ，並在**資訊清單**節點內新增下列內容。

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

禍以滑鼠右鍵按一 Android 專案並開啟專案的屬性。 在 [Android 資訊清單]**** 下，尋找 [必要權限]**** 區域並選取 [手電筒]**** 和 [相機]**** 權限。 這將會自動更新 **AndroidManifest.xml** 檔案。

藉由新增這些權限，[Google Play 會自動篩選掉裝置](https://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features)，不含特定硬體。 您可以透過將下列內容新增至 Android 專案中的 AssemblyInfo.cs 檔案來解決此問題：

```csharp
[assembly: UsesFeature("android.hardware.camera", Required = false)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = false)]
```

[!include[](~/essentials/includes/android-permissions.md)]

# <a name="ios"></a>[iOS](#tab/ios)

不需要進行額外設定。

# <a name="uwp"></a>[UWP](#tab/uwp)

不需要進行額外設定。

-----

## <a name="using-flashlight"></a>使用手電筒

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

手電筒可以透過 `TurnOnAsync` 和 `TurnOffAsync` 方法來開啟或關閉：

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

## <a name="platform-implementation-specifics"></a>平台實作特性

### <a name="android"></a>[Android](#tab/android)

手電筒類別已根據裝置的作業系統進行最佳化。

#### <a name="api-level-23-and-higher"></a>API 層級 23 與更高版本

在較新的 API 層級上，[手電筒模式](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode)將用於開啟或關閉裝置的閃光燈單位。

#### <a name="api-level-22-and-lower"></a>API 層級22 與更低版本

會建立相機表面紋理，以開啟或關閉相機單位的 `FlashMode`。

### <a name="ios"></a>[iOS](#tab/ios)

[AVCaptureDevice](xref:AVFoundation.AVCaptureDevice) 用於開啟或關閉裝置的手電筒或閃光燈模式。

### <a name="uwp"></a>[UWP](#tab/uwp)

[Lamp](https://docs.microsoft.com/uwp/api/windows.devices.lights.lamp) 用於偵測裝置背面的第一個燈是否開啟或關閉。

-----

## <a name="api"></a>API

- [Flashlight 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Flashlight)
- [Flashlight API 文件](xref:Xamarin.Essentials.Flashlight)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Flashlight-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
