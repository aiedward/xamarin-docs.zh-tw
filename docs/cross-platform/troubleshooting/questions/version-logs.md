---
title: 可以在哪裡找到我的版本資訊和記錄？
description: 本文件說明如何尋找 Xamarin 版本資訊和記錄檔。 診斷送出 bug，或取得支援的問題時，這項資訊是很有用。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: e389bc33538ec3c3d36eb749c746f5a4723aab3c
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830361"
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>可以在哪裡找到我的版本資訊和記錄？

## <a name="outline"></a>外框

- [版本資訊](#version-information)
    - Windows 版本資訊
    - Mac 版本資訊
    - Android SDK Tools，平台工具、 建置工具
- [IDE 和安裝程式記錄檔](#ide-and-installer-logs)
    - [Windows 記錄檔](#windows-logs)
        - Xamarin Studio
        - Xamarin for Visual Studio
        - Xamarin Universal installer
        - 個別`.msi`安裝程式，詳細資訊記錄檔
        - Visual Studio 啟動時，詳細資訊記錄檔
    - [Mac 記錄檔](#mac-logs)
        - 組建主機
    - Visual Studio for Mac
        - Xamarin Studio
        - Xamarin 安裝程式
- [詳細的組建輸出](#verbose-build-output-logs)
- [偵錯 Xamarin.Android 和 Xamarin.iOS 應用程式的記錄檔](#debug-logs-for-xamarin-apps)
    - Android `adb` logcat 記錄檔
    - iOS 模擬器的記錄檔 （在 Mac 上)
    - iOS 裝置記錄檔 （在 Mac 上)

## <a name="a-idversion-information-nameversion-information-version-information"></a><a id="version-information" name="version-information" />版本資訊

這通常是最好的方式傳送復原所有的資訊**複製資訊**按鈕。 否則，我們通常需要要求其他資訊。 例如，作業系統版本，Xcode 版本安裝的 Android API 層級，和.NET 版本都可以很重要時協助解決問題。

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Windows 版本資訊

#### <a name="xamarin-studio"></a>Xamarin Studio

**說明 > 關於 > 顯示詳細資料 > 複製資訊 [按鈕]**

#### <a name="visual-studio"></a>Visual Studio

**說明 > 關於 Microsoft Visual Studio > 複製資訊 [按鈕]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Mac 版本資訊

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**Visual Studio > 關於 Visual Studio > 顯示詳細資料 > [按鈕] 的複製資訊**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Android SDK Tools，平台工具、 建置工具

開啟 Android SDK 管理員 中，並採取最上方的螢幕擷取畫面**工具**一節。

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**工具 > 以開啟 Android SDK Manager**

#### <a name="visual-studio"></a>Visual Studio

**工具 > Android > 開啟 Android SDK 管理員...**

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />IDE 和安裝程式記錄檔

針對每個記錄檔位置，請務必壓縮，並將附加的整個記錄檔資料夾。

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />Windows 記錄檔

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" /> Visual Studio Tools for Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio 2017

[如何取得 Visual Studio 安裝記錄檔](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" /> Xamarin 的 「 通用 」 安裝程式

`%LOCALAPPDATA%\Xamarin\Universal`

這些是從記錄檔`XamarinInstaller.exe`安裝程式。

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />個別`.msi`安裝程式，詳細資訊記錄檔

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

參考：[命令列選項](https://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Visual Studio 啟動時，詳細資訊記錄檔

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

參考： [/Log (devenv.exe)](https://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />Mac 記錄檔

您可以選取**移 > 移至資料夾**功能表項目中搜尋工具，然後複製並將任何這些路徑貼到對話方塊。

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio for Mac

`~/Library/Logs/VisualStudio/7.0` （此數字可能會變更視您所使用的版本而定）

此資料夾也可以開啟透過 [說明]-> [開啟記錄檔目錄]。

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` （此數字可能會變更視您所使用的版本而定）

此資料夾也可以開啟透過 [說明]-> [開啟記錄檔目錄]。

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Xamarin 的 「 通用 」 安裝程式

`~/Library/Logs/XamarinInstaller/Universal`

這些是從記錄檔`XamarinInstaller.dmg`安裝程式。

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Xamarin 組建主機

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />詳細的組建輸出

1.  啟用[診斷 MSBuild 輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)。

2.  針對 iOS 應用程式，也可讓**verbose mtouch 輸出**加上`-v -v -v -v`之下**專案屬性 > iOS 組建 > 一般 （索引標籤） > 其他選項 > 其他 mtouch 引數**。

3.  清除並重建專案。

4.  複製並貼到文字檔的 IDE 的建置輸出。
     - Visual Studio (Windows):**檢視 > 輸出 > 顯示輸出來源：建置**
     - Visual Studio for Mac:**檢視 > 板 > 錯誤 > 建置輸出 （索引標籤）**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />偵錯 Xamarin.Android 和 Xamarin.iOS 應用程式的記錄檔

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**檢視 > 板 > 應用程式輸出**

（請注意，此功能表項目才會出現在啟動應用程式之後）。

### <a name="visual-studio"></a>Visual Studio

**檢視 > 輸出 > 顯示輸出來源：偵錯**

### <a name="a-idadb-logcat-nameadb-logcat-android-adbhttpsdeveloperandroidcomtoolshelpadbhtml-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Android [ `adb` ](https://developer.android.com/tools/help/adb.html) logcat 記錄檔

執行後`adb`命令，將附加後**android_logcat.txt**檔案從您的桌面。 這些指示假設您有只有一個連接的裝置。

另請參閱[Android 偵錯記錄檔](~/android/deploy-test/debugging/android-debug-log.md)頁面。

#### <a name="visual-studio"></a>Visual Studio

1. **工具 > Android > 啟動 Android Adb 命令提示字元**
2. 清除記錄檔： `adb logcat -c`
3. 重現問題。
4. 輸出記錄檔： `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. **工具 > 開啟 Android SDK 命令提示字元**
2. 清除記錄檔： `adb logcat -c`
3. 重現問題。
4. 輸出記錄檔： `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />iOS 模擬器的記錄檔 （在 Mac 上)

* 若要存取 系統記錄檔，請選取**偵錯 > 開啟系統記錄檔...** iOS 模擬器應用程式中。

* 若要檢視從模擬器當機報告，請開啟 Console.app 並瀏覽至`~/Library/Logs > DiagnosticReports`。

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />iOS 裝置記錄檔 （在 Mac 上)

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**檢視 > 板 > iOS 裝置記錄檔**

#### <a name="xcode"></a>Xcode

**視窗 > 裝置 > ${DeviceName}**

當機報告都能使用 [**檢視的裝置記錄**] 按鈕。 裝置的系統記錄檔會出現在視窗底部的下公開揭示箭號。

#### <a name="xcode-5"></a>Xcode 5

**視窗 > 組合管理 > （索引標籤） 的裝置 > ${DeviceName}**
