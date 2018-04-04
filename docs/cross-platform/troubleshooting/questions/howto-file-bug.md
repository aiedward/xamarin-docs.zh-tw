---
title: 時機和方式我應該提出問題報告？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 1b23ea33fe19e0d9ae07d1f1e6213c65439f58b1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>時機和方式我應該提出問題報告？


在 Xamarin 的 Bugzilla bug 追蹤程式這裡檔案錯誤： [ https://bugzilla.xamarin.com/enter_bug.cgi?classification=__all ](https://bugzilla.xamarin.com/enter_bug.cgi?classification=__all)。

## <a name="file-a-bug-if"></a>如果上提報 bug...


您有一組您認為 Xamarin 工程師可以使用來重現問題的起因是 Xamarin 的步驟。

OR

特別是如果您也可以描述與問題相關的某些精確的情況下，您可以仔細描述問題，明顯的問題。<sup> [[1]](#note-1)</sup>


## <a name="best-practices-to-help-xamarin-address-bugs-quickly-and-efficiently"></a>最佳作法，快速且有效地協助 Xamarin 位址錯誤


1. <a name="ref-1" />搜尋[Bugzilla](https://bugzilla.xamarin.com/query.cgi?format=specific&amp;bug_status=__all__)和網站上的現有錯誤報表或使用方式建議可能會直接處理的問題。<sup>[[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2" />請遵循[撰寫指導方針的 bug](https://bugzilla.xamarin.com/page.cgi?id=bug-writing.html)來描述因為清楚且精確地越好，包括描述的功能發生且已預期會發生的問題。

1. <a name="ref-3" />包括任何相關的堆疊追蹤、 錯誤訊息文字，或當機記錄。 <sup>[[4]](#note-4)</sup>

1. <a name="ref-4" />記下也會出現在螢幕擷取畫面附加檔案中以純文字的任何重要的錯誤訊息。

1. <a name="ref-5" />包含小型的獨立測試案例可重現的 bug，為盡可能少的程式碼。  如果您無法重現的問題 （使用其中一個內建的範本建立） 的新專案，請壓縮示範問題的專案，並將它附加至 bug 報告。  請盡量簡單範例專案之前將它連接。<sup> [[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6" />描述在發現 bug，包括作業系統的環境和[舊版 Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md)及任何相依性。

---

## <a name="additional-details"></a>其他詳細資料

1. <a name="note-1" />[*^*](#ref-1) 在理想情況下的 < 可見症狀 > 描述應包含足夠的詳細資料，供其他客戶可以確認它們是否會看到相同的問題 (相同的錯誤訊息、 相同的效能降低、 損毀，從相同的堆疊追蹤_等等。_). 用於 「 精確的情況，「 一個好的範例是如果您可以說類似: 「 我通常叫用問題 75%的時間，但如果我變更此一件事然後可以避免發生問題完全 」。 「 精確情況"的另一個類似範例是如果降級至舊版 Xamarin 停止的問題。

1. <a name="note-2" />[*^*](#ref-2) 如您所預期，錯誤文字 （或任何其他唯一的描述性文字） 的程式碼片段是通常是最佳的搜尋詞彙。 如果現有的錯誤報告不完整，您可以隨意地加入詳細資料或新檔案中，進一步 bug 報表。

1. <a name="note-3" />[*^*](#ref-3) 另一個重要的問題是相同的問題是否報告任何 java，Objective C 或迅速的應用程式。 如果是的話，此問題會是很有可能的 Android 或 iOS 本身的一部分，而不是屬於 Xamarin。

1. <a name="note-4" />[*^*](#ref-4) 要包含資訊的一些範例：

    1. 發生錯誤時建置專案，請包含完整[診斷組建輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)bug 報告上。
    
    1. 如需建置或偵錯 iOS 專案從 Visual Studio 時，會發生的錯誤，請執行_協助 > Xamarin > Zip 記錄檔_之後遇到錯誤，而且包括 bug 報告上產生的.zip 檔案。
    
    1. 例外狀況，或在 Android 或 iOS 的應用程式的當機，請包含相關"[偵錯 Xamarin.Android 和 Xamarin.iOS 應用程式記錄檔](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps)。 」

1. <a name="note-5" />[*^*](#ref-5) 如果可能的話您的特定問題的一個絕佳的選項是將少量的檔案從原始解決方案新增至全新的解決方案，以重新建立問題。 Xamarin 小組通常可以調查問題，即使在較大的測試案例 （假設重現的步驟會清楚地說明），但更簡單的測試案例提供最佳的可能發生的快速解決 bug。


1. <a name="note-6" />[*^*](#ref-6) 如果是_不_可能藉由新增少量檔案至新的方案中重現此問題，然後您可以壓縮並將整個方案資料夾的完整應用程式。 請刪除`bin`， `obj`， `Components`，和`packages`資料夾，以便更 zip 檔案較小。 （IDE，並在建置程序將通常還原或重新建立這些資料夾的內容，視需要）。您也可以刪除許多程式碼和資源檔從專案為您喜歡，只要所產生的解決方案還示範了原始問題。

