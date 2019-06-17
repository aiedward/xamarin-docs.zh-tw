---
title: Xamarin.Mac 的疑難排解秘訣
description: 本文件說明解決開發 Xamarin.Mac 應用程式時所遇到的問題的方法。 它也會討論如何取得支援。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: f498aab5bfaffc08a22f62a318f8f9f73ab0afca
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61237418"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Xamarin.Mac 的疑難排解秘訣

## <a name="overview"></a>總覽

有時，我們都停滯時從事的專案，無法取得 API，以便使用我們想要的方式或在嘗試解決的 bug。 我們在 Xamarin 的目標是讓您在撰寫您的行動和桌面應用程式，成功，我們提供了一些資源，幫助。

使用下列任何資源，有一些可幫助他們快速地解決問題的準備工作的步驟：

- 判斷最佳儘可能以報告損毀問題的根本原因：
 
     - 「 我的應用程式當機 」 很難診斷。 「 我的應用程式時當機我在此呼叫傳回空陣列"更可以更輕鬆地修正。

     - 「 我無法取得 NSTable 運作 」 是較有幫助比 「 在我 NSTableDelegate 方法，沒有任何似乎在此情況下呼叫。 」

- 如果可能的話，請提供顯示問題的小型範例程式。 探索透過原始程式碼來尋找問題的頁面中將會遠遠更多的時間和精力。

- 了解哪些變更才會出現對您造成問題的應用程式可以快速縮小問題的來源。 注意是否您最近已升級版本的 Xamarin.Mac、 調整您的應用程式，以找出造成問題，組件的區段，或測試前一個組建來尋找哪些變更導入的問題是很有幫助。


### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>您的應用程式損毀，沒有輸出時，該怎麼辦

在大部分情況下，Visual Studio for Mac 中的偵錯工具會攔截例外狀況中您的應用程式的當機並協助您找出根本原因。 不過有某些情況下，您的應用程式將 dock 上彈，然後結束，幾乎沒有任何輸出。 這些可能包括：

- 程式碼簽署問題。
- 某些 mono 執行階段損毀。
- 某些 objective-c 例外狀況和當機。
- 一些損毀極早期的程序的存留期。
- 某些堆疊溢位。
- MacOS 版本列在您**Info.plist**是較新，您目前已安裝的 macOS 版本，或其無效。

偵錯這些程式很令人沮喪，以尋找所需的資訊很難。 以下是幾種方法可協助：

- 請確認中所列的 macOS 版本**Info.plist**同一個為 macOS 電腦上目前安裝的版本。
- 檢查 Visual Studio for Mac 應用程式的輸出 (**檢視** -> **板** -> **應用程式輸出**) 的堆疊追蹤，或以紅色的輸出Cocoa 可能描述輸出。
- 從命令列執行您的應用程式，並查看 輸出 (在**終端機**應用程式) 使用： 

     `MyApp.app/Contents/MacOS/MyApp` (其中`MyApp`是您的應用程式的名稱)
