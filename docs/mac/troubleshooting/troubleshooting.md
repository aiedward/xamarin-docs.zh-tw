---
title: Xamarin.Mac 疑難排解秘訣
description: 本文件說明的方法來解決開發 Xamarin.Mac 應用程式時所遇到的問題。 它也會討論如何取得支援。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 5e6cd5b338034cfa9956244015d4585b4f005793
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792963"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Xamarin.Mac 疑難排解秘訣

## <a name="overview"></a>總覽

有時候，我們都堵塞無法取得 API，以依照我們想要的方式運作，或嘗試解決 bug 的專案上工作時。 Xamarin 的目標是要讓您成功撰寫您的行動裝置版和桌面應用程式，以及我們提供了一些資源可幫助。

使用這些資源，有的準備工作，以協助他們快速地解決問題，您可以採取一些步驟：

- 決定最佳盡可能報告損毀問題的根本原因：
 
     - 「 我的應用程式當機 」 很難診斷。 「 我的應用程式時，損毀此呼叫傳回空的陣列 」 就能輕鬆修正運作。

     - 「 我無法取得 NSTable 運作 」 相當有協助較不比 「 我 NSTableDelegate 上方法，沒有任何似乎在此情況下呼叫。 」

- 如果可能的話，請提供顯示問題的小型範例程式。 調查透過尋找問題的原始程式碼的頁面會依據重要順序更多的時間與精力。

- 了解什麼變更您要顯示對您造成問題的應用程式可以快速縮小問題的來源。 注意是否您最近已升級版本的 Xamarin.Mac，修剪出您的應用程式，以找出造成這個問題的組件的區段或測試前一個組建，以找出哪些變更導入的問題是很有幫助。


### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>您的應用程式損毀並沒有輸出時該怎麼辦

在大部分情況下，適用於 Mac 的 Visual Studio 中偵錯工具會攔截例外狀況和您的應用程式中發生當機，協助您追蹤根本原因。 但是有某些情況下，您的應用程式會停駐在您可以，並結束作業幾乎沒有任何輸出。 這些可以包括：

- 程式碼簽署的問題。
- 某些單聲道的執行階段當機。
- 某些 Objective c 例外狀況和損毀。
- 某些損毀非常早期的程序的存留期。
- 某些堆疊溢位。
- 中所列的 macOS 版本您**Info.plist**比目前安裝的 macOS 版本，或者它是無效的較新。

偵錯這些程式可能會令人沮喪，以尋找所需的資訊很難。 以下提供幾個方法可協助：

- 請確認中所列的 macOS 版本**Info.plist**是同一個 macOS 電腦上目前安裝的版本。
- 檢查 Visual Studio for Mac 應用程式輸出 (**檢視** -> **填補** -> **應用程式輸出**) 的堆疊追蹤，或以紅色從輸出Cocoa 可能描述輸出。
- 從命令列執行您的應用程式，並查看 輸出 (在**終端機**應用程式) 使用： 

     `MyApp.app/Contents/MacOS/MyApp` (其中`MyApp`是您的應用程式的名稱)
