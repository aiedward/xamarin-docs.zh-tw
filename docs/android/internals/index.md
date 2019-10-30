---
title: 進階概念與內部項目
description: Xamarin. Android 和其 API 設計背後的基礎架構。
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/21/2018
ms.openlocfilehash: 97382243ac5f767d94a782b895401c1f2f8ae554
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027850"
---
# <a name="advanced-concepts-and-internals"></a>進階概念與內部項目

_本章節包含的主題說明了 Xamarin 的架構、API 設計和限制。此外，它還包含說明其垃圾收集執行以及在 Xamarin 中提供之元件的主題。因為 Xamarin 是[開放原始](https://github.com/xamarin/xamarin-android)碼，所以也可以藉由檢查其原始程式碼來瞭解 xamarin 的內部運作方式。_

## <a name="architectureandroidinternalsarchitecturemd"></a>[Architecture (架構)](~/android/internals/architecture.md)

本文說明 Xamarin Android 應用程式背後的基礎架構。 其中說明 Xamarin Android 應用程式如何在 Mono 執行環境中和 Android 執行時間虛擬機器一起執行，並說明做為 Android 可呼叫包裝函式和受控可呼叫包裝函式的重要概念。 

## <a name="api-designandroidinternalsapi-designmd"></a>[API 設計](~/android/internals/api-design.md)

除了屬於 Mono 的核心基類庫，Xamarin 也隨附各種 Android Api 的系結，可讓開發人員使用 Mono 建立原生 Android 應用程式。

就 Xamarin 的核心而言，有一個 interop 引擎可將C#世界與 java 世界橋接，讓開發人員可以從C#或其他 .Net 語言存取 java api。

## <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[組件](~/cross-platform/internals/available-assemblies.md)

Xamarin 隨附數個元件。 就像 Silverlight 是桌面 .NET 元件的擴充子集，Xamarin 也是數個 Silverlight 和桌面 .NET 元件的擴充子集。 
