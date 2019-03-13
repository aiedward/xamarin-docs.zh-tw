---
title: 繫結進行疑難排解
description: 本指南說明如果您無法繫結 Objective C 程式庫，該怎麼辦。 特別是，它還會討論遺漏繫結，將 null 傳遞至繫結，並回報 bug 時的引數例外狀況。
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
author: asb3993
ms.author: amburns
ms.date: 10/19/2016
ms.openlocfilehash: fcdd712313becd1335479013f44886086dde7bff
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668058"
---
# <a name="binding-troubleshooting"></a>繫結進行疑難排解

疑難排解 macOS （之前稱為 OS X） 的繫結的一些秘訣 Xamarin.Mac 中的 Api。

## <a name="missing-bindings"></a>遺漏繫結

雖然 Xamarin.Mac 涵蓋了大部分的 Apple Api，有時候您可能需要呼叫一些沒有繫結的 Apple API 尚未。 在其他情況下，您必須呼叫第三方 C/OBJECTIVE-C 範圍以外的 Xamarin.Mac 繫結它。

如果您正在處理的 Apple API，第一個步驟是讓 Xamarin 知道您會達到 API 的一個區段，如尚未沒有涵蓋範圍。 [提報 bug](#reporting-bugs)注意遺漏的 API。 我們會使用來自客戶的報告來排定優先順序的 Api，我們正致力於下一步。 此外，如果您有商務或企業授權，而且缺乏此種繫結會封鎖您的進度，也請遵循指示[支援](http://xamarin.com/support)來提出票證。 我們無法保證繫結，但在某些情況下我們可以避開您的工作。

一旦您通知 Xamarin （如果適用） 您遺失的繫結下, 一步是考慮將它繫結您自己。 我們有完整的指南[此處](~/cross-platform/macios/binding/overview.md)和一些非官方文件[這裡](http://brendanzagaeski.appspot.com/xamarin/0002.html)包裝 OBJECTIVE-C 繫結，以手動方式。 如果您呼叫 C API，您可以使用C#的 P/Invoke 機制，都有文件[以下](https://www.mono-project.com/docs/advanced/pinvoke/)。

如果您決定要使用繫結上，請注意繫結中的錯誤可能會產生各種有趣的當機，在原生執行階段。 特別的是，要非常小心，您的簽章，在C#原生的簽章中的引數數目和每個引數大小相符。 失敗，若要這樣做可能會損毀記憶體和/或堆疊，而且您可能會損毀立即或在某個任意時間點在未來或損毀資料。

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>將 null 傳遞至繫結時的引數例外狀況

雖然 Xamarin 的運作方式來提供高品質並經過的 Apple Api 繫結有時錯誤和 bug 名單中。 到目前為止最常見的問題，您可能會遇到是一種 API 擲回`ArgumentNullException`當您傳遞 null 時基礎 API 接受`nil`。 通常定義 API 的原生的標頭檔不會提供足夠的資訊，在其 API 接受 nil，這將會損毀，如果您將它傳入。

如果您遇到的情況下，傳入`null`會擲回`ArgumentNullException`但您認為它應該運作，請遵循下列步驟：

1. 檢查的 Apple 文件及/或範例，以查看是否可以找到它所接受的證明`nil`。 如果您熟悉 OBJECTIVE-C，您可以撰寫的小型測試程式，以確認它。
2. [提報 bug](#reporting-bugs)。
3. 您可以解決 bug？ 如果您可以呼叫 API 來避免`null`，簡單的 null 檢查，在呼叫可以是簡單的因應措施。
4. 不過，某些 Api 需要傳入 null 來關閉或停用某些功能。 在這些情況下，您可以暫時解決此問題將組件瀏覽器 (請參閱[尋找C#指定的選取器成員](~/mac/app-fundamentals/mac-apis.md#finding_selector))，複製繫結，並移除 null 檢查。 請務必提出錯誤報告 （步驟 2） 如果您執行這項操作，因為您已複製的繫結不會收到更新和修正程式，我們讓 Xamarin.Mac，且應視為短期因應措施。

<a name="reporting-bugs"/>

## <a name="reporting-bugs"></a>回報 bug

您的意見反應對我們至關重要。 如果您發現任何問題，使用 Xamarin.Mac:

- 查看 [Xamarin.Mac 論壇](https://forums.xamarin.com/categories/mac) \(英文\)
- 搜尋[問題存放庫](https://github.com/xamarin/xamarin-macios/issues) \(英文\) 
- 在切換到 GitHub 問題之前，Xamarin 問題的追蹤是在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) \(英文\) 上進行的。 請從該處搜尋相符的問題。
- 如果您找不到相符的問題，請在 [GitHub 問題存放庫](https://github.com/xamarin/xamarin-macios/issues/new) \(英文\) 中提出新的問題。

GitHub 問題全都是公開的。 無法隱藏意見或附件。 

請儘量包含下列資訊：

- 一個可重現問題的簡單範例。 這是**非常寶貴的**，請盡您所能提供。 
- 損毀狀況的完整堆疊追蹤。
- 損毀狀況周圍的 C# 程式碼。 

## <a name="related-links"></a>相關連結

- [Xamarin University 課程：建置 OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University 課程：建置目標 Sharpie OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
