---
title: 活頁簿安裝與需求
description: 本文件說明如何下載並安裝 Xamarin Workbooks，討論支援的平台和系統需求。
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: a1001163d89a9a9cda16a7ee5e644307fcc9875c
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58506976"
---
# <a name="workbooks-installation-and-requirements"></a>活頁簿安裝與需求

<a name="install" />

## <a name="download-and-install"></a>下載並安裝

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. 請檢查[需求](#requirements)如下。
2. 下載並安裝[Xamarin 活頁簿的 Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi)。
3. 開始[摸索](~/tools/workbooks/workbook.md)活頁簿或試用[範例](https://developer.xamarin.com/workbooks)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. 請檢查[需求](#requirements)如下。
2. 下載並安裝[for Mac 的 Xamarin Workbooks](https://dl.xamarin.com/interactive/XamarinInteractive.pkg)。
3. 開始[摸索](~/tools/workbooks/workbook.md)活頁簿或試用[範例](https://developer.xamarin.com/workbooks)

-----

## <a name="requirements"></a>需求

#### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 或更新版本
- **Windows** -Windows 7 或更新版本 (使用 Internet Explorer 11 或更新版本和.NET 4.6.1 或更新版本)

#### <a name="supported-app-platforms"></a>支援的應用程式平台

|應用程式平台|OS 支援|注意|
|--- |--- |--- |
|Mac|在 Mac 上才支援|
|iOS|在 Mac 和 Windows 上支援|必須在 mac 上安裝 Xamarin.iOS 11.0 和 Xcode 9.0 或更新版本 Windows 上執行 iOS 活頁簿需要 Mac 組建主機，執行所有上述項目，而[遠端 iOS 模擬器](~/tools/ios-simulator/index.md)安裝在 Windows 上。|
|Android|在 Mac 和 Windows 上支援|必須與虛擬裝置搭配使用 Google、 Visual Studio 或 Xamarin Android 模擬器，> = 5.0|
|WPF|在 Windows 上才支援|
|主控台 (.NET Framework)|在 Mac 和 Windows 上支援|
|主控台 (.NET Core)|在 Mac 和 Windows 上支援|


## <a name="reporting-bugs"></a>回報 Bug

請[回報問題在 GitHub 上][bugs]，並包含所有的下列資訊：

### <a name="log-files"></a>記錄檔

請務必附加活頁簿用戶端記錄檔：

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4.x 也提供功能的能力，從主功能表中選取記錄檔中搜尋 (macOS) 或檔案總管 (Windows) 直接：

- **協助 > 顯示記錄檔**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>活頁簿 1.3 及更早版本的記錄檔路徑：

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>平台的版本資訊

它非常有幫助了解您的作業系統詳細資訊，並已安裝 Xamarin 產品。

從活頁簿中的主功能表：

* **協助 > 複製版本資訊**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>活頁簿 1.3 及更早版本的指示：

Visual Studio For Mac

- **Visual Studio > 關於 Visual Studio > 顯示詳細資料 > 複製資訊**
- 貼上錯誤報告

Visual Studio

- **說明 > 關於 Visual Studio > 複製資訊**
- 讓我們知道您的作業系統版本和您執行的 32 位元或 64 位元的 Windows。

### <a name="samples"></a>範例

如果您可以附加或連結至 **.workbooks**檔案有問題，可能有助於更快速地解決您的 bug。

### <a name="devices"></a>裝置

如果您的 iOS 或 Android 的活頁簿中，連線時遇到問題，而且已經簽[我們的疑難排解頁面](~/tools/workbooks/troubleshooting/index.md)，我們需要知道：

- 您嘗試連接到的裝置名稱
- 您的裝置的 OS 版本
- Android:請確認您使用的 x86 模擬器
- Android:您是否使用模擬器平台為何？ Google 模擬器嗎？
  Visual Studio Android 模擬器？ Xamarin Android Player？
- 在 Windows 上的 iOS:哪個版本的 Xamarin 遠端 iOS 模擬器執行您已安裝 (檢查**新增/移除程式**中**控制台**)？
- 在 Windows 上的 iOS:請為您的 Mac 組建主機也提供平台的版本資訊
- 裝置是否有網路連線 （透過 web 瀏覽器的核取）？

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>解除安裝

### <a name="windows"></a>Windows

根據您取得活頁簿，您可能必須執行兩個的解除安裝程序。 請檢查這兩個完全解除安裝軟體。

#### <a name="visual-studio-installer"></a>Visual Studio 安裝程式

如果您有 Visual Studio 2017，開啟**Visual Studio 安裝程式**，然後查看**個別元件**for **Xamarin Workbooks**。 如果有勾選，取消選取它，然後按一下**修改**解除安裝。

#### <a name="system-uninstall"></a>系統解除安裝

如果您的活頁簿自行以安裝下載的安裝程式，它必須透過解除安裝**應用程式與功能**系統設定頁面，在 Windows 10 上或透過**新增/移除程式**控制項中在舊版 Windows 上的面板。

> **開始 > 設定 > System > 應用程式與功能**

![](install-images/windows-remove.png "如下所示的 Xamarin Workbooks&quot;應用程式&amp;功能&quot;")

**您仍然應該遵循 Visual Studio 安裝程式，藉此確定活頁簿不會無法取得重新安裝您不知情的情況下的程序。**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

開頭[1.2.2](https://developer.xamarin.com/releases/interactive/interactive-1.2/)，Xamarin 活頁簿可以解除安裝從終端機執行：

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

檔案和目錄，則會移除，並要求確認才能繼續，將詳細說明，解除安裝程式。

傳遞`-help`引數`uninstall`指令碼的更進階的案例。

對於較舊版本，您必須手動移除下列項目：

1. 刪除 `"/Applications/Xamarin Workbooks.app"` 上的 Workbooks 應用程式
2. 刪除 `"Applications/Xamarin Inspector.app"` 上的 Inspector 應用程式
2. 刪除增益集：`"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` 和 `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
3. 刪除這裡的 Inspector 和支援檔：`/Library/Frameworks/Xamarin.Interactive.framework` 和 `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>降級

套件組合識別碼，如**應用程式/Xamarin Workbooks.app**從變更`com.xamarin.Inspector`到`com.xamarin.Workbooks`在 1.4 版中，為 Workbooks 和 Inspector 現在完全分割。

較舊的安裝程式中的錯誤，因為它不可能降級 1.4 或更新版本使用 1.3.2 或舊版的安裝程式的版本。

若要從 1.4 或更新版本才可 1.3.2 或更舊版本的降級：

1. [手動解除安裝 Workbooks 和 Inspector](#uninstall-macos)
2. 執行 1.3.2 或更舊版本`.pkg`安裝程式
