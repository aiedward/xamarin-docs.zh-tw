---
title: 如何手動安裝 Xamarin.Android.Support 套件所需的 Android 支援程式庫？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 84ee33fe174c01656144e55bc3cbba7c773950fd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153423"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>如何手動安裝 Xamarin.Android.Support 套件所需的 Android 支援程式庫？

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Xamarin.Android.Support.v4 的範例步驟 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

下載所需的 Xamarin.Android.Support NuGet 套件 （例如藉由使用 NuGet 套件管理員，請安裝它）。

使用`ildasm`若要檢查的版本**android_m2repository.zip** NuGet 套件需要：

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```
範例輸出：

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

下載**android\_m2repository.zip** Google 使用 URL 從傳回**ildasm**。 或者，您可以檢查哪一個版本的_Android Support Repository_您目前已安裝 Android SDK 管理員：

![[顯示 Android Support Repository 版本安裝的 32 android SDK 管理員]](install-android-support-library-images/sdk-extras.png)

如果版本符合您需要適用於 NuGet 套件，您不需要下載任何新項目。 您可以改為重新壓縮現有**m2repository**之下的目錄**extras\\android**中_SDK 路徑_（如下所示的 Android 頂端SDK 管理員 視窗）。

計算的 MD5 雜湊從傳回的 URL **ildasm**。 格式化產生使用全部大寫的字母和不含空格的字串。 比方說，調整`$url`變數所需，且然後執行下列 2 個幾行 (根據[原始C#程式碼從 Xamarin.Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)) 在 PowerShell 中：

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```
範例輸出：

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

複製**android\_m2repository.zip**成 **%LOCALAPPDATA%\\Xamarin\\zips\\** 資料夾。 若要使用從上一個計算步驟的 MD5 雜湊的 MD5 雜湊檔案重新命名。 例如：

**%LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF.zip**

（選擇性）將檔案解壓縮 **%LOCALAPPDATA%\\Xamarin\\Xamarin.Android.Support.v4\\23.4.0.0\\內容\\** (建立**內容\\m2repository**子目錄)。 如果您略過此步驟中，然後使用程式庫的第一個組建會較長的時間因為它必須完成此步驟。
子目錄的版本號碼 (**23.4.0.0**在此範例中) 不是 NuGet 套件版本完全相同。 您可以使用`ildasm`來尋找正確的版本號碼：

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```
範例輸出：

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

下載所需的 Xamarin.Android.Support NuGet 套件 （例如藉由使用 NuGet 套件管理員，請安裝它）。

按兩下_Xamarin.Android.Support.v4_下方的組件_參考_的 Visual Studio for Mac 將會在組件瀏覽器中開啟組件中的 Android 專案的一節。 請確認_語言_下拉式清單設為_C#_ ，然後選取最上層_Xamarin.Android.Support.v4_從組件瀏覽器瀏覽樹狀目錄的組件. 找出`SourceUrl`下的其中一個屬性`IncludeAndroidResourcesFrom`或`JavaLibraryReference`屬性：

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

下載**android\_m2repository.zip**使用 Google`SourceUrl`傳回**ildasm**。 或者，您可以檢查哪一個版本的_Android Support Repository_您目前已安裝 Android SDK 管理員：

![[顯示 Android Support Repository 版本安裝的 32 android SDK 管理員]](install-android-support-library-images/sdk-extras.png)

如果版本符合您需要適用於 NuGet 套件，您不需要下載任何新項目。 您可以改為重新壓縮現有**m2repository**之下的目錄**extras/android**中_SDK 路徑_（如下所示的 [Android SDK 管理員] 視窗頂端）.

計算的 MD5 雜湊從傳回的 URL **ildasm**。 格式化產生使用全部大寫的字母和不含空格的字串。 比方說，調整 為所需的 URL 字串，然後執行下列命令中**Terminal.app**命令提示字元：

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

另一個選項是使用`csharp`解譯器以執行[相同C#本身的 Xamarin.Android 所使用的程式碼](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)。
若要這樣做，請調整`url`變數所需，且在中，然後執行下列命令**Terminal.app**命令提示字元：

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```
範例輸出：

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

複製**android\_m2repository.zip**要 **$HOME/.local/share/Xamarin/zips/** 資料夾。 若要使用從上一個計算步驟的 MD5 雜湊的 MD5 雜湊檔案重新命名。 例如: 

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

（選擇性）將解壓縮到檔案： 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

(建立**內容/m2repository**子目錄)。 如果您略過此步驟中，然後使用程式庫的第一個組建會較長的時間因為它必須完成此步驟。

子目錄的版本號碼 (**23.4.0.0**在此範例中) 不是 NuGet 套件版本完全相同。 依照**ildasm**稍早步驟中，您也可以在 Visual Studio for Mac 中使用組件瀏覽器，尋找正確的版本號碼。 尋求`Version`下的其中一個屬性`IncludeAndroidResourcesFrom`或`JavaLibraryReference`屬性：

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----


## <a name="additional-references"></a>其他參考資料

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) – 錯誤"下載失敗。 請下載{0}並將它放到{1}目錄。 」 和 「 請安裝套件: '{0}' SDK 安裝程式中，您可以使用 「 Xamarin.Android.Support 套件相關的錯誤訊息

### <a name="next-steps"></a>後續步驟

本文件討論截至年 2016年目前的行為。 這份文件中所述的技巧不是 Xamarin 的套件，穩定的測試套件的一部分，因此它可能在未來會中斷。

如需進一步協助，請連絡我們，或如果此問題持續，即使利用上述的資訊，請參閱[適用於 Xamarin 有哪些支援選項？](~/cross-platform/troubleshooting/support-options.md)如需連絡選項，建議，以及如何如有需要請提出新錯誤報告。

