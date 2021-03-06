---
title: 使用 Xamarin 的檔案存取
description: 本指南將說明 Xamarin 中的檔案存取
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 746433293c52d7071a3289962ea021f716fd0cfe
ms.sourcegitcommit: f7fe46c0236a7130b63a33d9d1670d5111582dd2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186170"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>使用 Xamarin 的檔案儲存和存取

Android 應用程式的常見需求是操作 &ndash; 儲存圖片、下載檔案或匯出資料的檔案，以與其他程式共用。 以 Linux 為基礎的 Android () 藉由提供檔案儲存空間來支援此項。 Android 會將檔案系統分組為兩種不同類型的存放裝置：

* **內部儲存體** &ndash;這是檔案系統的一部分，只有應用程式或作業系統可以存取。
* **外部儲存體** &ndash;這是儲存檔案的磁碟分割，可供所有應用程式、使用者和其他裝置存取。 在某些裝置上，外部存放裝置可能是可移動 (，例如 SD 記憶卡) 。

這些群組僅為概念，不一定會參考裝置上的單一資料分割或目錄。 Android 裝置一律會提供內部儲存體和外部儲存體的磁碟分割。 某些裝置可能會有多個視為外部儲存體的磁碟分割。 無論資料分割為何，讀取、寫入或建立檔案的 Api 都相同。 Xamarin Android 應用程式可能會使用兩組 Api 來進行檔案存取：

1. **.Net api (由 Mono 提供，並由 Xamarin 包裝) ** &ndash; 這些包括[Xamarin](~/essentials/index.md?context=xamarin/android)所提供的[檔案系統](~/essentials/file-system-helpers.md?context=xamarin/android)協助程式。 .NET Api 提供最佳的跨平臺相容性，因此本指南的重點將位於這些 Api。
1. **原始 java 檔案存取 api (由 JAVA 提供，並由 Xamarin 包裝) ** &ndash;JAVA 提供自己的 Api 來讀取和寫入檔案。 這些是完全可接受的 .NET Api 替代方案，但僅適用于 Android，不適用於跨平臺的應用程式。

在 Xamarin 中讀取和寫入檔案幾乎是完全相同的，如同任何其他 .NET 應用程式一樣。 Xamarin 應用程式會決定要操作之檔案的路徑，然後使用標準的 .NET 慣用語來存取檔案。 由於內部和外部儲存體的實際路徑可能會因裝置而異，或從 Android 版本變更為 Android 版本，因此不建議將檔案的路徑硬編碼。 相反地，請使用 Xamarin Api 來判斷檔案的路徑。 如此一來，用來讀取和寫入檔案的 .NET Api 會公開原生 Android Api，以協助判斷內部和外部儲存體上的檔案路徑。

在討論與檔案存取相關的 Api 之前，請務必瞭解內部和外部儲存體的一些詳細資料。 這將在下一節中討論。

## <a name="internal-vs-external-storage"></a>內部與外部儲存體

就概念而言，內部儲存體和外部儲存體非常類似， &ndash; 它們都是 Xamarin android 應用程式可以儲存檔案的地方。 對於不熟悉 Android 的開發人員而言，這種相似性可能會造成混淆，因為當應用程式應該使用內部儲存體與外部儲存體時，並不清楚。

