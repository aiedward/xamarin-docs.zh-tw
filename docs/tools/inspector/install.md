---
title: Inspector 安裝和需求
description: 本檔說明如何安裝 Xamarin Inspector，並討論支援的作業系統、Ide 和應用程式平臺。
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: davidortinau
ms.author: daortin
ms.date: 06/19/2018
ms.openlocfilehash: 19c4a15fb2490c7bace4798b0cb8e062b1379a04
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305063"
---
# <a name="inspector-installation-and-requirements"></a>Inspector 安裝和需求

## <a name="download-and-installation"></a>下載並安裝

# <a name="windows"></a>[Windows](#tab/windows)

1. 下載並安裝[Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/)並選取 [**使用 .net 進行行動開發**] 工作負載。
1. 登入以啟用您的企業訂[用](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio)帳戶。
1. [檢查](~/tools/inspector/inspect.md)您自己的應用程式！

# <a name="macos"></a>[macOS](#tab/macos)

1. 下載並安裝[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。
1. 登入以啟用您的企業訂[用](https://docs.microsoft.com/visualstudio/mac/activation)帳戶。
1. [檢查](~/tools/inspector/inspect.md)您自己的應用程式！

-----

## <a name="requirements"></a>需求

### <a name="supported-operating-systems"></a>支援的作業系統

- **Mac** -OS X 10.11 或更新版本
- **Windows** -windows 7 或更新版本（包含 Internet Explorer 11 或更新版本，以及 .net 4.6.1 或更高版本）

### <a name="supported-ides"></a>支援的 Ide

- Visual Studio for Mac
- 使用 .NET 工作負載進行行動裝置**開發**Visual Studio 2017

即時應用程式檢查適用于企業客戶。

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>支援的應用程式平臺

|應用程式平台|IDE 支援|注意|
|--- |--- |--- |
|Mac|僅 Visual Studio for Mac 中支援|
|iOS|支援 Visual Studio 2017 和 Visual Studio for Mac| 連結器行為必須設定為 [**不要連結**] （在 [ **iOS 組建**專案] 選項底下） |
|Android|支援 Visual Studio 2017 和 Visual Studio for Mac|必須以 Android > = 4.0.3 為目標，且已啟用**fastdev** 。<br />必須使用 Google、Visual Studio 或 Xamarin Android 模擬器。 Android 7 模擬器目前可能不允許檢查。|
|WPF|僅 Visual Studio 2017 中支援|

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>報告錯誤 (Bug)

Bug 應該透過 Visual Studio 直接回報：

- **協助 > 傳送意見反應 > 回報問題**

請包含下列所有資訊：

### <a name="platform-version-information"></a>平臺版本資訊

這是很重要的資訊。

Mac 的 Visual Studio

- **Visual Studio > 關於 Visual Studio > 顯示詳細資料 > 複製資訊**
- 貼入 bug 報告

Visual Studio

- **協助 > Visual Studio > 複製資訊**
- 請讓我們知道您的作業系統版本，以及您是否執行32位或64位的 Windows。

### <a name="log-files"></a>記錄檔

一律附加 IDE 和偵測器用戶端記錄檔。

偵測器用戶端

- Mac：`~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4. x 也能夠直接從主功能表選取搜尋工具（macOS）或 Explorer （Windows）中的記錄檔：

- **說明 > 顯示記錄檔**

Mac 的 Visual Studio

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- [Visual Studio**輸出**] 窗格的內容可能也會有資訊。

### <a name="project-settings"></a>專案設定

如果您可以為嘗試檢查的專案附加 **.csproj** ，這會非常有説明。 這比詢問個別設定更容易。

此外，請確認您正在進行 [偵錯工具設定]。

### <a name="selected-devices"></a>選取的裝置

針對 Android 和 iOS，我們一定要知道您要在何時進行檢查的裝置。 我們需要知道：

- 您的 IDE 中所示的裝置名稱
- 裝置的 OS 版本
- Android：確認您使用的是 x86 模擬器
- Android：您所使用的模擬器平臺為何？ Google 模擬器？ Visual Studio Android Emulator 嗎？ Xamarin Android Player？
- 您正在偵測的應用程式是否會在裝置中正確顯示和運作？
- 裝置是否具有網路連線能力（透過網頁瀏覽器檢查）？

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
