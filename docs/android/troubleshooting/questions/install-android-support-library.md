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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026932"
---
# <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>如何手動安裝 Xamarin.Android.Support 套件所需的 Android 支援程式庫？

## <a name="example-steps-for-xamarinandroidsupportv4"></a>Xamarin.安卓.支援.v4 的示例步驟 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

下載所需的 Xamarin.Android.支援 NuGet 包(例如,透過使用 NuGet 套件管理器安裝它)。

檢查`ildasm`NuGet 套件需要哪個版本的**android_m2repository.zip:**

```cmd
ildasm /caverbal /text /item:Xamarin.Android.Support.v4 packages\Xamarin.Android.Support.v4.23.4.0.1\lib\MonoAndroid403\Xamarin.Android.Support.v4.dll | findstr SourceUrl
```

範例輸出︰

```cmd
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
property string 'SourceUrl' = string('https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip')
```

下載**\_android m2repository.zip**從谷歌使用從**ildasm**返回的網址。 或者,您可以檢查當前在 Android SDK 管理員中安裝的_Android 支援儲存庫_的版本:

!["Android SDK 管理器顯示安裝了 Android 支援存儲庫版本 32"](install-android-support-library-images/sdk-extras.png)

如果版本與 NuGet 套件所需的版本匹配,則無需下載任何新內容。 相反,您可以重新壓縮位於_SDK 路徑_中**\\額外 android**下的現有**m2 儲存庫**目錄(如 Android SDK 管理員視窗的頂部所示)。

計算從**ildasm**傳回的 URL 的 MD5 哈希。 格式化生成的字串以使用所有大寫字母,無需空格。 例如,根據需要調整變數`$url`,然後在 PowerShell 中運行以下 2 行(基於[來自 Xamarin.Android](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)的原始 C# 程式碼):

```powershell
$url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"
(([System.Security.Cryptography.MD5]::Create()).ComputeHash([System.Text.Encoding]::UTF8.GetBytes($url)) | %{ $_.ToString("X02") }) -join ""
```

範例輸出︰

```powershell
F16A3455987DBAE5783F058F19F7FCDF
```

將**\_android m2repository.zip**複製到 **%LOCALAPPDATA%\\\\Xamarin zips\\**資料夾。 重新命名檔案以使用上一個 MD5 哈希計算步驟中的 MD5 哈希。 例如：

**%本地APPDATA%\\Xamarin\\\\拉鍊 F16A3455987DBAE5783F058F19F7FCDF.zip**

( 選擇性的 )將檔案解壓縮到**\\%LOCALAPPDATA% Xamarin\\Xamarin.Android.support.v4\\23.4.0.0\\內容\\**(創建內容**\\m2儲存庫**子目錄)。 如果跳過此步驟,則使用庫的第一個生成將需要更長的時間,因為它需要完成此步驟。
子目錄的版本號(本例中**為 23.4.0.0)** 與 NuGet 包版本不同。 您可以使用`ildasm`尋找正確的版本號:

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

下載所需的 Xamarin.Android.支援 NuGet 包(例如,透過使用 NuGet 套件管理器安裝它)。

按兩下_Xamarin.Android.Support.v4_元件,在 Visual Studio 中的 Android 專案的_參考_部分下為 Mac 打開程式集瀏覽器中的程式集。 確保 _「語言_」下拉選單設置為_C#,_ 並從程式集瀏覽器導航樹中選擇頂級_Xamarin.Android.support.v4_程式集。 在`SourceUrl``IncludeAndroidResourcesFrom``JavaLibraryReference`或 屬性的一下尋找屬性:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

下載**\_android m2repository.zip**`SourceUrl`從谷歌使用 從**ildasm**返回。 或者,您可以檢查當前在 Android SDK 管理員中安裝的_Android 支援儲存庫_的版本:

!["Android SDK 管理器顯示安裝了 Android 支援存儲庫版本 32"](install-android-support-library-images/sdk-extras.png)

如果版本與 NuGet 套件所需的版本匹配,則無需下載任何新內容。 相反,您可以重新壓縮位於_SDK 路徑_中**附加功能/android**下的現有**m2 儲存庫**目錄(如 Android SDK 管理器視窗的頂部所示)。

計算從**ildasm**傳回的 URL 的 MD5 哈希。 格式化生成的字串以使用所有大寫字母,無需空格。 例如,根據需要調整 URL 字串,然後在**終端.app**命令提示符中執行以下命令:

```bash
echo -n "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip" | md5 | tr '[:lower:]' '[:upper:]'
```

另一個選項是使用`csharp`直譯器執行與[Xamarin.Android 本身使用的 C# 程式碼相同的 C# 程式碼](https://github.com/xamarin/xamarin-android/blob/8e8a4dd90f26eb39172876cc52181b6639e20524/src/Xamarin.Android.Build.Tasks/Tasks/GetAdditionalResourcesFromAssemblies.cs#L208)。
為此,請根據需要調整變數`url`,然後在**終端.app**命令提示符中運行以下命令:

```bash
csharp -e 'var url = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip"; string.Concat((System.Security.Cryptography.MD5.Create().ComputeHash(System.Text.Encoding.UTF8.GetBytes(url))).Select(b => b.ToString("X02")))'
```

範例輸出︰

```bash
F16A3455987DBAE5783F058F19F7FCDF
```

將**\_android m2repository.zip**複製到 **$HOME/.本地/共用/Xamarin/zips/** 資料夾。 重新命名檔案以使用上一個 MD5 哈希計算步驟中的 MD5 哈希。 例如：

**$HOME/.本地/股票/Xamarin/zips/F16A3455987DBAE5783F058F19F7FCDF.zip**

( 選擇性的 )解壓縮檔案到: 

**$HOME/.本地/股票/Xamarin/Xamarin.安卓.支援.v4/23.4.0.0/內容/**

(創建**內容/m2儲存庫**子目錄)。 如果跳過此步驟,則使用庫的第一個生成將需要更長的時間,因為它需要完成此步驟。

子目錄的版本號(本例中**為 23.4.0.0)** 與 NuGet 包版本不同。 與前面的**ildasm**步驟一樣,您可以使用 Visual Studio 中的 Mac 程式集瀏覽器查找正確的版本號。 在`Version``IncludeAndroidResourcesFrom``JavaLibraryReference`或 屬性的一下尋找屬性:

```csharp
[assembly: IncludeAndroidResourcesFrom ("./", PackageName = "Xamarin.Android.Support.v4", SourceUrl = "https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip", EmbeddedArchive = "m2repository/com/android/support/support-v4/23.4.0/support-v4-23.4.0.aar", Version = "23.4.0.0")]
```

-----

## <a name="additional-references"></a>其他參考

- [錯誤 43245](https://bugzilla.xamarin.com/show_bug.cgi?id=43245) = 不準確的" 下載失敗。 請下載{0}並將其{1}放到 目錄中。 和"請安裝包:''SDK{0}安裝程式中可用"與 Xamarin.Android.支援包相關的錯誤消息

### <a name="next-steps"></a>後續步驟

本文檔討論截至 2016 年 8 月的當前行為。 本文檔中描述的技術不是 Xamarin 穩定測試套件的一部分,因此將來可能會中斷。

如需進一步説明,請聯繫我們,或者如果此問題在利用上述資訊后仍然存在,請參閱[Xamarin 有哪些支援選項?](~/cross-platform/troubleshooting/support-options.md)有關聯絡人選項、建議以及如何在需要時提交新 Bug 的資訊。
