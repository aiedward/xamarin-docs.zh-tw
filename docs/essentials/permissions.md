---
title: Xamarin.Essentials：許可權
description: 本檔說明中的許可權類別 Xamarin.Essentials ，其提供檢查及要求執行時間許可權的能力。
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57
author: jamesmontemagno
ms.author: jamont
ms.custom: video
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 01902942c750a3cd278d648fa82499af4c5d3ab6
ms.sourcegitcommit: dac04cec56290fb19034f3e135708f6966a8f035
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92169965"
---
# <a name="no-locxamarinessentials-permissions"></a>Xamarin.Essentials：許可權

**許可權**類別提供檢查及要求執行時間許可權的能力。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

[!include[](~/essentials/includes/android-permissions.md)]

## <a name="using-permissions"></a>使用許可權

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>檢查權限

若要檢查許可權的目前狀態，請使用 `CheckStatusAsync` 方法以及特定的許可權來取得的狀態。

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

`PermissionException`如果未宣告必要的許可權，就會擲回。

建議您最好先檢查許可權的狀態，再要求它。 如果未曾提示使用者，每個作業系統都會傳回不同的預設狀態。 iOS 會傳回 `Unknown` ，而其他則會返回 `Denied` 。 如果狀態為，則不 `Granted` 需要進行其他呼叫。 在 iOS 上，如果狀態是 `Denied` ，您應該提示使用者變更設定中的許可權，而在 Android 上，您可以呼叫 `ShouldShowRationale` 來偵測使用者過去是否已拒絕許可權。

## <a name="requesting-permissions"></a>要求權限

若要向使用者要求許可權，請使用 `RequestAsync` 方法以及要求的特定許可權。 如果使用者先前已授與許可權，但尚未撤銷，則這個方法會 `Granted` 立即傳回，而不會顯示對話方塊。

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

`PermissionException`如果未宣告必要的許可權，就會擲回。

請注意，在某些平臺上，只有一次只能啟用許可權要求。 開發人員必須處理進一步的提示，以檢查許可權是否處於 `Denied` 狀態，並要求使用者手動將其開啟。 

## <a name="permission-status"></a>許可權狀態

使用 `CheckStatusAsync` 或時 `RequestAsync` `PermissionStatus` ，將會傳回，可用來判斷後續步驟：

* 未知-許可權處於未知狀態
* 拒絕-使用者已拒絕許可權要求
* 已停用-裝置上的功能已停用
* 已授與-使用者授與的許可權或自動授與
* 受限制-處於受限的狀態


## <a name="explain-why-permission-is-needed"></a>說明為何需要許可權

![發行前 API](~/media/shared/preview.png)

最好的作法是解釋您的應用程式需要特定許可權的原因。 在 iOS 上，您必須指定向使用者顯示的字串。 Android 沒有這個功能，而且也預設的許可權狀態為停用。 這會限制知道使用者是否已拒絕許可權或第一次提示使用者的能力。 `ShouldShowRationale`方法可以用來判斷是否應該顯示教育使用者介面。 如果方法傳回， `true` 則是因為使用者已拒絕或停用過去的許可權。 `false`當呼叫這個方法時，其他平臺一律會傳回。

## <a name="available-permissions"></a>可用的許可權

Xamarin.Essentials 嘗試盡可能抽象化最多許可權。 不過，每個作業系統都有一組不同的執行時間許可權。 此外，為某些許可權提供單一 API 時，會有一些差異。 以下是目前可用許可權的指南：

圖示指南：

* 支援![完整支援](~/media/shared/yes.png "完整支援")
* ![不支援](~/media/shared/no.png "不支援或不需要") -不支援/需要

