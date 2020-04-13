---
title: Java 與 Xamarin.安卓的集成
description: Java 生態系統包括多樣化和龐大的元件集合。 其中許多元件可用於減少開發 Android 應用程式所需的時間。 本文件將介紹並提供有關開發人員使用這些現有 JAVA 元件改進其 Xamarin.Android 應用程式開發體驗的一些方法的高級概述。
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/18/2017
ms.openlocfilehash: ecaa02e036c74074b4fa922ea079355b72ff02e2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020092"
---
# <a name="java-integration-with-xamarinandroid"></a>Java 與 Xamarin.安卓的集成

_Java 生態系統包括多樣化和龐大的元件集合。其中許多元件可用於減少開發 Android 應用程式所需的時間。本文件將介紹並提供有關開發人員使用這些現有 JAVA 元件改進其 Xamarin.Android 應用程式開發體驗的一些方法的高級概述。_

## <a name="overview"></a>概觀

鑒於JAVA生態系統的範圍,Xamarin.Android應用程式所需的任何給定功能很可能已經在JAVA中編碼。 因此,在創建 Xamarin.Android 應用程式時嘗試重用這些現有庫是很有吸引力的。

在 Xamarin.Android 應用程式中重用 JAVA 庫有三種可能的方法: 

- **使用此技術創建 Java 綁定庫**&ndash;,使用 Xamarin.Android 專案圍繞 JAVA 類型創建 C# 包裝。 然後,Xamarin.Android 應用程式可以引用此項目建立的 C# 包裝器,然後`.jar`使用該檔 。 

- **Java 本機介面**&ndash; *Java 本機**介面*(JNI) 是一個框架,它允許在 JVM 內運行的 Java 代碼調用或呼叫非 Java 代碼(如C++或 C#)。 

- **埠代碼**&ndash;此方法涉及獲取 Java 原始程式碼,然後將其轉換為 C#。 這可以手動完成,也可以通過使用自動工具(如銳化)來完成。 

前兩種技術的核心是*JAVA 本機介面*(JNI)。 JNI 是一個框架,它允許未寫入 Java 的應用程式與在 Java 虛擬機器中運行的 Java 代碼進行互動。 Xamarin.Android 使用 JNI 為 C# 程式碼建立*繫結*。 

第一種技術是一種更自動化的聲明性方法來綁定JAVA庫。 它涉及使用 Mac 的可視化工作室或由 Xamarin.AndroidJava&ndash;綁定庫提供的可視化工作室項目類型。 要成功創建這些綁定,JAVA 綁定庫可能仍然需要一些手動修改,但不如純 JNI 方法多。 有關 JAVA 綁定庫的詳細資訊[,請參閱綁定 Java 庫](~/android/platform/binding-java-library/index.md)。 

第二種技術使用JNI,在較低級別工作,但可以提供更精細的控制和對JAVA方法的訪問,這些方法通常無法通過JAVA綁定庫訪問。 

第三種技術與前兩種技術截然不同:將代碼從 JAVA 移植到 C#。 將代碼從一種語言移植到另一種語言可能是一個非常費力的過程,但可以通過名為*Sharpen*的工具來減少這種努力。 銳化是一種開源工具,是JAVA到C++轉換器。 

## <a name="summary"></a>總結

本文檔提供了一些不同方式的高級概述,其中 JAVA 的庫可以在 Xamarin.Android 應用程式中重用。 它介紹了綁定和託管可調用包裝器的概念,並討論了將 Java 代碼移植到 C# 的選項。 

## <a name="related-links"></a>相關連結

- [架構](~/android/internals/architecture.md)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [銳利化](https://github.com/slluis/sharpen)
- [Java 本機介面](https://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