內部儲存體是指 Android 配置給作業系統、Apk 和個別應用程式的非動態記憶體。 除了作業系統或應用程式以外，無法存取此空間。 Android 會在每個應用程式的內部儲存體分割區中配置一個目錄。 卸載應用程式時，也會一併刪除所有存放在該目錄中的內部存放裝置上的檔案。 內部儲存體最適合僅供應用程式存取，且不會與其他應用程式共用，或在應用程式卸載後才會有極小價值的檔案。 在 Android 6.0 或更高版本上，使用[android 6.0 中的自動備份功能](https://developer.android.com/guide/topics/data/autobackup)，Google 可能會自動備份內部儲存體上的檔案。 內部儲存體的缺點如下：

* 檔案無法共用。
* 卸載應用程式時，將會刪除檔案。
* 內部儲存體上的可用空間可能有限。

外部儲存體是指不是內部儲存體，且無法獨佔存取應用程式的檔案儲存體。 外部儲存體的主要目的是要提供一個位置來放置要在應用程式之間共用的檔案，或是太大而無法放入內部存放裝置。 外部儲存體的優點是，它通常會有比內部儲存體更多的檔案空間。 不過，外部儲存體不一定一定會出現在裝置上，而且可能需要使用者的特殊許可權才能存取它。

> [!NOTE]
> 對於支援多個使用者的裝置，Android 會在內部和外部儲存體上，為每個使用者提供自己的目錄。 裝置上的其他使用者無法存取此目錄。 只要應用程式不會將路徑硬式編碼到內部或外部儲存體上的檔案，就不會對其進行區分。

根據經驗法則，Xamarin 應用程式應該偏好在內部儲存體上儲存其檔案（如果有合理），並在需要與其他應用程式共用檔案時依賴外部存放裝置，非常大，或即使應用程式已卸載也應該保留。 例如，設定檔最適合用於內部儲存體，因為它沒有任何重要性，除了建立它的應用程式以外。  相較之下，相片是外部儲存體的理想候選。 它們可能非常龐大，而且在許多情況下，即使應用程式已卸載，使用者還是會想要共用或存取它們。

本指南將著重于內部儲存體。 如需在 Xamarin Android 應用程式中使用外部儲存體的詳細資訊，請參閱[外部儲存體](~/android/platform/files/external-storage.md)指南。

## <a name="working-with-internal-storage"></a>使用內部儲存體

應用程式的內部儲存體目錄是由作業系統所決定，並由屬性公開至 Android 應用程式 `Android.Content.Context.FilesDir` 。 這會傳回 `Java.IO.File` 物件，代表 Android 專門用於應用程式的目錄。  例如，套件名稱為**com**的應用程式，內部儲存體目錄可能是：

```bash
/data/user/0/com.companyname/files
```

本檔會將內部儲存體目錄稱為_內部 \_ 儲存體_。

> [!IMPORTANT]
> 內部儲存體目錄的確切路徑可能會因裝置而異，以及 Android 版本之間的差異。 因此，應用程式不能對內部檔案儲存目錄的路徑進行硬的編碼，而是改為使用像是的 Xamarin. Android Api `System.Environment.GetFolderPath()` 。

若要充分發揮程式碼共用、Xamarin Android 應用程式 (或 Xamarin。 Android) 的表單應用程式應使用 [`System.Environment.GetFolderPath()`](xref:System.Environment.GetFolderPath*) 方法。 在 Xamarin 中，這個方法會傳回與相同位置之目錄的字串 `Android.Content.Context.FilesDir` 。 這個方法會採用列舉， `System.Environment.SpecialFolder` 這是用來識別代表作業系統所使用之特殊資料夾路徑的一組列舉常數。 並非所有的 `System.Environment.SpecialFolder` 值都會對應到 Xamarin 上的有效目錄。 下表描述給定值可能預期的路徑 `System.Environment.SpecialFolder` ：

| System.Environment.SpecialFolder | 路徑  |
|----------------------|---|
| `ApplicationData` | **_內部 \_儲存體_/.config** |
| `Desktop` | **_內部 \_儲存體_/Desktop** |
| `LocalApplicationData` | **_內部 \_儲存體_/.local/share** |
| `MyDocuments` | **_內部 \_ 儲存體_** |
| `MyMusic` | **_內部 \_儲存體_/Music** |
| `MyPictures` | **_內部 \_儲存體_/Pictures** |
| `MyVideos` | **_內部 \_儲存體_/Videos** |
| `Personal` | **_內部 \_ 儲存體_** |
| `Fonts` | **_內部 \_儲存體_/.fonts** |
| `Templates` | **_內部 \_儲存體_/Templates** |
| `CommonApplicationData` | **/usr/share** |
| `CommonApplicationData` | **/usr/share** |

### <a name="reading-or-writing-to-files-on-internal-storage"></a>讀取或寫入內部儲存體上的檔案

任何用來[寫入檔案的 c # api](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file)都已經足夠;所有必要動作都是取得配置給應用程式之目錄中的檔案路徑。 強烈建議使用 .NET Api 的非同步版本，將任何可能與封鎖主執行緒的檔案存取相關聯的問題降至最低。

此程式碼片段是將整數寫入 UTF-8 文字檔到應用程式內部儲存體目錄的其中一個範例：

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

下一個程式碼片段提供一種方法來讀取儲存在文字檔中的整數值：

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

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>使用 Xamarin &ndash; 檔案系統協助程式

[Xamarin](~/essentials/file-system-helpers.md?context=xamarin/android)是一組 api，可用於撰寫跨平臺相容的程式碼。 [檔案系統](~/essentials/file-system-helpers.md?context=xamarin/android)helper 是一個類別，其中包含一系列協助程式，可簡化尋找應用程式的快取和資料目錄。 此程式碼片段提供一個範例，示範如何尋找應用程式的內部儲存體目錄和快取目錄：

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>隱藏檔案的來源`MediaStore`

`MediaStore`是一個 android 元件，它會收集關於媒體檔案的中繼資料， (在 Android 裝置上) 的影片、音樂、影像。 其目的是簡化在裝置上的所有 Android 應用程式共用這些檔案。

私人檔案不會顯示為可共用的媒體。 例如，如果應用程式將圖片儲存到其私用外部存放裝置，則媒體掃描器 () 不會挑選該檔案 `MediaStore` 。

公用檔案將由挑選 `MediaStore` 。 包含零個位元組檔案名的目錄 **。NOMEDIA**不會由掃描 `MediaStore` 。

## <a name="related-links"></a>相關連結

* [外部儲存體](~/android/platform/files/external-storage.md)
* [將檔案儲存在裝置存放裝置上](https://developer.android.com/training/data-storage/files)
* [Xamarin Essentials 檔案系統協助程式](~/essentials/file-system-helpers.md?context=xamarin/android)
* [使用自動備份來備份使用者資料](https://developer.android.com/guide/topics/data/autobackup)
* [Adoptable 儲存體](https://source.android.com/devices/storage/adoptable)
