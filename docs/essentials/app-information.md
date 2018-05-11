---
title: Xamarin.Essentials 應用程式資訊
description: AppInfo 類別提供您的應用程式的相關資訊。
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 32e3eb8fab719540e4c9ffec4e57f5510c10e3f5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
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

- [AppInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Essentials/AppInfo)
- [AppInfo API 文件](xref:Xamarin.Essentials.AppInfo)
