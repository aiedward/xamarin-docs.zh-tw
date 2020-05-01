---
title: Xamarin. Essentials：許可權
description: 本檔說明 Xamarin 中的許可權類別，其可提供檢查和要求執行時間許可權的功能。
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 3d61267ae78a4b84907a2bcf6e944eb286b113dd
ms.sourcegitcommit: 8b94b2af2ac69e4a60e210ddc764f4d276c8d88d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82605442"
---
# <a name="xamarinessentials-permissions"></a>Xamarin. Essentials：許可權

**許可權**類別提供檢查和要求執行時間許可權的功能。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-permissions"></a>使用許可權

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>檢查權限

若要檢查許可權的目前狀態，請使用`CheckStatusAsync`方法以及特定許可權，以取得的狀態。

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

如果`PermissionException`未宣告所需的許可權，就會擲回。

在要求許可權之前，最好先檢查其狀態。 如果使用者從未收到提示，則每個作業系統都會傳回不同的預設狀態。 iOS 會`Unknown`傳回，而其他`Denied`則會返回。

## <a name="requesting-permissions"></a>要求權限

若要向使用者要求許可權，請使用`RequestAsync`方法以及要求的特定許可權。 如果使用者先前已授與許可權，但尚未撤銷，則此方法會傳回`Granted` immediatelly，而不會顯示對話方塊。 

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

如果`PermissionException`未宣告所需的許可權，就會擲回。 

請注意，在某些平臺上，許可權要求只能在單一時間啟用。 開發人員必須處理進一步的提示，以檢查許可權是否處於`Denied`狀態，並要求使用者以手動方式開啟它。

## <a name="permission-status"></a>許可權狀態

使用`CheckStatusAsync`或`RequestAsync`時， `PermissionStatus`將會傳回用來決定後續步驟的。

* 不明-許可權處於不明狀態
* 已拒絕-使用者已拒絕許可權要求
* 已停用-已停用裝置上的功能
* 已授與-使用者授與的許可權或已自動授與
* 受限制-處於保留狀態

## <a name="available-permissions"></a>可用的許可權

Xamarin 會嘗試盡可能抽象化最多的許可權，但每個作業系統都有一組不同的執行時間許可權。 此外，針對某些許可權提供單一 API 的差異也有所不同。 以下是目前可用許可權的指南：

圖示指南：

* ![完整支援](~/media/shared/yes.png "完整支援")-支援
* ![不支援](~/media/shared/no.png "不支援或不需要")-不支援/不需要

| 權限 | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: 
| CalendarRead   | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| CalendarWrite | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 相機 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| ContactsRead | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| ContactsWrite | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 手電筒 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![不支援 iOS](~/media/shared/no.png "不支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| LocationWhenInUse | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS")  | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| LocationAlways | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| 媒體 | ![不支援 Android](~/media/shared/no.png "不支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 麥克風 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援的 Tizen](~/media/shared/yes.png "支援的 Tizen") |
| 電話 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 相片 | ![不支援 Android](~/media/shared/no.png "不支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![支援的 tvOS](~/media/shared/yes.png "支援的 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 提醒事項 | ![不支援 Android](~/media/shared/no.png "不支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 感應器 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援的 watchOS](~/media/shared/yes.png "支援的 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| Sms | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 語音 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援的 iOS](~/media/shared/yes.png "支援的 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| StorageRead | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![不支援 iOS](~/media/shared/no.png "不支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| StorageWrite | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![不支援 iOS](~/media/shared/no.png "不支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |

如果許可權標記為![不支援](~/media/shared/no.png "不支援")，則會`Granted`在核取或要求時一律傳回。

## <a name="general-usage"></a>一般使用方式
以下是用來處理許可權的一般使用模式。

```csharp
public async Task<PermissionStatus> CheckAndRequestLocationPermission()
{
    var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
    if (status != PermissionStatus.Granted)
    {
        status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
    }

    // Additionally could prompt the user to turn on in settings

    return status;
}
```

每個許可權類型都可以擁有它所建立的實例，以便可以直接呼叫方法。

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

針對需要不包含在 Xamarin 中的其他驗證或許可權的應用程式，已建立許可權 API 以提供彈性且可擴充。 建立繼承自`BasePermission`的新類別，並執行所需的抽象方法。 結果為 

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

在特定平臺中執行許可權時，可以從`BasePlatformPermission`繼承類別。 這會提供額外的平臺協助程式方法，以自動檢查宣告。

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="android"></a>[Android](#tab/android)

許可權必須在 Android 資訊清單檔案中設定相符的屬性。

如需詳細資訊，請參閱在[Xamarin 檔中的許可權](https://docs.microsoft.com/xamarin/android/app-fundamentals/permissions)。

# <a name="ios"></a>[iOS](#tab/ios)

許可權在檔案中`Info.plist`必須有相符的字串。 要求和拒絕許可權後，如果您第二次要求許可權，就不會再出現快顯。 您必須提示使用者手動調整 iOS 中 [應用程式設定] 畫面中的設定。

如需詳細資訊，請參閱[IOS 安全性和隱私權功能](https://docs.microsoft.com/xamarin/ios/app-fundamentals/security-privacy)檔。

# <a name="uwp"></a>[UWP](#tab/uwp)

許可權必須具有在封裝資訊清單中宣告的相符功能。

如需詳細資訊，請參閱[應用程式功能聲明](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations)檔。

--------------

## <a name="api"></a>API

- [許可權原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Permissions)
- [許可權 API 檔](xref:Xamarin.Essentials.Permissions)

