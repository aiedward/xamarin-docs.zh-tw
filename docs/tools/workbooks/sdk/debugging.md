---
title: 偵錯的整合
description: 本文件說明如何偵錯 Xamarin 活頁簿整合，代理程式端和用戶端上 Windows 和 mac。
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
author: topgenorth
ms.author: toopge
ms.date: 06/19/2018
ms.openlocfilehash: 6e37b1ac3d0fb78b5737ebe97b5a28ab40adb648
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36269053"
---
# <a name="debugging-integrations"></a>偵錯的整合

## <a name="debugging-agent-side-integrations"></a>偵錯的代理程式端整合

偵錯代理程式端整合最佳方式是使用所提供的記錄方法`Log`類別`Xamarin.Interactive.Logging`。 請參閱[ `API docs` ](https://developer.xamarin.com/api/type/Xamarin.Interactive.Logging.Log/)呼叫的方法。

MacOS，在記錄檔訊息，會出現在記錄檔檢視器功能表 (**視窗 > 記錄檔檢視器**) 和用戶端記錄檔中。 在 Windows 中，訊息只出現在用戶端記錄，因為有任何記錄檔檢視器中。

用戶端記錄檔位於 macOS 及 Windows 上的下列位置：

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

要注意的一點是，當載入透過一般整合`#r`在開發期間的機制，整合組件將會挑選做為_相依性_活頁簿和隨附的絕對路徑是否未使用。 這可能會造成似乎不會傳播，如同重建整合並沒有變更。

## <a name="debugging-client-side-integrations"></a>偵錯用戶端整合

當用戶端整合會以 JavaScript 撰寫，並載入我們的 web 瀏覽器介面 (請參閱[架構](~/tools/workbooks/sdk/architecture.md)文件)，進行偵錯的最佳方式是在 Mac 上，使用 WebKit 開發人員工具或 Windows 上使用 F12 選擇器.

這兩組工具可讓您檢視 JavaScript/TypeScript 原始檔、 設定中斷點、 檢視主控台輸出和檢查及修改 DOM

### <a name="mac"></a>Mac

若要啟用 Xamarin Mac 上的活頁簿的開發人員工具，請在您的終端機中執行下列命令：

```shell
defaults write com.xamarin.Workbooks WebKitDeveloperExtras -bool true
```

然後再重新啟動 Xamarin 活頁簿。 一旦您這樣做，您應該會看到**檢查項目**出現在您以滑鼠右鍵按一下內容功能表上，以及新**開發人員**窗格將可在活頁簿喜好設定。 此選項可讓您選擇要在啟動時開啟開發人員工具：

[![開發人員 窗格](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

這個喜好設定也僅限重新啟動時，您必須重新啟動，才能讓它才會生效，新的活頁簿上的活頁簿用戶端。 啟用開發人員工具，透過操作功能表或喜好設定，將會顯示在熟悉的 Safari UI:

[![Safari 開發人員工具](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

如需使用 Safari 開發人員工具的資訊，請參閱[WebKit 偵測器文件][webkit-docs]。

### <a name="windows"></a>Windows

在 Windows 中，IE 小組提供工具，稱為 「 F12 選擇器 」 也就是遠端偵錯工具針對內嵌的 Internet Explorer 執行個體。 您可以在下列位置找到此工具：

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

執行的 F12 選擇器，而且您應該會看到可提供簡單的活頁簿用戶端介面，在清單中的內嵌執行個體。 選擇它，並從 Internet Explorer 的偵錯工具隨即出現，熟悉 F12 附加至用戶端：

[![F12 工具](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector
