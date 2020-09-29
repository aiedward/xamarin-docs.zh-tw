---
title: Xamarin.Essentials：檔案系統協助程式
description: 中的 FileSystem 類別 Xamarin.Essentials 包含一系列協助程式，可尋找應用程式的快取和資料目錄，並開啟應用程式套件內的檔案。
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 967fa5f54ec9ccbb1f8bac2a87d77dca63caba3a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434327"
---
# <a name="no-locxamarinessentials-file-system-helpers"></a>Xamarin.Essentials：檔案系統協助程式

**FileSystem** 類別包含一系列協助程式，用於尋找應用程式的快取和資料目錄，並開啟應用程式套件內的檔案。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-file-system-helpers"></a>使用檔案系統協助程式

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

取得應用程式的目錄來儲存**快取資料**。 快取資料可用於需要比暫存資料持續更長時間的任何資料，但不應該是正常運作所需的資料，因為 OS 會指示清除此儲存體的時機。

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

取得任何非使用者資料檔案之檔案的應用程式最上層目錄。 這些檔案會使用作業系統同步處理架構進行備份。 請參閱以下平台實作細節。

```csharp
var mainDir = FileSystem.AppDataDirectory;
```

開啟組成應用程式套件的檔案：

```csharp
 using (var stream = await FileSystem.OpenAppPackageFileAsync(templateFileName))
 {
    using (var reader = new StreamReader(stream))
    {
        var fileContents = await reader.ReadToEndAsync();
    }
 }
```

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="android"></a>[Android](#tab/android)

- **CacheDirectory** – 傳回目前內容的 [CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir)。
- **AppDataDirectory** – 傳回目前內容的 [FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir)，並使用從 API 23 及更新版本開始的[自動備份](https://developer.android.com/guide/topics/data/autobackup.html)來進行備份。

將任何檔案新增至 Android 專案的 [資產]**** 資料夾中，並將建置動作標記為 **AndroidAsset**，以將其與 `OpenAppPackageFileAsync` 搭配使用。

# <a name="ios"></a>[iOS](#tab/ios)

- **CacheDirectory** – 傳回[程式庫/快取](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)目錄。
- **AppDataDirectory** – 傳回透過 iTunes 與 iCloud 備份的[程式庫](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)目錄。

> [!IMPORTANT]
> 在 iOS 模擬器中，應用程式識別碼 (是目錄名稱的一部分) 每個組建的變更，因此您每次為模擬器建立應用程式時，都必須取得正確的識別碼。

將任何檔案新增至 iOS 專案的 [資源]**** 資料夾中，並將建置動作標記為 **BundledResource**，以將其與 `OpenAppPackageFileAsync` 搭配使用。

# <a name="uwp"></a>[UWP](#tab/uwp)

- **CacheDirectory** – 傳回 [LocalCacheFolder](/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder) 目錄...
- **AppDataDirectory** – 傳回備份至雲端的 [LocalFolder](/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder) 目錄。

將任何檔案新增至 UWP 專案的根目錄中，並將建置動作標記為**內容**，以將其與 `OpenAppPackageFileAsync` 搭配使用。

--------------

## <a name="api"></a>API

- [檔案系統協助程式原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/FileSystem)
- [檔案系統 API 文件](xref:Xamarin.Essentials.FileSystem)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/File-System-Helpers-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]