---
title: 系結疑難排解
description: 本指南說明當您無法系結目標 C 程式庫時，該怎麼辦。 特別是，它會討論遺漏的系結、將 null 傳遞至系結時的引數例外狀況，以及報告錯誤。
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
author: davidortinau
ms.author: daortin
ms.date: 10/19/2016
ms.openlocfilehash: 8194c369aa0e4f8bb17a1a162354b4f72c6aaa41
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291816"
---
# <a name="binding-troubleshooting"></a>系結疑難排解

在 Xamarin 中針對 macOS （先前稱為 OS X） Api 的系結進行疑難排解的一些秘訣。

## <a name="missing-bindings"></a>遺失的系結

雖然 Xamarin 會涵蓋大部分的 Apple Api，但有時候您可能需要呼叫一些尚未有系結的 Apple API。 在其他情況下，您必須在 Xamarin. Mac 系結範圍之外，呼叫協力廠商 C/目標-C。

如果您處理的是 Apple API，第一個步驟是讓 Xamarin 知道您即將遇到我們尚未涵蓋的 API 區段。 提出[bug](#reporting-bugs) ，記中缺少的 API。 我們會使用客戶的報告來排定我們接下來要處理的 Api。 此外，如果您有業務或企業授權，而此缺少系結會封鎖您的進度，也請遵循[支援](https://visualstudio.microsoft.com/vs/support/)的指示來提出票證。 我們無法承諾系結，但在某些情況下，我們可以讓您解決此情況。

一旦您通知 Xamarin （如果適用的話）遺失的系結，下一步就是要考慮將它系結在一起。 [這裡有完整的指南，](~/cross-platform/macios/binding/overview.md)以及[這裡](https://brendanzagaeski.appspot.com/xamarin/0002.html)的一些非官方檔，可讓您以手動方式包裝目標 C 系結。 如果您要呼叫 C API，您可以使用C#的 P/Invoke 機制，檔位於[這裡](https://www.mono-project.com/docs/advanced/pinvoke/)。

如果您決定自行處理系結，請注意系結中的錯誤可能會在原生執行時間中產生各種有趣的當機。 特別值得注意的是，中C#的簽章必須符合引數數目和每個引數大小的原生簽章。 如果無法這樣做，可能會損毀記憶體和（或）堆疊，而且您可能會立即或在未來或損毀資料的某個任意點損毀。

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>將 null 傳遞至系結時的引數例外狀況

雖然 Xamarin 可以為 Apple Api 提供高品質且經過妥善測試的系結，但有時會出現錯誤和 bug。 最常見的問題是，當基礎 API 接受 `nil`時，當您傳入 null 時，您可能會遇到的 API 擲回 `ArgumentNullException`。 定義 API 的原生標頭檔通常不會提供足夠的資訊來處理哪些 Api 接受 nil，而當您將它傳入時，將會損毀。

如果您遇到傳入 `null` 擲回 `ArgumentNullException` 但您認為應該可以運作的情況，請遵循下列步驟：

1. 請查看 Apple 檔和/或範例，以查看是否可以找到它接受的證明 `nil`。 如果您很熟悉您的目標-C，可以撰寫一個小型測試程式來進行驗證。
2. 提出[bug](#reporting-bugs)。
3. 您是否可以解決 bug？ 如果您可以避免使用 `null`來呼叫 API，則呼叫前後的簡單 null 檢查可以是很簡單的解決方法。
4. 不過，某些 Api 需要傳入 null 以關閉或停用某些功能。 在這些情況下，您可以藉由啟動元件瀏覽器（請參閱[尋找給定選取器C#的成員](~/mac/app-fundamentals/mac-apis.md#finding_selector)）、複製系結，以及移除 null 檢查來解決此問題。 如果您這樣做，請務必提出錯誤（步驟2），因為您複製的系結將不會收到我們在 Xamarin 中所做的更新和修正程式，這應該被視為短期的解決方法。

<a name="reporting-bugs"/>

## <a name="reporting-bugs"></a>報告錯誤

您的意見反應對我們非常寶貴。 如果您發現 Xamarin. Mac 有任何問題：

- 查看 [Xamarin.Mac 論壇](https://forums.xamarin.com/categories/xamarin-mac) \(英文\)
- 搜尋[問題存放庫](https://github.com/xamarin/xamarin-macios/issues) \(英文\)
- 在切換到 GitHub 問題之前，Xamarin 問題的追蹤是在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) \(英文\) 上進行的。 請從該處搜尋相符的問題。
- 如果您找不到相符的問題，請在 [GitHub 問題存放庫](https://github.com/xamarin/xamarin-macios/issues/new) \(英文\) 中提出新的問題。

GitHub 問題全都是公開的。 無法隱藏意見或附件。

請儘量包含下列資訊：

- 一個可重現問題的簡單範例。 這是**非常寶貴的**，請盡您所能提供。
- 損毀狀況的完整堆疊追蹤。
- 損毀狀況周圍的 C# 程式碼。
