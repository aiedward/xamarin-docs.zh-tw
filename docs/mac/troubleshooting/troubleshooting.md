---
title: Xamarin. Mac 疑難排解秘訣
description: 本檔說明在開發 Xamarin. Mac 應用程式時所遇到之問題的解決方法。 它也會討論取得支援的方法。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: dea7337702e37895d1e8dec55bd433d8ac4d64c0
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065608"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Xamarin. Mac 疑難排解秘訣

## <a name="overview"></a>總覽

有時候, 在處理專案時, 我們全都會停滯, 因為無法取得 API 以我們想要的方式運作, 或嘗試解決 bug。 我們在 Xamarin 的目標是要讓您成功撰寫行動和桌面應用程式, 並提供一些資源來協助您。

有了這些資源, 您可以採取一些準備步驟, 協助他們快速解決您的問題:

- 盡可能判斷問題的根本原因, 以報告損毀:

  - 「我的應用程式當機」很容易診斷。 「我的應用程式當我將空陣列傳回給這個呼叫」時, 會更容易解決。

  - 「我無法讓 NSTable 工作」比「我的 NSTableDelegate 上的任何方法在此情況下似乎被呼叫」沒有用處。

- 可能的話, 請提供一個顯示問題的小型範例程式。 深入探討到尋找問題的源字碼頁面, 會使時間和工作的程度更高。

- 知道您對應用程式造成問題的變更, 可以快速縮小問題來源的範圍。 請注意, 如果您最近已升級 Xamarin. Mac 的版本, 請修剪應用程式的各個區段以找出造成問題的部分, 或測試先前的組建, 以找出問題的變更可能會很有説明。


### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>當您的應用程式損毀但沒有輸出時該怎麼辦

在大部分情況下, Visual Studio for Mac 中的偵錯工具將會攔截例外狀況, 並在您的應用程式中損毀, 並協助您追蹤根本原因。 不過, 在某些情況下, 您的應用程式會在 dock 上彈, 然後在很少或沒有輸出的情況下結束。 這些可能包括:

- 程式碼簽署問題。
- 特定 mono 執行時間損毀。
- 某些目標-c 例外狀況和當機。
- 有些會在進程存留期初期發生損毀。
- 某些堆疊溢位。
- 您的**資訊**中所列的 macOS 版本比您目前安裝的 macOS 版本還新, 或其無效。

對這些程式進行調試可能會令人沮喪, 因為尋找所需的資訊可能會很難。 以下是一些可能有説明的方法:

- 請確定**plist**中所列的 macOS 版本與目前安裝在電腦上的 macOS 版本相同。
- 檢查堆疊追蹤的 Visual Studio for Mac 應用程式輸出 (**View**  ->  **pad**  -> **應用程式輸出**), 或從可能描述輸出的 Cocoa 輸出 (以紅色表示)。
- 從命令列執行您的應用程式, 並使用來查看輸出 (在**終端**機應用程式中):

  `MyApp.app/Contents/MacOS/MyApp`(其中`MyApp`是您的應用程式名稱)
