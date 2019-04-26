---
title: OBJECTIVE-C 平台
description: 本文件說明各種使用 OBJECTIVE-C 程式碼時以內嵌.NET 目標平台。 它討論 macOS、 iOS、 tvOS 和 watchOS。
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 8091fb4e8328f61f1471d061b51b4735de3c089c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230720"
---
# <a name="objective-c-platforms"></a>OBJECTIVE-C 平台

.NET 內嵌可以各種不同平台為目標時產生 OBJECTIVE-C 程式碼：

* macOS
* iOS
* tvOS
* watchOS [尚未實作]

選取平台傳遞`--platform=<platform>`.NET 內嵌的命令列引數。

針對 iOS 建置時，tvOS 和 watchOS 平台，.NET 內嵌一律會建立內嵌 Xamarin.iOS，因為 Xamarin.iOS 包含許多執行階段支援程式碼必須在這些平台上的架構。

不過，針對 macOS 平台建置時，就可以選擇產生的 framework 是否應內嵌 Xamarin.Mac。 就可以不內嵌 Xamarin.Mac，如果繫結的組件並未參考 Xamarin.Mac.dll，（直接或間接），且選取此項目傳遞`--platform=macOS`.NET 內嵌工具。

如果繫結的組件包含 Xamarin.Mac.dll 的參考，就必須內嵌 Xamarin.Mac 和此外 embeddinator 必須知道要使用哪一個目標架構。

有三種可能的 Xamarin.Mac 目標架構： `modern` (先前稱為`mobile`)，`full`並`system`(說明 Xamarin.Mac 的每個之間的差異[目標 framework][ 1]文件)，以及每個已選取傳遞`--platform=macOS-modern`，`--platform=macOS-full`或`--platform=macOS-system`.NET 內嵌工具。

[1]: ~/mac/platform/target-framework.md
