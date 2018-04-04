---
title: 在 Java 中內嵌的.NET
description: 如何使用 Java 為基礎原生 Android 專案中的 Xamarin.NET 程式庫
ms.prod: xamarin
ms.assetid: A489EEF3-1008-4257-BF63-FE21D8C23821
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/28/2018
ms.openlocfilehash: 0ca855975db4b20fe80e98e2e818b8fde27f558c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="embedding-net-in-java"></a>在 Java 中內嵌的.NET

在某些情況下，您可能想要加入現有的原生 Android 專案中的 Xamarin.NET 程式庫。 若要這樣做，您可以使用[Embeddinator 4000](https://mono.github.io/Embeddinator-4000/)工具，以將您的.NET 程式庫轉換成可以結合到原生 Java Android 應用程式的原生程式庫。

 
## <a name="requirements"></a>需求

若要使用在 Android 上的 Java Embeddinator 4000，您需要下列項目：

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/preview/index.html)或更新版本必須安裝。

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://www.visualstudio.com/xamarin/)或更新版本必須安裝。

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本必須安裝。

-   **單聲道** &ndash; [Mono 5.0](http://www.mono-project.com/download/)或更新版本必須安裝。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

您可以使用 Visual Studio 來編輯和編譯 C# 程式碼。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

您可以使用 Visual Studio for Mac 來編輯和編譯 C# 程式碼。

-----

 
## <a name="using-the-embeddinator-4000"></a>使用 Embeddinator 4000

若要使用原生的 Android 專案中的.NET 程式庫，您可以使用下列步驟：

1.  建立 C# Android 程式庫專案。

2.  安裝 Embeddinator-4000 透過 NuGet。

3.  Android 程式庫組件上執行 Embeddinator。

4.  使用產生的 AAR 檔案，在 Android Studio 中的 Java 專案中。

中有詳細說明這些步驟[Embeddinator 4000](https://mono.github.io/Embeddinator-4000/getting-started-java-android.html)文件。