| 權限 | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---:
| CalendarRead   | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| CalendarWrite | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 相機 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") |
| ContactsRead | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| ContactsWrite | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| Flashlight | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![不支援 iOS](~/media/shared/no.png "不支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") |
| LocationWhenInUse | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS")  | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") |
| LocationAlways | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") |
| 媒體 | ![不支援 Android](~/media/shared/no.png "不支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 麥克風 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") |
| 電話 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 相片 | ![不支援 Android](~/media/shared/no.png "不支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 提醒事項 | ![不支援 Android](~/media/shared/no.png "不支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 感應器 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 短信 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 語音 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| StorageRead | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![不支援 iOS](~/media/shared/no.png "不支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| StorageWrite | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![不支援 iOS](~/media/shared/no.png "不支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |

如果許可權被標示為 ![不受支援](~/media/shared/no.png "不支援") ，則會 `Granted` 在選取或要求時一律傳回。

## <a name="general-usage"></a>一般使用方式
以下是處理許可權的一般使用模式。

```csharp
public async Task<PermissionStatus> CheckAndRequestLocationPermission()
{
    var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
    
    if (status == PermissionStatus.Granted)
        return status;
        
    
    if (status == PermissionStatus.Denied && DeviceInfo.Platform == DevicePlatform.iOS)
    {
        // Prompt the user to turn on in settings
        // On iOS once a permission has been denied it may not be requested again from the application
        return status;
    }
    
    if (Permissions.ShouldShowRationale<Permissions.LocationWhenInUse>())
    {
        // Prompt the user with additional information as to why the permission is needed
    }   

    status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();

    return status;
}
```

每個許可權類型都可以有它所建立的實例，可直接呼叫方法。

```csharp
public async Task GetLocationAsync()
{
    var status = await CheckAndRequestPermissionAsync(new Permissions.LocationWhenInUse());
    if (status != PermissionStatus.Granted)
    {
        // Notify user permission was denied
        return;
    }

    var location = await Geolocation.GetLocationAsync();
}

public async Task<PermissionStatus> CheckAndRequestPermissionAsync<T>(T permission)
            where T : BasePermission
{
    var status = await permission.CheckStatusAsync();
    if (status != PermissionStatus.Granted)
    {
        status = await permission.RequestAsync();
    }

    return status;
}
```

## <a name="extending-permissions"></a>擴充許可權

針對需要額外驗證的應用程式或不包含在中的許可權，建立了許可權 API 來提供彈性且可擴充 Xamarin.Essentials 。 建立繼承自的新類別， `BasePermission` 並執行所需的抽象方法。

```csharp
public class MyPermission : BasePermission
{
    // This method checks if current status of the permission
    public override Task<PermissionStatus> CheckStatusAsync()
    {
        throw new System.NotImplementedException();
    }

    // This method is optional and a PermissionException is often thrown if a permission is not declared
    public override void EnsureDeclared()
    {
        throw new System.NotImplementedException();
    }

    // Requests the user to accept or deny a permission
    public override Task<PermissionStatus> RequestAsync()
    {
        throw new System.NotImplementedException();
    }
}
```

在特定平臺中執行許可權時， `BasePlatformPermission` 可以從繼承類別。 這會提供額外的平臺 helper 方法來自動檢查宣告。 這有助於建立可分組的自訂許可權。 例如，您可以使用下列自訂許可權，在 Android 上要求儲存體的讀取和寫入存取權。

```csharp
public class ReadWriteStoragePermission : Xamarin.Essentials.Permissions.BasePlatformPermission
{
    public override (string androidPermission, bool isRuntime)[] RequiredPermissions => new List<(string androidPermission, bool isRuntime)>
    {
        (Android.Manifest.Permission.ReadExternalStorage, true),
        (Android.Manifest.Permission.WriteExternalStorage, true)
    }.ToArray();
}
```

然後，您可以從 Android 專案呼叫新的許可權。

```csharp
await Permissions.RequestAsync<ReadWriteStoragePermission>();
```

如果您想要從共用程式碼呼叫此 API，您可以建立介面，並使用相依性 [服務](../xamarin-forms/app-fundamentals/dependency-service/index.md) 來註冊並取得執行。

```csharp
public interface IReadWritePermission
{        
    Task<PermissionStatus> CheckStatusAsync();
    Task<PermissionStatus> RequestAsync();
}
```

然後在您的平臺專案中執行介面：

```csharp
public class ReadWriteStoragePermission : Xamarin.Essentials.Permissions.BasePlatformPermission, IReadWritePermission
{
    public override (string androidPermission, bool isRuntime)[] RequiredPermissions => new List<(string androidPermission, bool isRuntime)>
    {
        (Android.Manifest.Permission.ReadExternalStorage, true),
        (Android.Manifest.Permission.WriteExternalStorage, true)
    }.ToArray();
}
```

然後您可以註冊特定的實作為：

```csharp
DependencyService.Register<IReadWritePermission, ReadWriteStoragePermission>();
```
然後，您可以從共用的專案解析並使用它：

```csharp
var readWritePermission = DependencyService.Get<IReadWritePermission>();
var status = await readWritePermission.CheckStatusAsync();
if (status != PermissionStatus.Granted)
{
    status = await readWritePermission.RequestAsync();
}
```

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="android"></a>[Android](#tab/android)

許可權必須在 Android 資訊清單檔中設定相符的屬性。 許可權狀態的預設值為 [拒絕]。

在 [Xamarin. Android 檔中](../android/app-fundamentals/permissions.md) 深入瞭解許可權。

# <a name="ios"></a>[iOS](#tab/ios)

許可權在檔案中必須有相符的字串 `Info.plist` 。 要求並拒絕許可權之後，如果您第二次要求許可權，就不會再出現快顯視窗。 您必須提示使用者手動調整 iOS 中 [應用程式設定] 畫面的設定。 許可權狀態的預設值為 Unknown。

深入瞭解 [IOS 安全性和隱私權功能](../ios/app-fundamentals/security-privacy.md) 檔。

# <a name="uwp"></a>[UWP](#tab/uwp)

許可權必須具有在封裝資訊清單中宣告的相符功能。 在大部分的情況中，許可權狀態的預設值為 Unknown。

深入瞭解 [應用程式功能聲明](/windows/uwp/packaging/app-capability-declarations) 檔。

--------------

## <a name="api"></a>API

- [許可權來源程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Permissions)
- [許可權 API 檔](xref:Xamarin.Essentials.Permissions)


## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Permissions-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
