---
title: 使用 Xamarin.Android 的檔案存取
description: 本指南將說明在 Xamarin.Android 中的檔案存取
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: 2978f0b2bcbdd463876784a9addd7dec055b8af9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60949413"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>檔案儲存體和搭配 Xamarin.Android 的存取

Android 應用程式的一個常見需求是操控檔案&ndash;儲存圖片、 下載文件，或將資料匯出至共用與其他程式。 Android （以 Linux 為基礎） 會支援此檔案儲存體提供的空間。 Android 會分成兩種不同的儲存體中的檔案系統：

* **內部儲存體**&ndash;這是檔案系統可存取只能由應用程式或作業系統的一部分。
* **外部儲存體**&ndash;這是檔案的儲存體可由所有應用程式、 使用者和可能是其他裝置存取的資料分割。 在某些裝置，外部儲存體可能會卸除式 （例如 sd 記憶卡）。

這些群組則概念，並不一定是指單一資料分割或裝置上的目錄。 Android 裝置一律會提供資料分割內部儲存體和外部儲存體。 就某些裝置可能會有多個資料分割，會被視為外部儲存體。 無論磁碟分割進行讀取的 Api，撰寫，或建立檔案都相同。 有兩個 Xamarin.Android 應用程式可能使用之檔案存取的 Api 集：

1. **.NET Api （由 單聲道和 Xamarin.Android 所包裝）** &ndash; include[檔案系統協助程式](~/essentials/file-system-helpers.md?context=xamarin/android)所提供[Xamarin.Essentials](~/essentials/index.md?context=xamarin/android)。 .NET Api 提供最佳的跨平台相容性，因此本指南的重點會在這些 Api。
1. **原生的 Java 檔案存取 Api （由 Java 和 Xamarin.Android 所包裝）** &ndash; Java 提供它自己的 Api 來讀取和寫入檔案。 這些是使用.NET Api，完全可以接受替代方式專屬於 Android，但並不適合主要做為跨平台的應用程式。

讀取和寫入檔案是在 Xamarin.Android 中幾乎完全相同，因為它是任何其他.NET 應用程式。 Xamarin.Android 應用程式會判斷檔案要操作，然後使用標準.NET 語言存取檔案的路徑。 因為內部和外部存放裝置的實際路徑可能不同裝置，或從 Android 版本的 Android 版本，不建議以硬編碼檔案的路徑。 相反地，使用 Xamarin.Android Api 來判斷檔案的路徑。 如此一來，用於讀取和寫入檔案的.NET Api 會公開原生 Android Api，將幫助您判斷內部和外部的儲存體上檔案的路徑。

在討論之前涉及檔案存取的 Api，請務必了解一些周圍的內部和外部的儲存體的詳細資料。 這將在下一節中討論。

## <a name="internal-vs-external-storage"></a>內部與外部儲存體

就概念而言，內部儲存體和外部的儲存體是非常類似&ndash;它們是 Xamarin.Android 應用程式可能會將檔案儲存在這兩個位置。 這項相似性可能令人困惑的開發人員不熟悉 Android，因為它不是清除應用程式應該使用內部與外部儲存體時。

