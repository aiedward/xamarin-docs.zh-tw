---
title: Xamarin.Essentials： 應用程式資訊
description: 本文件說明在 Xamarin.Essentials，提供您的應用程式的相關資訊的應用程式資訊類別。 比方說，它會公開的應用程式名稱和版本。
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 7e79b3003f41b8de22950624e44e8c9e0e7e7e31
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831503"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials： 應用程式資訊

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**AppInfo**類別會提供您的應用程式的相關資訊。

## <a name="using-appinfo"></a>使用應用程式資訊

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>取得應用程式資訊：

透過 API 公開下列資訊：

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

**AppInfo**類別也可以顯示的應用程式的作業系統所維護的設定頁面：

```csharp
// Display settings page
AppInfo.OpenSettings();
```

此設定 頁面可讓使用者變更應用程式權限，並執行其他平台特定工作。

## <a name="api"></a>API

- [AppInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [AppInfo API 文件](xref:Xamarin.Essentials.AppInfo)
