---
title: 提出 Bug 報告的時機與方式為何？
description: 本檔說明何時、哪裡和如何提出錯誤報表。 它也提供 bug 報告的最佳作法，讓工程師能以最佳方式診斷問題。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: davidortinau
ms.author: daortin
ms.date: 08/01/2018
ms.openlocfilehash: 38a96f5f499b760aedcfc51dc9e6326c9c62e95b
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457870"
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>提出 Bug 報告的時機與方式為何？

> [!TIP]
> 使用 Visual Studio 中的 [回報 **問題** ] 功能表項目， &ndash; 這將會連同您的錯誤報表一起傳送診斷資訊，以協助解決問題。
>
> [Visual Studio 2019 或 Visual Studio 2017](/visualstudio/ide/how-to-report-a-problem-with-visual-studio)和[Visual Studio for Mac](/visualstudio/mac/report-a-problem)的詳細指示。
>
> 您可以在 [Visual Studio 開發人員社群](https://developercommunity.visualstudio.com/) 網站上搜尋現有的報表。

## <a name="file-a-bug-if"></a>如果 ...，請提出 bug

您有一組您認為工程師可以用來重現問題的步驟。

或者

您可以仔細描述問題的可見徵兆，特別是當您也可以描述與問題相關的一些精確情況時。<sup> [[1]](#note-1)</sup>

## <a name="best-practices-to-help-address-bugs-quickly-and-efficiently"></a>協助您快速且有效率地解決 bug 的最佳作法

1. <a name="ref-1"></a>搜尋 [Visual Studio 開發人員社群](https://developercommunity.visualstudio.com/) 和 web，以尋找可直接解決問題的現有 bug 報告或使用建議。<sup>[[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2"></a>盡可能清楚且簡潔地描述問題，包括發生狀況的描述，以及預期發生的情況。

1. <a name="ref-3"></a>如果您使用 [回報問題] 功能，請包含任何相關的堆疊追蹤、錯誤訊息正文或損毀記錄 (如果您使用 [回報 **問題** ] 功能，這些功能可以自動包含) 。 <sup>[億](#note-4)</sup>

1. <a name="ref-4"></a>以純文字形式寫下出現在螢幕擷取畫面附件中的任何重要錯誤訊息。

1. <a name="ref-5"></a>包含一個小型的獨立測試案例，可在最少程式碼的情況下重現 bug。  如果您無法使用其中一個內建) 範本 (建立新專案的問題，則請壓縮展示問題的專案，並將其附加至錯誤報表。  讓範例專案盡可能簡單，再附加它。<sup>[[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6"></a>描述發生 bug 的環境，包括作業系統和 [Xamarin 版本](~/cross-platform/troubleshooting/questions/version-logs.md) 以及任何相依性。

## <a name="additional-details"></a>其他詳細資料

1. <a name="note-1"></a>[*^*](#ref-1) 在理想情況下，「可見的徵兆」描述應該包含足夠的詳細資料，讓其他客戶可以確認是否看到相同的問題 (相同的錯誤訊息、相同的效能降低、損毀的相同堆疊追蹤 _等_) 。 在「精確的情況」中，有一個很好的例子，就是如果您可以說：「我通常會在75% 的時間遇到問題，但是如果我變更了這一件事，就可以完全避免問題。」 「確切情況」的另一個類似範例是，如果降級至舊版 Xamarin，就會停止問題。

1. <a name="note-2"></a>[*^*](#ref-2) 如您所預期，錯誤文字的程式碼片段 (或任何其他獨特的描述性文字) 通常是最佳的搜尋字詞。 如果現有的錯誤報表未完成，您就可以加入詳細資料，或提出新的更好的錯誤報表。

1. <a name="note-3"></a>[*^*](#ref-3) 另一個不錯的問題是，是否已針對任何 JAVA、目標 C 或 Swift 應用程式回報相同的問題。 如果是，則問題很可能是 Android 或 iOS 本身的一部分，而不是 Xamarin 的一部分。

1. <a name="note-4"></a>[*^*](#ref-4) 以下是一些要包含的資訊範例：

    1. 針對建立專案時所發生的錯誤，請在 bug 報告中包含完整的 [診斷組建輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output) 。

    1. 針對從 Visual Studio 建立或偵測 iOS 專案時所發生的錯誤，請在發生錯誤之後， **> Xamarin > Zip 記錄** 檔執行說明，並在 bug 報告中包含產生的 .zip 檔案。

    1. 針對 Android 或 iOS 應用程式中的例外狀況或損毀，請包含適用 [于 Xamarin 和 Xamarin ios 應用程式的相關 Debug 記錄](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps)檔。

1. <a name="note-5"></a>[*^*](#ref-5) 如果您的特定問題可能的話，有一個選項是從原始方案將少量的檔案新增至全新的解決方案，以重新建立問題。 Xamarin 團隊通常可以在較大型的測試案例上調查問題， (假設重現的步驟會清楚地) ，但是更簡單的測試案例提供最有機會迅速解決 bug 的機會。

1. <a name="note-6"></a>[*^*](#ref-6) 如果 _無法_ 藉由將少量的檔案新增至全新的解決方案來重現問題，您可以為整個應用程式壓縮和附加整個方案資料夾。 請刪除 `bin` 、、 `obj` `Components` 和資料夾， `packages` 讓 zip 檔案變得更小。  (IDE 和組建程式通常會視需要還原或重新建立這些資料夾的內容。 ) 您也可以視需要從專案中刪除任意數量的程式碼和資源檔，只要產生的解決方案仍會示範原始問題。