- 例如"MONO_LOG_LEVEL"新增至您的命令，在命令列上，您可以增加輸出： 

     `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- 您可以將原生的偵錯工具附加 (`lldb`) 到您的流程，請參閱如果可提供任何詳細資訊 （這需要付費的授權）。 例如，執行下列作業：

    1. 輸入`lldb MyApp.app/Contents/MacOS/MyApp`終端機中。
    2. 輸入`run`終端機中。
    3. 輸入`c`終端機中。
    4. 完成偵錯時，就會結束。
- 最後的手段之前呼叫`NSApplication.Init`在您`Main`方法 （或在其他位置，視需要），您可以將文字寫入至追蹤，以在您正在執行哪些步驟的啟動問題的已知位置中的檔案。

## <a name="known-issues"></a>已知問題

下列各節涵蓋的已知的問題和解決方案。

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>無法連線至沙箱化應用程式中偵錯工具

偵錯工具連接到 Xamarin.Mac 應用程式，透過 TCP，這表示，依預設啟用的沙箱功能時，無法連線到應用程式，因此如果您嘗試執行應用程式沒有啟用適當的權限，您會收到錯誤 *「 無法連接到偵錯工具 」*。 

[![編輯權利](troubleshooting-images/debug01.png "編輯權利")](troubleshooting-images/debug01-large.png#lightbox)

**允許連出網路連線 （用戶端）** 權限就是所需的偵錯工具，啟用這其中一個可正常地偵錯。 因為您無法偵錯，而不需要它，我們已更新`CompileEntitlements`為目標`msbuild`自動將該權限新增至權利，針對任何已沙箱化偵錯的應用程式的組建。 發行組建應使用指定在權利檔案中，未經修改的權利。

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>沒有資料也可用來編碼 437 System.NotSupportedException:
 
在 Xamarin.Mac 應用程式中包含第 3 個廠商程式庫，您可能會發生錯誤，在表單中 「 System.NotSupportedException:沒有資料可供編碼 437 」 嘗試編譯及執行應用程式時。 比方說，程式庫，例如`Ionic.Zip.ZipFile`，可能會擲回這個例外狀況，在作業期間。

藉由開啟 Xamarin.Mac 專案中，移至的選項來解決此**Mac 組建** > **國際化**並檢查**西部**國際化：

[![編輯組建選項](troubleshooting-images/issue01.png "編輯組建選項")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>無法編譯 (mm5103)

當新版本的 Xcode 版本，而且您已安裝新版本時，但不是尚未執行它，通常會造成這個錯誤。 然後再嘗試使用新的 Xcode 版本進行編譯，您需要先執行至少一次該版本。

第一次執行新版本的 Xcode，它會安裝數個 Xamarin.Mac 所需的命令列工具。 此外，您應該更新 Xcode 或您的 Xamarin.Mac 版本之後，執行乾淨的組建。

如果您不能解決此問題，請[提報 bug](#filing-a-bug)。

### <a name="missing-entitlementsplist"></a>遺漏的 entitlements.plist

最新版本的 Visual Studio for Mac 已移除權利區段，取自**Info.plist**編輯器並放在不同**Entitlements.plist**編輯器 （如需更佳的跨平台支援與 Xamarin.iOS)。

使用新的 Visual Studio for Mac 安裝，當您建立新的 Xamarin.Mac 應用程式專案**Entitlements.plist**檔案會自動加入至專案樹狀結構：

![選取的權利](troubleshooting-images/entitlements01.png "選取權利")

如果您按兩下**Entitlements.plist**權利編輯器的檔案將會顯示：

[![編輯權利](troubleshooting-images/entitlements02.png "編輯權利")](troubleshooting-images/entitlements02-large.png#lightbox)

對於現有的 Xamarin.Mac 專案中，您必須以手動方式建立**Entitlements.plist**檔案中的專案上按一下滑鼠右鍵**Solution Pad** ，然後選取**新增**  > **新檔...**.接下來，選取**Xamarin.Mac** > **空白屬性清單**:

![加入新的屬性清單](troubleshooting-images/entitlements03.png "新增新的屬性清單")

請輸入`Entitlements`的名稱，然後按一下**新增** 按鈕。 如果您的專案之前包含權利檔案中，系統會提示您將它新增至專案，而不是建立新的檔案：

[![正在驗證檔案的覆寫](troubleshooting-images/entitlements04.png "驗證檔案的覆寫")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="community-support-on-the-forums"></a>社群支援論壇

使用 Xamarin 產品的開發人員社群充滿驚奇，許多瀏覽我們[Xamarin.Mac 論壇](http://forums.xamarin.com/categories/mac)分享經驗和其專業知識。 此外，Xamarin 工程師定期造訪論壇幫助。

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>提出 bug

您的意見反應對我們至關重要。 如果您發現任何問題，使用 Xamarin.Mac:

- 搜尋[問題存放庫](https://github.com/xamarin/xamarin-macios/issues) \(英文\) 
- 在切換到 GitHub 問題之前，Xamarin 問題的追蹤是在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) \(英文\) 上進行的。 請從該處搜尋相符的問題。
- 如果您找不到相符的問題，請在 [GitHub 問題存放庫](https://github.com/xamarin/xamarin-macios/issues/new) \(英文\) 中提出新的問題。

GitHub 問題全都是公開的。 無法隱藏意見或附件。 

請儘量包含下列資訊：                                                                                                                                          

- 一個可重現問題的簡單範例。 這是**非常寶貴的**，請盡您所能提供。 
- 損毀狀況的完整堆疊追蹤。
- 損毀狀況周圍的 C# 程式碼。 
