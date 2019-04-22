---
title: 使用 Xamarin.Android 的外部儲存體上的檔案存取
description: 本指南會討論在 Xamarin.Android 中的外部儲存體上的檔案存取
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: 78051fce44239eea86948988a4d19ac37c5ea0d5
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58854895"
---
# <a name="external-storage"></a>外部儲存體

外部儲存體是指不是在內部儲存體上，且無法以獨佔方式存取的應用程式，負責檔案的檔案儲存體。 外部儲存體的主要目的是要提供一個位置來放置的檔案，為了在應用程式之間共用或是太大而無法放入內部儲存體。

在過去讀出、 外部儲存體參考卸除式媒體，例如 sd 記憶卡上的磁碟分割 (也稱為_可攜式儲存裝置_)。 這項區別不再為相關，因為 Android 裝置已演變，且許多 Android 裝置不會再支援卸除式儲存體。 改為某些裝置會配置一些其內部的非揮發性記憶體來執行相同的函式卸除式媒體的 Android。 這就所謂_模擬_儲存體，且仍會視為是外部儲存體。 或者，某些 Android 裝置可能有多個外部儲存體資料分割。 例如，Android 平板電腦 （除了其內部存放裝置） 可能會模擬儲存體和 sd 記憶卡的一或多個位置。 所有這些分割會被視為由 Android 外部儲存體。

在裝置上有多個使用者，每個使用者必須在其外部儲存體的主要的外部儲存體磁碟分割上的專用的目錄。 以一位使用者執行的應用程式不會存取檔案從裝置上的另一位使用者。 所有使用者的檔案仍是世界可讀取且可寫入世界;不過，Android 會沙箱與其他每個使用者設定檔。

讀取和寫入檔案是在 Xamarin.Android 中幾乎完全相同，因為它是任何其他.NET 應用程式。 Xamarin.Android 應用程式會判斷檔案要操作，然後使用標準.NET 語言存取檔案的路徑。 因為內部和外部存放裝置的實際路徑可能不同裝置，或從 Android 版本的 Android 版本，不建議以硬編碼檔案的路徑。 相反地，Xamarin.Android 會公開原生 Android Api，將幫助您判斷內部和外部的儲存體上檔案的路徑。

本指南會討論的概念與在 Android 外部儲存體專屬的 Api。

## <a name="public-and-private-files-on-external-storage"></a>在外部儲存體上的公開和私密金鑰檔案

有兩種不同的應用程式可能會保留在外部儲存體的檔案：

* **私用**檔案&ndash;私用的檔案是專屬於您的應用程式 （但仍可 world 讀取和可 world 寫入） 的檔案。 Android 需要私用的檔案會儲存在外部儲存體上的特定目錄中。 即使這些檔案稱為 「 私用 」，它們仍是可見且可供其他應用程式在裝置上存取，他們不會獲得任何特殊保護 android。

* **公用**檔案&ndash;這些是較不為應用程式特定的旨在自由共用的檔案。

這些檔案之間的差異是主要概念。 私用的檔案是私用，因為它們會被視為應用程式中，部分公用檔案時存在於外部儲存體的任何其他檔案。 Android 提供兩個不同的 Api 來解析的路徑至私人和公用檔案，但否則相同的.NET Api 來讀取和寫入這些檔案。 這些是在一節中討論的相同 Api[讀取和寫入](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage)。

### <a name="private-external-files"></a>私用的外部檔案

私用的外部檔案會被視為特定應用程式 （類似於內部的檔案），但保持在外部的儲存體，供任意數目的原因 （例如正在對內部儲存體而言太大）。 類似於內部的檔案，這些檔案將會刪除使用者解除安裝應用程式時。

藉由呼叫此方法找到私用的外部檔案的主要位置`Android.Content.Context.GetExternalFilesDir(string type)`。 這個方法會傳回`Java.IO.File`物件，表示應用程式的私用的外部儲存體目錄。 傳遞`null`這個方法會傳回路徑的應用程式的使用者的儲存目錄。 例如，應用程式的封裝名稱`com.companyname.app`，私用的外部檔案的 「 根 」 目錄會是：

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

這份文件會參考做為外部儲存體上的私用檔案的儲存體目錄_私人\_外部\_儲存體_。


參數`GetExternalFilesDir()`是一個字串，指定_應用程式目錄_。 這是要提供檔案的邏輯組織的標準位置的目錄。 字串值都是透過常數上`Android.OS.Environment`類別：

