---
title: Xamarin.Essentials：應用程式資訊
description: 本文件描述 Xamarin.Essentials 中的 AppInfo 類別，可提供應用程式的相關資訊。 例如，會公開應用程式名稱和版本。
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 3e67b605e485b724ec11f2ac94dcf3d1aa77d5cf
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057289"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials：應用程式資訊

**AppInfo** 類別會提供您應用程式的相關資訊。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-appinfo"></a>使用 AppInfo

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>取得應用程式資訊：

下列資訊會透過 API 公開：

```csharp
// Application Name
var appName = AppInfo.Name;

// Package Name/Application Identifier (com.microsoft.testapp)
var packageName = AppInfo.PackageName;

// Application Version (1.0.0)
var version = AppInfo.VersionString;

// Application Build Number (1)
var build = AppInfo.BuildString;
```

## <a name="displaying-application-settings"></a>顯示應用程式設定

**AppInfo** 類別也可以顯示作業系統為應用程式維護的設定頁面：

```csharp
// Display settings page
AppInfo.ShowSettingsUI();
```

此設定頁面可讓使用者變更應用程式權限，並執行其他平台特定的工作。

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

應用程式資訊會依據下列欄位，從 `AndroidManifest.xml` 中取得：

- **組建** - `manifest` 節點中的 `android:versionCode`
- **名稱** - `application` 節點中的 `android:label`
- **套件名稱**：`manifest` 節點中的 `package`
- **VersionString** - `application` 節點中的 `android:versionName`

# <a name="iostabios"></a>[iOS](#tab/ios)

應用程式資訊會依據下列欄位，從 `Info.plist` 中取得：

- **組建** - `CFBundleVersion`
- **名稱** - 如有設定，即為 `CFBundleDisplayName`；否則為 `CFBundleName`
- **套件名稱**：`CFBundleIdentifier`
- **VersionString** - `CFBundleShortVersionString`

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

應用程式資訊會依據下列欄位，從 `Package.appxmanifest` 中取得：

- **組建** - 使用 `Identity` 節點上 `Version` 中的 `Build`
- **名稱** - `Properties` 節點上的 `DisplayName`
- **PackageName**：`Identity` 節點上的 `Name`
- **VersionString** - `Identity` 節點上的 `Version`


--------------

## <a name="api"></a>API

- [AppInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [AppInfo API 文件](xref:Xamarin.Essentials.AppInfo)
