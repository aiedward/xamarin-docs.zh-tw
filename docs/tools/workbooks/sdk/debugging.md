---
title: 偵錯的整合
description: 本文件說明如何偵錯 Xamarin Workbooks 整合代理程式後端和用戶端上 Windows 和 mac。
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 86d9c6af93e7f59eb0e819730e46324688df7566
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105996"
---
# <a name="debugging-integrations"></a>偵錯的整合

## <a name="debugging-agent-side-integrations"></a>偵錯的代理程式端整合

使用所提供的記錄方法最適合執行偵錯代理程式端整合`Log`類別中`Xamarin.Interactive.Logging`。 請參閱[ `API docs` ](https://developer.xamarin.com/api/type/Xamarin.Interactive.Logging.Log/)呼叫的方法。

在 macOS 上，記錄訊息會出現在這兩個記錄檔檢視器 功能表 (**視窗中 > 記錄檔檢視器**) 和用戶端記錄檔中。 在 Windows 中，訊息只會出現在用戶端記錄檔中，因為沒有任何記錄檔檢視器有。

用戶端記錄檔是在 macOS 和 Windows 上的下列位置：

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

一件事要注意的是，當載入透過一般的整合`#r`在開發期間的機制，整合的組件將會選取做為_相依性_活頁簿並與它一起封裝，如果絕對路徑不使用。 這會導致對似乎不會傳播，如同重建整合未執行任何動作。

## <a name="debugging-client-side-integrations"></a>偵錯用戶端整合

因為是以 JavaScript 撰寫用戶端整合，並將其載入我們的 web 瀏覽器介面 (請參閱[架構](~/tools/workbooks/sdk/architecture.md)文件)，加以偵錯的最佳方式是在 Mac 上，使用 WebKit 開發人員工具，或在 Windows 上使用 f12 鍵選擇器.

這兩組工具可讓您檢視 JavaScript/TypeScript 原始檔、 設定中斷點、 檢視主控台輸出，並檢查和修改 DOM

### <a name="mac"></a>Mac

若要在 Mac 上的 Xamarin Workbooks 啟用開發人員工具，請在終端機執行下列命令：

```shell
defaults write com.xamarin.Workbooks WebKitDeveloperExtras -bool true
```

然後再重新啟動 Xamarin 活頁簿。 一旦您這樣做，您應該會看到**檢查項目**出現在您以滑鼠右鍵按一下內容功能表，然後新**開發人員**窗格將可在活頁簿的喜好設定。 此選項可讓您選擇是否要在啟動時開啟的開發人員工具：

[![開發人員 窗格](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

這個喜好設定也僅限重新啟動時，您必須重新啟動，才能讓它才會生效，新的活頁簿上的活頁簿用戶端。 啟用開發人員工具，透過操作功能表或喜好設定，將會顯示熟悉的 Safari UI:

[![Safari 開發工具](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

使用 Safari 開發人員工具的相關資訊，請參閱[WebKit 偵測器文件][webkit-docs]。

### <a name="windows"></a>Windows

在 Windows，IE 小組會提供工具，稱為 「 f12 鍵選擇器 」 也就是遠端偵錯工具內嵌的 Internet Explorer 執行個體。 您可以在下列位置找到此工具：

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

執行 f12 鍵選擇器，而且您應該會看到支援的活頁簿用戶端介面，在清單中的內嵌執行個體。 選擇附加至用戶端，以及熟悉的 F12 偵錯工具，從 Internet Explorer，會出現：

[![F12 工具](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector
