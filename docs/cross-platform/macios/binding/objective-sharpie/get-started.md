---
title: 具有目標 Sharpie 的消費者入門
description: 本檔提供目標 Sharpie 的高階總覽，這是用來自動建立C#目標 C 程式碼系結的工具。
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
author: conceptdev
ms.author: crdun
ms.date: 10/11/2017
ms.openlocfilehash: c34a6c09bf1298fd710e3e39a244294821a714ae
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290725"
---
# <a name="getting-started-with-objective-sharpie"></a>具有目標 Sharpie 的消費者入門

> [!IMPORTANT]
> 目標 Sharpie 是經驗豐富的 Xamarin 開發人員的工具，具備客觀的先進知識（以及擴充功能，C）。 在嘗試系結目標-C 程式庫之前，您應該已瞭解如何在命令列上建立原生程式庫（並瞭解原生程式庫的運作方式）。

<a name="installing" />

## <a name="installing-objective-sharpie"></a>安裝目標 Sharpie

目標 Sharpie 目前是 Mac OS X 10.10 和更新版本的獨立命令列工具，而且_不是完全支援的 Xamarin 產品_。 僅供 advanced 開發人員使用，以協助建立系結專案至協力廠商目標 C 程式庫。

目標 Sharpie 可以下載為標準 OS X 套件安裝程式。
執行安裝程式，並遵循安裝精靈中的所有工具提示：

- **目前的版本：3.4**
  - [下載最新版本](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
  - [論壇公告](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> [!TIP]
> `sharpie update`使用命令更新至最新版本。

## <a name="basic-walkthrough"></a>基本逐步解說

目標 Sharpie 是 Xamarin 提供的命令列工具，可協助建立將協力廠商目標 C 程式庫系結至C#所需的定義。
即使在使用目標 Sharpie 時，開發人員*也*必須在目標 Sharpie 完成後修改產生的檔案，以解決任何無法由工具自動處理的問題。

在可能的情況下，目標 Sharpie 會標注其不確定如何正確系結的 Api （許多原生程式碼中的結構都不明確）。
這些批註會顯示為[ `[Verify]`屬性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)。

目標 Sharpie 的輸出是一對[ `ApiDefinition.cs`檔案，可以`StructsAndEnums.cs` ](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md)用來建立系結專案，以編譯成可在 Xamarin 應用程式中使用的程式庫。

> [!IMPORTANT]
> 目標 Sharpie 會提供一個用於適當用法的**主要**規則：您必須絕對傳遞正確的 clang 編譯器命令列引數，以確保正確的剖析。 這是因為目標 Sharpie 剖析階段只是[針對 clang LIBTOOLING API 所執行](http://clang.llvm.org/docs/LibTooling.html)的工具。

這表示目標 Sharpie 具有 Clang 的完整功能（實際會編譯您要系結的原C++生程式庫的 c/目標-c/編譯器），以及其所有系結的標頭檔內部知識。
目標 Sharpie 會將 [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) 轉譯為C#系結 "scaffold"，而不是將剖析的 ast 轉譯為物件程式碼， `bmac`而`btouch`是適用于和 Xamarin 系結工具的輸入。

如果目標 Sharpie 在剖析期間發生錯誤，這表示在剖析階段嘗試建立 AST 時，clang 會錯誤出來，因此您必須找出原因。

**新增功能！** 3\.0 版嘗試直接支援 Xcode 專案，以解決這種複雜性的問題。 如果原生程式庫具有有效的 Xcode 專案，目標 Sharpie 可以針對指定的目標和設定評估專案，以推算必要的輸入標頭檔和編譯器旗標。

如果沒有可用的 Xcode 專案，您將需要更熟悉專案，方法是推算出正確的輸入標頭檔、標頭檔搜尋路徑和其他必要的編譯器旗標。 請務必瞭解，用來建立原生程式庫的編譯器旗標，必須傳遞至目標 Sharpie。 這是更手動的程式，其中一項需要熟悉使用 Clang 工具鏈在命令列上編譯機器碼。

**新增功能！** 3\.0 版也引進了一種工具，可讓您`sharpie pod`透過命令輕鬆系結 [CocoaPods](https://cocoapods.org)。
如果您感興趣的程式庫是以 CocoaPod 的形式提供，我們建議您先嘗試將 CocoaPod 系結至目標 Sharpie （相對於嘗試直接針對來源進行系結）。
