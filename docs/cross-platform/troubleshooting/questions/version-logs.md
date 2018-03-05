---
title: "可以在哪裡找到我的版本資訊和記錄檔？"
ms.topic: article
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 2d6ab8b939b7a6f8c7e66985a8c4238064ee1543
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>可以在哪裡找到我的版本資訊和記錄檔？

## <a name="outline"></a>外框

- [版本資訊](#version-information)
    - Windows 版本資訊
    - Mac 版本資訊
    - Android SDK 工具、 平台工具，建置工具
- [IDE 和安裝程式記錄檔](#ide-and-installer-logs)
    - [Windows 記錄檔](#windows-logs)
        - Xamarin Studio
        - Xamarin for Visual Studio
        - Xamarin Universal installer
        - 個別`.msi`安裝程式、 詳細資訊記錄檔
        - Visual Studio 啟動時，詳細資訊記錄檔
    - [Mac 的記錄檔](#mac-logs)
        - 建立主機
    - Visual Studio for Mac
        - Xamarin Studio
        - Xamarin 安裝程式
- [詳細資訊的組建輸出](#verbose-build-output-logs)
- [偵錯 Xamarin.Android 和 Xamarin.iOS 應用程式記錄檔](#debug-logs-for-xamarin-apps)
    - Android `adb` logcat 記錄檔
    - iOS 模擬器記錄 （在 Mac)
    - iOS 裝置記錄 （在 Mac)

## <a name="a-idversion-information-nameversion-information-version-information"></a><a id="version-information" name="version-information" />版本資訊

它通常是最佳的傳送回的所有資訊從**複製資訊**按鈕。 否則，我們通常需要要求其他資訊。 例如，作業系統版本 Xcode 版本安裝 Android API 層級，和.NET 版本都可以很重要時協助疑難排解問題。

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Windows 版本資訊

#### <a name="xamarin-studio"></a>Xamarin Studio

**協助 > 關於 > 顯示詳細資料 > 複製資訊 [按鈕]**

#### <a name="visual-studio"></a>Visual Studio

**協助 > 關於 Microsoft Visual Studio > 複製資訊 [按鈕]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Mac 版本資訊

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**Visual Studio > 關於 Visual Studio > 顯示詳細資料 > 複製資訊 [按鈕]**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Android SDK 工具、 平台工具，建置工具

開啟 Android SDK 管理員，然後頂端的螢幕擷取畫面**工具**> 一節。

![](https://kb.xamarin.com/customer/portal/attachments/337323 "螢幕擷取畫面的 Android SDK Manager > 工具 資料夾")

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**工具 > 開啟 Android SDK 管理員**

#### <a name="visual-studio"></a>Visual Studio

選取 SDK Manager 工具列圖示：

![](https://kb.xamarin.com/customer/portal/attachments/420238 "啟動 Visual Studio 中的 Android SDK Manager 工具列圖示")

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />IDE 和安裝程式記錄檔

針對每個記錄檔位置，請務必壓縮，並將連接的整個記錄檔資料夾。

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />Windows 記錄檔

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" /> Visual Studio Tools for Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio 2017

[如何取得 Visual Studio 安裝記錄檔](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" /> Xamarin 「 通用 」 的安裝程式

`%LOCALAPPDATA%\Xamarin\Universal`

這些是從記錄`XamarinInstaller.exe`安裝程式。

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />個別`.msi`安裝程式、 詳細資訊記錄檔

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

參考：[命令列選項](http://msdn.microsoft.com/en-us/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Visual Studio 啟動時，詳細資訊記錄檔

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

參考： [/Log (devenv.exe)](http://msdn.microsoft.com/en-us/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />Mac 的記錄檔

您可以選取**移 > 移至資料夾**功能表項目中尋找工具中，然後複製並貼入這些路徑的任何對話方塊。

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio for Mac

`~/Library/Logs/VisualStudio/7.0` （這個數目可能會變更根據您所使用的版本）

此資料夾也可以開啟透過 [說明]-> [開啟記錄檔目錄]。

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` （這個數目可能會變更根據您所使用的版本）

此資料夾也可以開啟透過 [說明]-> [開啟記錄檔目錄]。

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Xamarin 「 通用 」 的安裝程式

`~/Library/Logs/XamarinInstaller/Universal`

這些是從記錄`XamarinInstaller.dmg`安裝程式。

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Xamarin 組建主機

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />詳細資訊的組建輸出

1.  啟用[診斷 MSBuild 輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)。

2.  針對 iOS 應用程式，也啟用**verbose mtouch 輸出**加`-v -v -v -v`下**專案屬性 > iOS 組建 > 一般 [tab] > 其他選項 > 其他 mtouch 引數**。

    **Visual Studio (Windows)**

    [![](https://kb.xamarin.com/customer/portal/attachments/337319 "其他 mtouch 引數的輸入欄位中輸入四個虛線 v")](https://kb.xamarin.com/customer/portal/attachments/337319)

    **Visual Studio for Mac**

    [![](https://kb.xamarin.com/customer/portal/attachments/337316 "其他 mtouch 引數的輸入欄位中輸入四個虛線 v")](https://kb.xamarin.com/customer/portal/attachments/337316)

3.  清除並重建專案。

4.  複製並貼到文字檔案從 IDE 組建輸出。
     - Visual Studio (Windows):**檢視 > 輸出 > 顯示輸出來源： 建置**
     - Visual Studio for Mac:**檢視 > 填補 > 錯誤 > 組建輸出 [tab]**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />偵錯 Xamarin.Android 和 Xamarin.iOS 應用程式記錄檔

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**檢視 > 板 > 應用程式輸出**

![](https://kb.xamarin.com/customer/portal/attachments/337290 "在 Visual Studio for Mac 應用程式輸出板圖示")


（請注意已啟動應用程式之後才會出現這個功能表項目）。

### <a name="visual-studio"></a>Visual Studio

**檢視 > 輸出 > 顯示輸出來源： 偵錯**

![](https://kb.xamarin.com/customer/portal/attachments/337292 "輸出板顯示 Visual Studio 中偵錯選項")

### <a name="a-idadb-logcat-nameadb-logcat-android-adbhttpdeveloperandroidcomtoolshelpadbhtml-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Android [ `adb` ](http://developer.android.com/tools/help/adb.html) logcat 記錄檔

執行之後`adb`命令中，附加後**android_logcat.txt**檔案從您的桌面。 這些指示假設您有只有一個附加的裝置。

另請參閱[Android 偵錯記錄檔](~/android/deploy-test/debugging/android-debug-log.md)頁面。

#### <a name="visual-studio"></a>Visual Studio

1.  **工具 > Android > 啟動 Android Adb 命令提示字元**
2.  清除記錄檔： `adb logcat -c`
3.  重現問題。
4.  輸出記錄檔： `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1.  **工具 > 開啟 Android SDK 命令提示字元**
2.  清除記錄檔： `adb logcat -c`
3.  重現問題。
4.  輸出記錄檔： `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />iOS 模擬器記錄 （在 Mac)

*   若要存取系統記錄檔，請選取**偵錯 > 開啟系統記錄檔...** iOS 模擬器應用程式中。

    ![](https://kb.xamarin.com/customer/portal/attachments/382617 "偵錯 功能表顯示開啟的系統記錄檔選項")

*   若要檢視從模擬器的當機報告，請開啟 Console.app 並瀏覽至`~/Library/Logs > DiagnosticReports`。

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />iOS 裝置記錄 （在 Mac)

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

**檢視 > 填補 > iOS 裝置記錄檔**

#### <a name="xcode"></a>Xcode 

**視窗 > 裝置 > ${DeviceName}**

底下的當機報告可用**檢視裝置記錄檔** 按鈕。 裝置的系統記錄檔會出現在下方的洩漏箭號的視窗底部<img alt="Disclosure arrow" src="https://kb.xamarin.com/customer/portal/attachments/382618" style="width: 15px; height: 12px;" />。

#### <a name="xcode-5"></a>Xcode 5

**視窗 > 組合管理 > 裝置 [tab] > ${DeviceName}**