Android 配置到作業系統的 Apk，以及個別的應用程式的靜態記憶體是指內部儲存體。 這個空間不能存取由作業系統或應用程式除外。 Android 會配置在內部儲存體的資料分割的目錄，每個應用程式。 解除安裝應用程式時，會保留在該目錄中的內部儲存體的所有檔案也會被都刪除。 內部的儲存體是最適合用於檔案，才可存取應用程式，以及不會共用與其他應用程式或之後解除安裝應用程式時，會有非常小的值。 在 Android 6.0 或更新版本，在內部儲存體上的檔案可能會自動備份利用 Google [Android 6.0 中的自動備份功能](https://developer.android.com/guide/topics/data/autobackup)。 內部儲存體有下列缺點：

* 無法共用檔案。
* 解除安裝應用程式時，將會刪除檔案。
* 可能是有限的內部儲存體上的可用空間。

外部儲存體是指不是內部儲存體的檔案儲存體而無法以獨佔方式存取應用程式。 外部儲存體的主要目的是要提供一個位置來放置的檔案，為了在應用程式之間共用或是太大而無法放入內部儲存體。 外部儲存體的優點是通常會有更多空間供檔案比內部儲存體。 不過，外部儲存體不保證一定要出現在裝置上，而且可能需要特殊存取權限的使用者。

> [!NOTE]
> 針對支援多個使用者的裝置，Android 會提供每個使用者自己的目錄的內部和外部的儲存體。 這個目錄是在裝置上的其他使用者將無法存取。 這種區隔是看不到應用程式，只要他們做不到內部或外部儲存體上的檔案的硬式編碼路徑。

根據經驗法則，Xamarin.Android 應用程式應該偏好時很合理，儲存其內部儲存體上的檔案，並依賴外部儲存體，或檔案時需要與其他應用程式共用、 非常大，即使在解除安裝應用程式應該保留。 比方說，組態檔是最適合用於內部儲存體，因為它有除了不重要，且會建立它的應用程式。  相反地，相片是外部儲存體的良好候選項目。 它們可能會非常大，在許多情況下使用者可能想要共用它們，或存取它們，即使在解除安裝應用程式。

本指南將著重在內部儲存體。 請參閱本指南[外部儲存體](~/android/platform/files/external-storage.md)有關 Xamarin.Android 應用程式中使用外部儲存體詳細資料。

## <a name="working-with-internal-storage"></a>使用內部儲存體

應用程式的內部儲存體目錄取決於作業系統，而且公開給 Android 應用程式`Android.Content.Context.FilesDir`屬性。 這會傳回`Java.IO.File`物件，表示有專用的 Android 專用的應用程式的目錄。  例如，封裝名稱的應用程式**com.companyname**可能的內部儲存體目錄：

```bash
/data/user/0/com.companyname/files
```

本文件將參考的內部儲存體目錄_內部\_儲存體_。

> [!IMPORTANT]
> 內部儲存體目錄的確切路徑而異的裝置到裝置和 Android 版本之間。 基於這個原因，應用程式必須寫程式碼內部的檔案儲存體目錄的路徑並改為使用 Xamarin.Android 的 Api，例如`System.Environment.GetFolderPath()`。

若要最大化程式碼共用，Xamarin.Android 應用程式 （或目標 Xamarin.Android 的 Xamarin.Forms 應用程式） 應該使用[ `System.Environment.GetFolderPath()` ](xref:System.Environment.GetFolderPath*)方法。 在 Xamarin.Android 中，這個方法會傳回值的字串，與位於相同位置的目錄`Android.Content.Context.FilesDir`。 這個方法會採用列舉， `System.Environment.SpecialFolder`，用以識別一組的列舉常數，代表作業系統所使用的特殊資料夾的路徑。 並非所有`System.Environment.SpecialFolder`值會對應到 Xamarin.Android 有效的目錄。 下表描述的指定值的預期會有何種路徑`System.Environment.SpecialFolder`:

| System.Environment.SpecialFolder | 路徑  |
|----------------------|---|
| `ApplicationData` | **_INTERNAL\_STORAGE_/.config** |
| `Desktop` | **_INTERNAL\_STORAGE_/Desktop** |
| `LocalApplicationData` | **_INTERNAL\_STORAGE_/.local/share** |
| `MyDocuments` | **_INTERNAL\_STORAGE_** |
| `MyMusic` | **_INTERNAL\_STORAGE_/Music** |
| `MyPictures` | **_INTERNAL\_STORAGE_/Pictures** |
| `MyVideos` | **_INTERNAL\_STORAGE_/Videos** |
| `Personal` | **_INTERNAL\_STORAGE_** |


### <a name="reading-or-writing-to-files-on-internal-storage"></a>讀取或寫入在內部儲存體上的檔案

任一[C#撰寫的 Api](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file)檔案就已足夠; 只需要為取得配置給應用程式的目錄中檔案的路徑。 強烈建議您使用的非同步版本的.NET Api 用來減少任何可能的問題建立關聯，以封鎖主執行緒的檔案存取權。

此程式碼片段是一個範例中的整數寫入至應用程式的內部儲存體目錄的 utf-8 文字檔案：

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

下一步 的程式碼片段提供一個方式來讀取儲存在文字檔中的整數值：

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

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>使用 Xamarin.Essentials&ndash;檔案系統協助程式

[Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android)撰寫跨平台相容的程式碼是一組 Api。 [檔案系統協助程式](~/essentials/file-system-helpers.md?context=xamarin/android)是包含一系列的簡化尋找應用程式的快取和資料目錄的協助程式類別。 此程式碼片段提供如何尋找應用程式中的內部儲存目錄和快取目錄的範例：

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>隱藏檔案 `MediaStore`

`MediaStore`是 Android 裝置上的 Android 元件，會收集有關 （影片、 音樂、 映像） 的媒體檔案的中繼資料。 其目的是簡化跨所有裝置上的 Android 應用程式的這些檔案共用。

私用的檔案將不會顯示為可共用的媒體中。 比方說，如果應用程式會將其私用的外部儲存體中的圖片，然後該檔案將不會選取媒體掃描器 (`MediaStore`)。

公用檔案將會挑選`MediaStore`。 有零個位元組的檔案名稱的目錄 **。NOMEDIA**就不會掃描由`MediaStore`。

## <a name="related-links"></a>相關連結

* [外部儲存體](~/android/platform/files/external-storage.md)
* [將檔案儲存在裝置儲存空間](https://developer.android.com/training/data-storage/files)
* [Xamarin.Essentials 檔案系統協助程式](~/essentials/file-system-helpers.md?context=xamarin/android)
* [使用自動備份的備份使用者資料](https://developer.android.com/guide/topics/data/autobackup)
* [Adoptable 的儲存體](https://source.android.com/devices/storage/adoptable)
