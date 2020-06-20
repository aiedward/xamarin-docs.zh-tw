---
title: Xamarin.Essentials：版本追蹤
description: 中的 VersionTracking 類別 Xamarin.Essentials 可讓您檢查應用程式版本和組建編號，以及查看其他資訊，例如應用程式是否為第一次啟動，或針對目前版本取得先前的組建資訊等。
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 20819d76c23ca43f60073bcc2cd762abda280374
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802039"
---
# <a name="xamarinessentials-version-tracking"></a>Xamarin.Essentials：版本追蹤

**VersionTracking** 類別可讓您檢查應用程式版本和組建編號並查看其他資訊 (例如應用程式是否為第一次啟動，或針對目前版本取得先前的組建資訊等)。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-version-tracking"></a>使用版本追蹤

Xamarin.Essentials在您的類別中新增的參考：

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

所有版本資訊都是使用中的[喜好](preferences.md)設定 API 來儲存 Xamarin.Essentials ，並以 **[您的應用程式套件識別碼]. .xamarinessentials. versiontracking**的檔案名儲存，並遵循[偏好](preferences.md#persistence)設定檔中所述的相同資料持續性。

## <a name="api"></a>API

- [版本追蹤原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/VersionTracking)
- [版本追蹤 API 文件](xref:Xamarin.Essentials.VersionTracking)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Version-Tracking-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
