---
title: Java 整合概觀
description: Java 生態系統包括元件的各種及廣大的集合。 許多這些元件可以用來減少開發 Android 應用程式所花費的時間。 本文件將介紹，並提供一些開發人員可以使用這些現有的 Java 元件，以改善其 Xamarin.Android 應用程式開發體驗的方式的高階概觀。
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/18/2017
ms.openlocfilehash: 3ab31fb7cac97fbae3315f51daf3dd4b1edbcc1d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085286"
---
# <a name="java-integration-overview"></a>Java 整合概觀

_Java 生態系統包括元件的各種及廣大的集合。許多這些元件可以用來減少開發 Android 應用程式所花費的時間。本文件將介紹，並提供一些開發人員可以使用這些現有的 Java 元件，以改善其 Xamarin.Android 應用程式開發體驗的方式的高階概觀。_


## <a name="overview"></a>總覽

提供 Java 生態系統的範圍，就很可能是 Xamarin.Android 應用程式所需的任何特定的功能，已撰寫在 Java 中。 基於這個原因，是吸引人的嘗試，並建立 Xamarin.Android 應用程式時，重複使用這些現有的程式庫。 

有三種方式，以重複使用 Xamarin.Android 應用程式中的 Java 程式庫： 

-   **建立 Java 繫結程式庫**&ndash;使用此技術，Xamarin.Android 專案用來建立C#包裝函式圍繞 Java 類型。 Xamarin.Android 應用程式就可以參考C#包裝函式建立此專案，然後再使用`.jar`檔案。 

-   **Java Native Interface** &ndash; *Java 原生* *介面*(JNI) 是一種架構，可讓非 Java 程式碼 (例如C++或C#) 呼叫，或由 Java 程式碼呼叫JVM 內執行。 

-   **程式碼移植**&ndash;此方法牽涉到取得 Java 原始碼，然後將它轉換成C#。 這可以手動方式或使用自動化的工具，例如銳利化來完成。 

在前兩項技術的核心*Java Native Interface* (JNI)。 JNI 是一種架構，可讓不是以 Java 撰寫的應用程式在 Java 虛擬機器中執行的 Java 程式碼互動。 Xamarin.Android 來建立使用 JNI*繫結*的C#程式碼。 

第一種技術是更自動化、 宣告式方法繫結 Java 程式庫。 它牽涉到使用 Visual Studio for Mac 或 Visual Studio 專案類型提供的 Xamarin.Android &ndash; Java 繫結程式庫。 若要成功地建立這些繫結，Java 繫結程式庫可能仍然需要一些手動修改，但是不多如會純 JNI 方法。 請參閱[繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)for Java 繫結程式庫的詳細資訊。 

第二個技巧，使用 JNI，適用於低層級太多，但可以提供進行更細微的控制，並存取通常無法透過 Java 繫結程式庫可以存取的 Java 方法。 

第三種方法是從先前的兩個完全不同： 移植程式碼，從 Java C#。 移植至另一個從一種語言的程式碼可以是非常耗費人力的程序，但可以減少投入時間的一項工具協助稱為*銳利化*。 銳利化是一種開放原始碼工具，是 Java-到-C#轉換子。 



## <a name="summary"></a>總結

本文件提供的一些不同的方式，可在 Xamarin.Android 應用程式中重複使用 Java 程式庫的高階概觀。 它導入的繫結的概念和 managed 可呼叫包裝函式，並討論選項 Java 程式碼移植到C#。 


## <a name="related-links"></a>相關連結

- [架構](~/android/internals/architecture.md)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [銳利化](https://github.com/slluis/sharpen)
- [Java 原生介面](http://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
