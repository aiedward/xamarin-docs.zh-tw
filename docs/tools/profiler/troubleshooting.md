---
title: Xamarin Profiler 疑難排解
description: 本文件提供與 Xamarin Profiler 的疑難排解資訊。 它會描述記錄和診斷、 IDE 及其他主題的相關問題。
ms.prod: xamarin
ms.assetid: 0060E9D1-C003-4E4C-ADE8-B406978FE891
author: lobrien
ms.author: laobri
ms.date: 10/27/2017
ms.openlocfilehash: f9b4da5b6dfe3f0254340d9175b08198bd52a45a
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563338"
---
# <a name="xamarin-profiler-troubleshooting"></a>Xamarin Profiler 疑難排解

## <a name="logging-and-diagnostics"></a>記錄和診斷

Xamarin 小組可協助追蹤問題，如果您提供的詳細資訊，包括：

- 問題及損毀，或失敗，導致工作流程的螢幕錄製影片。
- 記錄會輸出 （如下所示）。
- **.Mlpd**正在產生程式碼剖析工作階段 （如下所示）。

### <a name="getting-log-outputs"></a>取得記錄輸出

在 Mac 上記錄檔會儲存到`~/Library/Logs/Xamarin.Profiler/Profiler.<date>.log`。

在 Windows 中，這些會儲存到`%appdata%Local//Xamarin/Log/Xamarin.Profiler/Profiler.<date>.log`每次您提交問題時，請包含最新的記錄檔。

我們將新增更多記錄，當我們經歷，因此這個輸出應該成長，並隨著時間變得更加實用。

<a name="gen_mlpd" />

### <a name="generating-mlpd-files"></a>產生.mlpd 檔案

**.Mlpd**檔案是壓縮 mono 執行階段程式碼剖析工具的輸出。 Xamarin Profiler GUI 資料讀入 **.mlpd**並將它顯示的使用者。 **.mlpd**檔案就非常有用的 Xamarin 偵錯工具，因為它們可以協助我們診斷的問題與您的資料，就可能發生的 Profiler 的工程師。

**.Mlpd**針對目前的工作階段會自動儲存您的 Mac`/tmp`目錄，並可由時間戳記加以識別。 如果您開啟記錄功能，為第一個輸出會的路徑 **.mlpd**檔案。 **.Mlpd**通常會啟動 ~/var/資料夾的目錄中儲存檔案...

**.Mlpd**也可以選擇儲存目前的工作階段的**檔案 > 另存新檔...** 從 [Profiler] 功能表中：

**Visual Studio for Mac**:

![](troubleshooting-images/image17.png "正在儲存.mlpd 檔案在 Visual Studio for Mac")

**Visual Studio**:

![](troubleshooting-images/image17-vs.png "在 Visual Studio 中儲存.mlpd 檔案")

請務必注意 **.mlpd**包含大量資訊，且檔案大小會很大。

## <a name="troubleshooting"></a>疑難排解

下列清單顯示常見的問題、 因應措施和祕訣和訣竅使用 Profiler。

> [!NOTE]
> **附註**： 您必須是 Visual Studio**企業**訂閱者若要解除這項功能在 Windows 上的其中一個 Visual Studio Enterprise 或 Visual Studio for mac。

#### <a name="i-cant-see-the-ios-profiler-option-or-it-is-greyed-out-visual-studio-and-visual-studio-for-mac"></a>我看不到 [iOS 程式碼剖析工具] 選項，或會呈現灰色 [Visual Studio 和 Visual Studio for Mac]

請檢查下列設定，以解決此問題：

- 請確定您使用偵錯組態
- 請確定您使用 SGen 記憶體回收行程。
- 請確定您的平台[支援](~/tools/profiler/index.md#Profiler_Support)。
- 請確定您有適當的授權。
- 確保已記錄在以及已正確驗證。
- [Visual Studio]您必須使用[Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/enterprise/)且具有有效的企業授權。

#### <a name="i-get-an-error-when-i-try-to-launch-the-profiler"></a>當我嘗試啟動分析工具時，會發生錯誤

如果您會遇到此錯誤的方塊，在 Visual Studio 中使用分析工具時：

![](troubleshooting-images/error.png "在 Visual Studio 中使用分析工具時的錯誤方塊")

它通常是由於無法啟動模擬器 / 模擬器。 請嘗試並正常執行的應用程式，修正的問題，它可提供，並再試一次使用 Profiler。

#### <a name="to-watch-a-specific-thread"></a>若要觀看特定執行緒

如果您有一個您想要特別注意的執行緒，很適合用來命名的執行緒，以取得其建立的最開頭`ThreadName`而不是`0x0`。 例如若要設定執行緒名稱做為`UI`，您可以使用下列程式碼：

```csharp
RunOnUiThread (() => {
  Thread.CurrentThread.Name  = "UI";
});
```

## <a name="related-links"></a>相關連結

- [逐步解說-使用 Xamarin Profiler](~/tools/profiler/index.md)
- [記憶體和效能的最佳作法](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [版本資訊](https://developer.xamarin.com/releases/profiler/preview/)
