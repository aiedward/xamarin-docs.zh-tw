---
title: "繫結疑難排解"
description: "本指南說明如果您無法繫結 Objective C 程式庫，該怎麼辦。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/19/2016
ms.openlocfilehash: 2db7fe30f05224f6b74b4d2189606da59946bda0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="binding-troubleshooting"></a>繫結疑難排解

疑難排解 macOS （之前稱為 OS X） 的繫結的一些秘訣 Xamarin.Mac 中的 Api。

## <a name="missing-bindings"></a>遺漏繫結

儘管 Xamarin.Mac 涵蓋大部分的 Apple 應用程式開發介面，有時候您可能需要呼叫沒有繫結某些 Apple API 尚未。 在其他情況下，您需要呼叫第三方 C/OBJECTIVE-C 它 Xamarin.Mac 繫結的範圍之外。

如果您正在處理的 Apple 應用程式開發介面，第一個步驟是讓 Xamarin 知道，您到達區段的應用程式開發介面，我們針對還沒有涵蓋範圍。 [將 bug 歸檔](#reporting-bugs)注意遺漏的 API。 我們會使用來自客戶的報表來排列優先順序的應用程式開發介面，我們處理下一步。 此外，如果您有商務或企業授權，而且這種欠缺的繫結會封鎖您的進度，也請遵循指示[支援](http://xamarin.com/support)檔案票證。 我們無法保證繫結，但在某些情況下我們可以避開您的工作。

一旦您通知 Xamarin （如果適用） 您遺失的繫結下, 一步是考慮繫結它自己。 我們有完整指南[這裡](~/cross-platform/macios/binding/overview.md)和某些非官方文件[這裡](http://brendanzagaeski.appspot.com/xamarin/0002.html)以包裝 OBJECTIVE-C 繫結，以手動方式。 如果您呼叫 C API，您可以使用 C# P/Invoke 機制，文件是[這裡](http://www.mono-project.com/docs/advanced/pinvoke/)。

如果您決定要使用繫結上，請注意繫結中的錯誤可能會產生各式各樣有趣的當機，原生執行階段中。 特別是，要非常小心，您在 C# 中的簽章比對原生的簽章的引數數目，而每個引數大小。 這樣可能會損毀記憶體及/或堆疊，您可以在未來損毀立即或在某個任意時間點或資料損毀。

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>將 null 傳遞至繫結時的引數例外狀況

雖然 Xamarin 的運作以提供高品質的 Apple 應用程式開發介面，以及測試繫結有時錯誤和 bug 名單中。 截至目前為止，最常見的問題，您可能會遇到是一種 API 擲回`ArgumentNullException`當您要傳入 null 基礎 API 接受時`nil`。 通常定義 API 的原生的標頭檔不會提供足夠的資訊的應用程式開發介面接受 nil 和這將會損毀，如果您將在。

如果您遇到的大小寫的傳入`null`會擲回`ArgumentNullException`但您認為它應該工作，請遵循下列步驟：

1. 檢查的 Apple 文件及/或範例，請參閱是否可以找到它可接受的證明`nil`。 如果您是熟悉 Objective C，您可以撰寫小型測試程式加以驗證。
2. [將 bug 歸檔](#reporting-bugs)。
3. 您可以解決 bug？ 如果呼叫的 API，您可以避免`null`，簡單的 null 檢查，在呼叫可以是簡單的因應措施。
4. 不過，某些應用程式開發介面需要傳入 null 關閉或停用某些功能。 在這些情況下，您可以暫時解決此問題的組件的瀏覽器開啟 (請參閱[給定選取器中尋找 C# 成員](~/mac/app-fundamentals/mac-apis.md#finding_selector))，將複製繫結，並移除 null 檢查。 請務必為 bug 建檔 （步驟 2） 如果您這樣做，當您複製繫結不會收到更新和修正程式，我們在 Xamarin.Mac，而且這也應該考量短期因應措施。

<a name="reporting-bugs"/>

## <a name="reporting-bugs"></a>回報 bug

您的意見反應對我們很重要。 如果您發現任何 Xamarin.Mac 的問題：

- 請檢查[Xamarin.Mac 論壇](https://forums.xamarin.com/categories/mac)
- 搜尋[問題儲存機制](https://github.com/xamarin/xamarin-macios/issues) 
- 在切換之前 GitHub 問題，Xamarin 問題追蹤上[Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi)。 請搜尋那里相符的問題。
- 如果找不到相符的問題，請檔案中的新問題[GitHub 問題儲存機制](https://github.com/xamarin/xamarin-macios/issues/new)。

GitHub 問題是所有公用的。 您不可以隱藏註解或附件。 

請提供下盡量：

- 重現問題的簡單範例。 這是**一兩**盡可能。 
- 當機的完整的堆疊追蹤。
- C# 程式碼周圍的損毀。 
