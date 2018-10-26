---
title: 內嵌在 Android 上的.NET
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: lobrien
ms.author: laobri
ms.date: 06/15/2018
ms.openlocfilehash: 5c8d493bf54ee1a8a1e7d4b3266451c78a4aa51e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123676"
---
# <a name="net-embedding-on-android"></a>內嵌在 Android 上的.NET

在某些情況下，您可能想要將 Xamarin.NET 程式庫新增至現有的原生 Android 專案。 若要這樣做，您可以使用[Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/)工具，以將您的.NET 程式庫轉換成可整合到原生的 Java 型 Android 應用程式的原生程式庫。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="xamarinandroid-requirements"></a>Xamarin.Android 需求

使用.NET 內嵌 xamarin.android，您需要下列項目：

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/)或更新版本必須安裝。

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/)或更新版本必須安裝。

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本必須安裝。


## <a name="using-embeddinator-4000"></a>使用 Embeddinator 4000

若要使用原生的 Android 專案中的.NET 程式庫，請使用下列步驟：

1.  建立C#Android 程式庫專案。

2.  安裝[Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/)。

3.  找出**Embeddinator 4000.exe**並將它加入您**路徑**。 例如: 

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4.  在 程式庫組件上執行的 Embeddinator 4000。 例如: 

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  使用產生的 AAR 檔案，在 Android Studio 中的 Java 專案中。


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="xamarinandroid-requirements"></a>Xamarin.Android 需求

使用.NET 內嵌 xamarin.android，您需要下列項目：

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/)或更新版本必須安裝。

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/)或更新版本必須安裝。

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本必須安裝。

-   **單聲道** &ndash; [Mono 5.0](http://www.mono-project.com/download/)或更新版本必須安裝 （單聲道會隨 Visual Studio for Mac）。


## <a name="using-embeddinator-4000"></a>使用 Embeddinator 4000

若要使用原生的 Android 專案中的.NET 程式庫，請使用下列步驟：

1.  建立C#Android 程式庫專案。

2.  安裝[Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/)。

3.  找出**Embeddinator 4000.exe**並加入**mono**至您的路徑。 例如: 

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4.  在 程式庫組件上執行的 Embeddinator 4000。 例如: 

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  使用產生的 AAR 檔案，在 Android Studio 中的 Java 專案中。

-----

使用方式和命令列選項如下所述[Embeddinator 4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c)文件。


## <a name="callbacks"></a>回呼

深入了解[之間的呼叫C#和 Java](callbacks.md)。

## <a name="samples"></a>範例

* [天氣範例應用程式](https://github.com/jamesmontemagno/embeddinator-weather)
