---
title: 提出 Bug 報告的時機與方式為何？
description: 本文件說明何時、 何處，以及如何提出錯誤報告。 它也會提供最佳的作法，讓工程師最佳診斷問題的錯誤報告。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: conceptdev
ms.author: crdun
ms.date: 08/01/2018
ms.openlocfilehash: 1224d38a2230fa2f5c7ca08f6e33c5468886c206
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61356983"
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>提出 Bug 報告的時機與方式為何？

> [!TIP]
> 使用**回報的問題**Visual Studio 中的功能表項目&ndash;這會傳送錯誤報表可協助您解決問題以及診斷資訊。
>
> 有詳細的指示[Visual Studio 2019 或 Visual Studio 2017](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio)並[Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/report-a-problem)。
>
> 您可以在搜尋現有的報表[Visual Studio 開發人員社群](https://developercommunity.visualstudio.com/)網站。

## <a name="file-a-bug-if"></a>如果上提報 bug...

您有一組步驟的您認為工程師將能夠使用來重現問題。

OR

特別是如果您也可以描述與問題相關的某些精確的情況下，您可以仔細描述問題，明顯的問題。<sup> [[1]](#note-1)</sup>

## <a name="best-practices-to-help-address-bugs-quickly-and-efficiently"></a>最佳作法，快速且有效率地協助位址錯誤

1. <a name="ref-1" />搜尋[Visual Studio 開發人員社群](https://developercommunity.visualstudio.com/)和網站上的現有 bug 報告] 或 [可能直接解決問題的使用方式建議。<sup>[[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2" />描述的問題，以清楚且精確地越好，包括什麼發生，而必須進行的描述。

1. <a name="ref-3" />包括任何相關的堆疊追蹤、 錯誤訊息文字，或當機記錄 (如果您使用**回報問題**功能，這些可以是包含自動)。 <sup>[[4]](#note-4)</sup>

1. <a name="ref-4" />記下也會出現在螢幕擷取畫面附件純文字格式的任何重要的錯誤訊息。

1. <a name="ref-5" />包含小型的獨立測試案例可重現的 bug，為盡可能少的程式碼。  如果您無法重現 （使用其中一個內建的範本建立） 的全新專案的問題，請壓縮示範問題的專案，並將它附加至 bug 報告。  請再將它附加範例專案越簡單越好。<sup> [[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6" />描述其中發現 bug，包括作業系統的環境並[版本的 Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md)和任何相依性。

## <a name="additional-details"></a>其他詳細資料

1. <a name="note-1" />[*^*](#ref-1) 在理想情況下的 < 顯示徵狀 > 描述應包含足夠的詳細資料，如此其他客戶可以確認他們是否看到相同的問題 (相同的錯誤訊息、 相同的效能降低、 當機，從相同的堆疊追蹤_等等。_). 「 精確的情況下 」，一個很好的範例會是如果您可以說：「 我通常按問題 75%的時間，但如果我將這一件事然後我可以避免發生問題完全 」。 另一個類似"精確情況 」 範例是如果降級至舊版 Xamarin 會停止的問題。

1. <a name="note-2" />[*^*](#ref-2) 如您所預期，錯誤文字 （或任何其他的唯一描述性文字） 的程式碼片段是通常是最佳的搜尋詞彙。 如果現有的 bug 報告不完整，您可以自由地新增詳細資料或新檔案中，進一步錯誤報告。

1. <a name="note-3" />[*^*](#ref-3) 另一個重要的問題是相同的問題是否已報告的任何 Java、 OBJECTIVE-C、 或 Swift 應用程式。 如果是這樣，問題則很有可能的 Android 或 iOS 本身的一部分，而不是 Xamarin 的一部分。

1. <a name="note-4" />[*^*](#ref-4) 要包含的資訊的一些範例：

    1. 發生錯誤時建置專案，請包含完整[診斷組建輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)bug 報告上。

    1. 建置或偵錯 iOS 專案從 Visual Studio 時所發生的錯誤，請執行**協助 > Xamarin > Zip 記錄檔**之後遇到錯誤，並包含在 bug 報告上產生的.zip 檔案。

    1. 例外狀況或 Android 或 iOS 的應用程式中的當機，請包含相關[偵錯 Xamarin.Android 和 Xamarin.iOS 應用程式的記錄檔](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps)。

1. <a name="note-5" />[*^*](#ref-5) 如果可能的話您特定的問題，其中一個選項是透過將從您的原始方案的一小部分的檔案新增到全新的解決方案中重現問題。 Xamarin 小組通常可以調查甚至是在較大的測試案例 （假設重現的步驟會清楚地說明），但更簡單的測試案例提供最佳的可能發生的錯誤將會快速地解決問題。

1. <a name="note-6" />[*^*](#ref-6) 如果它是_不_可能藉由將全新的解決方案中的一小部分的檔案重現問題，然後您可以壓縮並將整個方案的資料夾完整應用程式。 請刪除`bin`， `obj`， `Components`，和`packages`資料夾，以便更 zip 檔案較小。 （IDE 和建置程序將通常還原或重新建立這些資料夾的內容，視需要）。只要所產生的方案仍會示範了原始問題，您也可以刪除多個程式碼和資源檔從專案依照您的意願。
