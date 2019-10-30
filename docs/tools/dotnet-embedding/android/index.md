---
title: Android 上的 .NET 內嵌
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: davidortinau
ms.author: daortin
ms.date: 06/15/2018
ms.openlocfilehash: fef422b799ab5280aef205f4d5e55fd91050da39
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007331"
---
# <a name="net-embedding-on-android"></a>Android 上的 .NET 內嵌

在某些情況下，您可能會想要將 Xamarin .NET 程式庫新增至現有的原生 Android 專案。 若要這樣做，您可以使用[Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/)工具將您的 .net 程式庫轉換成原生程式庫，以併入原生的 JAVA 型 Android 應用程式。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="xamarinandroid-requirements"></a>Xamarin. Android 需求

若要讓 Xamarin 使用 .NET 內嵌，您需要下列各項：

- 您必須安裝**xamarin** &ndash;[xamarin. android 7.5](https://visualstudio.microsoft.com/xamarin/)或更新版本。

- 必須安裝**Android Studio** &ndash;[Android Studio](https://developer.android.com/studio/) 3.x 或更新版本。

- 必須安裝 java**開發工具組**&ndash;[java 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本。

## <a name="using-embeddinator-4000"></a>使用 Embeddinator-4000

若要使用原生 Android 專案中的 .NET 程式庫，請執行下列步驟：

1. 建立C# Android 程式庫專案。

2. 安裝[Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/)。

3. 找出**Embeddinator-4000** ，並將它新增至您的**路徑**。 例如:

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4. 在程式庫元件上執行 Embeddinator-4000。 例如:

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. 在 Android Studio 的 JAVA 專案中使用產生的 AAR 檔案。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="xamarinandroid-requirements"></a>Xamarin. Android 需求

若要讓 Xamarin 使用 .NET 內嵌，您需要下列各項：

- 您必須安裝**xamarin** &ndash;[xamarin. android 7.5](https://visualstudio.microsoft.com/xamarin/)或更新版本。

- 必須安裝**Android Studio** &ndash;[Android Studio](https://developer.android.com/studio/) 3.x 或更新版本。

- 必須安裝 java**開發工具組**&ndash;[java 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更新版本。

- 必須安裝**mono** &ndash;[mono 5.0](https://www.mono-project.com/download/)或更新版本（mono 是隨 Visual Studio for Mac 安裝）。

## <a name="using-embeddinator-4000"></a>使用 Embeddinator-4000

若要使用原生 Android 專案中的 .NET 程式庫，請執行下列步驟：

1. 建立C# Android 程式庫專案。

2. 安裝[Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/)。

3. 找出**Embeddinator-4000** ，並在您的路徑中新增**mono** 。 例如:

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4. 在程式庫元件上執行 Embeddinator-4000。 例如:

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. 在 Android Studio 的 JAVA 專案中使用產生的 AAR 檔案。

-----

[使用方式] 和 [命令列] 選項會在[Embeddinator-4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c)檔中說明。

## <a name="callbacks"></a>叫

瞭解如何[在和 JAVA C#之間進行呼叫](callbacks.md)。

## <a name="samples"></a>範例

- [天氣範例應用程式](https://github.com/jamesmontemagno/embeddinator-weather)
