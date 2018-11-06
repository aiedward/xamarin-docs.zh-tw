---
title: Xamarin.Essentials：應用程式資訊
description: 本文件描述 Xamarin.Essentials 中的 AppInfo 類別，可提供應用程式的相關資訊。 例如，會公開應用程式名稱和版本。
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 00419fb746609464b49be343938905614c59ab29
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675129"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials：應用程式資訊

![發行前的 NuGet](~/media/shared/pre-release.png)

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
AppInfo.OpenSettings();
```

此設定頁面可讓使用者變更應用程式權限，並執行其他平台特定的工作。

## <a name="api"></a>API

- [AppInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [AppInfo API 文件](xref:Xamarin.Essentials.AppInfo)
