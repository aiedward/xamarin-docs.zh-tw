---
title: Xamarin.要點:權限
description: 本文件介紹 Xamarin.Essentials 中的許可權類,它提供了檢查和請求運行時許可權的功能。
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 21f2079ace4adae6fd84d89426e5d66692af2a0a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78289799"
---
# <a name="xamarinessentials-permissions"></a>Xamarin.要點:權限

**許可權**類提供檢查和請求運行時許可權的功能。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-permissions"></a>使用權限

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>檢查權限

要檢查權限的目前狀態`CheckStatusAsync`, 請使用方法以及特定權限來取得 的狀態。

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

如果未`PermissionException`聲明所需的許可權,則引發 A。

## <a name="requesting-permissions"></a>要求權限

要向使用者請求許可權,請使用該方法`RequestAsync`以及請求的特定許可權。 如果使用者以前授予的許可權並且尚未撤銷許可權,則此方法將立即返回`Granted`,並且不顯示對話方塊。 

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

如果未`PermissionException`聲明所需的許可權,則引發 A。 

請注意,在某些平臺上,許可權請求只能一次啟動。 開發人員必須處理進一步的提示,以檢查穿孔是否處於`Denied`狀態,並要求用戶手動打開它。

## <a name="permission-status"></a>權限狀態

使用`CheckStatusAsync`時`RequestAsync`會`PermissionStatus`傳回 用於確定後續步驟的 或傳回 。

* 未知 - 權限處未知狀態
* 拒絕 - 使用者拒絕權限要求
* 關閉 - 裝置中關閉此功能
* 給 - 使用者權限或自動設定
* 受限 - 處於受限狀態

## <a name="available-permissions"></a>可用權限

Xamarin.Essentials 嘗試抽象盡可能多的許可權,但是每個作業系統都有一組不同的運行時許可權。 此外,在為某些許可權提供單個 API 方面存在差異。 以下是目前可用權限的指南:

圖示指南：

* ![完全支援](~/media/shared/yes.png "完整支援")- 支援
* ![不支援](~/media/shared/no.png "不支援或不需要")- 不支援/ 必要

| 權限 | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: 
| 行事曆閱讀   | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 行事曆書寫 | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 相機 | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") |
| 連絡人閱讀 | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 連絡人寫作 | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 手電筒 | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![不支援 iOS](~/media/shared/no.png "不支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") |
| 位置時間使用 | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS")  | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") |
| 位置始終 | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") |
| 媒體 | ![不支援安卓](~/media/shared/no.png "不支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 麥克風 | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![Tizen 支援](~/media/shared/yes.png "Tizen 支援") |
| 電話 | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 相片 | ![不支援安卓](~/media/shared/no.png "不支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![支援 tvOS](~/media/shared/yes.png "支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 提醒事項 | ![不支援安卓](~/media/shared/no.png "不支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 感應器 | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![支援 UWP](~/media/shared/yes.png "支援 UWP") | ![支援 watchOS](~/media/shared/yes.png "支援 watchOS") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 短信 | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 語音 | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![支援 iOS](~/media/shared/yes.png "支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 儲存讀取 | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![不支援 iOS](~/media/shared/no.png "不支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |
| 儲存寫入 | ![支援安卓](~/media/shared/yes.png "支援安卓") | ![不支援 iOS](~/media/shared/no.png "不支援 iOS") | ![不支援 UWP](~/media/shared/no.png "不支援 UWP") | ![不支援手錶作業系統](~/media/shared/no.png "不支援手錶作業系統") | ![不支援 tvOS](~/media/shared/no.png "不支援 tvOS") | ![不支援 Tizen](~/media/shared/no.png "不支援 Tizen") |

如果許可權標記為![不受支援](~/media/shared/no.png "不支援"),則始終在選中`Granted`或請求 時返回。

## <a name="general-usage"></a>一般使用方式
下面是處理許可權的一般用法。

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

每個許可權類型都可以創建一個實例,可以直接調用方法。

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

權限 API 的建立對於需要其他驗證或權限的應用程式具有靈活性和可擴充性,這些驗證或權限不包括在 Xamarin. 創建一個新類,該類繼承`BasePermission`並實現所需的抽象方法。 結果為 

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

在特定平臺中實現許可權時,可以從繼承`BasePlatformPermission`類。 這提供了自動檢查聲明的其他平台説明程式方法。

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="android"></a>[Android](#tab/android)

許可權必須在 Android 清單檔中設置匹配屬性。

閱讀有關[Xamarin.Android](https://docs.microsoft.com/xamarin/android/app-fundamentals/permissions)文檔中的許可權的更多內容。

# <a name="ios"></a>[iOS](#tab/ios)

權限必須在檔中具有匹配`Info.plist`的字串。 請求並拒絕許可權後,如果您再次請求該許可權,則不會再出現彈出視窗。 您必須提示使用者在 iOS 中的應用程式設置螢幕中手動調整設置。

閱讀有關[iOS 安全和隱私功能](https://docs.microsoft.com/xamarin/ios/app-fundamentals/security-privacy)文檔的更多資訊。

# <a name="uwp"></a>[UWP](#tab/uwp)

許可權必須具有在包清單中聲明的匹配功能。

閱讀有關[應用功能聲明](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations)文檔的更多內容。

--------------

## <a name="api"></a>API

- [權限碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Permissions)
- [權限 API 文件](xref:Xamarin.Essentials.Permissions)

