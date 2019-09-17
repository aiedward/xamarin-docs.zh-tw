---
title: 建立具有目標 Sharpie 的系結
description: 本節提供目標 Sharpie 的簡介，這是 Xamarin 的命令列工具，用來將建立系結至目標 C 程式庫的程式自動化
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: conceptdev
ms.author: crdun
ms.date: 10/11/2017
ms.openlocfilehash: d5b9fa1edc09b831dbc69ab092dfb5270942e67a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765703"
---
# <a name="creating-bindings-with-objective-sharpie"></a>建立具有目標 Sharpie 的系結

_本節提供目標 Sharpie 的簡介，這是 Xamarin 的命令列工具，用來將建立系結至目標 C 程式庫的程式自動化_

- [總覽](#overview) & 歷程[記錄](#history)
- [快速入門](get-started.md)
- [工具與命令](tools.md)
- [功能](platform/index.md)
- [範例](examples/index.md)
- [完整逐步解說](~/ios/platform/binding-objective-c/walkthrough.md)
- [發行記錄](releases.md)

## <a name="overview"></a>總覽

目標 Sharpie 是一種命令列工具，可協助啟動程式的第一次傳遞。
其運作方式是剖析原生程式庫的標頭檔，將公用 API 對應至系結[定義](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file)（先前手動執行的進程）。

目標 Sharpie 會使用 Clang 剖析標頭檔，因此系結會盡可能精確且全面地進行。 這可以大幅減少產生品質系結所需的時間和工作。

> [!IMPORTANT]
> 目標 Sharpie 是經驗豐富的 Xamarin 開發人員的工具，具備客觀的先進知識（以及擴充功能，C）。 在嘗試系結目標-C 程式庫之前，您應該已瞭解如何在命令列上建立原生程式庫（並瞭解原生程式庫的運作方式）。

## <a name="history"></a>歷程

過去三年來，我們一直在 Xamarin 的 Sharpie 中發展並使用目標。 由於 iOS 8、Mac OS X 10.10 和 watchOS 2.0 的 Sharpie，已與目標 Sharpie 完全啟動，因此在證明時，在 Xamarin 和 Xamarin 中引進的 Api。 Xamarin 高度依賴于內部的目標 Sharpie，以建立自己的產品。

不過，目標 Sharpie 是一項非常先進的工具，需要先進的目標 C 和 C 知識，如何在命令列上使用 clang 編譯器，以及一般如何將原生程式庫放在一起。 由於這個高階的原因，我們覺得 GUI wizard 的設定不正確，因此目標 Sharpie 目前僅以命令列工具的形式提供。

## <a name="related-links"></a>相關連結

- [目標 Sharpie 下載](https://aka.ms/objective-sharpie)
- [逐步解說：系結目標-C 程式庫](~/ios/platform/binding-objective-c/walkthrough.md)
- [繫結 Objective-C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)
- [系結詳細資料](~/cross-platform/macios/binding/overview.md)
- [系結類型參考指南](~/cross-platform/macios/binding/binding-types-reference.md)
- [適用於 Objective-C 開發人員的 Xamarin](~/ios/get-started/objective-c-developers/index.md)
