---
title: 活頁簿安裝與需求
description: 本文件說明如何下載並安裝 Xamarin 活頁簿，討論支援的平台和系統需求。
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: topgenorth
ms.author: toopge
ms.openlocfilehash: b5e94a9b0bc3b9e9f141ad9fd262d29e01eb8a10
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793817"
---
# <a name="workbooks-installation-and-requirements"></a>活頁簿安裝與需求

<a name="install" />

## <a name="download-and-install"></a>下載並安裝

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. 請檢查[需求](#requirements)下方。
2. 下載並安裝[Windows 的 Xamarin 活頁簿](https://dl.xamarin.com/interactive/XamarinInteractive.msi)。
3. 啟動[播放](~/tools/workbooks/workbook.md)使用活頁簿或試用[範例](https://developer.xamarin.com/workbooks)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. 請檢查[需求](#Requirements)下方。
2. 下載並安裝[Xamarin 適用於 Mac 的活頁簿](https://dl.xamarin.com/interactive/XamarinInteractive.pkg)。
3. 啟動[播放](~/tools/workbooks/workbook.md)使用活頁簿或試用[範例](https://developer.xamarin.com/workbooks)

-----

## <a name="requirements"></a>需求

#### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 或更新版本
- **Windows** -Windows 7 或更新版本 (使用 Internet Explorer 11 或更高和.NET 4.6.1 或更新版本)

#### <a name="supported-app-platforms"></a>支援的應用程式平台

|應用程式平台|作業系統支援|注意|
|--- |--- |--- |
|Mac （整合）|在 Mac 上才支援|
|iOS （整合）|於 Mac 和 Windows 支援|必須在 mac 上安裝 Xamarin.iOS 11.0 和 Xcode 9.0 或更新版本 在 Windows 上執行 iOS 的活頁簿需要 Mac 組建主機執行所有上述項目，而[遠端 iOS 模擬器](~/tools/ios-simulator.md)安裝在 Windows 上。|
|Android|於 Mac 和 Windows 支援|必須與虛擬裝置搭配使用 Google、 Visual Studio 或 Xamarin Android 模擬器，> = 5.0|
|WPF|在 Windows 上才支援|
|主控台 (.NET Framework)|於 Mac 和 Windows 支援|
|主控台 （.NET Core）|於 Mac 和 Windows 支援|


## <a name="reporting-bugs"></a>回報 Bug

請[GitHub 上回報問題][bugs]，並包含所有的下列資訊：

### <a name="log-files"></a>記錄檔

請務必附加活頁簿用戶端記錄檔：

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4.x 也提供功能的能力，從主功能表中選取記錄檔中搜尋 (macOS) 或檔案總管 (Windows) 直接：

- **協助 > 顯示記錄檔**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>活頁簿 1.3 及更早版本的記錄檔路徑：

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>平台版本資訊

它是很有幫助知道您作業系統的相關詳細資料，並已安裝 Xamarin 產品。

從活頁簿中的主功能表：

* **協助 > 複製版本資訊**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>活頁簿 1.3 及更早版本的指示：

Visual Studio For Mac

- **Visual Studio > 有關 Visual Studio > 顯示詳細資料 > 複製資訊**
- 貼到 錯誤報告

Visual Studio

- **協助 > 有關 Visual Studio > 複製資訊**
- 讓我們知道您的作業系統版本和您執行的 32 位元或 64 位元 Windows。

### <a name="samples"></a>範例

如果您附加或連結至 **.workbooks**檔案發生問題，以幫助您更快速地解決 bug。

### <a name="devices"></a>裝置

如果您無法連線您的 iOS 或 Android 的活頁簿，並檢查沒有已經[我們疑難排解頁面](~/tools/workbooks/troubleshooting/index.md)，我們需要知道：

- 您嘗試連接到裝置的名稱
- 您的裝置 OS 版本
- Android： 確認您使用的 x86 模擬器
- Android： 模擬器平台您使用？ Google 模擬器嗎？
  Android 的 visual Studio 模擬器嗎？ Xamarin Android Player 嗎？
- 在 Windows 上的 iOS: Xamarin 遠端 ios 模擬器版本您已安裝 (檢查**新增/移除程式**中**控制台**)？
- 在 Windows 上的 iOS： 請為您的 Mac 組建主機也提供平台版本資訊
- 裝置是否有網路連線能力 （透過 web 瀏覽器檢查）？

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>解除安裝

### <a name="windows"></a>Windows

根據您如何取得活頁簿 （& s） 偵測器，您可能執行兩個解除安裝程序。 請檢查這兩個完全解除安裝軟體。

#### <a name="visual-studio-installer"></a>Visual Studio 安裝程式

如果您有 Visual Studio 2017，開啟**Visual Studio 安裝程式**，然後查看**個別元件**如**Xamarin 活頁簿**。 如果已核取，請取消選取，然後按一下**修改**解除安裝。

#### <a name="system-uninstall"></a>系統解除安裝

如果您已安裝的活頁簿 （& s） 偵測器自行下載安裝程式，就會需要透過解除安裝**應用程式和功能**系統設定 頁面在 Windows 10 上或透過**新增/移除程式**較舊版本的 Windows 控制台中。

> **開始 > 設定 > System > 應用程式和功能**

![](install-images/windows-remove.png "Xamarin 活頁簿和如下所示的偵測器&quot;應用程式&amp;功能&quot;")

**您仍應遵循 Visual Studio 安裝程式，以便確定活頁簿的程序 （& s) 偵測器不會不取得重新安裝您不知情的情況下。**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

從開始[1.2.2](https://developer.xamarin.com/releases/interactive/interactive-1.2/)，Xamarin 活頁簿 （& s） 偵測器可以從解除安裝終端機執行：

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

檔案和目錄，則會移除，並要求確認才能繼續解除安裝程式將會詳細說明。

傳遞`-help`引數`uninstall`更進階案例中的指令碼。

對於較舊版本，您必須手動移除下列項目：

1. 刪除 `"/Applications/Xamarin Workbooks.app"` 上的 Workbooks 應用程式
2. 刪除 `"Applications/Xamarin Inspector.app"` 上的 Inspector 應用程式
2. 刪除增益集：`"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` 和 `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
3. 刪除這裡的 Inspector 和支援檔：`/Library/Frameworks/Xamarin.Interactive.framework` 和 `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>降級

套件組合識別碼**Xamarin 應用程式/Workbooks.app**從變更`com.xamarin.Inspector`至`com.xamarin.Workbooks`1.4 以便未來分割 Xamarin 活頁簿 （& s） 偵測器安裝程式的版本中。

由於較舊的安裝程式中的錯誤，不可能降級 1.4 或更新版本使用 1.3.2 或較舊的安裝程式的版本。

若要從 1.4 或更新版本才能 1.3.2 或較舊降級：

1. [手動解除安裝活頁簿 （& s） 偵測器](#macOS)
2. 執行 1.3.2 或較舊`.pkg`安裝程式
