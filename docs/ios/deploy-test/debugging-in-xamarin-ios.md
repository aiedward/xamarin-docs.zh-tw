---
title: 偵錯 Xamarin.iOS 應用程式
description: 本文件描述如何在 Visual Studio for Mac 或 Visual Studio 2019 中使用偵錯工具來偵錯 Xamarin.iOS 應用程式，包括設定中斷點、無線偵錯等。
ms.prod: xamarin
ms.assetid: 05460010-99E1-DC38-F855-2D691EF54484
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: ae140f7ea75c1631c6c96170af738f3fac04dcdb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026513"
---
# <a name="debugging-xamarinios-apps"></a>偵錯 Xamarin.iOS 應用程式

_Xamarin.iOS 應用程式可以使用 Visual Studio for Mac 或 Visual Studio 中的內建偵錯工具進行偵錯。_

使用 Visual Studio for Mac 的原生偵錯支援來針對 C# 和其他受控語言程式碼進行偵錯，並在您需要針對可能與您的 Xamarin.iOS 專案連結之 C、C++ 或 Objective C 程式碼進行偵錯時使用 [LLDB](https://lldb.llvm.org/tutorial.html)。

> [!NOTE]
> 當您在「偵錯」模式中編譯應用程式時，Xamarin.iOS 將產生較慢且較大的應用程式，因為每一行程式碼都必須進行檢測。 在發行之前，請務必先完成 [發行] 組建。

Xamarin.iOS 偵錯工具會整合到您的 IDE 中，而且它可讓開發人員在模擬器和裝置上針對使用 Xamarin.iOS 所支援之任何受控語言建置的 Xamarin.iOS 應用程式進行偵錯。

Xamarin.iOS 偵錯工具會使用 [Mono 軟偵錯工具](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/) \(英文\)，這表示產生的程式碼和 Mono 執行階段會與 IDE 配合，以提供偵錯體驗。 這不同於像是 LLDB 或 MDB 等硬偵錯工具，這些偵錯工具會在被偵錯之程式不知情或未與被偵錯之程式合作的情況下控制該程式。

## <a name="setting-breakpoints"></a>設定中斷點

