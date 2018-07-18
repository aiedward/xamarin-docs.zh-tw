---
title: 內嵌在 Android 上的.NET
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: topgenorth
ms.author: toopge
ms.date: 06/15/2018
ms.openlocfilehash: e90d1e6258d4cfd9c918c566c9e18c358ee7668a
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067303"
---
# <a name="net-embedding-on-android"></a>內嵌在 Android 上的.NET

在某些情況下，您可能想要加入現有的原生 Android 專案中的 Xamarin.NET 程式庫。 若要這樣做，您可以使用[Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/)工具，以將您的.NET 程式庫轉換成可以結合到原生 Java Android 應用程式的原生程式庫。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="xamarinandroid-requirements"></a>Xamarin.Android 需求

若要使用.NET 內嵌 Xamarin.Android，您需要下列：

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/)或更新版本必須安裝。

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/)或更新版本必須安裝。

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本必須安裝。


## <a name="using-embeddinator-4000"></a>使用 Embeddinator 4000

若要使用的.NET 程式庫，原生的 Android 專案中，使用下列步驟：

1.  建立 C# Android 程式庫專案。

2.  安裝[Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/)。

3.  找出**Embeddinator 4000.exe**並將它加入您**路徑**。 例如: 

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4.  在程式庫組件上執行 Embeddinator-4000。 例如: 

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  使用產生的 AAR 檔案，在 Android Studio 中的 Java 專案中。


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="xamarinandroid-requirements"></a>Xamarin.Android 需求

若要使用.NET 內嵌 Xamarin.Android，您需要下列：

-   **Xamarin.Android** &ndash; [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/)或更新版本必須安裝。

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/)或更新版本必須安裝。

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本必須安裝。

-   **單聲道** &ndash; [單聲道 5.0](http://www.mono-project.com/download/)或更新版本必須安裝 （單聲道會隨 Visual Studio for Mac）。


## <a name="using-embeddinator-4000"></a>使用 Embeddinator 4000

若要使用的.NET 程式庫，原生的 Android 專案中，使用下列步驟：

1.  建立 C# Android 程式庫專案。

2.  安裝[Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/)。

3.  找出**Embeddinator 4000.exe**並加入**單聲道**至您的路徑。 例如: 

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4.  在程式庫組件上執行 Embeddinator-4000。 例如: 

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  使用產生的 AAR 檔案，在 Android Studio 中的 Java 專案中。

-----

使用方式和命令列選項如下所述[Embeddinator 4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c)文件。


## <a name="callbacks"></a>回呼

深入了解[進行呼叫，C# 和 Java 之間](callbacks.md)。

## <a name="samples"></a>範例

* [天氣範例應用程式](https://github.com/jamesmontemagno/embeddinator-weather)
