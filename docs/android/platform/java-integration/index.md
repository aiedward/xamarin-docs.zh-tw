---
title: Java 整合概觀
description: Java 生態系統包含元件的各種及廣大的集合。 這些元件的許多可用來減少開發 Android 應用程式所花費的時間。 本文件將介紹，並提供一些開發人員可以使用這些現有的 Java 元件，以改善其 Xamarin.Android 應用程式開發體驗的方式的高層級概觀。
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/18/2017
ms.openlocfilehash: dbaf17479ae077fced425df5ac31bdbbc4e06b64
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766942"
---
# <a name="java-integration-overview"></a>Java 整合概觀

_Java 生態系統包含元件的各種及廣大的集合。這些元件的許多可用來減少開發 Android 應用程式所花費的時間。本文件將介紹，並提供一些開發人員可以使用這些現有的 Java 元件，以改善其 Xamarin.Android 應用程式開發體驗的方式的高層級概觀。_


## <a name="overview"></a>總覽

提供 Java 生態系統的範圍，就很可能是 Xamarin.Android 應用程式所需的任何特定的功能，已在 Java 中撰寫。 因為這個緣故，所以嘗試和重複使用這些現有的程式庫建立 Xamarin.Android 應用程式時，極具吸引力。 

有三種方式，若要重複使用 Xamarin.Android 應用程式中的 Java 程式庫： 

-   **建立 Java 繫結庫** &ndash; Xamarin.Android 專案這項技巧，用來建立 C# 包裝函式的 Java 類型。 Xamarin.Android 應用程式可以參考此專案中，所建立的 C# 包裝函數，然後使用`.jar`檔案。 

-   **Java 原生介面** &ndash; *Java 原生**介面*(JNI) 是一種架構，讓使用者能夠呼叫或由 JVM 內執行的 Java 程式碼呼叫的非 Java 程式碼 （例如 c + + 或 C#）. 

-   **將程式碼移植**&ndash;此方法牽涉到建立 Java 原始碼，並再將它轉換成 C#。 這可以手動方式或透過自動化的工具，例如銳利完成。 

前兩項技術的核心是*Java 原生介面*(JNI)。 JNI 是此架構可讓不是以 Java 撰寫的應用程式在 Java 虛擬機器中執行的 Java 程式碼互動。 Xamarin.Android 來建立使用 JNI*繫結*C# 程式碼。 

第一種技術是更自動化、 宣告式繫結 Java 文件庫。 它牽涉到使用 Visual Studio for Mac 或 Visual Studio 專案類型提供的 Xamarin.Android &ndash; Java 繫結程式庫。 若要成功建立這些繫結，Java 繫結的程式庫仍可能需要一些手動修改，但不是盡如同會純 JNI 方法。 請參閱[繫結 Java 文件庫](~/android/platform/binding-java-library/index.md)for Java 繫結的程式庫的詳細資訊。 

第二個的技巧，使用 JNI，也可以在較低層級，，但可以提供較細微的控制和存取通常無法存取透過 Java 繫結程式庫的 Java 方法。 

第三種方法是從先前的兩個截然不同： 移植到 C# 程式碼從 Java。 移植至另一個程式碼從一種語言是非常耗費人力的程序，但它有可能降低投入時間的協助工具的呼叫*銳利*。 銳利化是 Java 開放原始碼工具-到-C# 轉換子。 



## <a name="summary"></a>總結

本文件提供的一些不同的方式，可在 Xamarin.Android 應用程式中重複使用 Java 程式庫的高階概觀。 它導入的繫結的概念和管理可呼叫包裝函式，並討論的 Java 程式碼移植到 C# 選項。 


## <a name="related-links"></a>相關連結

- [架構](~/android/internals/architecture.md)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [銳利化](https://github.com/slluis/sharpen)
- [Java 原生介面](http://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
