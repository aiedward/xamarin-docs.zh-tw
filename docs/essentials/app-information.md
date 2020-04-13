---
title: Xamarin.Essentials：應用程式資訊
description: 本文件描述 Xamarin.Essentials 中的 AppInfo 類別，可提供應用程式的相關資訊。 例如，會公開應用程式名稱和版本。
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 01/29/2019
ms.custom: video
ms.openlocfilehash: 69d0cb503d329ccfb4c29fb6cc4a589bef97e893
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70756984"
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

# <a name="android"></a>[Android](#tab/android)

應用程式資訊會依據下列欄位，從 `AndroidManifest.xml` 中取得：

- **在**節點`android:versionCode`中`manifest`產生 =
- **Name** - 節點`android:label`中`application`的名稱
- **套件名稱**`package``manifest`: 在節點中
- **版本String** `android:versionName` `application`= 在節點中

# <a name="ios"></a>[iOS](#tab/ios)

應用程式資訊會依據下列欄位，從 `Info.plist` 中取得：

- **生成**|`CFBundleVersion`
- **Name**名稱`CFBundleDisplayName`(如果已設定), - 否則`CFBundleName`
- **套件名稱**:`CFBundleIdentifier`
- **版本字串**|`CFBundleShortVersionString`

# <a name="uwp"></a>[UWP](#tab/uwp)

應用程式資訊會依據下列欄位，從 `Package.appxmanifest` 中取得：

- **組建** - 使用 `Identity` 節點上 `Version` 中的 `Build`
- **Name**節點上`Properties`的名稱 - `DisplayName`
- **套件名稱**`Name``Identity`: 在節點上
- **版本字串**`Version`=`Identity`節點上

--------------

## <a name="api"></a>API

- [AppInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [AppInfo API 文件](xref:Xamarin.Essentials.AppInfo)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/App-Information-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
