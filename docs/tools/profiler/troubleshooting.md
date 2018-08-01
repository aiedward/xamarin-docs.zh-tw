---
title: Xamarin 分析工具疑難排解
description: 本文件提供 Xamarin 分析工具的相關的疑難排解資訊。 它會描述與記錄和診斷，在 IDE 中其他主題相關的問題。
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: topgenorth
ms.author: toopge
ms.date: 10/27/2017
ms.openlocfilehash: 71faf79ef9b783480dbb6ff4674859a9148abca3
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066906"
---
# <a name="xamarin-profiler-troubleshooting"></a>Xamarin 分析工具疑難排解

## <a name="logging-and-diagnostics"></a>記錄和診斷功能

Xamarin 小組有助於追蹤問題，如果您提供使用資訊，包括：

- 問題、 損毀，或失敗以及它導致工作流程錄影畫面。
- 記錄檔輸出 （如下所示）。
- **.Mlpd**所產生的程式碼剖析工作階段 （請參閱下文）。

### <a name="getting-log-outputs"></a>取得記錄檔輸出

記錄檔儲存至 Mac 上`~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`。

在 Windows 中，這些會儲存到`%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log`每當您提交的問題，請包含最新的記錄。

我們正在將加入詳細記錄，因為我們移，讓這個輸出應該成長，並隨著時間變得更有用。

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>產生.mlpd 檔案

**.Mlpd**檔案是壓縮單聲道的執行階段程式碼剖析工具的輸出。 Xamarin 分析工具 GUI 會讀取來自資料 **.mlpd**並將它顯示為使用者。 **.mlpd**檔案就非常有用的 Xamarin 偵錯工具，因為它們可以協助我們診斷的問題與您的資料，分析工具可能會發生的工程師。

**.Mlpd**的目前工作階段會自動儲存您的 Mac`/tmp`目錄，而且可以識別依時間戳記。 如果您開啟記錄功能時，第一個輸出會路徑 **.mlpd**檔案。 **.Mlpd**檔案通常會儲存在目錄啟動 ~/var/資料夾...

**.Mlpd**也可以選擇儲存目前的工作階段的**檔案 > 另存新檔...** 從程式碼剖析工具 功能表：

**Visual Studio for Mac**:

![](troubleshooting-images/image17.png "正在儲存.mlpd 檔案在 Visual Studio for Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "在 Visual Studio 中儲存.mlpd 檔案")

請務必注意 **.mlpd**包含許多資訊，而且檔案大小會是大。

## <a name="troubleshooting"></a>疑難排解

下列清單顯示常見的問題、 因應措施，和秘訣和訣竅使用分析工具。

> [!NOTE]
> **請注意**： 您必須是 Visual Studio**企業**「 訂閱者 」 解除這項功能在 Windows 上的其中一個 Visual Studio Enterprise 或 Visual Studio for mac。

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>看不到 [iOS 程式碼剖析工具] 選項，或灰色 [Visual Studio 和 Visual Studio for Mac]

檢查以解決此問題的下列設定：

- 請確定您使用偵錯組態
- 確認您使用 SGen 記憶體回收行程。
- 請確定平台是[支援](~/tools/profiler/index.md#Profiler_Support)。
- 請確定您具有正確的授權。
- 確認您已登入，並正確通過驗證。
- [Visual Studio]您必須使用[Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/)而且具有有效的企業授權。

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>當我嘗試啟動分析工具時收到錯誤

如果您在此錯誤中時執行使用 Visual Studio 中的程式碼剖析工具：

![](troubleshooting-images/error.png "錯誤方塊時使用 Visual Studio 中的程式碼剖析工具")

它通常是由於無法啟動模擬器 / 模擬器。 再試一次，並執行應用程式通常，修正的問題，它可提供，然後再試一次使用分析工具。

#### <a name="to-watch-a-specific-thread"></a>若要觀察特定的執行緒

如果您有想要特別注意的執行緒，很適合用來命名執行緒中的最開頭建立，好讓 get 取得`ThreadName`而不是`0x0`。 例如若要為 UI 中設定執行緒名稱，您可以使用下列程式碼：

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>相關連結

- [逐步解說-使用 Xamarin 分析工具](~/tools/profiler/index.md)
- [記憶體和效能的最佳作法](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [版本資訊](https://developer.xamarin.com/releases/profiler/preview/)
