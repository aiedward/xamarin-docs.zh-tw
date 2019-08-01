---
title: 建立繫結與目標 Sharpie
description: 本節提供目標 Sharpie，用來自動化程序建立繫結到 OBJECTIVE-C 程式庫的 Xamarin 的命令列工具的簡介
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: c298d3dc772929a123b6b65b0efe929f023a6563
ms.sourcegitcommit: 7dbfac5f68c55f40dd726aa1b03cff62082fe711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67866360"
---
# <a name="creating-bindings-with-objective-sharpie"></a>建立繫結與目標 Sharpie

_本節提供目標 Sharpie，用來自動化程序建立繫結到 OBJECTIVE-C 程式庫的 Xamarin 的命令列工具的簡介_

- [概觀](#overview) & [歷程記錄](#history)
- [快速入門](get-started.md)
- [工具與命令](tools.md)
- [功能](platform/index.md)
- [範例](examples/index.md)
- [完成逐步解說](~/ios/platform/binding-objective-c/walkthrough.md)
- [發行記錄](releases.md)

## <a name="overview"></a>總覽

目標 Sharpie 是協助啟動程序繫結的第一個階段的命令列工具。
它藉由剖析對應到公用 API 的原生程式庫的標頭檔的運作方式[繫結定義](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file)（先前已手動完成這個程序）。

目標 Sharpie 使用 Clang 剖析的標頭檔，因此繫結是以完全相符和方面盡可能詳盡地。 這可大幅減少產生品質繫結所花費的心力與時間。

> [!IMPORTANT]
> 目標 Sharpie 是經驗豐富的 Xamarin 開發人員提供進階知識的 Objective C （並延伸，C） 的工具。 然後再嘗試繫結 Objective C 程式庫中，您應該有穩固的了解如何建置命令列 （和深入了解原生程式庫的運作方式） 上的原生程式庫。

## <a name="history"></a>歷程

我們已發展並使用目標 Sharpie 內部 Xamarin 在過去三年。 目標 Sharpie 的理由，Api 中導入 Xamarin.iOS 和 Xamarin.Mac iOS 8、 Mac OS 起，因為，watchOS 2.0 已完全與目標 Sharpie 啟動而已。 Xamarin 依賴目標 Sharpie 內部建置自己的產品。

不過，目標 Sharpie 是一種非常進階的工具，需要進階的 Objective C 和 C、 clang 編譯器命令列上使用，以及通常如何原生程式庫會放在一起的知識。 由於高列中，我們認為有 GUI 精靈 會設定錯誤的預期，且此情況下，目標 Sharpie 目前只提供做為命令列工具。

## <a name="related-links"></a>相關連結

- [目標 Sharpie 下載](https://aka.ms/objective-sharpie)
- [逐步解說：繫結 Objective C 程式庫](~/ios/platform/binding-objective-c/walkthrough.md)
- [繫結 Objective-C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)
- [繫結詳細資料](~/cross-platform/macios/binding/overview.md)
- [繫結型別參考指南](~/cross-platform/macios/binding/binding-types-reference.md)
- [適用於 Objective-C 開發人員的 Xamarin](~/ios/get-started/objective-c-developers/index.md)