- 您可以在命令列上將 "MONO_LOG_LEVEL" 新增至命令, 以增加輸出, 例如:

  `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- 您可以將原生偵錯工具`lldb`() 附加至您的進程, 以查看是否有提供任何更多資訊 (這需要付費授權)。 例如, 請執行下列動作:

  1. 在`lldb MyApp.app/Contents/MacOS/MyApp`終端機中輸入。
  2. 在`run`終端機中輸入。
  3. 在`c`終端機中輸入。
  4. 完成調試時結束。
- 做為最後的手段, 在`NSApplication.Init`您`Main`的方法中呼叫 (或在其他地方視需要) 時, 您可以將文字寫入已知位置中的檔案, 以追蹤遇到問題的啟動步驟。

## <a name="known-issues"></a>已知問題

下列各節涵蓋已知問題及其解決方案。

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>無法連接至沙箱應用程式中的偵錯工具

偵錯工具會透過 TCP 連線到 Xamarin. Mac 應用程式, 這表示根據預設, 當您啟用沙箱時, 它無法連線到應用程式, 因此如果您嘗試在未啟用適當許可權的情況下執行應用程式, 就會收到錯誤「*無法連接到偵錯工具」* .

[![編輯權利](troubleshooting-images/debug01.png "編輯權利")](troubleshooting-images/debug01-large.png#lightbox)

「**允許外寄網路連線 (用戶端)** 」許可權就是偵錯工具所需的許可權, 讓這種連線能夠正常進行偵測。 因為您無法在沒有此程式的情況下進行`CompileEntitlements` debug, `msbuild`所以我們已更新的目標, 以便將該許可權自動新增至任何已針對 debug build 進行沙箱化之應用程式的權利。 發行組建應使用權利檔案中所指定的權利 (未經修改)。

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>NotSupportedException: 沒有可供編碼437使用的資料

在您的 Xamarin. Mac 應用程式中包含協力廠商程式庫時, 可能會收到下列格式的錯誤: 「NotSupportedException:嘗試編譯和執行應用程式時, 沒有任何資料可供編碼437使用。 例如, 程式庫 (例如`Ionic.Zip.ZipFile`) 可能會在操作期間擲回此例外狀況。

這可以藉由開啟 Xamarin 專案的選項, 前往**mac 組建** > **國際化**並檢查**West**國際化來解決:

[![編輯組建選項](troubleshooting-images/issue01.png "編輯組建選項")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>無法編譯 (mm5103)

此錯誤通常是因為 Xcode 的新版本已發行, 而您已安裝新版本但尚未執行。 嘗試使用新版本的 Xcode 進行編譯之前, 您必須先執行該版本至少一次。

當您第一次執行新版本的 Xcode 時, 它會安裝 Xamarin 所需的數個命令列工具。 此外, 您應該在更新 Xcode 或您的 Xamarin. Mac 版本之後, 執行乾淨的組建。

如果您無法解決此問題, 請提出[bug](#filing-a-bug)。

### <a name="missing-entitlementsplist"></a>缺少權利。 plist

最新版的 Visual Studio for Mac 已移除**plist**編輯器中的 [權利] 區段, 並將它放在不同的**權利中。 plist**編輯器 (以支援 Xamarin 的跨平臺支援)。

安裝新的 Visual Studio for Mac 之後, 當您建立新的 Xamarin. Mac 應用程式專案時, **plist**檔案將會自動加入至專案樹狀結構中:

![選取權利](troubleshooting-images/entitlements01.png "選取權利")

如果您按兩下**plist**檔案, 將會顯示 [權利編輯器]:

[![編輯權利](troubleshooting-images/entitlements02.png "編輯權利")](troubleshooting-images/entitlements02-large.png#lightbox)

針對現有的 Xamarin 專案, 您必須以滑鼠右鍵按一下  **Solution Pad**中的專案  > , 然後選取 **新增檔案 ...** , 以手動方式建立**plist**檔案。接下來, 選取  **Xamarin**  >  **空白屬性清單**:

![加入新的屬性清單](troubleshooting-images/entitlements03.png "加入新的屬性清單")

輸入`Entitlements`作為名稱, 然後按一下 [**新增**] 按鈕。 如果您的專案先前已包含權利檔案, 系統會提示您將其新增至專案, 而不是建立新的檔案:

[![驗證檔案的覆寫](troubleshooting-images/entitlements04.png "驗證檔案的覆寫")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="community-support-on-the-forums"></a>論壇上的社區支援

使用 Xamarin 產品的開發人員群很驚人, 而且有許多人可以造訪我們的[xamarin 論壇](http://forums.xamarin.com/categories/mac), 分享經驗和其專長。 此外, Xamarin 工程師會定期流覽論壇以提供協助。

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>提出 bug

您的意見反應對我們很重要。 如果您發現 Xamarin. Mac 有任何問題:

- 搜尋[問題存放庫](https://github.com/xamarin/xamarin-macios/issues) \(英文\)
- 在切換到 GitHub 問題之前，Xamarin 問題的追蹤是在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) \(英文\) 上進行的。 請從該處搜尋相符的問題。
- 如果您找不到相符的問題，請在 [GitHub 問題存放庫](https://github.com/xamarin/xamarin-macios/issues/new) \(英文\) 中提出新的問題。

GitHub 問題全都是公開的。 無法隱藏意見或附件。

請儘量包含下列資訊：

- 一個可重現問題的簡單範例。 這是**非常寶貴的**，請盡您所能提供。
- 損毀狀況的完整堆疊追蹤。
- 損毀狀況周圍的 C# 程式碼。
