---
title: Xamarin.Essentials：版本追蹤
description: Xamarin.Essentials 中的 VersionTracking 類別可讓您檢查應用程式版本和組建編號並查看其他資訊 (例如應用程式是否為第一次啟動，或針對目前版本取得先前的組建資訊等)。
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 1eae0bf7c21dd7efa7655633896bdb2897f9d782
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674856"
---
# <a name="xamarinessentials-version-tracking"></a>Xamarin.Essentials：版本追蹤

![發行前的 NuGet](~/media/shared/pre-release.png)

**VersionTracking** 類別可讓您檢查應用程式版本和組建編號並查看其他資訊 (例如應用程式是否為第一次啟動，或針對目前版本取得先前的組建資訊等)。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-version-tracking"></a>使用版本追蹤

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

您第一次使用 **VersionTracking** 類別時，就會開始追蹤目前版本。 僅當應用程式每次載入時，您必須儘早呼叫 `Track`，以確保追蹤目前版本資訊：

```csharp
VersionTracking.Track();
```

呼叫起始的 `Track` 後，即可以讀取版本資訊：

```csharp

// First time ever launched application
var firstLaunch = VersionTracking.IsFirstLaunchEver;

// First time launching current version
var firstLaunchCurrent = VersionTracking.IsFirstLaunchForCurrentVersion;

// First time launching current build
var firstLaunchBuild = VersionTracking.IsFirstLaunchForCurrentBuild;

// Current app version (2.0.0)
var currentVersion = VersionTracking.CurrentVersion;

// Current build (2)
var currentBuild = VersionTracking.CurrentBuild;

// Previous app version (1.0.0)
var previousVersion = VersionTracking.PreviousVersion;

// Previous app build (1)
var previousBuild = VersionTracking.PreviousBuild;

// First version of app installed (1.0.0)
var firstVersion = VersionTracking.FirstInstalledVersion;

// First build of app installed (1)
var firstBuild = VersionTracking.FirstInstalledBuild;

// List of versions installed (1.0.0, 2.0.0)
var versionHistory = VersionTracking.VersionHistory;

// List of builds installed (1, 2)
var buildHistory = VersionTracking.BuildHistory;
```

## <a name="platform-implementation-specifics"></a>平台實作特性

所有版本資訊都使用 Xamarin.Essentials 中的[喜好設定](preferences.md) API 來儲存，並以 **[您的應用程式套件識別碼].xamarinessentials.versiontracking**  檔案名稱儲存，且遵循[喜好設定](preferences.md#persistence)文件中概述的相同資料持續性。

## <a name="api"></a>API

- [版本追蹤原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/VersionTracking)
- [版本追蹤 API 文件](xref:Xamarin.Essentials.VersionTracking)