- 您可以增加輸出，例如將"MONO_LOG_LEVEL"加入至您在命令列上的命令： 

     `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- 您可以將原生偵錯工具附加 (`lldb`) 您看到的處理序如果可提供任何詳細資訊 （這需要付費的授權）。 例如，執行下列作業：

    1. 輸入`lldb MyApp.app/Contents/MacOS/MyApp`終端機中。
    2. 輸入`run`終端機中。
    3. 輸入`c`終端機中。
    4. 完成偵錯時結束。
- 作為最後的方法呼叫之前`NSApplication.Init`中您`Main`方法 （或其他地方，視需要），您無法將文字寫入至檔案，以追蹤在您正在執行哪些步驟的啟動可能造成問題至已知位置。

## <a name="known-issues"></a>已知問題

下列各節涵蓋已知的問題與解決方案。

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>無法連接至沙箱化應用程式中偵錯工具

偵錯工具連接到 Xamarin.Mac 應用程式，透過 TCP，這表示，依預設啟用沙箱，當它是無法連線到應用程式，因此如果您嘗試執行應用程式沒有啟用適當的權限，您會收到錯誤 *「 無法連接到偵錯工具 」*。 

[![編輯權利](troubleshooting-images/debug01.png "編輯權利")](troubleshooting-images/debug01-large.png#lightbox)

**允許傳出網路連線 （用戶端）** 權限就是所需的偵錯工具，啟用此選項將允許正常地偵錯。 您無法偵錯，而它，因為我們已更新`CompileEntitlements`目標`msbuild`為沙箱化針對偵錯任何應用程式只建置的自動將該權限新增至權利。 發行組建應該使用未經修改的權利檔案中指定的權利。

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>沒有資料也可用來編碼 437 System.NotSupportedException:
 
當包含 Xamarin.Mac 應用程式中的第 3 個合作對象文件庫，您可能會發生錯誤，在表單中"System.NotSupportedException： 沒有資料可用於編碼 437 」 嘗試編譯並執行應用程式時。 例如，程式庫，例如`Ionic.Zip.ZipFile`，可能會擲回這個例外狀況，在作業期間。

這可以藉由開啟 Xamarin.Mac 專案中，移至的選項來解決**Mac 建置** > **國際化**和檢查**西**國際化：

[![編輯組建選項](troubleshooting-images/issue01.png "編輯組建選項")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>無法編譯 (mm5103)

當新版本 Xcode 是版本，您已安裝新版本，但不是尚未執行此程式碼，通常被造成這個錯誤。 之前嘗試編譯使用 Xcode 的新版本，您必須先執行至少一次該版本。

第一次執行新版本 Xcode，它會安裝數個 Xamarin.Mac 所需的命令列工具。 此外，您應該更新 Xcode 或 Xamarin.Mac 版本之後，執行乾淨的組建。

如果您不能解決此問題，請[提報 bug](#filing-a-bug)。

### <a name="missing-entitlementsplist"></a>遺漏 entitlements.plist

最新版的 Visual Studio for Mac 已移除權利區段中的，從**Info.plist**編輯器並放在不同**Entitlements.plist**編輯器 （如需更好的跨平台支援與 Xamarin.iOS)。

使用新的 Visual Studio for Mac 安裝，當您建立新的 Xamarin.Mac 應用程式專案， **Entitlements.plist**檔案將自動加入至專案樹狀結構：

![選取權利](troubleshooting-images/entitlements01.png "選取權利")

如果您按兩下**Entitlements.plist**權利編輯器的檔案將會顯示：

[![編輯權利](troubleshooting-images/entitlements02.png "編輯權利")](troubleshooting-images/entitlements02-large.png#lightbox)

對於現有 Xamarin.Mac 專案，您必須手動建立**Entitlements.plist**檔案中的專案上按一下滑鼠右鍵**方案板**，然後選取**新增**  > **新的檔案...**.接下來，選取**Xamarin.Mac** > **空白屬性清單**:

![加入新的屬性清單](troubleshooting-images/entitlements03.png "加入新的屬性清單")

輸入`Entitlements`的名稱，然後按一下**新增** 按鈕。 如果您的專案先前包含權利檔案中，系統會提示您將它加入至專案，而不是建立新的檔案：

[![正在驗證檔案的覆寫](troubleshooting-images/entitlements04.png "驗證檔案的覆寫")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="contacting-support-business-or-enterprise-licenses"></a>連絡支援服務 （商務或企業授權）

如果您有商務或企業版授權，您必須能夠直接透過支援票證 Xamarin 工程師請求協助。 請參閱[xamarin.com/support](http://xamarin.com/support)如需詳細資訊。

## <a name="community-support-on-the-forums"></a>在論壇社群支援

使用 Xamarin 產品的開發人員社群為令人讚嘆，許多瀏覽我們[Xamarin.Mac 論壇](http://forums.xamarin.com/categories/mac)分享體驗和其專業知識。 此外，Xamarin 工程師定期瀏覽論壇幫助。

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>提出 bug

您的意見反應對我們很重要。 如果您發現任何 Xamarin.Mac 的問題：

- 搜尋[問題存放庫](https://github.com/xamarin/xamarin-macios/issues) \(英文\) 
- 在切換到 GitHub 問題之前，Xamarin 問題的追蹤是在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) \(英文\) 上進行的。 請從該處搜尋相符的問題。
- 如果您找不到相符的問題，請在 [GitHub 問題存放庫](https://github.com/xamarin/xamarin-macios/issues/new) \(英文\) 中提出新的問題。

GitHub 問題全都是公開的。 無法隱藏意見或附件。 

請儘量包含下列資訊：                                                                                                                                          

- 一個可重現問題的簡單範例。 這是**非常寶貴的**，請盡您所能提供。 
- 損毀狀況的完整堆疊追蹤。
- 損毀狀況周圍的 C# 程式碼。 
