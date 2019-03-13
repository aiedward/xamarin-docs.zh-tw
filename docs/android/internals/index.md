---
title: 進階的概念與內部項目
description: Xamarin.Android 和它的 API 設計背後的基礎架構。
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/21/2018
ms.openlocfilehash: f5844dd4340afa0596219a33ed1e479a0dbcfa76
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114167"
---
# <a name="advanced-concepts-and-internals"></a>進階的概念與內部項目

_本節中的主題，說明架構、 API 設計，以及 Xamarin.Android 的限制。此外，它包含記憶體回收集合實作，可在 Xamarin.Android 中的組件的說明主題。因為 Xamarin.Android[開放原始碼](https://github.com/xamarin/xamarin-android)，您也可了解 Xamarin.Android 的內部運作方式，藉由檢查其原始程式碼。_


##  <a name="architectureandroidinternalsarchitecturemd"></a>[架構](~/android/internals/architecture.md)

這篇文章說明 Xamarin.Android 應用程式背後的基礎架構。 它說明如何將 Xamarin.Android 應用程式執行與 Android 執行階段的虛擬機器與 Mono 的執行環境內，並說明這類重要的概念，為 Android 可呼叫包裝函式和受管理的可呼叫包裝函式。 



##  <a name="api-designandroidinternalsapi-designmd"></a>[API 設計](~/android/internals/api-design.md)

除了核心屬於 Mono 的基底類別庫，Xamarin.Android 會隨附針對各種 Android Api，可讓開發人員建立與 Mono 的原生 Android 應用程式的繫結。

Xamarin.Android 的核心有是 interop 引擎與 Java 全世界該橋接器的 C# 世界，從 C# 或其他.NET 語言撰寫 Java api 提供開發人員存取。



##  <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[組件](~/cross-platform/internals/available-assemblies.md)

Xamarin.Android 隨附數個組件。 就像 Silverlight 是桌面的.NET 組件中擴充的子集，Xamarin.Android 也是數個 Silverlight 和桌面的.NET 組件延伸的子集。 

