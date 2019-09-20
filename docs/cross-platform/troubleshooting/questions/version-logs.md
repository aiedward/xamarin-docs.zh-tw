---
title: 可以在哪裡找到我的版本資訊和記錄？
description: 本檔說明尋找 Xamarin 版本資訊和記錄檔的位置。 這種資訊在診斷問題、提交 bug 或取得支援時很有用。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: f2d9921795d2a788a6646aad36712a0691c07d50
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291168"
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
    - 個別`.msi`安裝程式，詳細資訊記錄檔
    - Visual Studio 啟動，詳細資訊記錄
  - [Mac 記錄檔](#mac-logs)
    - 組建主機
  - Visual Studio for Mac
    - Xamarin Studio
    - Xamarin 安裝程式
- [詳細資訊組建輸出](#verbose-build-output-logs)
- [適用于 Xamarin 和 Xamarin iOS 應用程式的 Debug 記錄檔](#debug-logs-for-xamarin-apps)
  - Android `adb` logcat 記錄檔
  - iOS 模擬器記錄檔（在 Mac 上）
  - iOS 裝置記錄（在 Mac 上）

## <a name="a-idversion-information-nameversion-information-version-information"></a><a id="version-information" name="version-information" />版本資訊

通常最好是從 [**複製資訊**] 按鈕送回所有資訊。 否則，我們通常需要要求其他資訊。 例如，在協助疑難排解問題時，作業系統版本、Xcode 版本、已安裝的 Android API 層級和 .NET 版本都很重要。

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Windows 版本資訊

#### <a name="xamarin-studio"></a>Xamarin Studio

**說明 > 關於 > 顯示詳細資料 > 複製資訊 [按鈕]**

#### <a name="visual-studio"></a>Visual Studio

**有關 Microsoft Visual Studio > 複製資訊 [按鈕] 的說明 >**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Mac 版本資訊

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**Visual Studio > 關於 Visual Studio > 顯示詳細資料 > [按鈕] 的複製資訊**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Android SDK Tools，平臺工具，組建工具

開啟 [Android SDK 管理員]，並取出 [最熱門**工具**] 區段的螢幕擷取畫面。

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**> 開啟 Android SDK 管理員的工具**

#### <a name="visual-studio"></a>Visual Studio

**> Android > 的工具開啟 Android SDK 管理員...**

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />IDE 和安裝程式記錄檔

針對每個記錄檔位置，請務必壓縮並附加整個記錄檔資料夾。

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />Windows 記錄檔

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" />適用于 Xamarin 的 Visual Studio Tools

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" />Visual Studio 2017

[如何取得 Visual Studio 安裝記錄](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" />Xamarin "通用" 安裝程式

`%LOCALAPPDATA%\Xamarin\Universal`

這些是`XamarinInstaller.exe`安裝程式中的記錄檔。

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />個別`.msi`安裝程式，詳細資訊記錄檔

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

證明[命令列選項](https://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Visual Studio 啟動，詳細資訊記錄

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

參考： [/log （devenv .exe）](https://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />Mac 記錄檔

您可以在搜尋工具中選取 [ **go > 移至資料夾**] 功能表項目，然後將這些路徑複製並貼到對話方塊中。

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio for Mac

`~/Library/Logs/VisualStudio/7.0`（視您使用的版本而定，此數目可能會變更）

此資料夾也可以透過 [說明-> 開啟記錄檔目錄] 來開啟。

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0`（視您使用的版本而定，此數目可能會變更）

此資料夾也可以透過 [說明-> 開啟記錄檔目錄] 來開啟。

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Xamarin "通用" 安裝程式

`~/Library/Logs/XamarinInstaller/Universal`

這些是`XamarinInstaller.dmg`安裝程式中的記錄檔。

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Xamarin 組建主機

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />詳細資訊組建輸出

1. 啟用[診斷 MSBuild 輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)。

2. 針對 iOS 應用程式，也請`-v -v -v -v`在 **[專案屬性] > [ios 組建 > 一般] （索引標籤） > 其他選項 > 其他 mtouch 引數）** ，以啟用詳細資訊**mtouch 輸出**。

3. 清除並重建專案。

4. 將 IDE 的組建輸出複製並貼到文字檔中。
     - Visual Studio （Windows）：**View > 輸出 > 顯示輸出來源：建置**
     - Visual Studio for Mac：**View > Pad > 錯誤 > 組建輸出（tab）**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />適用于 Xamarin 和 Xamarin iOS 應用程式的 Debug 記錄檔

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**View > Pad > 應用程式輸出**

（請注意，此功能表項目只會在應用程式啟動後才會出現）。

### <a name="visual-studio"></a>Visual Studio

**View > 輸出 > 顯示輸出來源：Debug.exe**

### <a name="a-idadb-logcat-nameadb-logcat-android-adbhttpsdeveloperandroidcomtoolshelpadbhtml-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Android [`adb`](https://developer.android.com/tools/help/adb.html) logcat 記錄檔

執行命令之後，請從您的桌面附加回**android_logcat。** `adb` 這些指示假設您只連接一個裝置。

另請參閱[Android Debug 記錄](~/android/deploy-test/debugging/android-debug-log.md)頁面。

#### <a name="visual-studio"></a>Visual Studio

1. **工具 > Android > 啟動 Android Adb 命令提示字元**
2. 清除記錄檔：`adb logcat -c`
3. 重現問題。
4. 輸出記錄檔：`adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. **工具 > 開啟 Android SDK 命令提示字元**
2. 清除記錄檔：`adb logcat -c`
3. 重現問題。
4. 輸出記錄檔：`adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />iOS 模擬器記錄檔（在 Mac 上）

- 若要存取系統記錄檔，請選取 [Debug] > 開啟 iOS 模擬器應用程式中的 [**系統記錄檔 ...** ]。

- 若要從模擬器查看損毀報告，請開啟 [主控台]， `~/Library/Logs > DiagnosticReports`然後流覽至。

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />iOS 裝置記錄（在 Mac 上）

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**View > Pad > iOS 裝置記錄檔**

#### <a name="xcode"></a>Xcode

**Windows > 裝置 > $ {DeviceName}**

當機報告可在 [**查看裝置記錄**檔] 按鈕下取得。 裝置的系統記錄會出現在視窗底部的 [洩漏] 箭號底下。

#### <a name="xcode-5"></a>Xcode 5

**Window > 召集人 > 裝置（tab） > $ {DeviceName}**