| `Android.OS.Environment` | Directory |
|-|-|
| DirectoryAlarms | **_PRIVATE\_EXTERNAL\_STORAGE_/Alarms** |
| DirectoryDcim | **_PRIVATE\_EXTERNAL\_STORAGE_/DCIM** |
| DirectoryDownloads | **_私用\_外部\_儲存體_  /下載** |
| DirectoryDocuments | **_PRIVATE\_EXTERNAL\_STORAGE_/Documents** |
| DirectoryMovies | **_PRIVATE\_EXTERNAL\_STORAGE_/Movies** |
| DirectoryMusic | **_PRIVATE\_EXTERNAL\_STORAGE_/Music** |
| DirectoryNotifications | **_私用\_外部\_儲存體_/Notifications** |
| DirectoryPodcasts | **_PRIVATE\_EXTERNAL\_STORAGE_/Podcasts** |
| DirectoryRingtones | **_私用\_外部\_儲存體_/Ringtones** |
| DirectoryPictures | **_私用\_外部\_儲存體_  /圖片** |

對於具有多個外部儲存體磁碟分割的裝置，每個分割區必須是私用檔案的目錄。 此方法`Android.Content.Context.GetExternalFilesDirs(string type)`會傳回的陣列`Java.IO.Files`。 每個物件將代表私用的應用程式特定目錄上應用程式可以在其中放置檔案的所有外部共用存放裝置其所擁有。

> [!IMPORTANT]
> 私用 exteral 儲存體目錄的確切路徑而異的裝置到裝置和 Android 版本之間。 基於這個原因，應用程式必須沒有硬式程式碼的路徑，此目錄中，並改為使用 Xamarin.Android 的 Api，例如`Android.Content.Context.GetExternalFilesDir()`。

### <a name="public-external-files"></a>公開的外部檔案

公用檔案是存在於外部儲存體，不會儲存在 Android 配置的私用檔案的目錄中的檔案。 解除安裝應用程式時，不會刪除公用檔案。 Android 應用程式必須授與權限才能讀取或寫入任何公用的檔案。 公用檔案，以在外部儲存體，但依照慣例 Android 需要這些公用檔案存在於屬性所識別的目錄`Android.OS.Environment.ExternalStorageDirectory`。 這個屬性會傳回`Java.IO.File`代表主要的外部儲存體目錄的物件。 例如，`Android.OS.Environment.ExternalStorageDirectory`可能會參考到下列目錄：

```bash
/storage/emulated/0/
```

這份文件會參考做為外部儲存體上的公用檔案的儲存體目錄_公開金鑰\_外部\_儲存體_。


Android 上也支援之應用程式目錄的概念_公開金鑰\_外部\_儲存體_。 這些目錄並完全相同的應用程式目錄`_PRIVATE\_EXTERNAL\_STORAGE_`和上一節中的表格所述。 此方法`Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)`會傳回`Java.IO.File`對應至公用應用程式目錄的物件。 `directoryType`參數是必要參數，而且不能是`null`。

例如，呼叫`Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath`會傳回字串，其中會類似這樣：

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> 公用外部儲存體目錄的確切路徑可以從裝置而異的裝置和 Android 版本之間。 基於這個原因，應用程式必須沒有硬式程式碼的路徑，此目錄中，並改為使用 Xamarin.Android 的 Api，例如`Android.OS.Environment.ExternalStorageDirectory`。

## <a name="working-with-external-storage"></a>使用外部儲存體

一旦將 Xamarin.Android 應用程式已取得檔案的完整路徑，它應該使用任何標準的.NET Api 建立、 讀取、 寫入或刪除檔案。 這可最大化跨平台相容的應用程式程式碼的數量。 不過，然後再嘗試存取檔案的 Xamarin.Android 應用程式必須確定，可以存取該檔案。

1. **確認外部儲存體**&ndash;取決於外部儲存體的本質，很可能，它可能會無法掛接和使用的應用程式。 所有應用程式應該檢查外接式存放裝置的狀態，然後再嘗試使用它。
2. **執行執行階段權限檢查** &ndash; Android 應用程式必須要求使用者的權限才能存取外部儲存體。 這表示，應該在任何檔案存取權之前執行的權限要求的執行階段。 快速入門[權限在 Xamarin.Android](~/android/app-fundamentals/permissions.md)包含 Android 權限的更多詳細資料。

