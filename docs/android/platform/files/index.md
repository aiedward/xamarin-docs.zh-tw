---
title: 使用 Xamarin.安卓的檔案存取
description: 本指南將解釋 Xamarin.安卓中的檔案訪問
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 1bb0fae73a1e3647cdc0e3266c7b44ac04fcc1ee
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304405"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>檔案儲存和存取與Xamarin.安卓

Android 應用的常見要求是&ndash;操作 檔保存圖片、下載文檔或匯出數據以與其他程序共用。 Android(基於Linux)通過提供檔存儲空間來支援這一點。 Android 將檔案系統群組到兩種不同類型的儲存中:

* **內部存儲**&ndash;這是檔案系統的一部分,只能由應用程式或操作系統訪問。
* **外部存儲**&ndash;這是用於存儲檔分區,所有應用、使用者可能以及其他設備都可以訪問這些檔。 在某些設備上,外部存儲可能是可移動的(如 SD 卡)。

這些分組僅是概念性的,不一定引用設備上的單個分區或目錄。 Android 設備將始終為內部存儲和外部存儲提供分區。 某些設備可能有多個分區被視為外部存儲。 無論用於讀取、寫入或創建檔的 API 是什麼分區,都是相同的。 Xamarin.Android 應用程式可以使用兩組 API 進行檔案存取:

1. **.NET API(由 Mono 提供,由 Xamarin.Android 包裝)**&ndash;這些包括[Xamarin](~/essentials/index.md?context=xamarin/android)提供的[檔案系統協助器](~/essentials/file-system-helpers.md?context=xamarin/android)。 .NET API 提供最佳的跨平臺相容性,因此本指南的重點將放在這些 API 上。
1. **本機 Java 檔案存取 API(由 Java 提供並由 Xamarin.Android 包裝)Java**&ndash;提供了用於讀取和寫入檔自己的 API。 這些是 .NET API 完全可接受的替代方案,但特定於 Android,不適合旨在跨平臺的應用。

在 Xamarin.Android 中,讀取和寫入文件幾乎與任何其他 .NET 應用程式相同。 Xamarin.Android 應用確定要操作的檔的路徑,然後使用標準的 .NET 習慣用法進行文件訪問。 由於內部和外部存儲的實際路徑可能因設備而異,或者從 Android 版本到 Android 版本,因此不建議對檔的路徑進行硬編碼。 相反,請使用 Xamarin.Android API 來確定檔的路徑。 這樣,用於讀取和寫入檔的 .NET API 會公開本機 Android API,這將有助於確定內部和外部存儲上檔的路徑。

在討論文件訪問所涉及的 API 之前,瞭解內部和外部存儲的一些詳細資訊非常重要。 下一節將討論這一點。

## <a name="internal-vs-external-storage"></a>內部儲存與外部儲存

從概念上講,內部存儲和外部存儲非常相似&ndash;,它們都是 Xamarin.Android 應用程式可以保存檔的地方。 這種相似性可能會混淆開發人員誰不熟悉Android,因為不清楚什麼時候應用應該使用內部存儲與外部存儲。

