---
title: 追蹤 Xamarin.Essentials 版本
description: VersionTracking 類別可讓您檢查應用程式版本和組建編號，以及查看其他資訊這類，如同它是第一個時間曾啟動應用程式，或針對最新版本，取得先前的建置資訊、 等等。
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 41e0b704715b648e642f4a4c99554ff3f085a39a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-version-tracking"></a>追蹤 Xamarin.Essentials 版本

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**VersionTracking**類別可讓您檢查應用程式版本和組建編號，以及查看其他資訊這類，如同它是第一個時間曾啟動應用程式，或針對最新版本，取得上一個組建資訊等等。

## <a name="using-version-tracking"></a>使用追蹤版本

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

第一次您使用**VersionTracking**類別就會開始追蹤目前的版本。 您必須呼叫`Track`早期只在您的應用程式每次載入以確保會追蹤目前的版本資訊：

```csharp
VersionTracking.Track();
```

初始`Track`稱為可以閱讀版本資訊：

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

所有的版本資訊會儲存使用[喜好設定](preferences.md)API Xamarin.Essentials 中的儲存的檔案名稱和 **[您的應用程式-封裝-ID].xamarinessentials**。

解除安裝應用程式會使_LocalSettings_，和追蹤資訊要移除的所有版本。

## <a name="api"></a>API

- [版本追蹤程式碼](https://github.com/xamarin/Essentials/tree/master/Essentials/VersionTracking)
- [版本追蹤 API 文件](xref:Xamarin.Essentials.VersionTracking)