將下面討論每個這兩項工作。

### <a name="verifying-that-external-storage-is-available"></a>正在驗證外部儲存體可用

寫入外部儲存體之前的第一個步驟是檢查它可讀取或寫入。 `Android.OS.Environment.ExternalStorageState`屬性會保留可識別的外接式存放裝置狀態的字串。 這個屬性會傳回字串，表示狀態。 此資料表是一份`ExternalStorageState`可能傳回的值`Environment.ExternalStorageState`:

| ExternalStorageState | 描述  |
|----------------------|---|
| MediaBadRemoval      | 媒體已意外移除不正確正在卸載。 |
| MediaChecking        | 媒體已存在，但目前正在進行磁碟檢查。  |
| MediaEjecting        | 媒體正在卸載，而退出。  |
| MediaMounted         | 媒體已裝載，並可以讀取或寫入。  |
| MediaMountedReadOnly | 媒體已裝載，但只能從讀取。 |
| MediaNofs            | 媒體存在，但是不包含適用於 Android 的檔案系統。 |
| MediaRemoved         | 不沒有出現任何媒體。 |
| MediaShared          | 媒體已存在，但未裝載。 正在透過 USB 與共用另一部裝置。|
| MediaUnknown         | Android 無法辨識媒體的狀態。 |
| MediaUnmountable     | 媒體存在，但是無法由 Android 掛接。 |
| MediaUnmounted       | 媒體存在，但是未裝載。 |


大部分的 Android 應用程式只需要核取 是否裝載外部儲存體。 下列程式碼片段示範驗證外部儲存體已掛接為唯讀或讀寫存取的方法：

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>外部儲存體的權限

Android 會考慮存取外部儲存體以進行_危險的使用權限_，通常需要使用者授與他們存取資源的使用權限。 使用者可以撤銷此權限，在任何時間。  這表示，應該在任何檔案存取權之前執行的權限要求的執行階段。 應用程式會自動授與權限來讀取和寫入自己的私人檔案。 您可針對讀取和寫入私人檔案屬於其他應用程式後的應用程式[授與權限](~/android/app-fundamentals/permissions.md)使用者。

所有的 Android 應用程式必須宣告一個外部儲存體中的兩個權限**AndroidManifest.xml** 。 若要識別的權限，下列兩個的其中一個`uses-permission`必須將項目新增至**AndroidManifest.xml**:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> 如果使用者授與`WRITE_EXTERNAL_STORAGE`，然後`READ_EXTERNAL_STORAGE`還有以隱含方式授與。 您不需要要求中的兩個權限**AndroidManifest.xml**。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

權限可能也會加入使用**Android 資訊清單**索引標籤**方案屬性**:

![方案總管-適用於 Visual Studio 的必要權限](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

權限可能也會加入使用**Android 資訊清單**索引標籤**方案屬性板**:

[![Solution Pad-必要的權限，Visual Studio for Mac](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

一般而言，使用者必須核准所有危險的使用權限。 外部儲存體的權限的異常狀況，在於有這項規則，根據應用程式執行的 Android 版本而定的例外狀況：

![外部儲存體的權限檢查的流程圖](./images/external-permission-check-flowchart.png)

如需有關如何執行執行階段權限要求的詳細資訊，請參閱此指南[權限在 Xamarin.Android](~/android/app-fundamentals/permissions.md)。 **Monodroid 範例** [LocalFiles](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)也會示範其中一種執行執行階段權限檢查。

#### <a name="granting-and-revoking-permissions-with-adb"></a>授與及撤銷權限，與 ADB

在過程中開發 Android 應用程式，它可能需要授與及撤銷權限來測試各種執行階段權限檢查相關的工作流程。 您可在使用 ADB 命令提示字元執行這項操作。 下列的命令列程式碼片段示範如何授與或撤銷權限的 Android 應用程式封裝名稱是使用 ADB **com.companyname.app**:

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>刪除檔案

任何一種標準 C# Api 可用來從外部存放區，刪除檔案時，也將這類[ `System.IO.File.Delete` ](xref:System.IO.File.Delete*)。 它也可使用 Java Api，但代價是程式碼可攜性。 例如: 

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>相關連結

* [Xamarin.Android 的本機檔案範例上**monodroid 範例**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [在 Xamarin.Android 中的權限](~/android/app-fundamentals/permissions.md)
