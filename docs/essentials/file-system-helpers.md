---
title: Xamarin.Essentials 檔案系統的協助程式
description: FileSystem 類別包含一系列的協助程式，以尋找應用程式的快取和資料目錄，並開啟應用程式套件內的檔案。
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 871d164df982d1d170e8ba5bffd3bd6600a4cdda
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-file-system-helpers"></a>Xamarin.Essentials 檔案系統的協助程式

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**FileSystem**類別包含一系列的 helper 來尋找應用程式的快取和資料目錄，並開啟應用程式套件內的檔案。

## <a name="using-file-system-helpers"></a>使用檔案系統 Helper

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

若要取得儲存的應用程式的目錄**快取資料**。 快取資料可以用於任何需要保存的時間超過暫存資料，但不是能正常運作所需資料的資料。

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

若要取得應用程式的最上層 diredctory 不是使用者資料檔案的任何檔案。 同步處理架構的作業系統會備份這些檔案。 請參閱以下的平台實作細節。

```csharp
var mainDir = FileSystem.AppDataDirectory;
```

若要開啟配套到應用程式套件檔案：

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

- **CacheDirectory** – 傳回[CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir)的目前內容。
- **AppDataDirectory** – 傳回[FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir)的目前內容，是使用備份[Autu 備份](https://developer.android.com/guide/topics/data/autobackup.html)開始在 API 23 及更新版本。

加入到任何檔案**資產**資料夾中的 Android 專案，並將標示為建置動作**AndroidAsset**用於`OpenAppPackageFileAsync`。

# <a name="iostabios"></a>[iOS](#tab/ios)

- **CacheDirectory** – 傳回[程式庫/快取](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)目錄。
- **AppDataDirectory** – 傳回[文件庫](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)由 iTunes 與 iCloud 備份目錄。

加入到任何檔案**資源**資料夾中的 iOS 專案，並將標示為建置動作**BundledResource**用於`OpenAppPackageFileAsync`。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- **CacheDirectory** – 傳回[LocalCacheFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder)目錄...
- **AppDataDirectory** – 傳回[LocalFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder)會備份到雲端的目錄。

將任何檔案加入至 UWP 專案中的根目錄，並將標示建置動作**內容**用於`OpenAppPackageFileAsync`。

--------------

## <a name="api"></a>API

- [檔案系統的協助程式原始碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/FileSystem)
- [檔案系統 API 文件](xref:Xamarin.Essentials.FileSystem)
