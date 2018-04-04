---
title: 快速入門
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 139a5102b2f2d8a9d803fd1ac593934aa73e2415
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started"></a>快速入門

> [!IMPORTANT]
> 目標 Sharpie 是有經驗的 Xamarin 開發人員了解 OBJECTIVE-C （而且藉由擴充，C） 的進階工具。 然後再嘗試將繫結 Objective C 程式庫中，您應該具備如何建立命令列 （以及充分了解原生程式庫的運作方式） 的原生程式庫的實心知識。

<a name="installing" />

## <a name="installing-objective-sharpie"></a>安裝目標 Sharpie

目標 Sharpie 目前是獨立的命令列工具的 Mac OS X 10.10 和更新版本中，且_不完全支援的 Xamarin 產品_。 它應該只能由進階開發人員可協助建立繫結專案第 3 合作對象 Objective C 程式庫。

您可以下載目標 Sharpie 為標準的 OS X 套件安裝程式。
執行安裝程式，並遵循所有的螢幕上從安裝精靈的提示：

- **目前的版本： 3.4**
  - [下載最新版本](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
  - [論壇公告](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> [!TIP]
> 使用`sharpie update`命令，更新為最新版本。

## <a name="basic-walkthrough"></a>基本的逐步解說

目標 Sharpie 係由可協助建立的 Xamarin 定義繫結所需的第 3 個合作對象 Objective C 程式庫 C# 命令列工具。
即使是使用目標 Sharpie，開發人員*將*需要目標 Sharpie 完成來解決任何問題，無法自動處理此工具之後，修改產生的檔案。

如果可行，目標 Sharpie 會加上註解與它必須有一些不確定如何正確繫結的應用程式開發介面 （原生程式碼中的許多建構模稜兩可）。
這些註解將會顯示為[`[Verify]`屬性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)。

目標 Sharpie 的輸出是一組檔案- [ `ApiDefinition.cs`和`StructsAndEnums.cs` ](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) -，可用來建立繫結的專案，您可以使用哪些編譯成為程式庫中的 Xamarin 應用程式。

> [!IMPORTANT]
> 目標 Sharpie 隨附一個**主要**規則的正確用法： 您必須有絕對傳送它的正確 clang 編譯器命令列引數以確保正確剖析。 這是因為在剖析階段目標 Sharpie 是簡單工具[對 clang libtooling API 實作](http://clang.llvm.org/docs/LibTooling.html)。

這表示目標 Sharpie Clang （實際上會編譯原生程式庫，您將繫結目標的 C/C 編譯器） 和所有其內部知識的繫結的標頭檔的完整功能。
而不是轉譯的已剖析[AST](http://en.wikipedia.org/wiki/Abstract_syntax_tree)物件程式碼中，目標 Sharpie 轉譯至 C# 繫結 」 scaffold"適合輸入至 AST`bmac`和`btouch`Xamarin 繫結工具。

如果目標 Sharpie 出剖析期間發生錯誤，則表示該 clang 期間錯誤出其剖析階段嘗試建構 AST，而且您需要找出原因。

**NEW!** 3.0 版嘗試解決部分這種複雜性藉由直接支援的 Xcode 專案。 如果原生程式庫中有有效的 Xcode 專案，目標 Sharpie 可以評估指定的目標和組態來推算的必要輸入標頭檔和編譯器旗標的專案。

如果沒有 Xcode 專案功能，您必須要由較為熟悉以專案推算出的正確輸入標頭檔、 標頭檔搜尋路徑和其他必要的編譯器旗標。 請務必了解用來建立原生程式庫的編譯器旗標都相同，必須傳遞至目標 Sharpie。 這是更多的手動程序，以及一需要熟悉命令列中利用 Clang 工具鏈的原生程式碼編譯的位元。

**NEW!** 3.0 也引進了輕鬆地繫結的工具版本[CocoaPods](https://cocoapods.org)透過`sharpie pod`命令。
如果您感興趣的程式庫功能為 CocoaPod，我們建議您一開始會嘗試與目標 Sharpie CocoaPod 連結 （相對於嘗試針對來源直接繫結）。
