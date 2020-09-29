---
title: 可以在哪裡找到我的版本資訊和記錄？
description: 本檔說明尋找 Xamarin 版本資訊和記錄檔的位置。 這項資訊有助於診斷問題、提交 bug 或取得支援。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 997c6398c4cd9c4f4be6fbcd60847d82b0cae13d
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457831"
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>可以在哪裡找到我的版本資訊和記錄？

## <a name="outline"></a>外框

- [版本資訊](#version-information)
  - Windows 版本資訊
  - Mac 版本資訊
  - Android SDK Tools，平臺工具，組建工具
- [IDE 和安裝程式記錄檔](#ide-and-installer-logs)
  - [Windows 記錄檔](#windows-logs)
    - Xamarin Studio
    - Xamarin for Visual Studio
    - Xamarin 通用安裝程式
    - 個別 `.msi` 安裝程式，詳細資訊記錄
    - Visual Studio 啟動，詳細資訊記錄
  - [Mac 記錄檔](#mac-logs)
    - 組建主機
  - Visual Studio for Mac
    - Xamarin Studio
    - Xamarin 安裝程式
- [詳細資訊組建輸出](#verbose-build-output-logs)
- [適用于 Xamarin 和 Xamarin iOS 應用程式的 Debug 記錄](#debug-logs-for-xamarin-apps)
  - Android `adb` logcat 記錄
  -  (Mac 上的 iOS 模擬器記錄) 
  - Mac 上的 iOS 裝置記錄 () 

## <a name="version-information"></a><a id="version-information" name="version-information" />版本資訊

通常最好將 **複製資訊** 按鈕的所有資訊傳回。 否則，我們通常需要要求其他資訊。 例如，當您協助針對問題進行疑難排解時，作業系統版本、Xcode 版本、已安裝的 Android API 層級和 .NET 版本都可能很重要。

### <a name="windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Windows 版本資訊

#### <a name="xamarin-studio"></a>Xamarin Studio

**> 有關 > 顯示詳細資料 > 複製資訊 [按鈕] 的詳細資訊**

#### <a name="visual-studio"></a>Visual Studio

**說明 > Microsoft Visual Studio > 複製資訊 [按鈕]**

### <a name="mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Mac 版本資訊

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**Visual Studio > Visual Studio > 顯示詳細資料 > 複製資訊 [按鈕]**

### <a name="android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Android SDK Tools，平臺工具，組建工具

開啟 Android SDK 管理員，並取得 [最上層 **工具** ] 區段的螢幕擷取畫面。

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**工具 > 開啟 Android SDK 管理員**

#### <a name="visual-studio"></a>Visual Studio

**> Android > 開啟 Android SDK 管理員的工具 .。。**

## <a name="ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />IDE 和安裝程式記錄檔

針對每個記錄檔位置，請務必壓縮並附加整個記錄資料夾。

### <a name="windows-logs"></a><a id="windows-logs" name="windows-logs" />Windows 記錄檔

#### <a name="visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" /> Visual Studio Tools for Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio 2017

[如何取得 Visual Studio 安裝記錄檔](/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" /> Xamarin 「通用」安裝程式

`%LOCALAPPDATA%\Xamarin\Universal`

這些是安裝程式的記錄檔 `XamarinInstaller.exe` 。

#### <a name="individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />個別 `.msi` 安裝程式，詳細資訊記錄

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

參考： [命令列選項](/windows/win32/msi/command-line-options)

#### <a name="visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Visual Studio 啟動，詳細資訊記錄

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

參考： [/log ( # A0) ](/visualstudio/ide/reference/log-devenv-exe)

### <a name="mac-logs"></a><a id="mac-logs" name="mac-logs" />Mac 記錄檔

您可以在 Finder 中選取 [ **移 > 移至資料夾** ] 功能表項目，然後將這些路徑複製並貼到對話方塊中。

#### <a name="visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio for Mac

`~/Library/Logs/VisualStudio/7.0` (這個數位可能會根據您使用的版本而變更) 

此資料夾也可以透過 [說明-> 開啟記錄檔目錄] 來開啟。

#### <a name="xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` (這個數位可能會根據您使用的版本而變更) 

此資料夾也可以透過 [說明-> 開啟記錄檔目錄] 來開啟。

#### <a name="xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Xamarin 「通用」安裝程式

`~/Library/Logs/XamarinInstaller/Universal`

這些是安裝程式的記錄檔 `XamarinInstaller.dmg` 。

#### <a name="xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Xamarin 組建主機

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />詳細資訊組建輸出

1. 啟用 [診斷 MSBuild 輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)。

2. 若為 iOS 應用程式， **verbose mtouch output**也可以 `-v -v -v -v` 在 **[iOS 組建 > 一般 (] 索引標籤的 [專案 > 屬性] 下新增 mtouch 輸出，) > 其他 Mtouch 引數 > 其他選項**。

3. 清除並重建專案。

4. 將 IDE 中的組建輸出複製並貼到文字檔中。
     - Visual Studio (Windows) ： **View > output > 顯示輸出來源：組建**
     - Visual Studio for Mac： **> 組建輸出 (索引標籤上，查看 > 板 > 錯誤) **

## <a name="debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />適用于 Xamarin 和 Xamarin iOS 應用程式的 Debug 記錄

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**View > Pad > 應用程式輸出**

 (請注意，此功能表項目只會在應用程式啟動後出現。 ) 

### <a name="visual-studio"></a>Visual Studio

**View > Output > 顯示輸出來源： Debug**

### <a name="android-adb-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Android [`adb`](https://developer.android.com/tools/help/adb.html) logcat 記錄

執行命令之後 `adb` ，請從您的桌面上將 **android_logcat.txt** 檔案附加回來。 這些指示假設您只連接了一個裝置。

另請參閱 [Android Debug 記錄](~/android/deploy-test/debugging/android-debug-log.md) 頁面。

#### <a name="visual-studio"></a>Visual Studio

1. **工具 > Android > 啟動 Android Adb 命令提示字元**
2. 清除記錄檔： `adb logcat -c`
3. 重現問題。
4. 輸出記錄： `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. **工具 > 開啟 Android SDK 命令提示字元**
2. 清除記錄檔： `adb logcat -c`
3. 重現問題。
4. 輸出記錄： `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" /> (Mac 上的 iOS 模擬器記錄) 

- 若要存取系統記錄檔，請在 iOS 模擬器應用程式中選取 [ **Debug > 開啟系統記錄** 檔]。

- 若要從模擬器查看損毀報告，請開啟主控台，然後流覽至 `~/Library/Logs > DiagnosticReports` 。

### <a name="ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />Mac 上的 iOS 裝置記錄 () 

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**View > Pad > iOS 裝置記錄**

#### <a name="xcode"></a>Xcode

**Windows > > $ {DeviceName} 的裝置**

當機報告位於 [ **查看裝置記錄** ] 按鈕下時。 裝置的系統記錄會出現在視窗底部的 [洩漏] 箭號底下。

#### <a name="xcode-5"></a>Xcode 5

**視窗 > 召集人 > 裝置 (tab) > $ {DeviceName}**