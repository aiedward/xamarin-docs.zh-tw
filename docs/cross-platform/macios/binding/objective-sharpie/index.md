---
title: 目標 Sharpie
description: 本節提供目標 Sharpie，用來自動化的程序建立繫結到 Objective C 程式庫的 Xamarin 的命令列工具的簡介
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 4341aa7d2ea0bec88e9c36c1804c5a909fd6fd5c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="objective-sharpie"></a>目標 Sharpie

_本節提供目標 Sharpie，用來自動化的程序建立繫結到 Objective C 程式庫的 Xamarin 的命令列工具的簡介_

- [概觀](#overview) & [歷程記錄](#history)
- [快速入門](get-started.md)
- [工具與命令](tools.md)
- [功能](platform/index.md)
- [範例](examples/index.md)
- [完成逐步解說](~/ios/platform/binding-objective-c/walkthrough.md)
- [發行記錄](releases.md)

## <a name="overview"></a>總覽

目標 Sharpie 是協助啟動繫結的第一次傳遞的命令列工具。
可藉由剖析對應到公用 API 的原生程式庫的標頭檔來運作[繫結定義](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file)（先前已手動完成此程序）。

目標 Sharpie 使用 Clang 剖析標頭檔，因此繫結是為完全相符和徹底越好。 這可以顯著降低的時間和產生品質繫結所需的投入時間。

> [!IMPORTANT]
> 目標 Sharpie 是有經驗的 Xamarin 開發人員了解 OBJECTIVE-C （而且藉由擴充，C） 的進階工具。 然後再嘗試將繫結 Objective C 程式庫中，您應該具備如何建立命令列 （以及充分了解原生程式庫的運作方式） 的原生程式庫的實心知識。

## <a name="history"></a>歷程

我們已進化並目標 Sharpie 內部在使用 Xamarin 過去三年。 目標 Sharpie 冪的建築，Api Xamarin.iOS 和 Xamarin.Mac 中導入自 iOS 8、 Mac OS X 10.10，和 watchOS 2.0 所需目標 Sharpie 完全使用啟動載入。 Xamarin 高度依賴目標 Sharpie 在內部建立自己的產品。

不過，目標 Sharpie 是非常進階的工具，需要進階的知識 Objective C 和 C、 如何在命令列上使用 clang 編譯器和通常如何原生程式庫會放在一起。 由於高列中，我們認為需要 GUI 精靈設定錯誤的預期，而且，目標 Sharpie 目前只可做為命令列工具。

## <a name="related-links"></a>相關連結

- [目標 Sharpie 下載](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
- [逐步解說： 繫結 Objective C 程式庫](~/ios/platform/binding-objective-c/walkthrough.md)
- [繫結 Objective-C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)
- [繫結詳細資料](~/cross-platform/macios/binding/overview.md)
- [繫結型別參考指南](~/cross-platform/macios/binding/binding-types-reference.md)
- [適用於 Objective-C 開發人員的 Xamarin](~/ios/get-started/objective-c-developers/index.md)
