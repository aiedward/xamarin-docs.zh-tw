---
title: 目標-C 平臺
description: 本檔說明當使用目標 C 程式碼時，.NET 內嵌可以做為目標的各種平臺。 其中討論 macOS、iOS、tvOS 和 watchOS。
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: dbc2c40e06f14ec636b4aa4cc11fc4f2d230ee6d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73006416"
---
# <a name="objective-c-platforms"></a>目標-C 平臺

在產生目標 C 程式碼時，.NET 內嵌可以鎖定各種平臺：

* macOS
* iOS
* tvOS
* watchOS [尚未執行]

平臺是藉由將 `--platform=<platform>` 命令列引數傳遞至 .NET 內嵌來選取。

針對 iOS、tvOS 和 watchOS 平臺建立時，.NET 內嵌一律會建立內嵌 Xamarin 的架構，因為 Xamarin 包含許多在這些平臺上需要的執行時間支援程式碼。

不過，在建立 macOS 平臺時，可以選擇產生的架構是否應內嵌 Xamarin. Mac。 如果系結元件未參考 Xamarin. mac （直接或間接），則可以不內嵌 Xamarin，而且這是藉由將 `--platform=macOS` 傳遞至 .NET 內嵌工具而選取的。

如果系結元件包含 Xamarin. Mac 的參考，則必須內嵌 Xamarin，此外，embeddinator 必須知道要使用哪一個目標架構。

有三種可能的 Xamarin 目標架構： `modern` （先前稱為 `mobile`）、`full` 和 `system` （各項之間的差異會在 Xamarin 的[目標 framework][1]檔中說明），而且每個都是藉由傳遞來選取。`--platform=macOS-modern`，`--platform=macOS-full` 或 `--platform=macOS-system` 至 .NET 嵌入工具。

[1]: ~/mac/platform/target-framework.md
