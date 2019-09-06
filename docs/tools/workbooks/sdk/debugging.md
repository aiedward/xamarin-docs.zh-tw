---
title: 調試
description: 本檔說明如何在 Windows 和 Mac 上的代理程式端和用戶端之間，進行 Xamarin Workbooks 整合的調試。
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
author: conceptdev
ms.author: crdun
ms.date: 06/19/2018
ms.openlocfilehash: fbb5673a70328ad6edde78af1b35d2801fe65ca8
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283921"
---
# <a name="debugging-integrations"></a>調試

## <a name="debugging-agent-side-integrations"></a>偵錯工具端整合

您最好使用中`Log` `Xamarin.Interactive.Logging`的類別所提供的記錄方法，來進行代理程式端整合的調試。

在 macOS 上，記錄檔訊息會同時出現在 [記錄檔檢視器] 功能表（[**視窗 > 記錄檔檢視器]** ）和用戶端記錄檔中。 在 Windows 中，訊息只會出現在用戶端記錄檔中，因為沒有記錄檔檢視器。

用戶端記錄檔位於 macOS 和 Windows 上的下列位置：

- Mac`~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows：`%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

有一點要注意的是，在開發期間透過一般`#r`機制載入整合時，將會以活頁簿的相依性來挑選整合元件，並在未使用絕對路徑的情況下，將其封裝在一起。 這可能會導致變更看起來不會傳播，如同重建整合並未進行任何事。

## <a name="debugging-client-side-integrations"></a>用戶端整合的調試

當用戶端整合是以 JavaScript 撰寫並載入我們的網頁瀏覽器介面（請參閱[架構](~/tools/workbooks/sdk/architecture.md)檔）時，使用 Mac 上的 WebKit 開發人員工具或在 Windows 上使用 F12 選擇器，就能將它們進行程式設計的最佳方式。

這兩組工具可讓您查看 JavaScript/TypeScript 來源、設定中斷點、查看主控台輸出，以及檢查和修改 DOM。

### <a name="mac"></a>Mac

若要啟用 Xamarin Workbooks 在 Mac 上的開發人員工具，請在您的終端機中執行下列命令：

```shell
defaults write com.xamarin.Workbooks WebKitDeveloperExtras -bool true
```

然後重新開機 Xamarin Workbooks。 執行此動作之後，您應該會看到 [**檢查**] 專案出現在滑鼠右鍵內容功能表中，而 [活頁簿偏好設定] 中會提供新的**開發人員**窗格。 此選項可讓您選擇是否要在啟動時開啟開發人員工具：

[![開發人員窗格](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

此喜好設定也會重新開機-您必須重新開機活頁簿用戶端，才能在新的活頁簿上生效。 透過操作功能表或喜好設定來啟用開發人員工具，將會顯示熟悉的 Safari UI：

[![Safari 開發工具](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

如需有關使用 Safari 開發人員工具的詳細資訊，請參閱[WebKit inspector 檔][webkit-docs]。

### <a name="windows"></a>Windows

在 Windows 上，IE 小組會提供稱為「F12 選擇器」的工具，這是內嵌 Internet Explorer 實例的遠端偵錯程式。 您可以在下列位置找到此工具：

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

執行 F12 選擇器，您應該會在清單中看到支援活頁簿用戶端介面的內嵌實例。 選擇它，將會顯示 Internet Explorer 中熟悉的 F12 調試工具，並附加到用戶端：

[![F12 工具](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector
