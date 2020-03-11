---
title: 如何手動安裝 Xamarin.Android.Support 套件所需的 Android 支援程式庫？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A9CB8CA8-8A6D-405E-B84C-A16CE452C0F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 99571e0b62592597bb1fffdc8d3ed8336fe050b2
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73026932"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>如何手動安裝 Xamarin.Android.Support 套件所需的 Android 支援程式庫？

## <a name="example-steps-for-xamarinandroidsupportv4"></a>支援 v4 的範例步驟 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

下載所需的 Xamarin 支援 NuGet 套件（例如，使用 NuGet 套件管理員安裝它）。

使用 `ildasm` 來檢查 NuGet 套件所需的**android_m2repository .zip**版本：

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```

範例輸出︰

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

使用從**ildasm**傳回的 URL，從 Google 下載**android\_m2repository。** 或者，您可以檢查 Android SDK Manager 中目前安裝的_Android 支援儲存_機制版本：

![「Android SDK Manager 顯示 Android 支援存放庫32版已安裝」](install-android-support-library-images/sdk-extras.png)

如果版本與您所需的 NuGet 套件相符，則您不需要下載任何新的專案。 您可以改為在_SDK 路徑_中，將位於 [**額外\\android** ] 底下的現有**m2repository**目錄重新壓縮（如 [Android SDK 管理員] 視窗的頂端所示）。

計算從**ildasm**傳回之 URL 的 MD5 雜湊。 將產生的字串格式化，以使用全部大寫字母且不含空格。 例如，視需要調整 `$url` 變數，然後在 PowerShell 中執行下列2行（根據[Xamarin 中的C#原始程式碼](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)）：

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```

範例輸出︰

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

將**android\_m2repository**複製到 **% LOCALAPPDATA%\\Xamarin\\zips\\** 資料夾。 將檔案重新命名，以使用先前的 MD5 雜湊計算步驟中的 MD5 雜湊。 例如：

**% LOCALAPPDATA%\\Xamarin\\zips\\F16A3455987DBAE5783F058F19F7FCDF .zip**

選擇性將檔案解壓縮至 **% LOCALAPPDATA%\\xamarin\\xamarin. m2repository. v4\\23.4.0.0\\內容\\** （建立**內容\\** 子目錄）。 如果您略過此步驟，則使用該程式庫的第一個組建將需要較長的時間，因為它需要完成此步驟。
子目錄的版本號碼（在此範例中為**23.4.0.0** ）與 NuGet 套件版本不相同。 您可以使用 `ildasm` 來尋找正確的版本號碼：

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr /C:"string 'Version'"
```

範例輸出︰

```cmd
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
property string 'Version' = string('23.4.0.0')}
```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

下載所需的 Xamarin 支援 NuGet 套件（例如，使用 NuGet 套件管理員安裝它）。

在 Visual Studio for Mac 的 Android 專案的 [_參考_] 區段下，按兩下 [ _Xamarin. Support._ ]，以在 [元件瀏覽器] 中開啟元件。 確定 [_語言_] 下拉式的設定為_C#_ ，並從 [元件瀏覽器] 導覽樹狀目錄中選取最上層的 [ _Xamarin_ ]。 找出其中一個 `IncludeAndroidResourcesFrom` 或 `JavaLibraryReference` 屬性底下的 [`SourceUrl`] 屬性：

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

使用從**ildasm**傳回的 `SourceUrl`，從 Google 下載**android\_m2repository。** 或者，您可以檢查 Android SDK Manager 中目前安裝的_Android 支援儲存_機制版本：

![「Android SDK Manager 顯示 Android 支援存放庫32版已安裝」](install-android-support-library-images/sdk-extras.png)

如果版本與您所需的 NuGet 套件相符，則您不需要下載任何新的專案。 您可以改為在_SDK 路徑_中，將位於 [**額外/android** ] 底下的現有**m2repository**目錄重新壓縮（如 [Android SDK 管理員] 視窗的頂端所示）。

計算從**ildasm**傳回之 URL 的 MD5 雜湊。 將產生的字串格式化，以使用全部大寫字母且不含空格。 例如，視需要調整 URL 字串，然後在終端機中執行下列命令 **。應用程式**命令提示字元：

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

另一個選項是使用 `csharp` 解譯器來執行[Xamarin Android C#本身所使用的相同程式碼](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)。
若要這麼做，請視需要調整 `url` 變數，然後在終端機中執行下列命令 **。應用程式**命令提示字元：

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```

範例輸出︰

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

將**android\_m2repository**複製到 **$HOME/.local/share/xamarin/zips/** 資料夾。 將檔案重新命名，以使用先前的 MD5 雜湊計算步驟中的 MD5 雜湊。 例如：

**$HOME/.local/share/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

選擇性將檔案解壓縮至： 

**$HOME/.local/share/Xamarin/Xamarin.Android.Support.v4/23.4.0.0/content/**

（建立**content/m2repository**子目錄）。 如果您略過此步驟，則使用該程式庫的第一個組建將需要較長的時間，因為它需要完成此步驟。

子目錄的版本號碼（在此範例中為**23.4.0.0** ）與 NuGet 套件版本不相同。 如同稍早在**ildasm**步驟中，您可以使用 Visual Studio for Mac 中的元件瀏覽器來尋找正確的版本號碼。 在其中一個 `IncludeAndroidResourcesFrom` 或 `JavaLibraryReference` 屬性下尋找 `Version` 屬性：

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----

## <a name="additional-references"></a>其他參考

- [Bug 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) –不正確的下載失敗。 請下載 {0} 並將它放到 {1} 目錄。」 和支援套件相關錯誤訊息中的「請安裝套件： '{0}'，SDK 安裝程式中提供

### <a name="next-steps"></a>後續步驟

本檔討論2016年8月起的目前行為。 本檔中所述的技術不是適用于 Xamarin 的穩定測試套件的一部分，因此未來可能會中斷。

如需進一步的協助，請洽詢我們，或即使在使用上述資訊之後仍然會發生此問題，請參閱適用[于 Xamarin 的支援選項？](~/cross-platform/troubleshooting/support-options.md)以取得連絡人選項、建議的相關資訊，以及如何在需要時提出新的 bug。
