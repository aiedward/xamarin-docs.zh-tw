---
title: 開始使用 JAVA
description: 本檔說明如何開始搭配使用 .NET 內嵌與 JAVA。 其中討論系統需求、安裝和支援的平臺。
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
author: davidortinau
ms.author: daortin
ms.date: 03/28/2018
ms.openlocfilehash: 09ea33724c2b1184654ce7768ea1cb2525b62c28
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007370"
---
# <a name="getting-started-with-java"></a>開始使用 JAVA

這是適用于 JAVA 的快速入門頁面，其中涵蓋所有支援平臺的基本概念。

## <a name="requirements"></a>需求

若要搭配 JAVA 使用 .NET 內嵌，您將需要：

* JAVA 1.8 或更新版本
* [Mono 5。0](https://www.mono-project.com/download/)

針對 Mac：

* Xcode 8.3.2 或更新版本

若為 Windows：

* Visual Studio 2017 與C++支援
* Windows 10 SDK

針對 Android：

* [Xamarin. Android 7.5](https://visualstudio.microsoft.com/xamarin/)或更新版本
* 使用 JAVA 1.8 [Android Studio](https://developer.android.com/studio/index.html) 3。x

您可以使用[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)來編輯和編譯器C#代碼。

> [!NOTE]
> 舊版的 Xcode、Visual Studio、Xamarin、Android Studio 和 Mono_可能可以_使用，但尚未經過測試且不受支援。

## <a name="installation"></a>安裝

.NET 內嵌目前可在[NuGet](https://www.nuget.org/packages/Embeddinator-4000/)上取得：

```shell
nuget install Embeddinator-4000
```

這會將**Embeddinator-4000**放入**封裝/Embeddinator-4000/tools**目錄中。

此外，您可以從來源建立 .NET 內嵌，請參閱我們的[git 存放庫](https://github.com/mono/Embeddinator-4000/)和[參與](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)檔以取得相關指示。

## <a name="platforms"></a>平台

JAVA 目前處於適用于 macOS、Windows 和 Android 的預覽狀態。

平臺是藉由將 `--platform=<platform>` 命令列引數傳遞至 .NET 內嵌工具來選取。 目前支援 `macOS`、`Windows`和 `Android`。

### <a name="macos-and-windows"></a>macOS 和 Windows

若要進行開發，您應該能夠使用支援 JAVA 1.8 的任何 JAVA IDE。 您甚至可以視需要使用此 Android Studio，[請參閱這裡](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects)。 您可以使用 JAR 檔案輸出，就像處理任何標準的 JAVA JAR 檔案一樣。

### <a name="android"></a>Android

請先確定您已設定好開發 Android 應用程式，再嘗試使用 .NET 內嵌來建立它。 [下列指示](~/tools/dotnet-embedding/get-started/java/android.md)假設您已成功從電腦建立並部署 Android 應用程式。

建議使用 Android Studio 進行開發，但只要有[AAR 檔案格式](https://developer.android.com/studio/projects/android-library.html)的支援，其他 ide 應可正常執行。

## <a name="further-reading"></a>進一步閱讀

* [Android 上的消費者入門](~/tools/dotnet-embedding/get-started/java/android.md)
* [Android 上的回呼](~/tools/dotnet-embedding/android/callbacks.md)
* [初稿 Android Research](~/tools/dotnet-embedding/android/index.md)
* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [錯誤碼和描述](~/tools/dotnet-embedding/errors.md)
