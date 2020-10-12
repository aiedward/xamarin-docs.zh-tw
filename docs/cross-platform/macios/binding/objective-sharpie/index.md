---
title: 建立具有目標 Sharpie 的系結
description: 本節提供目標 Sharpie 的簡介，這是 Xamarin 的命令列工具，可用來將建立系結至目標 C 程式庫的程式自動化
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: davidortinau
ms.author: daortin
ms.date: 10/11/2017
ms.openlocfilehash: 7fe8c563b78459959a5c8b50883ed539040f7a7b
ms.sourcegitcommit: 37f5b0380df75da6e2b09ef39a8e71deb50bfe0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91943593"
---
# <a name="creating-bindings-with-objective-sharpie"></a>建立具有目標 Sharpie 的系結

_本節提供目標 Sharpie 的簡介，這是 Xamarin 的命令列工具，可用來將建立系結至目標 C 程式庫的程式自動化_

- [總覽](#overview)  & 歷程[記錄](#history)
- [快速入門](get-started.md)
- [工具與命令](tools.md)
- [功能](platform/index.md)
- [範例](examples/index.md)
- [完成逐步解說](~/ios/platform/binding-objective-c/walkthrough.md)
- [發行大事記](releases.md)

## <a name="overview"></a>概觀

目標 Sharpie 是一種命令列工具，可協助啟動系結的第一次傳遞。
它的運作方式是剖析原生程式庫的標頭檔，以將公用 API 對應到先前手動完成) 的進程，以將公用 API 對應到系結 ([定義](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) 中。

目標 Sharpie 會使用 Clang 來剖析標頭檔，因此系結會盡可能精確且完整。 這樣可以大幅減少產生品質系結所需的時間和精力。

> [!IMPORTANT]
> 目標 Sharpie 是一種工具，可讓有經驗的 Xamarin 開發人員熟悉目標-C (以及 C) 擴充功能。 嘗試系結目標 C 程式庫之前，您應該先瞭解如何在命令列上建立原生程式庫 (以及如何) 原生程式庫的運作方式。

## <a name="history"></a>歷程記錄

過去三年來，我們已在 Xamarin 內部不斷地 Sharpie 目標，並使用目標。 作為目標 Sharpie 的證明，自 iOS 8、Mac OS X 10.10 和 watchOS 2.0 起，從 iOS 8、和引進的 Api 完全是以目標 Sharpie 來啟動。 Xamarin 高度依賴內部目標 Sharpie 來建立自己的產品。

不過，目標 Sharpie 是非常先進的工具，需要對目標-C 和 C 的深入知識、如何在命令列上使用 clang 編譯器，以及通常如何將原生程式庫放在一起。 由於這個高階橫條的原因，我們覺得 GUI wizard 設定了錯誤的期望，因此目標 Sharpie 目前僅提供作為命令列工具。

## <a name="related-links"></a>相關連結

- [目標 Sharpie 下載](https://aka.ms/objective-sharpie)
- [逐步解說：系結目標-C 程式庫](~/ios/platform/binding-objective-c/walkthrough.md)
- [繫結 Objective-C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)
- [系結詳細資料](~/cross-platform/macios/binding/overview.md)
- [系結類型參考指南](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin for 目標-C 開發人員](~/ios/get-started/objective-c-developers/index.md)
