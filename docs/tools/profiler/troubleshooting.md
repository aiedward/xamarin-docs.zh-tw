---
title: Xamarin Profiler 疑難排解
description: 本檔提供與 Xamarin Profiler 相關的疑難排解資訊。 它會描述與記錄和診斷、IDE 和其他主題相關的問題。
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: davidortinau
ms.author: daortin
ms.date: 10/27/2017
ms.openlocfilehash: 93c3f4dcb56710c72cdc61c25aa6481fbd27582e
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86934910"
---
# <a name="xamarin-profiler-troubleshooting"></a>Xamarin Profiler 疑難排解

## <a name="logging-and-diagnostics"></a>記錄和診斷

如果您提供資訊給我們，Xamarin 小組可以協助追蹤問題，包括：

- 問題、損毀或失敗的螢幕錄製影片，以及您的工作流程。
- 記錄輸出（請參閱下文）。
- 為程式碼剖析會話產生的**mlpd** （請參閱下文）。

### <a name="getting-log-outputs"></a>取得記錄輸出

Mac 記錄檔上的會儲存到 `~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log` 。

在 Windows 上，這些會儲存到， `%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log` 請在每次提交問題時包含最新的記錄檔。

我們會加入更多記錄，因此，此輸出應該隨著時間成長而變得更有用。

<a name="gen_mlpd"></a>

### <a name="generating-mlpd-files"></a>產生 mlpd 檔案

**Mlpd**檔案是 mono 執行時間分析工具的壓縮輸出。 Xamarin Profiler GUI 會從**mlpd**讀取資料，並將它顯示給使用者。 **Mlpd**檔案是適用于 Xamarin 的偵錯工具，因為它們可協助我們的工程師診斷分析工具可能與您的資料有關的問題。

目前會話的**mlpd**會自動儲存在 Mac 的 `/tmp` 目錄中，並可由時間戳記識別。 如果您開啟記錄功能，第一個輸出將會是**mlpd**檔案的路徑。 **Mlpd**檔案通常會儲存在目錄中，從 ~/var/folders.。。

您也可以選擇 [檔案] **> [另存**新檔 ...]，來儲存目前會話的**mlpd** 。 從 Profiler 的功能表：

**Visual Studio for Mac**：

![在 Visual Studio for Mac 中儲存 mlpd 檔案](troubleshooting-images/image17.png)

**Visual Studio**：

![在 Visual Studio 中儲存 mlpd 檔案](troubleshooting-images/image17-vs.png)

請務必注意， **mlpd**包含許多資訊，而且檔案大小會很大。

## <a name="troubleshooting"></a>疑難排解

下列清單顯示流量分析工具的常見問題、因應措施，以及秘訣和訣竅。

> [!NOTE]
> 您必須是 Visual Studio **Enterprise**訂閱者，才能在 Windows 或 Visual Studio for Mac 上的 Visual Studio Enterprise 解除鎖定這項功能。

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>我看不到 [iOS profiler] 選項，或是呈現灰色 [Visual Studio 和 Visual Studio for Mac]

請檢查下列設定以解決此問題：

- 請確定您使用的是 Debug 設定
- 請確定您使用的是 SGen 垃圾收集行程。
- 請確定已[支援](~/tools/profiler/index.md#Profiler_Support)平臺。
- 請確定您擁有正確的授權。
- 請確定您已登入並適當地進行驗證。
- [Visual Studio]您必須使用[Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/)並擁有有效的企業授權。

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>我在嘗試啟動 profiler 時收到錯誤

如果您在 Visual Studio 中使用 profiler 時遇到此錯誤方塊：

![在 Visual Studio 中使用 profiler 時的錯誤方塊](troubleshooting-images/error.png)

通常是因為無法啟動至模擬器/模擬器。 請嘗試正常執行應用程式，修正它所提供的問題，然後再次嘗試使用 Profiler。

#### <a name="to-watch-a-specific-thread"></a>監看特定執行緒

如果您有想要特別監看的執行緒，最好是在其建立的開頭，將執行緒命名為 get， `ThreadName` 而不是 `0x0` 。 例如，若要將執行緒名稱設定為 `UI` ，您可以使用下列程式碼：

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>相關連結

- [逐步解說-使用 Xamarin Profiler](~/tools/profiler/index.md)
- [記憶體和效能最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [版本資訊](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/profiler/preview/index.md)