當您準備好開始針對您的應用程式進行偵錯時，第一步就是[設定應用程式的中斷點](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)。 這可以透過按一下編輯器的邊界區域來完成，就在您想要中斷的程式碼行號旁邊：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](debugging-in-xamarin-ios-images/debugging1.png "Setting Breakpoints")](debugging-in-xamarin-ios-images/debugging1.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](debugging-in-xamarin-ios-images/debugging1a.png "Setting Breakpoints")](debugging-in-xamarin-ios-images/debugging1a.png#lightbox)

-----

您可以移至 [中斷點] 面板來檢視程式碼中已設定的所有中斷點：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](debugging-in-xamarin-ios-images/image0a.png "The Breakpoints pad")](debugging-in-xamarin-ios-images/image0a.png#lightbox)

 如果 [中斷點] 面板未自動顯示，您可以選取 [檢視] > [偵錯 Windows] > [中斷點] 來顯示它

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](debugging-in-xamarin-ios-images/image0.png "The Breakpoints pad")](debugging-in-xamarin-ios-images/image0.png#lightbox)

 如果 [中斷點] 面板未自動顯示，您可以選取 [偵錯] > [Windows] > [中斷點] 來顯示它

-----

在開始針對任何應用程式進行偵錯之前，請一律確定組態設定為 [偵錯]，因為這包含了一組支援偵錯的實用工具，例如中斷點、使用資料視覺化檢視，以及檢視呼叫堆疊：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](debugging-in-xamarin-ios-images/debugging7.png "Debugging on simulator")](debugging-in-xamarin-ios-images/debugging7.png#lightbox)
[![](debugging-in-xamarin-ios-images/debugging7a.png "Debugging on a physical device")](debugging-in-xamarin-ios-images/debugging7a.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](debugging-in-xamarin-ios-images/debugging7c.png "Debugging on simulator")](debugging-in-xamarin-ios-images/debugging7c.png#lightbox)
[![](debugging-in-xamarin-ios-images/debugging7d.png "Debugging on a physical device")](debugging-in-xamarin-ios-images/debugging7d.png#lightbox)

-----

## <a name="start-debugging"></a>開始偵錯
若要開始偵錯，請在 IDE 選取目標裝置或類似：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](debugging-in-xamarin-ios-images/debugging7b.png "Select the target device")](debugging-in-xamarin-ios-images/debugging7b.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](debugging-in-xamarin-ios-images/debugging7e.png "Select the target device")](debugging-in-xamarin-ios-images/debugging7e.png#lightbox)

-----

然後按 [執行] 按鈕部署您的應用程式。

當您到達中斷點時，程式碼反白顯示為黃色：

[![](debugging-in-xamarin-ios-images/image2.png "The code will be highlighted yellow")](debugging-in-xamarin-ios-images/image2.png#lightbox)

偵錯工具，例如用來檢查物件值的工具，可在此時用來取得程式碼中發生情況的詳細資訊：

[![](debugging-in-xamarin-ios-images/image3.png "Displaying a color value")](debugging-in-xamarin-ios-images/image3.png#lightbox)

## <a name="conditional-breakpoints"></a>條件中斷點

您也可以設定規則，指出應該發生中斷點的情況，這就是新增「條件式中斷點」。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要設定條件中斷點，請存取 [中斷點屬性] 視窗，這可以透過兩種方式進行：

- 若要新增條件中斷點，請在您想要設定中斷點的程式碼行號左邊，以滑鼠右鍵按一下編輯器邊界，然後選取 [新增中斷點]：

  [![](debugging-in-xamarin-ios-images/image4.png "Select New Breakpoint")](debugging-in-xamarin-ios-images/image4.png#lightbox)

- 若要為現有中斷點新增條件，請以滑鼠右鍵按一下中斷點，然後選取 [中斷點屬性]，或在 [中斷點] 面板中選取 [屬性] 按鈕，如下所示：

  [![](debugging-in-xamarin-ios-images/image5.png "The Breakpoints Pad")](debugging-in-xamarin-ios-images/image5.png#lightbox)

然後，您可以輸入想要中斷點發生的條件：

[![](debugging-in-xamarin-ios-images/image6.png "Enter the condition for the breakpoint to occur")](debugging-in-xamarin-ios-images/image6.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中設定條件式中斷點，請先[設定一般中斷點](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)。 以滑鼠右鍵按一下中斷點，以顯示其快顯功能表：

 [![](debugging-in-xamarin-ios-images/image4vs.png "The breakpoint context menu")](debugging-in-xamarin-ios-images/image4vs.png#lightbox)

選取 [條件] 以顯示 [中斷點設定] 功能表：

 [![](debugging-in-xamarin-ios-images/image6vs.png "The Breakpoint Settings menu")](debugging-in-xamarin-ios-images/image6vs.png#lightbox)

然後，您可以輸入想要中斷點發生的條件

如需有關在舊版 Visual Studio 中使用中斷點條件的詳細資訊，請參閱關於此主題的 [Visual Studio 文件](https://docs.microsoft.com/visualstudio/debugger/using-breakpoints)。

-----

## <a name="navigating-through-code"></a>瀏覽程式碼

當已到達中斷點時，偵錯工具可讓您控制程式的執行。 IDE 會顯示四個按鈕，可讓您執行與逐步執行程式碼。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，它們看起來像下面這樣：

 [![](debugging-in-xamarin-ios-images/image7.png "The Debug tools enable the developer to get control over the program’s execution")](debugging-in-xamarin-ios-images/image7.png#lightbox)

這些是：

- **執行/停止**：這會開始/停止執行程式碼，直到下一個中斷點。
- **不進入函式**：這會執行下一行程式碼。 如果下一行是函式呼叫，不進入函式會執行該函式，並在函式「之後」的下一行程式碼停止。
- **逐步執行**：這也會執行下一行程式碼。 如果下一行是函式呼叫，逐步執行會停止在函式的第一行，讓您繼續一行一行地進行函式的偵錯。 如果下一行不是函式，它的行為與「不進入函式」相同。
- **跳離函式**：這會回到呼叫目前函式的行。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中，它們看起來像下面這樣：

[![](debugging-in-xamarin-ios-images/image7vs.png "The Debug tools enable the developer to get control over the program’s execution")](debugging-in-xamarin-ios-images/image7vs.png#lightbox)

這些是：

- **執行/停止**：這會開始/停止執行程式碼，直到下一個中斷點。
- **不進入函式 (F11)** ：這會執行下一行程式碼。 如果下一行是函式呼叫，不進入函式會執行該函式，並在函式「之後」的下一行程式碼停止。
- **逐步執行 (F10)** ：這也會執行下一行程式碼。 如果下一行是函式呼叫，逐步執行會停止在函式的第一行，讓您繼續一行一行地進行函式的偵錯。 如果下一行不是函式，它的行為與「不進入函式」相同。
- **跳離函式 (Shift + F11)** 這會回到呼叫目前函式的行。

如需更多關於偵錯的深入文件，請參閱[使用 Visual Studio 偵錯工具巡覽程式碼](https://docs.microsoft.com/visualstudio/debugger/navigating-through-code-with-the-debugger)。

-----

### <a name="breakpoints"></a>中斷點

請了解 iOS 僅提供應用程式數秒 (10) 來設定及完成應用程式委派中的 `FinishedLaunching` 方法。 如果應用程式沒有在 10 秒內完成此方法，iOS 會終止處理序。

這表示它是幾乎不可能在您程式的啟始程式碼中設定中斷點。 如果您想要針對您的啟始程式碼進行偵錯，您應該延遲其初始化，並將它放入計時器叫用的方法，或者放在 FinishedLaunching 終止之後執行之部分其他形式的回呼方法中。

## <a name="device-diagnostics"></a>裝置診斷

如果設定偵錯工具時發生錯誤，您可以在您的 [專案選項] 中將 "-v -v -v" 新增至其他 mtouch 引數以啟用詳細診斷。 這會將詳細錯誤訊息列印到裝置主控台。

 <a name="WiFi_Debugging" />

## <a name="wireless-debugging"></a>無線偵錯

Xamarin.iOS 中的預設值是透過 USB 連線針對您裝置上的應用程式進行偵錯。 有時候可能需要 USB 裝置，以測試插入/拔除用來開發 ExternalAccessory 供電之應用程式的纜線。 在那些情況下，您可以透過無線網路使用偵錯。

如需無線部署和偵錯的詳細資訊，請參閱[無線部署](~/ios/deploy-test/wireless-deployment.md)指南。

<a name="Technical_Details" />

## <a name="technical-details"></a>技術詳細資料

Xamarin.iOS 使用新的 Mono 軟偵錯工具。 標準 Mono 偵錯工具是一種透過使用作業系統介面來控制個別程序的程式，軟偵錯工具與其不同，它的運作方式是讓 Mono 執行階段透過網路通訊協定公開偵錯功能。

在啟動時，要進行偵錯的應用程式會聯繫偵錯程式，而偵錯程式就會開始運作。 在 Visual Studio 的 Xamarin.iOS 中，Xamarin Mac 代理程式會充當應用程式 (在 Visual Studio 中) 和偵錯程式之間的中間人。

在裝置上執行時，此軟偵錯工具會需要合作式的偵錯配置。 這表示偵錯時您的二進位組建會較大，因為程式碼已通過檢測，可在每個序列點包含額外的程式碼以支援偵錯。

<a name="Accessing_the_Console" />

## <a name="accessing-the-console"></a>存取主控台

當機記錄檔和主控台類別的輸出將會傳送至 iPhone 主控台。 您可以利用 Xcode 使用「組合管理」，並從組合管理中選取您的裝置來存取此主控台。

或者，如果您不想要啟動 Xcode，您可以使用 Apple 的 [iPhone Configuration Utility](https://www.apple.com/support/iphone/enterprise/)，來直接存取主控台。 這麼做會有額外的好處，如果您正在針對欄位中的問題進行偵錯，您可以從 Windows 電腦存取主控台記錄檔。

針對 Visual Studio 使用者，在 [輸出] 視窗中有幾個記錄檔可以使用，但是您應該切換至您的 Mac，以取得更徹底且更詳細的記錄檔。

-----

<a name="Debugging_Mono's_Class_Libraries" />

## <a name="debugging-monos-class-libraries"></a>針對 Mono 類別庫進行偵錯

Xamarin.iOS 隨附 Mono 類別庫的原始程式碼，您可以使用它從偵錯工具逐步查看一切背後運作的情況。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

因為此功能會在偵錯期間消耗較多的記憶體，所以預設會關閉。

若要啟用此功能，請確定 [Visual Studio for Mac] > [喜好設定] > [偵錯工具] 功能表下的 [只偵錯專案程式碼; 不涉及架構程式碼] 選項已取消選取，如下所示：

[![](debugging-in-xamarin-ios-images/debugging6.png "Debugging Mono's Class Libraries")](debugging-in-xamarin-ios-images/debugging6.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要針對 Visual Studio 中的類別庫進行偵錯，您必須停用 [偵錯] > [選項] 功能表下的 [Just My Code]。 在 [偵錯] > [一般] 節點中，清除 [啟用 Just My Code] 核取方塊：

[![](debugging-in-xamarin-ios-images/debugging6vs.png "Debugging Mono's Class Libraries")](debugging-in-xamarin-ios-images/debugging6vs.png#lightbox)

-----

一旦您這樣做，您可以啟動您的應用程式並逐步執行任何 Mono 的核心類別庫。

## <a name="related-links"></a>相關連結

- [以 Xamarin 偵錯](/visualstudio/mac/debugging/)
- [資料視覺效果](/visualstudio/mac/data-visualizations/)
- [設定中斷點](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)
- [逐步執行程式碼](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)
- [輸出資訊至記錄視窗](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)
