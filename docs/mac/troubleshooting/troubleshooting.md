---
title: Xamarin. Mac 疑難排解秘訣
description: 本檔說明在開發 Xamarin. Mac 應用程式時所遇到之問題的解決方法。 它也會討論取得支援的方法。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 7bb17abf8cdb943780bb3939aae8e461925b6517
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001600"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Xamarin. Mac 疑難排解秘訣

## <a name="overview"></a>總覽

有時候，在處理專案時，我們全都會停滯，因為無法取得 API 以我們想要的方式運作，或嘗試解決 bug。 我們在 Xamarin 的目標是要讓您成功撰寫行動和桌面應用程式，並提供一些資源來協助您。

有了這些資源，您可以採取一些準備步驟，協助他們快速解決您的問題：

- 盡可能判斷問題的根本原因，以報告損毀：

  - 「我的應用程式當機」很容易診斷。 「我的應用程式當我將空陣列傳回給這個呼叫」時，會更容易解決。

  - 「我無法讓 NSTable 工作」比「我的 NSTableDelegate 上的任何方法在此情況下似乎被呼叫」沒有用處。

- 可能的話，請提供一個顯示問題的小型範例程式。 Digging through pages of source code looking for the issue takes orders of magnitude more time and effort.

- Knowing what changes you’ve made to your application to cause an issue to appear can quickly narrow down the source of the problem. Noting if you’ve recently upgraded versions of Xamarin.Mac, trimming out sections of your application to find the part causing the issue, or testing previous builds to find what change introduced the issue can be very helpful.

### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>What to do when your app crashes with no output

In most cases, the debugger in Visual Studio for Mac will catch exceptions and crashes in your application and help you track down the root cause. However there are some cases where your application will bounce on the dock and then exit with little or no output. These can include:

- Code signing issues.
- Certain mono runtime crashes.
- Some Objective-c exceptions and crashes.
- Some crashes very early the process lifetime.
- Some stack overflows.
- The macOS version listed in your **Info.plist** is newer than your currently installed macOS version or it is invalid.

Debugging these programs can be frustrating, as finding the information necessary can be difficult. Here are a few approaches that may help:

- Ensure that the macOS version listed in the **Info.plist** is the same one as the version of macOS currently installed on the computer.
- Check the Visual Studio for Mac Application Output (**View** -> **Pads** -> **Application Output**) for stack traces or output in red from Cocoa that may describe the output.
- Run your application from the command line and look at the output (in the **Terminal** app) by using:

  `MyApp.app/Contents/MacOS/MyApp` (where `MyApp` is the name of your application)
- You can increase the output by adding "MONO_LOG_LEVEL" to your command on the command line, for example:

  `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- You could attach a native debugger (`lldb`) to your process to see if that provides any more information (this requires a paid license). For example, do the following:

  1. Enter `lldb MyApp.app/Contents/MacOS/MyApp` in the Terminal.
  2. Enter `run` in the Terminal.
  3. Enter `c` in the Terminal.
  4. Exit when finished debugging.
- As a last resort, before calling `NSApplication.Init` in your `Main` method (or in other places as required), you could write text to a file in a known location to track down at what step of launch you are running into trouble.

## <a name="known-issues"></a>已知問題

The following sections cover known issues and their solutions.

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>Unable to connect to the debugger in sandboxed apps

The debugger connects to Xamarin.Mac apps through TCP, which means that by default when you enable sandboxing, it is unable to connect to the app, so if you try to run the app without the proper permissions enabled, you get an error *“Unable to connect to the debugger”* .

[![Editing the entitlements](troubleshooting-images/debug01.png "編輯權利")](troubleshooting-images/debug01-large.png#lightbox)

The **Allow Outgoing Network Connections (Client)** permission is the one required for the debugger, enabling this one will allow debugging normally. Since you can’t debug without it, we have updated the `CompileEntitlements` target for `msbuild` to automatically add that permission to the entitlements for any app that is sandboxed for debug builds only. Release builds should use the entitlements specified in the entitlements file, unmodified.

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System.NotSupportedException: no data is available for encoding 437

When including 3rd party libraries in your Xamarin.Mac app, you might get an error in the form "System.NotSupportedException: No data is available for encoding 437" when trying to compile and run the app. For example, libraries, such as `Ionic.Zip.ZipFile`, may throw this exception during operation.

This can be solved by opening the options for the Xamarin.Mac project, going to **Mac Build** > **Internationalization** and checking the **West** internationalization:

[![Editing the build options](troubleshooting-images/issue01.png "編輯組建選項")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>Failed to compile (mm5103)

This error is usually caused when a new version of Xcode is release and you have installed the new version but no yet run it. 嘗試使用新版本的 Xcode 進行編譯之前，您必須先執行該版本至少一次。

當您第一次執行新版本的 Xcode 時，它會安裝 Xamarin 所需的數個命令列工具。 此外，您應該在更新 Xcode 或您的 Xamarin. Mac 版本之後，執行乾淨的組建。

如果您無法解決此問題，請提出[bug](#filing-a-bug)。

### <a name="missing-entitlementsplist"></a>缺少權利。 plist

最新版的 Visual Studio for Mac 已移除**plist**編輯器中的 [權利] 區段，並將它放在不同的**權利中。 plist**編輯器（以支援 Xamarin 的跨平臺支援）。

安裝新的 Visual Studio for Mac 之後，當您建立新的 Xamarin. Mac 應用程式專案時， **plist**檔案將會自動加入至專案樹狀結構中：

![選取權利](troubleshooting-images/entitlements01.png "選取權利")

如果您按兩下**plist**檔案，將會顯示 [權利編輯器]：

[![編輯權利](troubleshooting-images/entitlements02.png "編輯權利")](troubleshooting-images/entitlements02-large.png#lightbox)

針對現有的 Xamarin 專案，您必須以滑鼠右鍵按一下  **Solution Pad**中的專案，然後選取 **加入** > **新增**檔案 ...，以手動方式建立**plist**檔案。接下來，選取  **Xamarin** > **空的屬性清單**：

![加入新的屬性清單](troubleshooting-images/entitlements03.png "加入新的屬性清單")

在 [名稱] 中輸入 `Entitlements`，然後按一下 [**新增**] 按鈕。 如果您的專案先前已包含權利檔案，系統會提示您將其新增至專案，而不是建立新的檔案：

[![驗證檔案的覆寫](troubleshooting-images/entitlements04.png "驗證檔案的覆寫")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="community-support-on-the-forums"></a>論壇上的社區支援

使用 Xamarin 產品的開發人員群很驚人，而且有許多人可以造訪我們的[xamarin 論壇](https://forums.xamarin.com/categories/mac)，分享經驗和其專長。 此外，Xamarin 工程師會定期流覽論壇以提供協助。

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>提出 bug

您的意見反應對我們很重要。 如果您發現 Xamarin. Mac 有任何問題：

- 搜尋[問題存放庫](https://github.com/xamarin/xamarin-macios/issues) \(英文\)
- 在切換到 GitHub 問題之前，Xamarin 問題的追蹤是在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) \(英文\) 上進行的。 請從該處搜尋相符的問題。
- 如果您找不到相符的問題，請在 [GitHub 問題存放庫](https://github.com/xamarin/xamarin-macios/issues/new) \(英文\) 中提出新的問題。

GitHub 問題全都是公開的。 無法隱藏意見或附件。

請儘量包含下列資訊：

- 一個可重現問題的簡單範例。 這是**非常寶貴的**，請盡您所能提供。
- 損毀狀況的完整堆疊追蹤。
- 損毀狀況周圍的 C# 程式碼。
