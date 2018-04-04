---
title: 如何手動安裝 Xamarin.Android.Support 套件所需的 Android 支援程式庫？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: e760a87cbd1e0220ed5cf3a350d3539ffe29650e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>如何手動安裝 Xamarin.Android.Support 套件所需的 Android 支援程式庫？

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Xamarin.Android.Support.v4 範例步驟 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

下載所需的 Xamarin.Android.Support NuGet 封裝 （例如藉由使用 NuGet 封裝管理員安裝）。

使用`ildasm`檢查哪一個版本的**android_m2repository.zip**需要 NuGet 封裝：

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```
範例輸出：

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

下載**android\_m2repository.zip** google 使用 URL 從傳回**ildasm**。 或者，您可以檢查哪一個版本的_Android 支援儲存機制_目前已安裝在 Android SDK 管理員：

![「 顯示 Android 支援儲存機制版本安裝的 32 android SDK Manager 」](install-android-support-library-images/sdk-extras.png)

如果版本符合您需要為 NuGet 套件，您不需要下載任何新項目。 您可以改為重新壓縮現有**m2repository**之下的目錄**extras\\android**中_SDK 路徑_（如所示的 Android 頂端SDK Manager 視窗）。

計算所傳回的 URL 的 MD5 雜湊**ildasm**。 格式產生的字串，以使用全部大寫的字母和不能有空格。 例如，調整`$url`變數所需，且然後執行下列 2 行 (根據[C# 中的原始程式碼 Xamarin.Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) 在 PowerShell 中：

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```
範例輸出：

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

複製**android\_m2repository.zip**到**%LOCALAPPDATA%\\Xamarin\\送達\\**資料夾。 若要使用從先前的 MD5 雜湊計算步驟的 MD5 雜湊檔案重新命名。 例如: 

**%LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

（選擇性）解壓縮檔案貼入**%LOCALAPPDATA%\\Xamarin\\Xamarin.Android.Support.v4\\23.4.0.0\\內容\\** (建立**內容\\m2repository**子目錄)。 如果您略過此步驟中，然後使用該程式庫的第一個組建需要較長的時間因為它將需要完成這個步驟。
子目錄的版本號碼 (**23.4.0.0**在此範例中) 不是 NuGet 封裝版本完全相同。 您可以使用`ildasm`來尋找正確的版本號碼：

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```
範例輸出：

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

下載所需的 Xamarin.Android.Support NuGet 封裝 （例如藉由使用 NuGet 封裝管理員安裝）。

按兩下_Xamarin.Android.Support.v4_組件_參考_區段適用於 Mac 的組件瀏覽器中開啟組件的 Visual Studio 中的 Android 專案。 請確認_語言_下拉式清單設定為_C#_和選取最上層_Xamarin.Android.Support.v4_從組件的瀏覽器瀏覽樹狀目錄的組件。 找出`SourceUrl`下的其中一個屬性`IncludeAndroidResourcesFrom`或`JavaLibraryReference`屬性：

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

下載**android\_m2repository.zip** Google 使用`SourceUrl`從傳回**ildasm**。 或者，您可以檢查哪一個版本的_Android 支援儲存機制_目前已安裝在 Android SDK 管理員：

![「 顯示 Android 支援儲存機制版本安裝的 32 android SDK Manager 」](install-android-support-library-images/sdk-extras.png)

如果版本符合您需要為 NuGet 套件，您不需要下載任何新項目。 您可以改為重新壓縮現有**m2repository**之下的目錄**extras/android**中_SDK 路徑_（如所示視窗頂端的 Android SDK Manager）.

計算所傳回的 URL 的 MD5 雜湊**ildasm**。 格式產生的字串，以使用全部大寫的字母和不能有空格。 例如，調整為所需的 URL 字串和中執行下列命令**Terminal.app**命令提示字元：

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

另一個選項是使用`csharp`解譯器執行[相同 C# 程式碼會使用本身的 Xamarin.Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)。
若要這樣做，請調整`url`變數所需，且在中，然後執行下列命令**Terminal.app**命令提示字元：

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```
範例輸出：

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

複製**android\_m2repository.zip**至**$HOME/.local/share/Xamarin/zips/**資料夾。 若要使用從先前的 MD5 雜湊計算步驟的 MD5 雜湊檔案重新命名。 例如: 

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

（選擇性）解壓縮到檔案： 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(建立**內容/m2repository**子目錄)。 如果您略過此步驟中，然後使用該程式庫的第一個組建需要較長的時間因為它將需要完成這個步驟。

子目錄的版本號碼 (**23.4.0.0**在此範例中) 不是 NuGet 封裝版本完全相同。 在**ildasm**先前步驟中，您也可以在 Visual Studio for Mac 使用組件的瀏覽器，尋找正確的版本號碼。 尋找`Version`下的其中一個屬性`IncludeAndroidResourcesFrom`或`JavaLibraryReference`屬性：

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----


## <a name="additional-references"></a>其他參考資料

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – 錯誤"下載失敗。 請下載 {0} 並將它放到 {1} 目錄。" 和 「 請安裝套件: '{0}' 用於 SDK 安裝程式 」 與 Xamarin.Android.Support 封裝相關的錯誤訊息

### <a name="next-steps"></a>後續步驟

本文將討論從 2016 年 8 月開始目前的行為。 這份文件中所述的技巧不屬於 xamarin，穩定的測試套件，讓它在未來將會中斷。

如需進一步協助，請連絡我們，或如果此問題仍然發生即使利用上述的資訊，請參閱[xamarin 有哪些支援的選項？](~/cross-platform/troubleshooting/support-options.md)有關連絡人的選項，建議，以及如何如有需要的檔案新的 bug。

