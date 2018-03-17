---
title: "工具和命令"
description: "包含在與目標 Sharpie 命令列引數來使用這些工具的概觀。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: 4985d67f74d54644cbc033d6c76148f88d741ee1
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="tools--commands"></a>工具和命令

_包含在與目標 Sharpie 命令列引數來使用這些工具的概觀。_

<style type="text/css"> .terminal 藍色 {色彩： rgb(10,96,254);}.terminal 綠色 {色彩： rgb(12,156,26);}.terminal 洋紅 {色彩： rgb(152,12,103);} </style>


一旦目標 Sharpie 成功[安裝](~/cross-platform/macios/binding/objective-sharpie/get-started.md)，開啟終端機，並熟悉<em>命令</em>目標 Sharpie 所提供：

<pre>$ <b>sharpie -help</b>
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, -help                Show detailed help
  -v, -version             Show version information

Telemetry Options:
  -tlm-about               Show a detailed overview of what usage and binding
                             information will be submitted to Xamarin by
                             default when using Objective Sharpie.
  -tlm-do-not-submit       Do not submit any usage or binding information to
                             Xamarin. Run 'sharpie -tml-about' for more
                             information.
  -tlm-do-not-identify     Do not submit Xamarin account information when
                             submitting usage or binding information to Xamarin
                             for analysis. Binding attempts and usage data will
                             be submitted anonymously if this option is
                             specified.

Available Tools:
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation</pre>

目標 Sharpie 提供下列工具：

|工具|描述|
|--- |--- |
|**xcode**|提供有關目前 Xcode 安裝和版本的 iOS 和 Mac Sdk 所提供的資訊。 我們將使用這項資訊稍後當我們產生我們的繫結。|
|**pod**|搜尋、 設定、 安裝 （在本機目錄中），並繫結 OBJECTIVE-C [CocoaPod](https://cocoapods.org/)主版規格的儲存機制從程式庫。 此工具會評估來自動推算要傳遞給正確的輸入已安裝的 CocoaPod`bind`下列工具。 在 3.0 新功能 ！|
|**bind**|剖析的標頭檔 (`*.h`) 至初始 Objective C 程式庫中[ApiDefinition.cs 和 StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md)檔案。|
|**update**|會檢查有較新版本的目標 Sharpie 和下載，並啟動安裝程式如果有的話。|
|**verify-docs**|會顯示詳細的資訊`[Verify]`屬性。|
|**docs**|瀏覽至文件預設網頁瀏覽器中。|

若要取得特定的目標 Sharpie 工具的說明，請輸入工具的名稱和`-help`選項。 例如，`sharpie xcode -help`會傳回下列輸出：

<pre>$ <b>sharpie xcode -help</b>
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.</pre>

我們可以開始繫結程序之前，我們需要終端機中輸入下列命令來取得我們目前已安裝的 Sdk 的相關資訊`sharpie xcode -sdks`。 您的輸出可能已安裝 Xcode 版本不同。 目標 Sharpie 會尋找任何已安裝的 Sdk`Xcode*.app`下`/Applications`目錄：

<pre>$ <b>sharpie xcode -sdks</b>
<span class="terminal-blue">sdk:</span> appletvos9.0    <span class="terminal-green">arch:</span> arm64
<span class="terminal-blue">sdk:</span> iphoneos9.1     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos9.0     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos8.4     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> macosx10.11     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> macosx10.10     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> watchos2.0      <span class="terminal-green">arch:</span> armv7</pre>

從上述項目，我們可以看到我們有`iphoneos9.1`我們的機器上安裝 SDK，而且有`arm64`架構支援。 我們將在本節中的所有範例都使用這個值。 這項資訊的位置中，我們已準備好剖析 Objective C 程式庫標頭檔至初始`ApiDefinition.cs`和`StructsAndEnums.cs`繫結專案。

