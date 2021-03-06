---
title: Xamarin. Mac 疑難排解秘訣
description: 本檔說明解決開發 Xamarin. Mac 應用程式時所遇到之問題的方法。 它也會討論取得支援的方法。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 723278bb5b0a76d9fd2560209385bd4cfdac54a9
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697497"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Xamarin. Mac 疑難排解秘訣

## <a name="overview"></a>概觀

有時候，我們會在處理專案時遇到問題，不論是無法讓 API 以我們想要的方式運作，或是嘗試解決錯誤。 我們在 Xamarin 的目標是讓您能夠成功撰寫您的行動和桌面應用程式，並提供一些資源來協助您。

有了這些資源，您可以採取一些準備工作，以協助他們快速解決您的問題：

- 判斷問題的根本原因，盡可能能回報損毀：

  - 「我的應用程式損毀」很難診斷。 「我的應用程式當我將空的陣列傳回給這個呼叫時，我的應用程式損毀」在修正時更容易處理。

  - 「我無法取得 NSTable 工作」比「我的 NSTableDelegate 上的方法似乎不會在此情況下呼叫」更有説明。

- 可能的話，請提供一個小範例程式來顯示問題。 深入探討至尋找問題的源字碼頁面，會以更多的時間和精力進行大量的順序。

- 知道您對應用程式所做的變更，使問題出現，可以迅速縮小問題的根源。 請注意，如果您最近升級了 Xamarin 的版本，請修剪應用程式區段以找出造成問題的部分，或測試先前的組建以找出造成問題的變更，可能會很有説明。

### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>當您的應用程式損毀而沒有輸出時該怎麼辦

在大部分的情況下，Visual Studio for Mac 中的偵錯工具會攔截例外狀況，並在您的應用程式中損毀，並協助您追蹤根本原因。 不過，在某些情況下，您的應用程式會在 dock 上彈跳，然後在很少或沒有輸出的情況下結束。 這些包括：

- 程式碼簽署問題。
- 特定 mono 執行時間損毀。
- 某些目標 c 例外狀況和損毀。
- 部分在進程存留期初期損毀。
- 某些堆疊溢位。
- 您的 **資訊** 中所列的 macOS 版本比您目前安裝的 macOS 版本新，或其無效。

對這些程式進行錯錯可能很令人沮喪，因為尋找所需的資訊可能會很困難。 以下是一些可能會有説明的方法：

- 請確定 **plist** 中所列的 macOS 版本與目前安裝在電腦上的 macOS 版本相同。
- 核取 [應用程式輸出] (**View** Visual Studio for Mac  ->    ->  堆疊追蹤的 **應用程式) 輸出**，或是可能描述輸出的 Cocoa 以紅色輸出輸出。
- 從命令列執行您的應用程式，並使用下列命令在 **終端** 機應用程式) 中查看輸出 (：

  `MyApp.app/Contents/MacOS/MyApp` (，其中 `MyApp` 是您的應用程式名稱) 
- 您可以在命令列上將 "MONO_LOG_LEVEL" 新增至命令，以增加輸出，例如：

  `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- 您可以將原生偵錯工具 () 附加至 `lldb` 進程，以查看是否提供任何詳細資訊 (這需要付費授權) 。 例如，請執行下列作業：

  1. `lldb MyApp.app/Contents/MacOS/MyApp`在終端機中輸入。
  2. `run`在終端機中輸入。
  3. `c`在終端機中輸入。
  4. 完成錯錯時結束。
- 最後一種方法是，在 `NSApplication.Init` 您的 `Main` 方法中呼叫 (或在其他地方) 所需的情況下，您可以將文字寫入已知位置中的檔案，以追蹤您遇到問題的啟動步驟。

## <a name="known-issues"></a>已知問題

下列各節涵蓋已知問題及其解決方案。

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>無法連接至沙箱化應用程式中的偵錯工具

偵錯工具會透過 TCP 連線至 Xamarin 應用程式，這表示當您啟用沙箱時，預設無法連線到應用程式，因此如果您嘗試在未啟用適當許可權的情況下執行應用程式，您會收到「 *無法連接到偵錯工具*」錯誤。

[![編輯應用程式沙箱中的權利。](troubleshooting-images/debug01.png "編輯權利")](troubleshooting-images/debug01-large.png#lightbox)

[ **允許連出網路連線] (用戶端)** 許可權是偵錯工具所需的許可權，如此一來，啟用這項功能就能正常進行偵錯工具。 因為您無法在沒有它的情況下進行 debug，所以我們已將的 `CompileEntitlements` 目標更新 `msbuild` 為，以自動將該許可權新增至僅針對沙箱化的任何應用程式的權利。 發行組建應使用權利檔案中指定的權利（未經修改）。

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>NotSupportedException：沒有任何資料可用於編碼437

在您的 Xamarin 應用程式中包含協力廠商程式庫時，您可能會在嘗試編譯和執行應用程式時，收到「NotSupportedException：沒有資料可供編碼437」的錯誤。 例如，程式庫（例如 `Ionic.Zip.ZipFile` ）可能會在作業期間擲回此例外狀況。

您可以開啟 Xamarin 專案的選項，前往 **mac 組建**  >  **國際化** 並檢查 **West** 國際化，以解決此狀況：

[![編輯建置選項](troubleshooting-images/issue01.png "編輯建置選項")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>無法編譯 (mm5103) 

此錯誤通常是因為新版本的 Xcode 發行，且您已安裝新版本但尚未執行。 在嘗試使用新版 Xcode 進行編譯之前，您必須先執行至少一次該版本。

當您第一次執行新版本的 Xcode 時，它會安裝 Xamarin 所需的數個命令列工具。 此外，您應該在更新 Xcode 或您的 Xamarin 版本之後，進行乾淨的組建。

如果您無法解決此問題，請提出 [錯誤](#filing-a-bug)。

### <a name="missing-entitlementsplist"></a>遺漏權利。 plist

Visual Studio for Mac 的最新版本已從 **plist** 編輯器中移除 [權利] 區段，並將其放在個別的 **plist** (編輯器中，以取得與 Xamarin) 更佳的跨平臺支援。

安裝新的 Visual Studio for Mac 之後，當您建立新的 Xamarin 應用程式專案時， **plist** 檔案會自動新增至專案樹狀結構：

![選取權利](troubleshooting-images/entitlements01.png "選取權利")

如果您按兩下 **plist** 檔案，則會顯示 [權利編輯器]：

[![編輯權利](troubleshooting-images/entitlements02.png "編輯權利")](troubleshooting-images/entitlements02-large.png#lightbox)

針對現有的 Xamarin 專案，您必須以滑鼠右鍵按一下 **Solution Pad** 中的專案，**然後選取 [** 新增檔案 ...]，以手動方式建立 **plist** 檔案。  >  接下來，選取 [ **Xamarin**  >  **空白屬性清單**：

![加入新的屬性清單](troubleshooting-images/entitlements03.png "加入新的屬性清單")

輸入 `Entitlements` 名稱，然後按一下 [ **新增** ] 按鈕。 如果您的專案先前包含權利檔案，系統將會提示您將其新增至專案，而不是建立新的檔案：

[![驗證檔案的覆寫](troubleshooting-images/entitlements04.png "驗證檔案的覆寫")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="community-support-on-the-forums"></a>論壇上的社區支援

使用 Xamarin 產品的開發人員群體很棒，而且許多人都造訪我們的 [xamarin 論壇](https://forums.xamarin.com/categories/xamarin-mac) ，分享經驗和專業知識。 此外，Xamarin 工程師會定期造訪論壇以提供協助。

<a name="filing-a-bug"></a>

## <a name="filing-a-bug"></a>提出 bug

您的意見反應對我們非常寶貴。 如果您發現 Xamarin 有任何問題：

- 搜尋[問題存放庫](https://github.com/xamarin/xamarin-macios/issues) \(英文\)
- 在切換到 GitHub 問題之前，Xamarin 問題的追蹤是在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) \(英文\) 上進行的。 請從該處搜尋相符的問題。
- 如果您找不到相符的問題，請在 [GitHub 問題存放庫](https://github.com/xamarin/xamarin-macios/issues/new) \(英文\) 中提出新的問題。

GitHub 問題全都是公開的。 無法隱藏意見或附件。

請儘量包含下列資訊：

- 一個可重現問題的簡單範例。 這是 **非常寶貴的**，請盡您所能提供。
- 損毀狀況的完整堆疊追蹤。
- 損毀狀況周圍的 C# 程式碼。
