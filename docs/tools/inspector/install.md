---
title: 偵測器安裝與需求
description: 本文件說明如何安裝 Xamarin Inspector，並討論支援的作業系統、 Ide 及應用程式平台。
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 2bbf0bda42b7bce483d9d036ebf39314dcb73072
ms.sourcegitcommit: fabd3b51dca6d904009d0670137c5fb5ee6c32ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2019
ms.locfileid: "54249186"
---
# <a name="inspector-installation-and-requirements"></a>偵測器安裝與需求

## <a name="download-and-installation"></a>下載和安裝

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. 下載並安裝[Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/) ，然後選取**使用.NET 進行行動開發**工作負載。
1. [登入](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio)以啟用您 Enterprise 訂用帳戶。
1. [檢查](~/tools/inspector/inspect.md)自己的應用程式 ！

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. 下載並安裝[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。
1. [登入](https://docs.microsoft.com/visualstudio/mac/activation)以啟用您 Enterprise 訂用帳戶。
1. [檢查](~/tools/inspector/inspect.md)自己的應用程式 ！

-----

## <a name="requirements"></a>需求

### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 或更新版本
- **Windows** -Windows 7 或更新版本 (使用 Internet Explorer 11 或更新版本和.NET 4.6.1 或更新版本)

### <a name="supported-ides"></a>支援的 Ide

- Visual Studio for Mac
- Visual Studio 2017**使用.NET 進行行動開發**工作負載

使用適用於企業客戶的即時應用程式檢查。

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>支援的應用程式平台

|應用程式平台|IDE 支援|注意|
|--- |--- |--- |
|Mac|只有在 Visual Studio for Mac 支援|
|iOS|在 Visual Studio 2017 和 Visual Studio for Mac 支援| 連結器行為必須設為**不要連結**(底下**iOS 組建**專案選項) |
|Android|在 Visual Studio 2017 和 Visual Studio for Mac 支援|Android 的目標必須 > = 4.0.3，with **fastdev**啟用。<br />必須使用 Google、 Visual Studio 或 Xamarin Android 模擬器。 Android 7 模擬器可能不允許在此階段的檢查。|
|WPF|只有在 Visual Studio 2017 中，|

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>回報 Bug

直接透過 Visual Studio，就應該報告錯誤：

- **協助 > 傳送意見反應 > 回報問題**

請包含所有的下列資訊：

### <a name="platform-version-information"></a>平台的版本資訊

這項資訊非常重要。

Visual Studio For Mac

- **Visual Studio > 關於 Visual Studio > 顯示詳細資料 > 複製資訊**
- 貼上錯誤報告

Visual Studio

- **說明 > 關於 Visual Studio > 複製資訊**
- 讓我們知道您的作業系統版本和您執行的 32 位元或 64 位元的 Windows。

### <a name="log-files"></a>記錄檔

請務必附加 IDE 和偵測器的用戶端記錄檔。

檢查用戶端

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4.x 也提供功能的能力，從主功能表中選取記錄檔中搜尋 (macOS) 或檔案總管 (Windows) 直接：

- **協助 > 顯示記錄檔**

Visual Studio For Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Visual Studio 的內容**輸出**窗格也可能會有幫助。

### <a name="project-settings"></a>專案設定

如果您可以將附加 **.csproj**對於想要檢查的專案，它會非常有幫助。 這是容易詢問有關個別設定。

也請確認您是在偵錯組態。

### <a name="selected-devices"></a>選取的裝置

適用於 Android 和 iOS，很重要，我們知道您正在偵錯在當您想要檢查哪些裝置。 我們需要知道：

- 您的 IDE 中所示的裝置名稱
- 您的裝置的 OS 版本
- Android:請確認您使用的 x86 模擬器
- Android:您是否使用模擬器平台為何？ Google 模擬器嗎？ Visual Studio Android 模擬器？ Xamarin Android Player？
- 顯示與裝置在運作正常進行偵錯的應用程式？
- 裝置是否有網路連線 （透過 web 瀏覽器的核取）？

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
