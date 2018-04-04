---
title: 進階的概念和內部項目
description: Xamarin.Android 和它的應用程式開發介面設計背後的基本架構。
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/13/2017
ms.openlocfilehash: 517e21f2decd0dabbd03d752f13831a891ad7138
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="advanced-concepts-and-internals"></a>進階的概念和內部項目


##  <a name="architectureandroidinternalsarchitecturemd"></a>[架構](~/android/internals/architecture.md)

本文說明 Xamarin.Android 應用程式背後的基本架構。 它說明 Xamarin.Android 應用程式如何與 Android runtime 虛擬機器連同單聲道執行環境內執行，並說明這類重要的概念為 Android 可呼叫包裝函式和管理可呼叫包裝函式。 



##  <a name="api-designandroidinternalsapi-designmd"></a>[API 設計](~/android/internals/api-design.md)

除了基底類別庫屬於 Mono 核心 Xamarin.Android 隨附的各種 Android 應用程式開發介面可讓開發人員建立單聲道的原生 Android 應用程式繫結。

核心 Xamarin.Android 有 interop 的引擎與 Java 世界該橋接器的 C# 世界，提供開發人員存取 Java api 從 C# 或其他.NET 語言。



##  <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[組件](~/cross-platform/internals/available-assemblies.md)

Xamarin.Android 隨附數個組件。 就像是 Silverlight 桌面的.NET 組件中擴充的子集，Xamarin.Android 也是數個 Silverlight 和桌面的.NET 組件擴充的子集。 

