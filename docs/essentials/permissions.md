---
title: Xamarin.Essentials:權限
description: 此文件描述 Xamarin.Essentials 中的 Permissions 類別，其能提供檢查及要求執行階段權限的能力。
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 2b1413c6bce7fc4ce43a190cd25494c69dbadedb
ms.sourcegitcommit: ec62c7f28abc8e121656f1b93146657d90a4cab4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77555686"
---
# <a name="xamarinessentials-permissions"></a>Xamarin.Essentials:權限

**Permissions** 類別能提供檢查及要求執行階段權限的能力。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-permissions"></a>使用權限

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>檢查權限

若要檢查某個權限的目前狀態，請搭配特定的權限使用 `CheckStatusAsync` 方法來取得其狀態。

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

如果所需的權限未宣告，便會擲回 `PermissionException`。

## <a name="requesting-permissions"></a>要求權限

若要從使用者要求權限，請搭配特定的權限使用 `RequestAsync` 方法來要求。 如果使用者先前已授與權限且尚未加以撤銷，此方法將會立即傳回 `Granted` 且不會顯示對話方塊。 

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

如果所需的權限未宣告，便會擲回 `PermissionException`。 

請注意，在某些平台上，權限要求只能單次啟動。 開發人員必須處理進一步的提示，以檢查權限是否處於 `Denied` 狀態，並要求使用者手動加以開啟。

## <a name="permission-status"></a>權限狀態

使用 `CheckStatusAsync` 或 `RequestAsync` 時，系統將會傳回 `PermissionStatus` 以用來判斷下一個步驟。

* Unknown：權限處於未知的狀態
* Denied：使用者已拒絕權限要求
* Disabled：裝置上已停用該功能
* Granted：使用者已授與權限，或是系統已自動授與權限
* Restricted：處於受限制的狀態

## <a name="available-permissions"></a>可用的權限

Xamarin.Essentials 會盡量嘗試將大部分的權限抽象化，但每個作業系統都有一組不同的執行階段權限。 此外，在針對某些權限提供單一 API 的能力上也有所差異。 以下是目前可用權限的指南：

圖示指南：

* ![完整支援](~/media/shared/yes.png "完整支援")：已支援
* ![不支援](~/media/shared/no.png "不支援或不需要")：不支援/不需要

| 權限 | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: 
| CalendarRead   | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| CalendarWrite | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 觀景窗 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") |
| ContactsRead | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| ContactsWrite | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 手電筒 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![不支援 iOS](~/media/shared/no.png "不支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") |
| LocationWhenInUse | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS")  | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") |
| LocationAlways | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") |
| 媒體 | ![不支援 Android](~/media/shared/no.png "不支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 麥克風 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![支援 Tizen](~/media/shared/yes.png "支援 Tizen") |
| Phone | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 相片 | ![不支援 Android](~/media/shared/no.png "不支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| Reminders | ![不支援 Android](~/media/shared/no.png "不支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| Sensors | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| Sms | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 語音 | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| StorageRead | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![不支援 iOS](~/media/shared/no.png "不支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| StorageWrite | ![支援 Android](~/media/shared/yes.png "支援 Android") | ![不支援 iOS](~/media/shared/no.png "不支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援 watchOS](~/media/shared/no.png "不支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |

如果權限是以 ❌ 標記，對其進行檢查或要求時，將一律會傳回 `Granted`。

## <a name="general-usage"></a>一般使用方式
以下是處理權限的一般使用模式。

```csharp
public async Task<PermissionStatus> CheckAndRequestPermissionAsync<TPermission>()
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

每個權限類型都可以建立其自己的執行個體，以用來直接呼叫方法。

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

## <a name="extending-permissions"></a>延伸權限

Permissions API 的設計使其能針對需要未包含在 Xamarin.Essentials 中之額外驗證或權限的應用程式提供彈性及延伸性。 建立繼承自 `BasePermission` 的新類別，並實作必要的抽象方法。 然後 

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

在特定的平台上實作權限時，便可以繼承自 `BasePlatformPermission` 類別。 這能提供額外的平台協助程式方法以自動檢查宣告。

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="android"></a>[Android](#tab/android)

權限必須擁有在 Android 資訊清單檔中設定的相符屬性。

請在 [Xamarin.Android 中的權限](https://docs.microsoft.com/xamarin/android/app-fundamentals/permissions) \(英文\) 文件中深入閱讀。

# <a name="ios"></a>[iOS](#tab/ios)

權限在 `Info.plist` 檔案中必須擁有相符字串。 在要求權限並被拒絕之後，如果您再次要求該權限，將不會出現快顯。 您必須提示使用者手動調整 iOS 中應用程式設定畫面上的設定。

請在 [iOS 安全性和隱私權功能](https://docs.microsoft.com/xamarin/ios/app-fundamentals/security-privacy) \(部分機器翻譯\) 文件中深入閱讀。

# <a name="uwp"></a>[UWP](#tab/uwp)

權限必須擁有在套件資訊清單中宣告的相符功能。

請在[應用程式功能宣告](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations) \(部分機器翻譯\) 文件中深入閱讀。

--------------

## <a name="api"></a>API

- [Permissions 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Permissions) \(英文\)
- [Permissions API 文件](xref:Xamarin.Essentials.Permissions)

