---
title: Xamarin.Essentials 應用程式資訊
description: AppInfo 類別提供您的應用程式的相關資訊。
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 4591f1256dc574299bb573ef9ec5afb35af7c542
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials 應用程式資訊

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**AppInfo**類別會提供應用程式的相關資訊。

## <a name="using-appinfo"></a>使用應用程式資訊

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

透過應用程式開發介面公開下列資訊：

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

## <a name="api"></a>API

- [AppInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [AppInfo API 文件](xref:Xamarin.Essentials.AppInfo)
