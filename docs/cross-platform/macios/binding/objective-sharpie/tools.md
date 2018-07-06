---
title: 目標 Sharpie 工具與命令
description: 本文件提供隨附於目標 Sharpie 和命令列引數來使用這些工具的概觀。
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: 718b5104ddc4593d080b88b062c42d371d9e8e2e
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855064"
---
# <a name="objective-sharpie-tools--commands"></a>目標 Sharpie 工具與命令

_包含在與目標 Sharpie 命令列引數來使用這些工具的概觀。_

<style type="text/css"> .terminal 藍色 {color: rgb(10,96,254);}.terminal 綠色 {色彩： rgb(12,156,26);}.terminal 洋紅 {色彩： rgb(152,12,103);} </style>


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
|**xcode**|提供目前的 Xcode 安裝和版本的 iOS 和 Mac Sdk 所提供的資訊。 我們將使用這項資訊稍後當我們產生我們的繫結。|
|**pod**|搜尋、 設定、 安裝 （在本機目錄中），並繫結 OBJECTIVE-C [CocoaPod](https://cocoapods.org/)主要規格的儲存機制從程式庫。 此工具會評估來自動推算要傳遞至正確的輸入已安裝的 CocoaPod`bind`下列工具。 在 3.0 新功能 ！|
|**bind**|剖析的標頭檔 (`*.h`) 在初始的 Objective C 程式庫中[ApiDefinition.cs 和 StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md)檔案。|
|**update**|檢查有較新版本的目標 Sharpie 下載並啟動安裝程式，如果有的話。|
|**verify-docs**|顯示詳細的資訊，關於`[Verify]`屬性。|
|**docs**|瀏覽至您的預設網頁瀏覽器中的這份文件。|

若要取得特定的目標 Sharpie 工具的說明，請輸入工具的名稱和`-help`選項。 比方說，`sharpie xcode -help`傳回下列輸出：

<pre>$ <b>sharpie xcode -help</b>
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.</pre>

我們可以開始繫結程序之前，我們需要在終端機中輸入下列命令來取得我們目前已安裝的 Sdk 的相關資訊`sharpie xcode -sdks`。 您的輸出取決於您已安裝的 Xcode 版本而異。 尋找 Sdk 安裝在任何目標 Sharpie`Xcode*.app`下`/Applications`目錄：

<pre>$ <b>sharpie xcode -sdks</b>
<span class="terminal-blue">sdk:</span> appletvos9.0    <span class="terminal-green">arch:</span> arm64
<span class="terminal-blue">sdk:</span> iphoneos9.1     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos9.0     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> iphoneos8.4     <span class="terminal-green">arch:</span> arm64   armv7
<span class="terminal-blue">sdk:</span> macosx10.11     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> macosx10.10     <span class="terminal-green">arch:</span> x86_64  i386
<span class="terminal-blue">sdk:</span> watchos2.0      <span class="terminal-green">arch:</span> armv7</pre>

從上述項目，我們可以看到，我們有`iphoneos9.1`SDK 安裝在電腦上，而且有`arm64`架構支援。 我們將在本節中的所有範例使用此值。 在利用此資訊的地方，我們已準備好將 OBJECTIVE-C 程式庫標頭檔案剖析為初始`ApiDefinition.cs`和`StructsAndEnums.cs`繫結專案。

## <a name="related-links"></a>相關連結

- [Xamarin University 課程： 建置 OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University 課程： 建置目標 Sharpie OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)