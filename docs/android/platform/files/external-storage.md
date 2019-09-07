---
title: 使用 Xamarin 進行外部儲存體的檔案存取
description: 本指南將討論在 Xamarin. Android 中的外部儲存體上的檔案存取
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: e30f726cfb783fc47bc09f7590a523eb0e487105
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756522"
---
# <a name="external-storage"></a>外部儲存體

外部儲存體指的是不在內部儲存體上的檔案儲存體，而不是由負責該檔案的應用程式存取。 外部儲存體的主要目的是要提供一個位置來放置要在應用程式之間共用的檔案，或是太大而無法放入內部存放裝置。

在過去，外部儲存體指的是卸載式媒體（例如 SD 記憶卡）上的磁碟分割（也稱為_便攜存放裝置_）。 這種區別已不再與 Android 裝置進化相關，而許多 Android 裝置已不再支援卸除式存放裝置。 相反地，有些裝置會配置一些內部的非動態記憶體，而 Android 會執行相同的功能卸載式媒體。 這就是所謂的_模擬_儲存體，而且仍然被視為外部儲存體。 或者，有些 Android 裝置可能會有多個外部儲存體磁碟分割。 例如，Android 平板電腦（除了其內部存放裝置）可能會有模擬存放裝置，以及一或多個 SD 記憶卡的插槽。 Android 會將所有這些磁碟分割視為外部儲存體。

在具有多個使用者的裝置上，每個使用者在其外部儲存區的主要外部儲存磁碟分割上都會有專用目錄。 以一位使用者身分執行的應用程式將無法存取裝置上另一位使用者的檔案。 所有使用者的檔案仍可供全球讀取，且可由全球寫入;不過，Android 會將每個使用者設定檔從其他檔案進行沙箱處理。

在 Xamarin 中讀取和寫入檔案幾乎是完全相同的，如同任何其他 .NET 應用程式一樣。 Xamarin 應用程式會決定要操作之檔案的路徑，然後使用標準的 .NET 慣用語來存取檔案。 由於內部和外部儲存體的實際路徑可能會因裝置而異，或從 Android 版本變更為 Android 版本，因此不建議將檔案的路徑硬編碼。 取而代之的是，Xamarin 會公開原生 Android Api，以協助判斷內部和外部儲存體上的檔案路徑。

本指南將討論 Android 中適用于外部儲存體的概念和 Api。

## <a name="public-and-private-files-on-external-storage"></a>外部儲存體上的公用和私用檔案

應用程式可能會在外部存放裝置上保留兩種不同類型的檔案：

* **私用**檔案私用檔案是您的應用程式特有的檔案（但仍然是世界可讀取且可供全球寫入的檔案）。&ndash; Android 預期私人檔案會儲存在外部存放裝置上的特定目錄中。 雖然檔案稱為「私用」，但裝置上的其他應用程式仍會看到這些檔案並可供存取，因此不會受到 Android 的任何特殊保護。

* **公用檔案**：這些檔案不會被視為應用程式特有的檔案，而是要自由共用。&ndash;

這些檔案之間的差異主要是概念。 私用檔案是私用的，因為它們被視為應用程式的一部分，而公用檔案則是存在於外部儲存區上的任何其他檔案。 Android 提供兩個不同的 Api 來解析私人和公用檔案的路徑，否則會使用相同的 .NET Api 來讀取和寫入這些檔案。 這些是與[讀取和寫入](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage)一節中所討論的相同 api。

### <a name="private-external-files"></a>私用外部檔案

私用外部檔案被視為應用程式的特定（類似于內部檔案），但因為任何原因而保留在外部存放區（例如對內部儲存體而言太大）。 與內部檔案類似，當使用者卸載應用程式時，將會刪除這些檔案。

藉由呼叫方法`Android.Content.Context.GetExternalFilesDir(string type)`，即可找到私用外部檔案的主要位置。 這個方法會`Java.IO.File`傳回代表應用程式之私用外部儲存體目錄的物件。 傳遞`null`至這個方法將會傳回使用者的應用程式儲存目錄路徑。 例如，針對具有封裝名稱`com.companyname.app`的應用程式，私用外部檔案的 "root" 目錄會是：

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

本檔會參考外部儲存體上私人檔案的儲存體目錄作為 _\_私人外部\_儲存體_。

的參數`GetExternalFilesDir()`是指定_應用程式目錄_的字串。 這是一個目錄，目的是要為檔案的邏輯組織提供標準位置。 字串值可透過`Android.OS.Environment`類別上的常數取得：

| `Android.OS.Environment` | 目錄 |
|-|-|
| DirectoryAlarms | **_私\_用外部\_儲存體_/Alarms** |
| DirectoryDcim | **_PRIVATE\_EXTERNAL\_STORAGE_/DCIM** |
| DirectoryDownloads | **_私\_用外部\_儲存體_/Download** |
| DirectoryDocuments | **_私\_用外部\_儲存體_/Documents** |
| DirectoryMovies | **_私\_用外部\_儲存體_/Movies** |
| DirectoryMusic | **_私\_用外部\_儲存體_/Music** |
| DirectoryNotifications | **_私\_用外部\_儲存體_/Notifications** |
| DirectoryPodcasts | **_私\_用外部\_儲存體_/Podcasts** |
| DirectoryRingtones | **_私\_用外部\_儲存體_/Ringtones** |
| DirectoryPictures | **_私\_用外部\_儲存體_/Pictures** |

對於具有多個外部儲存體磁碟分割的裝置，每個分割區都有一個適用于私人檔案的目錄。 方法`Android.Content.Context.GetExternalFilesDirs(string type)`會傳回的`Java.IO.Files`陣列。 每個物件都將代表所有共用/外部存放裝置上的私用應用程式特定目錄，應用程式可以在其中放置所擁有的檔案。

> [!IMPORTANT]
> 私人 exteral 儲存體目錄的確切路徑可能會因裝置而異，以及 Android 版本之間的差異。 因此，應用程式不一定要將路徑硬編碼到此目錄，而是改為使用 Xamarin Api，例如`Android.Content.Context.GetExternalFilesDir()`。

### <a name="public-external-files"></a>公用外部檔案

公用檔案是存在於外部存放裝置上的檔案，這些檔案不會儲存在 Android 配置給私人檔案的目錄中。 卸載應用程式時，不會刪除公用檔案。 Android 應用程式必須被授與許可權，才能讀取或寫入任何公用檔案。 公用檔案可以存在於外部儲存位置上，但依照慣例，Android 預期公用檔案存在於屬性`Android.OS.Environment.ExternalStorageDirectory`所識別的目錄中。 這個屬性會`Java.IO.File`傳回代表主要外部儲存體目錄的物件。 例如， `Android.OS.Environment.ExternalStorageDirectory`可能會參考下列目錄：

```bash
/storage/emulated/0/
```

本檔會將外部儲存體上的公用檔案儲存目錄視為 _\_公用外部\_儲存體_。

Android 也支援 _\_公用外部\_儲存體_上的應用程式目錄概念。 這些目錄與`_PRIVATE\_EXTERNAL\_STORAGE_`和的應用程式目錄完全相同，如前一節的表格中所述。 方法`Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)` 會`Java.IO.File`傳回對應至公用應用程式目錄的物件。 參數是必要參數，而且不能是`null`。 `directoryType`

例如，呼叫`Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath`將會傳回類似下列內容的字串：

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> 公用外部儲存體目錄的確切路徑，可能會因裝置與裝置，以及 Android 版本之間的不同而有所不同。 因此，應用程式不一定要將路徑硬編碼到此目錄，而是改為使用 Xamarin Api，例如`Android.OS.Environment.ExternalStorageDirectory`。

## <a name="working-with-external-storage"></a>使用外部儲存體

在 Xamarin Android 應用程式取得檔案的完整路徑之後，它應該利用任何標準 .NET Api 來建立、讀取、寫入或刪除檔案。 這可將應用程式的跨平臺相容程式碼數量最大化。 不過，在嘗試存取檔案之前，您必須先確定是否能夠存取該檔案。

1. **確認外部儲存體**&ndash;視外部儲存體的本質而定，它可能不是由應用程式裝載並可供使用。 所有應用程式都應該先檢查外部存放裝置的狀態，然後再嘗試使用它。
2. **執行執行時間許可權檢查**&ndash; Android 應用程式必須向使用者要求許可權，才能存取外部儲存體。 這表示在存取任何檔案之前，應該先執行執行時間許可權要求。 [Xamarin 中](~/android/app-fundamentals/permissions.md)的指南許可權包含 android 許可權的更多詳細資料。

以下將討論這兩項工作。

