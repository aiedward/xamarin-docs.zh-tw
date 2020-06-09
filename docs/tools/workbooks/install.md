---
title: 活頁簿安裝和需求
description: 本檔說明如何下載和安裝 Xamarin Workbooks，並討論支援的平臺和系統需求。
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: davidortinau
ms.author: daortin
ms.date: 06/19/2018
ms.openlocfilehash: a044169f86b46abff4158011e99320c528180ffc
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573881"
---
# <a name="workbooks-installation-and-requirements"></a>活頁簿安裝和需求

<a name="install"></a>

## <a name="download-and-install"></a>下載並安裝

<!-- markdownlint-disable MD001 -->

# <a name="windows"></a>[Windows](#tab/windows)

1. 請檢查下列[需求](#requirements)。
2. 下載並安裝[適用于 Windows 的 Xamarin Workbooks](https://dl.xamarin.com/interactive/XamarinInteractive.msi)。
3. 開始[試用](~/tools/workbooks/workbook.md)活頁簿。

# <a name="macos"></a>[macOS](#tab/macos)

1. 請檢查下列[需求](#requirements)。
2. 下載並安裝[適用于 Mac 的 Xamarin Workbooks](https://dl.xamarin.com/interactive/XamarinInteractive.pkg)。
3. 開始[試用](~/tools/workbooks/workbook.md)。

-----

## <a name="requirements"></a>規格需求

#### <a name="supported-operating-systems"></a>支援的作業系統

- **Mac** -OS X 10.11 或更新版本
- **Windows** -windows 7 或更新版本（包含 Internet Explorer 11 或更新版本，以及 .net 4.6.1 或更高版本）

#### <a name="supported-app-platforms"></a>支援的應用程式平臺

|應用程式平台|OS 支援|注意|
|--- |--- |--- |
|Mac|僅在 Mac 上支援|
|iOS|在 Mac 和 Windows 上支援|您必須在 Mac 上安裝 Xamarin. iOS 11.0 和 Xcode 9.0 或更新版本。 在 Windows 上執行 iOS 活頁簿需要 Mac 組建主機，並在 Windows 上安裝上述所有和[遠端 iOS](~/tools/ios-simulator/index.md)模擬器。|
|Android|在 Mac 和 Windows 上支援|必須搭配虛擬裝置使用 Google、Visual Studio 或 Xamarin Android 模擬器 >= 5。0|
|WPF|只有在 Windows 上才支援|
|主控台（.NET Framework）|在 Mac 和 Windows 上支援|
|主控台（.NET Core）|在 Mac 和 Windows 上支援|

## <a name="reporting-bugs"></a>報告錯誤 (Bug)

請[報告 GitHub 上的問題][bugs]，並包含下列所有資訊：

### <a name="log-files"></a>記錄檔

一律附加活頁簿用戶端記錄檔：

- Mac：`~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4. x 也能夠直接從主功能表選取搜尋工具（macOS）或 Explorer （Windows）中的記錄檔：

- **說明 > 顯示記錄檔**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>活頁簿1.3 和更早版本的記錄檔路徑：

- Mac：`~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>平臺版本資訊

瞭解作業系統和已安裝 Xamarin 產品的詳細資料非常有説明。

從活頁簿的主功能表：

- **協助 > 複製版本資訊**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>活頁簿1.3 和更早版本的指示：

Mac 的 Visual Studio

- **Visual Studio > 關於 Visual Studio > 顯示詳細資料 > 複製資訊**
- 貼入 bug 報告

Visual Studio

- **協助 > Visual Studio > 複製資訊**
- 請讓我們知道您的作業系統版本，以及您是否執行32位或64位的 Windows。

### <a name="samples"></a>範例

如果您可以附加或連結至您遇到問題的活頁**簿**檔案，這可能有助於更快速地解決 bug。

### <a name="devices"></a>裝置

如果您在連接 iOS 或 Android 活頁簿時發生問題，而且已經檢查過[我們的疑難排解頁面](~/tools/workbooks/troubleshooting/index.md)，我們必須知道：

- 您嘗試連接的裝置名稱
- 裝置的 OS 版本
- Android：確認您使用的是 x86 模擬器
- Android：您所使用的模擬器平臺為何？ Google 模擬器？
  Visual Studio Android Emulator 嗎？ Xamarin Android Player？
- Windows 上的 iOS：您已安裝的 Xamarin 遠端 iOS 模擬器版本（請選取 [**控制台**] 中的 [**新增/移除程式**]）？
- Windows 上的 iOS：請同時提供 Mac 組建主機的平臺版本資訊
- 裝置是否具有網路連線能力（透過網頁瀏覽器檢查）？

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>解除安裝

### <a name="windows"></a>Windows

視您取得活頁簿的方式而定，您可能需要執行兩個卸載程式。 請檢查這兩項，以完全卸載軟體。

#### <a name="visual-studio-installer"></a>Visual Studio 安裝程式

如果您有 Visual Studio 2017，請開啟**Visual Studio 安裝程式**，並查看**Xamarin Workbooks**的**個別元件**。 如果已核取此選項，請將其取消選取，然後按一下 [**修改**] 以卸載。

#### <a name="system-uninstall"></a>系統卸載

如果您使用已下載的安裝程式自行安裝活頁簿，則必須透過 Windows 10 上的 [**應用程式] [& 功能**] [系統設定] 頁面，或在舊版 Windows 的 [控制台] 中透過 [**新增/移除程式**] 來卸載。

> **開始 > 設定 > 系統 > 應用程式 & 功能**

![](install-images/windows-remove.png "Xamarin Workbooks as listed in &quot;Apps &amp; features&quot;")

**您仍應遵循 Visual Studio 安裝程式的程式，以確保活頁簿不會在您不知情的情況下重新安裝。**

<a name="uninstall-macos"></a>

### <a name="macos"></a>macOS

從[1.2.2](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/interactive/interactive-1.2.md)開始，您可以執行下列程式，從終端機卸載 Xamarin Workbooks：

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

卸載程式會詳細說明將移除的檔案和目錄，並在繼續進行之前要求確認。

將 `-help` 引數傳遞至 `uninstall` 腳本，以進行更先進的案例。

對於較舊版本，您必須手動移除下列項目：

1. 刪除 `"/Applications/Xamarin Workbooks.app"` 上的 Workbooks 應用程式
2. 刪除 `"Applications/Xamarin Inspector.app"` 上的 Inspector 應用程式
3. 刪除增益集：`"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` 和 `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
4. 刪除這裡的 Inspector 和支援檔：`/Library/Frameworks/Xamarin.Interactive.framework` 和 `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>降級

/Applications/Xamarin 活頁簿的套件組合識別碼 **。應用程式** `com.xamarin.Inspector` `com.xamarin.Workbooks` 在1.4 版本中已從變更為，因為活頁簿和偵測器現在已完全分割。

由於舊版安裝程式中有錯誤，因此無法使用1.3.2 或舊版安裝程式降級1.4 或更新版本。

若要從1.4 或更新版本降級為1.3.2 或更舊版本：

1. [手動將活頁簿卸載 & Inspector](#uninstall-macos)
2. 執行1.3.2 或舊版 `.pkg` 安裝程式
