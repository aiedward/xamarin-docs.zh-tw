---
title: 目標-C 平臺
description: 本檔說明當使用目標 C 程式碼時，.NET 內嵌可以做為目標的各種平臺。 其中討論 macOS、iOS、tvOS 和 watchOS。
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: f97b595f129cb1ad1ea56e3ae43b0f0a477fef5a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282734"
---
# <a name="objective-c-platforms"></a>目標-C 平臺

在產生目標 C 程式碼時，.NET 內嵌可以鎖定各種平臺：

* macOS
* iOS
* tvOS
* watchOS [尚未執行]

平臺是藉由將`--platform=<platform>`命令列引數傳遞至 .net 內嵌來選取。

針對 iOS、tvOS 和 watchOS 平臺建立時，.NET 內嵌一律會建立內嵌 Xamarin 的架構，因為 Xamarin 包含許多在這些平臺上需要的執行時間支援程式碼。

不過，在建立 macOS 平臺時，可以選擇產生的架構是否應內嵌 Xamarin. Mac。 如果系結元件未參考 xamarin. mac （直接或間接），則可能不會內嵌 Xamarin，而是藉由傳遞`--platform=macOS`至 .net 內嵌工具來選取此選項。

如果系結元件包含 Xamarin. Mac 的參考，則必須內嵌 Xamarin，此外，embeddinator 必須知道要使用哪一個目標架構。

有三種可能的 Xamarin 目標架構： `modern` （先前已呼叫`mobile`） `full`和`system` （各項之間的差異會在 Xamarin 的[目標 framework][1]檔中說明），而且每個會藉由將`--platform=macOS-modern` `--platform=macOS-full`或`--platform=macOS-system`傳遞至 .net 內嵌工具來選取。

[1]: ~/mac/platform/target-framework.md
