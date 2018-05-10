---
title: Objective C 平台
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 8c48a14e5d58f95bf93363e0b354fcf3afd94b44
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="objective-c-platforms"></a>Objective C 平台

.NET 內嵌可以各種平台為目標時產生 Objective C 程式碼：

* macOS
* iOS
* tvOS
* watchOS [尚未實作]

平台會選取傳遞`--platform=<platform>`.NET 內嵌的命令列引數。

針對 iOS 建置時，tvOS 和 watchOS 平台，.NET 內嵌一律將建立內嵌 Xamarin.iOS，因為 Xamarin.iOS 包含多個執行階段支援程式碼在這些平台所需的架構。

不過，針對 macOS 平台建置時，便可選擇產生的 framework 是否應內嵌 Xamarin.Mac。 可內嵌 Xamarin.Mac 如果繫結的組件並未參考 Xamarin.Mac.dll，（直接或間接），且選取此選項將傳遞`--platform=macOS`.NET 內嵌的工具。

如果繫結的組件包含 Xamarin.Mac.dll 的參考，則需要內嵌 Xamarin.Mac，並另外 embeddinator 必須知道要使用哪一個目標架構。

有三個可能的 Xamarin.Mac 目標架構： `modern` (先前稱為`mobile`)，`full`和`system`(每個差異述 Xamarin.Mac 的[目標 framework][ 1]文件)，且每個為已選取傳遞`--platform=macOS-modern`，`--platform=macOS-full`或`--platform=macOS-system`.NET 內嵌的工具。

[1]: ~/mac/platform/target-framework.md
