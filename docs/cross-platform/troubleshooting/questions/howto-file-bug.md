---
title: 提出 Bug 報告的時機與方式為何？
description: 本檔說明何時、何處和如何提出錯誤報表。 它也提供 bug 報告最佳作法，讓工程師能夠最佳地診斷問題。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: davidortinau
ms.author: daortin
ms.date: 08/01/2018
ms.openlocfilehash: df00eebe682d2d06b99721a2d3c3b90d13454c75
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013998"
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>提出 Bug 報告的時機與方式為何？

> [!TIP]
> 使用 Visual Studio 中的 [回報**問題**] 功能表項目 &ndash; 這會將診斷資訊連同您的錯誤報表一起傳送，以協助解決問題。
>
> [Visual Studio 2019 或 Visual Studio 2017](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio)和[Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/report-a-problem)的詳細指示。
>
> 您可以在[Visual Studio Developer 論壇](https://developercommunity.visualstudio.com/)網站上搜尋現有的報告。

## <a name="file-a-bug-if"></a>若 ...，請提出 bug

您有一組您認為工程師能夠用來重現問題的步驟。

OR

您可以仔細描述問題的可見徵兆，特別是當您也可以描述與問題相關的一些精確情況時。<sup> [[1]](#note-1)</sup>

## <a name="best-practices-to-help-address-bugs-quickly-and-efficiently"></a>協助快速且有效率地解決 bug 的最佳做法

1. <a name="ref-1" />搜尋[Visual Studio 開發人員的社區](https://developercommunity.visualstudio.com/)和 web，以取得可能直接解決問題的現有錯誤報表或使用方式建議。<sup>[[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2" />以清楚且簡潔的方式來描述問題，包括發生的原因和預期發生的描述。

1. <a name="ref-3" />包含任何相關的堆疊追蹤、錯誤訊息正文或損毀記錄檔（如果您使用 [回報**問題**] 功能，這些都可以自動包含）。 <sup>[4gb](#note-4)</sup>

1. <a name="ref-4" />寫下以純文字顯示在螢幕擷取畫面附件中的任何重要錯誤訊息。

1. <a name="ref-5" />包含一個小型、獨立的測試案例，會盡可能少的程式碼重現 bug。  如果您無法使用全新的專案（使用其中一個內建範本建立）來重現問題，則請壓縮示範問題的專案，並將其附加至 bug 報告。  在附加範例專案之前，盡可能將它設為簡單。<sup>[[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6" />描述遇到 bug 的環境，包括作業系統和[Xamarin 版本](~/cross-platform/troubleshooting/questions/version-logs.md)，以及任何相依性。

## <a name="additional-details"></a>其他詳細資料

1. <a name="note-1" />[ *^* ](#ref-1)在理想的情況下，「可見的徵兆」的描述應該包含足夠的詳細資料，讓其他客戶可以確認是否看到相同的問題（相同的錯誤訊息、效能降低、與相同的堆疊追蹤損毀_等等）。_ 針對「精確的情況」，有一個很好的例子，就是如果您可以說：「我通常會遇到75% 的問題，但如果我變更了這一項，就可以完全避免這個問題。」 「精確情況」的另一個類似範例是，如果降級為舊版 Xamarin 會停止問題。

1. <a name="note-2" />[ *^* ](#ref-2)如您所預期，錯誤文字程式碼片段（或任何其他唯一描述性文字）通常是最佳的搜尋詞彙。 如果現有的 bug 報告不完整，您可以加入詳細資料，或提出新的更好的錯誤報表。

1. <a name="note-3" />[ *^* ](#ref-3)另一個很好的問題，就是是否已針對任何 JAVA、目標 C 或 Swift 應用程式回報相同的問題。 若是如此，問題很可能是 Android 或 iOS 本身的一部分，而不是 Xamarin 的一部分。

1. <a name="note-4" />[ *^* ](#ref-4)幾個資訊範例，包括：

    1. 若為建立專案時所發生的錯誤，請在錯誤報表中包含完整的[診斷組建輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)。

    1. 針對從 Visual Studio 建立或偵測 iOS 專案時所發生的錯誤，請在遇到錯誤之後， **> Xamarin > Zip 記錄**檔中執行說明，並在錯誤報表中包含產生的 .zip 檔案。

    1. 若為 Android 或 iOS 應用程式中的例外狀況或損毀，請包含適用于[xamarin 和 Xamarin ios 應用程式的相關 Debug 記錄](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps)檔。

1. 針對您的特定問題，<a name="note-5" />[ *^* ](#ref-5) ，其中一個選項是將少量檔案從原始解決方案新增至全新的解決方案，以重新建立問題。 即使在較大型的測試案例中，Xamarin 小組通常也可以調查問題（假設重現的步驟很清楚），但是較簡單的測試案例可讓您更快速地解決 bug。

1. <a name="note-6" />[ *^* ](#ref-6)如果_無法_藉由將少量檔案新增至全新的解決方案來重現問題，則可以為完整的應用程式壓縮並附加整個解決方案資料夾。 請刪除 `bin`、`obj`、`Components`和 `packages` 資料夾，讓 zip 檔案變小。 （IDE 和組建程式通常會視需要還原或重新建立這些資料夾的內容）。您也可以視需要刪除專案中的多個程式碼和資源檔，前提是產生的解決方案仍然會展示原始問題。
