---
title: 使用 Xamarin.安卓在外部儲存上的檔案存取
description: 本指南將討論 Xamarin.安卓中外部存儲上的檔案存取
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 96b0d6a00c7825939b1f89ed63e3e5559ca4ef59
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020474"
---
# <a name="external-storage"></a>外部儲存

外部存儲是指不在內部存儲上且對負責該檔的應用不獨佔存取的檔案存儲。 外部存儲的主要目的是提供一個位置,用於放置旨在在應用之間共用的檔或太大而不適合內部存儲的檔。

從歷史上看,外部存儲是指可移動介質(如 SD 卡(也稱為_便攜式存儲_)上的磁碟分區。 這種區別不再像 Android 設備已經演變的那樣相關,許多 Android 設備不再支援可移動存儲。 相反,一些設備將分配一些內部非易失性記憶體,Android將執行相同的功能可移動媒體。 這稱為_類比_存儲,仍被視為外部存儲。 或者,某些 Android 設備可能具有多個外部存儲分區。 例如,Android 平板電腦(除了其內部存儲)可能已類比存儲和 SD 卡的一個或多個插槽。 所有這些分區都由 Android 視為外部存儲。

在具有多個使用者的設備上,每個使用者將在主外部存儲分區上為其外部存儲提供專用目錄。 作為一個使用者運行的應用將無法訪問設備上其他使用者的檔。 所有使用者的檔仍然可讀且可世界寫入;但是,Android 會從其他使用者配置檔中沙箱。

在 Xamarin.Android 中,讀取和寫入文件幾乎與任何其他 .NET 應用程式相同。 Xamarin.Android 應用確定要操作的檔的路徑,然後使用標準的 .NET 習慣用法進行文件訪問。 由於內部和外部存儲的實際路徑可能因設備而異,或者從 Android 版本到 Android 版本,因此不建議對檔的路徑進行硬編碼。 相反,Xamarin.Android 公開了本機 Android API,這將有助於確定內部和外部存儲上檔的路徑。

本指南將討論 Android 中特定於外部存儲的概念和 API。

## <a name="public-and-private-files-on-external-storage"></a>外部儲存上的公共和私有檔案

套用可能會在外部儲存中保留兩種不同類型的檔案:

* **私有**&ndash;檔案 私有檔是特定於應用程式的檔(但仍可讀且可世界可寫)。 Android 期望私有檔存儲在外部存儲的特定目錄中。 即使這些檔被稱為"私有",它們仍然可見,並且被設備上的其他應用程序訪問,但 Android 不會提供任何特殊保護。

* **公共**&ndash;檔案 這些檔不被視為特定於應用程式,並且旨在自由共用。

這些文件之間的差異主要是概念性的。 私有檔是私有的,因為它們被視為應用程式的一部分,而公共檔是外部存儲上存在的任何其他檔。 Android 提供了兩種不同的 API 來解決私有和公共檔的路徑,但除此之外,相同的 .NET API 用於讀取和寫入這些檔。 這些是[閱讀和寫作](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage)一節中討論的相同 API。

### <a name="private-external-files"></a>私有外部檔案

私有外部檔被視為特定於應用程式(類似於內部檔),但由於各種原因(例如對於內部存儲來說太大),它們被保存在外部存儲中。 與內部文件類似,當使用者卸載應用時,這些檔將被刪除。

通過調用`Android.Content.Context.GetExternalFilesDir(string type)`方法 找到私有外部檔的主要位置。 此方法將返回表示應用`Java.IO.File`的專用外部存儲目錄的物件。 傳遞到`null`此方法將返回應用程式的使用者的存儲目錄的路徑。 例如,對於具有套件名稱`com.companyname.app`的應用程式,私有外部檔的「根」目錄是:

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

此文件將外部儲存的儲存目錄的儲存目錄為_\_私有外部\_儲存_。

的`GetExternalFilesDir()`參數是指定_應用程式目錄_的字串。 這是一個目錄,旨在為檔的邏輯組織提供標準位置。 字串值可透過類上的`Android.OS.Environment`常數獲得:

| `Android.OS.Environment` | 目錄 |
|-|-|
| 目錄警報 | **_專用\_\_外部儲存_/警報** |
| 目錄Dcim | **_私有\_外部\_儲存_/DCIM** |
| 目錄下載 | **_私人\_外部\_儲存_/下載** |
| 目錄文件 | **_私人\_外部\_儲存_/文件** |
| 目錄電影 | **_私人\_\_外部儲存_/電影** |
| 目錄音樂 | **_私人\_外部\_儲存_/音樂** |
| 目錄通知 | **_專用\_\_外部儲存_/通知** |
| 目錄播客 | **_私人\_\_外部儲存_/Podcast** |
| 目錄鈴聲 | **_私人\_\_外部儲存_/鈴聲** |
| 目錄圖片 | **_私人\_\_外部記憶體_/圖片** |

對於具有多個外部存儲分區的設備,每個分區將有一個用於私有檔的目錄。 該方法`Android.Content.Context.GetExternalFilesDirs(string type)`將返回的`Java.IO.Files`陣列。 每個物件將在所有共用/外部儲存設備上表示專用應用程式特定的目錄,應用程式可以在其中放置其擁有的檔。

> [!IMPORTANT]
> 專用外部存儲目錄的確切路徑可能因設備而異,在 Android 版本之間有所不同。 因此,應用不得硬編碼到此目錄的路徑,而是使用 Xamarin.Android API,如`Android.Content.Context.GetExternalFilesDir()`。

### <a name="public-external-files"></a>公開外部檔案

公共檔是存在於外部存儲上的文件,它們未存儲在 Android 為私有檔分配的目錄中。 卸載應用時,不會刪除公共檔。 Android 應用必須先獲得許可權,然後才能讀取或寫入任何公共檔。 公共檔案可能存在於外部存儲的任意位置,但按照慣例,Android 希望公共檔存在於`Android.OS.Environment.ExternalStorageDirectory`屬性 標識的目錄中。 此屬性將返回表示主`Java.IO.File`外部存儲目錄的物件。 例如,`Android.OS.Environment.ExternalStorageDirectory`可以參考以下目錄:

```bash
/storage/emulated/0/
```

此文件將外部儲存的檔案儲存目錄為_\_公共外部\_儲存_。

Android 還支援_\_\_公共外部存儲_上的應用程式目錄的概念。 這些目錄與上`PRIVATE_EXTERNAL_STORAGE`一節中的表中所述的應用程式目錄完全相同。 該方法`Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)`將返回對應`Java.IO.File`於 公共應用程式目錄的物件。 參數`directoryType`是必填參數,不能`null`為 。

例如,呼叫`Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath`將傳回類似於的字串:

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> 公共外部存儲目錄的確切路徑可能因設備而異,在Android版本之間有所不同。 因此,應用不得硬編碼到此目錄的路徑,而是使用 Xamarin.Android API,如`Android.OS.Environment.ExternalStorageDirectory`。

## <a name="working-with-external-storage"></a>使用外部儲存

一旦 Xamarin.Android 應用程式獲得了檔的完整路徑,它應利用任何標準的 .NET API 來創建、讀取、寫入或刪除檔。 這樣可以最大化應用的跨平臺相容代碼量。 但是,在嘗試訪問檔之前,Xamarin.Android 應用必須確保可以訪問該檔。

1. **驗證外部存儲**&ndash;根據外部存儲的性質,應用可能無法裝載和使用它。 在嘗試使用外部存儲之前,所有應用都應檢查其狀態。
2. **執行執行時權限檢查**&ndash;Android 應用必須向使用者請求許可權才能存取外部儲存。 這意味著應在任何文件訪問之前執行運行時許可權請求。 [Xamarin.Android 中的指南許可權](~/android/app-fundamentals/permissions.md)包含有關 Android 許可權的更多詳細資訊。

下面將討論這兩項任務中的每一個。

### <a name="verifying-that-external-storage-is-available"></a>驗證外部儲存是否可用

寫入外部存儲之前的第一步是檢查它是可讀的還是可寫入的。 屬性`Android.OS.Environment.ExternalStorageState`包含識別外部儲存狀態的字串。 此屬性將返回表示狀態的字串。 此表是 可能`ExternalStorageState`由`Environment.ExternalStorageState`傳回的值的清單:

| 外部儲存狀態 | 描述  |
|----------------------|---|
| 媒體巴德爾刪除      | 介質突然被移除,未正確卸載。 |
| 媒體檢查        | 介質存在,但正在進行磁片檢查。  |
| 媒體彈出        | 介質正在卸載和彈出過程中。  |
| 媒體安裝         | 介質已安裝,可以讀取或寫入。  |
| 只安裝讀取 | 介質已安裝,但只能從中讀取。 |
| 媒體納芬斯            | 媒體存在,但不包含適合 Android 的檔案系統。 |
| 媒體刪除         | 沒有媒體存在。 |
| 媒體分享          | 介質存在,但未裝入。 它通過 USB 與其他設備共用。|
| 媒體未知         | Android 無法識別媒體的狀態。 |
| 無法安裝     | 介質存在,但不能由 Android 安裝。 |
| 未安裝媒體       | 介質存在,但未裝入。 |

大多數 Android 應用只需要檢查是否安裝了外部存儲。 以下代碼段展示如何驗證外部儲存是否載入用於唯讀取或讀寫存取:

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>外部儲存權限

Android 認為訪問外部存儲是一_種危險的許可權_,通常需要使用者授予其訪問資源的許可權。 用戶可以隨時撤銷此許可權。  這意味著應在任何文件訪問之前執行運行時許可權請求。 應用將自動被授予讀取和寫入其自己的私有文件的許可權。 應用可以在使用者[授予許可權](~/android/app-fundamentals/permissions.md)後讀取和寫入屬於其他應用的私有檔。

所有 Android 應用程式都必須聲明**AndroidManifest.xml**中外部存儲的兩個許可權之一。 要標識權限,必須將以下`uses-permission`兩個元素之一添加到**AndroidManifest.xml**中:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> 如果使用者授予`WRITE_EXTERNAL_STORAGE`,`READ_EXTERNAL_STORAGE`則也會隱式授予。 沒有必要在**AndroidManifest.xml**中請求這兩個許可權。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

可使用**解決方案屬性**的 Android**清單**選項卡添加權限:

![解決方案資源管理員 ─視覺化工作室所需的權限](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

還可以使用**解決方案屬性墊**的**Android 清單**選項卡添加權限:

[![解決方案墊 - Mac 視覺工作室需要的權限](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

一般來說,所有危險許可權都必須由使用者批准。 外部存儲的許可權是一種異常,因為此規則存在例外情況,具體取決於應用正在運行的 Android 版本:

![外部儲存權限檢查的流程圖](./images/external-permission-check-flowchart.png)

有關執行執行時權限請求的詳細資訊,請參閱[Xamarin.Android 中的指南許可權](~/android/app-fundamentals/permissions.md)。 **單體範例** [LocalFiles](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)還演示執行執行時權限檢查的一種方法。

#### <a name="granting-and-revoking-permissions-with-adb"></a>向亞列授予及撤銷權限

在開發 Android 應用的過程中,可能需要授予和撤銷用於測試運行時權限檢查所涉及的各種工作流程的許可權。 可以使用亞行在命令提示符下執行此操作。 以下命令列代碼段展示如何使用 ADB 授予或撤銷其套件名稱為**com.companyname.app**的 Android 應用的許可權:

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>刪除檔案

任何標準 C# API 都可用於從外部儲存中移除[`System.IO.File.Delete`](xref:System.IO.File.Delete*)檔案,例如 。 也可以使用 JAVA API,但代價是代碼可移植性。 例如：

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>相關連結

* [Xamarin.Android 本地檔案範例,位於**單體樣本**上](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [Xamarin.安卓中的許可權](~/android/app-fundamentals/permissions.md)
