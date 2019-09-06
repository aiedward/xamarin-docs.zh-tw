---
title: 目標 Sharpie 工具 & 命令
description: 本檔概述目標 Sharpie 所包含的工具，以及要搭配使用的命令列引數。
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: conceptdev
ms.author: crdun
ms.date: 10/05/2015
ms.openlocfilehash: 13f3000315e91cec7ff2422cff3b520997ba26fd
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280914"
---
# <a name="objective-sharpie-tools--commands"></a>目標 Sharpie 工具 & 命令

_概述目標 Sharpie 所包含的工具，以及使用它們的命令列引數。_

成功[安裝](~/cross-platform/macios/binding/objective-sharpie/get-started.md)目標 Sharpie 之後，請開啟終端機，並熟悉 [目標 Sharpie] 所提供的*命令*：

```
$ sharpie -help
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
  docs               Open the Objective Sharpie online documentation
```

目標 Sharpie 提供下列工具：

|工具|說明|
|--- |--- |
|**xcode**|提供目前的 Xcode 安裝以及可用的 iOS 和 Mac Sdk 版本的相關資訊。 稍後當我們產生系結時，我們將會用到此資訊。|
|**pod**|搜尋、設定、安裝（在本機目錄中），並系結主要規格存放庫中可用的[CocoaPod](https://cocoapods.org/)程式庫。 此工具會評估已安裝的 CocoaPod，以自動推算要傳遞至`bind`下列工具的正確輸入。 3\.0 中的新功能！|
|**bind**|將目標 C 程式庫`*.h`中的標頭檔（）剖析為初始[ApiDefinition.cs 和 StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md)檔案。|
|**update**|檢查是否有較新版本的目標 Sharpie 和下載，並啟動安裝程式（如果有的話）。|
|**verify-docs**|顯示`[Verify]`屬性的詳細資訊。|
|**docs**|在您的預設網頁瀏覽器中流覽到這份檔。|

若要取得特定目標 Sharpie 工具的說明，請輸入工具的名稱和`-help`選項。 例如， `sharpie xcode -help`會傳回下列輸出：

```
$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.
```

在開始進行系結程式之前，我們必須先在終端`sharpie xcode -sdks`機中輸入下列命令，以取得目前已安裝 sdk 的相關資訊。 您的輸出可能會根據您已安裝的 Xcode 版本而有所不同。 目標 Sharpie 會尋找安裝在`Xcode*.app` `/Applications`目錄底下任一處的 sdk：

```
$ sharpie xcode -sdks
sdk: appletvos9.0    arch: arm64
sdk: iphoneos9.1     arch: arm64   armv7
sdk: iphoneos9.0     arch: arm64   armv7
sdk: iphoneos8.4     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: macosx10.10     arch: x86_64  i386
sdk: watchos2.0      arch: armv7
```

在上述程式中，我們可以看到我們的`iphoneos9.1`機器上已安裝 SDK，而且它具有`arm64`架構支援。 本章節中的所有範例都會使用此值。 有了這項資訊之後，我們就可以開始將目標 C 程式庫標頭檔剖析成系`ApiDefinition.cs`結`StructsAndEnums.cs`專案的初始和。