### <a name="verifying-that-external-storage-is-available"></a>正在驗證外部存放裝置是否可用

寫入外部儲存體之前的第一個步驟是檢查它是否可讀取或寫入。 `Android.OS.Environment.ExternalStorageState`屬性會保存識別外部儲存體狀態的字串。 這個屬性會傳回代表狀態的字串。 此資料表是可能`ExternalStorageState` `Environment.ExternalStorageState`傳回的值清單：

| ExternalStorageState | 描述  |
|----------------------|---|
| MediaBadRemoval      | 媒體已突然移除，而未適當地卸載。 |
| MediaChecking        | 媒體已存在，但正在進行磁片檢查。  |
| MediaEjecting        | 媒體正在卸載和退出。  |
| MediaMounted         | 媒體已裝載，而且可以讀取或寫入。  |
| MediaMountedReadOnly | 媒體已裝載，但只能從讀取。 |
| MediaNofs            | 媒體存在，但不包含適用于 Android 的檔案系統。 |
| MediaRemoved         | 沒有媒體存在。 |
| MediaShared          | 媒體存在，但未裝載。 它正透過 USB 與其他裝置共用。|
| MediaUnknown         | Android 無法辨識媒體的狀態。 |
| MediaUnmountable     | 媒體存在，但無法由 Android 裝載。 |
| MediaUnmounted       | 媒體存在，但未裝載。 |

大部分的 Android 應用程式只需要檢查是否已掛接外部儲存體。 下列程式碼片段示範如何確認外部儲存體已掛接以進行唯讀存取或讀寫存取：

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>外部儲存體許可權

Android 會將外部存放裝置視為_危險許可權_，通常會要求使用者授與其存取資源的許可權。 使用者可以隨時撤銷此許可權。  這表示在存取任何檔案之前，應該先執行執行時間許可權要求。 應用程式會自動被授與讀取和寫入自己的私用檔案的許可權。 應用程式可能會在授與使用者的[許可權](~/android/app-fundamentals/permissions.md)之後，讀取及寫入屬於其他應用程式的私用檔案。

所有 Android 應用程式都必須在**androidmanifest.xml**中宣告外部儲存區的兩個許可權其中之一。 若要識別許可權，必須將下列兩個`uses-permission`元素的其中一個新增至**androidmanifest.xml**：

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> 如果使用者`WRITE_EXTERNAL_STORAGE`授與`READ_EXTERNAL_STORAGE` ，則也會以隱含方式授與。 不需要在**androidmanifest.xml**中要求這兩個許可權。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

您也可以使用 [**方案屬性**] 的 [ **Android 資訊清單**] 索引標籤來新增許可權：

![方案總管-Visual Studio 的必要許可權](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

您也可以使用 [**方案屬性] pad**的 [ **Android 資訊清單**] 索引標籤來新增許可權：

[![Solution Pad-Visual Studio for Mac 的必要許可權](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

一般來說，所有危險的許可權都必須由使用者核准。 外部儲存體的許可權是異常的，因為此規則有例外狀況，視應用程式執行的 Android 版本而定：

![外部儲存體許可權檢查的流程圖](./images/external-permission-check-flowchart.png)

如需有關執行執行時間許可權要求的詳細資訊，請參閱在[Xamarin 中的許可權](~/android/app-fundamentals/permissions.md)指南。 **Monodroid 範例** [LocalFiles](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)也會示範執行執行時間許可權檢查的其中一種方式。

#### <a name="granting-and-revoking-permissions-with-adb"></a>使用 ADB 授與及撤銷許可權

在開發 Android 應用程式的過程中，可能需要授與及撤銷許可權，以測試與執行時間許可權檢查相關的各種工作流程。 您可以在命令提示字元中使用 ADB 來執行此動作。 下列命令列程式碼片段示範如何使用 ADB 為其套件名稱為 .com 的 Android 應用程式，授與或撤銷許可權 **。**

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>刪除檔案

任何標準C# api 都可以用來刪除外部儲存體的檔案，例如[`System.IO.File.Delete`](xref:System.IO.File.Delete*)。 您也可以使用 JAVA Api，而犧牲程式碼可攜性。 例如：

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>相關連結

* [Monodroid 上的 Xamarin Android 本機檔案範例 **-範例**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [Xamarin 中的許可權](~/android/app-fundamentals/permissions.md)
