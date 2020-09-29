---
title: 偵測器安裝和需求
description: 本檔說明如何安裝 Xamarin Inspector，並討論支援的作業系統、Ide 和應用程式平臺。
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: davidortinau
ms.author: daortin
ms.date: 06/19/2018
ms.openlocfilehash: 70bc0f90b2802587dba8e2da19430164fccfe861
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457337"
---
# <a name="inspector-installation-and-requirements"></a>偵測器安裝和需求

## <a name="download-and-installation"></a>下載並安裝

# <a name="windows"></a>[Windows](#tab/windows)

1. 下載並安裝 [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/) ，並選取 **使用 .net** 工作負載的行動裝置開發。
1. 登入以啟用您的 Enterprise 訂[用](/visualstudio/ide/signing-in-to-visual-studio)帳戶。
1. [檢查](~/tools/inspector/inspect.md) 您自己的應用程式！

# <a name="macos"></a>[macOS](#tab/macos)

1. 下載並安裝 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。
1. 登入以啟用您的 Enterprise 訂[用](/visualstudio/mac/activation)帳戶。
1. [檢查](~/tools/inspector/inspect.md) 您自己的應用程式！

-----

## <a name="requirements"></a>需求

### <a name="supported-operating-systems"></a>支援的作業系統

- **Mac** -OS X 10.11 或更新版本
- **Windows** -windows 7 或更高版本 (Internet Explorer 11 或更新版本，以及 .net 4.6.1 或更高版本) 

### <a name="supported-ides"></a>支援的 Ide

- Visual Studio for Mac
- 使用 .NET 工作負載的行動裝置 **開發** Visual Studio 2017

適用于企業客戶的即時應用程式檢查。

<a name="supported-platforms"></a>

### <a name="supported-app-platforms"></a>支援的應用程式平臺

|應用程式平台|IDE 支援|備註|
|--- |--- |--- |
|Mac|僅 Visual Studio for Mac 支援|
|iOS|支援 Visual Studio 2017 和 Visual Studio for Mac| 連結器行為必須設定為 [**不**](~/ios/deploy-test/linker.md) 在 **iOS 組建** 專案選項下連結 ()  |
|Android|支援 Visual Studio 2017 和 Visual Studio for Mac|必須將目標設為 Android >= 4.0.3，並啟用 **fastdev** 。<br />必須使用 Google、Visual Studio 或 Xamarin Android 模擬器。 Android 7 模擬器目前可能不允許檢查。|
|WPF|只有 Visual Studio 2017 才支援|

<a name="reporting-bugs"></a>

## <a name="reporting-bugs"></a>報告錯誤 (Bug)

Bug 應該透過 Visual Studio 直接回報：

- **協助 > 傳送意見反應 > 回報問題**

請包含下列所有資訊：

### <a name="platform-version-information"></a>平臺版本資訊

這項資訊非常重要。

適用于 Mac 的 Visual Studio

- **Visual Studio > Visual Studio > 顯示詳細資料 > 複製資訊**
- 貼上 bug 報告

Visual Studio

- **協助 > Visual Studio > 複製資訊**
- 請讓我們知道您的作業系統版本，以及您是否正在執行32位或64位 Windows。

### <a name="log-files"></a>記錄檔

一律附加 IDE 和偵測器用戶端記錄檔。

偵測器用戶端

- Mac：`~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4. x 也提供在搜尋工具中選取記錄檔的功能 (macOS) 或 Explorer (Windows) 直接從主功能表中選取：

- **說明 > 顯示記錄檔**

適用于 Mac 的 Visual Studio

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Visual Studio **輸出** ] 窗格的內容也可能有資訊。

### <a name="project-settings"></a>專案設定

如果您可以附加您嘗試檢查之專案的 **.csproj** ，這會非常有説明。 這比詢問個別設定更容易。

此外，請確認您正在進行偵錯工具設定。

### <a name="selected-devices"></a>選取的裝置

針對 Android 和 iOS，當您想要檢查時，我們一定要知道您正在進行哪些裝置的偵錯工具。 我們需要知道：

- 您的 IDE 中所顯示的裝置名稱
- 裝置的作業系統版本
- Android：確認您使用的是 x86 模擬器
- Android：您使用的是什麼模擬器平臺？ Google 模擬器？ Visual Studio Android Emulator？ Xamarin Android Player？
- 您正在進行偵錯工具的應用程式是否正確出現並在裝置中運作？
- 裝置是否具有網路連線能力 (透過網頁瀏覽器) 檢查？

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new