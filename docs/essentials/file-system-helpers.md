---
title: Xamarin.Essentials： 檔案系統協助程式
description: 檔案系統中的類別 Xamarin.Essentials 包含一系列的協助人員尋找應用程式的快取和資料目錄，並開啟應用程式套件內的檔案。
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 13293ec05261cbdc1e70fd278002d1af18654851
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815614"
---
# <a name="xamarinessentials-file-system-helpers"></a>Xamarin.Essentials： 檔案系統協助程式

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**FileSystem**類別包含一系列的協助人員尋找應用程式的快取和資料目錄，並開啟應用程式套件內的檔案。

## <a name="using-file-system-helpers"></a>使用檔案系統協助程式

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

若要取得應用程式的目錄來儲存**快取資料**。 快取資料可以用於任何需要保有超過暫存資料，但不是應該正常運作所需資料的資料。

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

若要取得應用程式的最上層目錄的任何不是使用者資料檔案的檔案。 這些檔案會與同步處理架構的作業系統備份。 請參閱下方的平台實作細節。

```csharp
var mainDir = FileSystem.AppDataDirectory;
```

若要開啟會打包成應用程式套件檔案：

```csharp
 using (var stream = await FileSystem.OpenAppPackageFileAsync(templateFileName))
 {
    using (var reader = new StreamReader(stream))
    {
        var fileContents = await reader.ReadToEndAsync();
    }
 }
```

## <a name="platform-implementation-specifics"></a>平台實作的特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

- **CacheDirectory** – 傳回[CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir)目前內容。
- **AppDataDirectory** – 傳回[FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir)以及與的目前內容是使用備份[自動備份](https://developer.android.com/guide/topics/data/autobackup.html)啟動 API 23 和更新版本。

新增任何檔案**資產**資料夾中的 Android 專案，並將標示為建置動作**AndroidAsset**若要使用它來搭配`OpenAppPackageFileAsync`。

# <a name="iostabios"></a>[iOS](#tab/ios)

- **CacheDirectory** – 傳回[程式庫/快取](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)目錄。
- **AppDataDirectory** – 傳回[程式庫](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)透過 iTunes 與 iCloud 備份的目錄。

新增任何檔案**資源**資料夾中的 iOS 專案，並將標示為建置動作**BundledResource**若要使用它來搭配`OpenAppPackageFileAsync`。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- **CacheDirectory** – 傳回[LocalCacheFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder)目錄...
- **AppDataDirectory** – 傳回[LocalFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder)會備份到雲端的目錄。

加入在 UWP 專案的根目錄中的任何檔案，並將標示為建置動作**內容**若要使用它來搭配`OpenAppPackageFileAsync`。

--------------

## <a name="api"></a>API

- [檔案系統協助程式原始碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/FileSystem)
- [檔案系統 API 文件](xref:Xamarin.Essentials.FileSystem)
