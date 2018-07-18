---
title: 偵測器的安裝和需求
description: 本文件說明如何安裝 Xamarin 偵測器，並討論支援的作業系統、 Ide，以及應用程式平台。
ms.prod: xamarin
ms.assetid: 81174493-02D3-4FF5-AD57-04F3288A7F94
author: topgenorth
ms.author: toopge
ms.date: 06/19/2018
ms.openlocfilehash: 690329aa1577c66b3aa2794342a8e367477d3a74
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066919"
---
# <a name="inspector-installation-and-requirements"></a>偵測器的安裝和需求

## <a name="download-and-installation"></a>下載和安裝

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. 下載並安裝[Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/)選取**行動應用程式開發的.NET**工作負載。
1. [登入](https://docs.microsoft.com/visualstudio/ide/signing-in-to-visual-studio)啟用您的企業訂閱。
1. [檢查](~/tools/inspector/inspect.md)您自己的應用程式 ！

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. 下載並安裝[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。
1. [登入](https://docs.microsoft.com/visualstudio/mac/activation)啟用您的企業訂閱。
1. [檢查](~/tools/inspector/inspect.md)您自己的應用程式 ！

-----

## <a name="requirements"></a>需求

### <a name="supported-operating-systems"></a>Supported Operating Systems

- **Mac** -OS X 10.11 或更新版本
- **Windows** -Windows 7 或更新版本 (使用 Internet Explorer 11 或更高和.NET 4.6.1 或更新版本)

### <a name="supported-ides"></a>支援的 Ide

- Visual Studio for Mac
- 使用 visual Studio 2017**行動應用程式開發的.NET**工作負載

企業客戶可使用即時應用程式檢查。

<a name="supported-platforms" />

### <a name="supported-app-platforms"></a>支援的應用程式平台

|應用程式平台|IDE 支援|注意|
|--- |--- |--- |
|Mac|只支援 Visual Studio for Mac|
|iOS|在 Visual Studio 2017 和 Visual Studio for Mac 支援| |
|Android|在 Visual Studio 2017 和 Visual Studio for Mac 支援|必須為目標 Android > = 4.0.3， **fastdev**啟用。<br />必須使用 Google、 Visual Studio 或 Xamarin Android 模擬器。 Android 7 模擬器可能不允許在此階段的檢查。|
|WPF|在 Visual Studio 2017 中才支援|

<a name="reporting-bugs" />

## <a name="reporting-bugs"></a>回報 Bug

應該直接透過 Visual Studio 報告的 bug︰

- **協助 > 傳送意見反應 > 回報問題**

請包含所有的下列資訊：

### <a name="platform-version-information"></a>平台版本資訊

這項資訊是很重要。

Visual Studio For Mac

- **Visual Studio > 有關 Visual Studio > 顯示詳細資料 > 複製資訊**
- 貼到 錯誤報告

Visual Studio

- **協助 > 有關 Visual Studio > 複製資訊**
- 讓我們知道您的作業系統版本和您執行的 32 位元或 64 位元 Windows。

### <a name="log-files"></a>記錄檔

請務必附加 IDE 與 檢查用戶端記錄檔。

偵測器用戶端

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

1.4.x 也提供功能的能力，從主功能表中選取記錄檔中搜尋 (macOS) 或檔案總管 (Windows) 直接：

- **協助 > 顯示記錄檔**

Visual Studio For Mac

- `~/Library/Logs/VisualStudio/7.0/Ide.log`

Visual Studio

- `%LOCALAPPDATA%\Xamarin\Logs\{VS version}\Inspector {date}.log`
- Visual Studio 內容**輸出**窗格也可能會有幫助。

### <a name="project-settings"></a>專案設定

如果您可以將附加 **.csproj**對於想要檢查的專案，會是非常有幫助。 這是更為詢問有關個別設定。

也請確認您是在偵錯組態。

### <a name="selected-devices"></a>選取的裝置

適用於 Android 和 iOS，很重要，我們會知道您正在偵錯在當您想要檢查哪些裝置。 我們需要知道：

- 您的 IDE 中所示的裝置名稱
- 您的裝置 OS 版本
- Android： 確認您使用的 x86 模擬器
- Android： 模擬器平台您使用？ Google 模擬器嗎？ Android 的 visual Studio 模擬器嗎？ Xamarin Android Player 嗎？
- 未正確地進行偵錯應用程式會出現和函式中的裝置？
- 裝置是否有網路連線能力 （透過 web 瀏覽器檢查）？

[client-bugs]: https://github.com/Microsoft/workbooks/issues/new
