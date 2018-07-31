---
title: 追蹤 Xamarin.Essentials： 版本
description: Xamarin.Essentials VersionTracking 類別可讓您檢查應用程式版本和組建編號，以及查看其他資訊這類，好像第一次曾經啟動應用程式，或取得最新版本中前, 一個組建資訊及其他資訊。
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 81dc67fa5a4975f31d0fbf9f7219637596a827ce
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353655"
---
# <a name="xamarinessentials-version-tracking"></a>追蹤 Xamarin.Essentials： 版本

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**VersionTracking**類別可讓您檢查應用程式版本和組建編號，以及查看其他資訊這類，如同它是第一個時間曾經啟動應用程式，或針對最新版本中，取得上一個建置資訊及其他資訊。

## <a name="using-version-tracking"></a>使用版本追蹤

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

您使用第一次**VersionTracking**類別就會開始追蹤目前的版本。 您必須呼叫`Track`早期只在您的應用程式每次載入，以確保會追蹤目前的版本資訊：

```csharp
VersionTracking.Track();
```

完成初始`Track`呼叫可以閱讀版本資訊：

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

## <a name="platform-implementation-specifics"></a>平台實作的特性

使用儲存所有的版本資訊[喜好設定](preferences.md)Xamarin.Essentials 中的 API 和儲存的檔案名稱 **[您的應用程式-封裝-識別碼].xamarinessentials.versiontracking**會遵循相同資料持續性中所述[喜好設定](preferences.md#persistence)文件。

## <a name="api"></a>API

- [版本追蹤來源的程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/VersionTracking)
- [版本追蹤 API 文件](xref:Xamarin.Essentials.VersionTracking)