內部存儲是指 Android 分配給作業系統、APK 和各個應用的非易失性記憶體。 除作業系統或應用外,無法訪問此空間。 Android將在每個應用的內部存儲分區中分配一個目錄。 卸載應用後,該目錄中保存在內部存儲中的所有檔也將被刪除。 內部存儲最適合僅對應用訪問且不會與其他應用共用的檔,或者一旦應用卸載,其價值將非常小。 在 Android 6.0 或更高版本上,Google 可能會使用[Android 6.0 中的自動備份功能](https://developer.android.com/guide/topics/data/autobackup)自動備份內部存儲上的檔。 內部儲存有以下缺點:

* 無法共用檔。
* 卸載應用時,檔將被刪除。
* 內部存儲上的空間可能有限。

外部存儲是指不是內部存儲且應用程序無法獨佔的檔案存儲。 外部存儲的主要目的是提供一個位置,用於放置旨在在應用之間共用的檔或太大而不適合內部存儲的檔。 外部存儲的優點是,它通常比內部存儲有更多的文件空間。 但是,外部存儲並不總是保證存在於設備上,並且可能需要使用者的特殊許可權才能訪問它。

> [!NOTE]
> 對於支援多個使用者的設備,Android 將在內部和外部存儲上為每個使用者提供自己的目錄。 設備上的其他使用者無法訪問此目錄。 只要應用不硬編碼到內部或外部存儲上的檔的路徑,這種分離對應用是不可見的。

根據經驗,Xamarin.Android 應用應該喜歡在合理時將其檔保存在內部存儲上,並且當檔需要與其他應用共用時,依賴於外部存儲,檔非常大,或者即使卸載了應用,也應該保留。 例如,配置檔最適合內部存儲,因為它除了創建它的應用之外沒有重要性。  相比之下,照片是外部存儲的好人選。 它們可能非常大,在許多情況下,即使應用已卸載,使用者可能也希望共用它們或訪問它們。

本指南將重點介紹內部存儲。 有關在 Xamarin.Android 應用程式中使用外部存儲的詳細資訊,請參閱[外部存儲](~/android/platform/files/external-storage.md)指南。

## <a name="working-with-internal-storage"></a>使用內部儲存

應用程式的內部儲存目錄由操作系統確定,並由`Android.Content.Context.FilesDir`屬性向 Android 應用公開。 這將返回表示`Java.IO.File`Android 專用於應用的目錄的物件。  例如,具有套件**名稱 com.公司名稱**的應用程式的內部儲存目錄可能是:

```bash
/data/user/0/com.companyname/files
```

此文件將內部儲存目錄是_內部\_儲存_。

> [!IMPORTANT]
> 內部存儲目錄的確切路徑可能因設備而異,在Android版本之間有所不同。 因此,應用程式不得對內部檔儲存目錄的路徑進行硬編碼,而是使用 Xamarin.Android API,如`System.Environment.GetFolderPath()`。

為了最大化代碼共用,Xamarin.Android 應用(或針對 Xamarin.Android 的 Xamarin.Forms[`System.Environment.GetFolderPath()`](xref:System.Environment.GetFolderPath*)應用程式)應使用該方法。 在 Xamarin.Android 中,此方法將`Android.Content.Context.FilesDir`返回與 的位置相同的目錄的字串。 此方法採用枚舉`System.Environment.SpecialFolder`, 用於識別一組表示作業系統使用的特殊資料夾的路徑的枚舉常量。 並非所有`System.Environment.SpecialFolder`值都將映射到 Xamarin.Android 上的有效目錄。 下表描述了對於`System.Environment.SpecialFolder`給定值 :

| System.Environment.SpecialFolder | Path  |
|----------------------|---|
| `ApplicationData` | **_內部\_儲存_/.設定** |
| `Desktop` | **_內部\_儲存_/桌面** |
| `LocalApplicationData` | **_內部\_儲存_/.本地/共用** |
| `MyDocuments` | **_內部\_儲存_** |
| `MyMusic` | **_內部\_儲存_/音樂** |
| `MyPictures` | **_內部\_儲存_/圖片** |
| `MyVideos` | **_內部\_儲存_/視訊** |
| `Personal` | **_內部\_儲存_** |

### <a name="reading-or-writing-to-files-on-internal-storage"></a>讀取或寫入內部儲存的檔案

[任何用於寫入檔的 C# API](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file)都就足夠了;只需要獲取分配給應用程式的目錄中的檔的路徑。 強烈建議使用 .NET API 的非同步版本來最小化可能與阻止主線程的檔案存取相關的任何問題。

此程式碼是將整數寫入 UTF-8 文字檔案到應用程式的內部儲存目錄的範例:

```csharp
public async Task SaveCountAsync(int count)
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");
    using (var writer = File.CreateText(backingFile))
    {
        await writer.WriteLineAsync(count.ToString());
    }
}
```

下一個程式碼段提供讀取儲存在文字檔中的整數值的方法:

```csharp
public async Task<int> ReadCountAsync()
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");

    if (backingFile == null || !File.Exists(backingFile))
    {
        return 0;
    }

    var count = 0;
    using (var reader = new StreamReader(backingFile, true))
    {
        string line;
        while ((line = await reader.ReadLineAsync()) != null)
        {
            if (int.TryParse(line, out var newcount))
            {
                count = newcount;
            }
        }
    }

    return count;
}
```

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>使用 Xamarin.&ndash;基本 檔案系統說明器

[Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android)是一組用於編寫跨平臺相容代碼的 API。 [檔案系統協助器](~/essentials/file-system-helpers.md?context=xamarin/android)是一個類,它包含一系列幫助器,以簡化應用程式的快取和資料目錄的定位。 此程式碼段提供如何尋找應用程式的內部儲存目錄與快取目錄的範例:

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>從`MediaStore`

是`MediaStore`一個 Android 元件,用於收集有關 Android 設備上的媒體檔(視頻、音樂、圖像)的中繼資料。 其目的是簡化這些檔在設備上的所有 Android 應用中的共用。

私有檔不會作為可共用媒體顯示。 例如,如果應用將圖片保存到其專用外部存儲,則媒體掃描程式 ()`MediaStore`不會拾取該檔。

公開檔案將被拾取`MediaStore`。 具有零位元組檔案名稱的目錄 **。NOMEDIA**不會由`MediaStore`掃描 。

## <a name="related-links"></a>相關連結

* [外部儲存體](~/android/platform/files/external-storage.md)
* [儲存檔案儲存檔案](https://developer.android.com/training/data-storage/files)
* [Xamarin.基本檔案系統協助器](~/essentials/file-system-helpers.md?context=xamarin/android)
* [使用自動備份備份使用者資料](https://developer.android.com/guide/topics/data/autobackup)
* [可採用儲存](https://source.android.com/devices/storage/adoptable)
