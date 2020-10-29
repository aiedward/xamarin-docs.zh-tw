---
title: 使用目標 Sharpie 消費者入門
description: '本檔提供目標 Sharpie 的高階總覽，此工具可用來自動建立 c # 系結至目標 C 程式碼。'
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
author: davidortinau
ms.author: daortin
ms.date: 10/11/2017
ms.openlocfilehash: 5bee8df72290cab3ed6d5c23fef6c2ae2f1a2559
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928512"
---
# <a name="getting-started-with-objective-sharpie"></a>使用目標 Sharpie 消費者入門

> [!IMPORTANT]
> 目標 Sharpie 是一種工具，可讓有經驗的 Xamarin 開發人員熟悉目標-C (以及 C) 擴充功能。 嘗試系結目標 C 程式庫之前，您應該先瞭解如何在命令列上建立原生程式庫 (以及如何) 原生程式庫的運作方式。

<a name="installing"></a>

## <a name="installing-objective-sharpie"></a>安裝目標 Sharpie

目標 Sharpie 目前是適用于 Mac OS X 10.10 和更新版本的獨立命令列工具，而且 _不是完全支援的 Xamarin 產品_ 。 它應該僅供 advanced 開發人員使用，以協助建立協力廠商目標 C 程式庫的系結專案。

目標 Sharpie 可作為標準 OS X 套件安裝程式下載。
執行安裝程式，並遵循安裝 wizard 的所有螢幕上的提示：

- **目前版本：3。4**
  - [下載最新版本](https://aka.ms/objective-sharpie)
  - [論壇公告](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> [!TIP]
> 使用 `sharpie update` 命令更新為最新版本。

## <a name="basic-walkthrough"></a>基本逐步解說

目標 Sharpie 是 Xamarin 所提供的一項命令列工具，可協助您建立將協力廠商目標 C 程式庫系結至 c # 所需的定義。
即使在使用目標 Sharpie 時，開發人員 *也* 必須在目標 Sharpie 完成後修改產生的檔案，以解決工具無法自動處理的任何問題。

在可能的情況下，目標 Sharpie 會標注 Api，而該 Api 在原生程式碼中要如何適當地系結 (許多結構並不明確) 。
這些批註將會顯示為[ `[Verify]` 屬性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)。

目標 Sharpie 的輸出是一組檔案， [ `ApiDefinition.cs` 而且 `StructsAndEnums.cs` ](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md)可以用來建立系結專案，以編譯成可在 Xamarin 應用程式中使用的程式庫。

> [!IMPORTANT]
> 目標 Sharpie 有一個用於適當使用的 **主要** 規則：您必須將正確的 clang 編譯器命令列引數傳遞給它，以確保正確剖析。 這是因為目標 Sharpie 剖析階段只是 [針對 clang LIBTOOLING API 所執行](https://clang.llvm.org/docs/LibTooling.html)的工具。

這表示目標 Sharpie 有 Clang (的完整功能，它會實際編譯您要系結) 的原生程式庫，以及它對所有要系結之標頭檔的內部知識。
目標 Sharpie 會將 AST 轉譯為 c # 系結 "scaffold" （適用于和 Xamarin 系結工具的輸入），而不是將剖析的 [ast](https://en.wikipedia.org/wiki/Abstract_syntax_tree) 轉譯為物件程式碼 `bmac` `btouch` 。

如果目標 Sharpie 在剖析期間發生錯誤，則表示 clang 在剖析階段嘗試建立 AST 時錯誤，而您必須找出原因。

**新增功能！** 3.0 版會藉由直接支援 Xcode 專案來解決這種複雜性。 如果原生程式庫具有有效的 Xcode 專案，目標 Sharpie 可以評估指定目標和設定的專案，以推算必要的輸入標頭檔和編譯器旗標。

如果沒有可用的 Xcode 專案，您必須推算出正確的輸入標頭檔、標頭檔搜尋路徑和其他必要的編譯器旗標，更熟悉專案。 請務必瞭解用來建立原生程式庫的編譯器旗標，與必須傳遞至目標 Sharpie 的編譯器旗標相同。 這是更手動的程式，而且需要稍微熟悉在命令列上使用 Clang 工具鏈編譯器代碼的程式。

**新增功能！** 3.0 版也引進了一種工具，可讓您輕鬆地透過命令系結 [CocoaPods](https://cocoapods.org) `sharpie pod` 。
如果您感興趣的程式庫是以 CocoaPod 的形式提供，我們建議您先嘗試系結 CocoaPod 與目標 Sharpie (，再嘗試系結直接) 的來源。
