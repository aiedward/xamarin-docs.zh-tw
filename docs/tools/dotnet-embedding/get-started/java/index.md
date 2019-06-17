---
title: 開始使用 Java
description: 本文件說明如何開始使用 Java 中使用 .NET 內嵌。 它討論系統需求、 安裝和支援的平台。
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
author: lobrien
ms.author: laobri
ms.date: 03/28/2018
ms.openlocfilehash: 79a483743946c4f7509833867f2afe4b1e055183
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61198938"
---
# <a name="getting-started-with-java"></a>開始使用 Java

這是適用於 Java，其中涵蓋了所有支援平台的基本概念開始使用 頁面。

## <a name="requirements"></a>需求

若要搭配使用內嵌.NET 與 Java 中，您將需要：

* Java 1.8 或更新版本
* [Mono 5.0](https://www.mono-project.com/download/)

For Mac:

* Xcode 8.3.2 或更新版本

針對 Windows:

* Visual Studio 2017C++支援
* Windows 10 SDK

針對 Android:

* [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/)或更新版本
* [Android Studio 3.x](https://developer.android.com/studio/index.html)使用 Java 1.8

您可以使用[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)編輯並編譯您C#程式碼。

> [!NOTE]
> 較早版本的 Xcode，Visual Studio、 Xamarin.Android，Android Studio 中和 Mono_可能_運作，但是未經測試，並且不受支援。

## <a name="installation"></a>安裝

.NET 內嵌位於目前[NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```shell
nuget install Embeddinator-4000
```

這會將**Embeddinator 4000.exe**成**封裝/Embeddinator 4000/工具**目錄。

此外，您可以在其中建置.NET 內嵌來源，請參閱我們[git 儲存機制](https://github.com/mono/Embeddinator-4000/)並[參與](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)文件，如需相關指示。

## <a name="platforms"></a>平台

Java 目前為預覽狀態適用於 macOS、 Windows、 和 Android。

選取平台傳遞`--platform=<platform>`.NET 內嵌工具的命令列引數。 目前`macOS`， `Windows`，和`Android`支援。

### <a name="macos-and-windows"></a>macOS 及 Windows

程式開發中，您應該能夠使用任何支援 Java 1.8 的 Java IDE。 您甚至可以使用 Android Studio 這個視[在這裡看到](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects)。 如同任何標準的 Java jar 檔案，您可以使用輸出 JAR 檔案。

### <a name="android"></a>Android

請確定您已經設定好開發 Android 應用程式，然後再嘗試建立一個使用.NET 內嵌。 [指示](~/tools/dotnet-embedding/get-started/java/android.md)假設您有已經成功建置和部署 Android 應用程式從您的電腦。

Android Studio 建議進行開發，但其他 Ide 應該運作，只要支援[AAR 檔案格式](https://developer.android.com/studio/projects/android-library.html)。

## <a name="further-reading"></a>進一步閱讀

* [在 Android 上開始使用](~/tools/dotnet-embedding/get-started/java/android.md)
* [在 Android 上的回撥](~/tools/dotnet-embedding/android/callbacks.md)
* [Android 的初步研究](~/tools/dotnet-embedding/android/index.md)
* [.NET 內嵌限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [錯誤碼和描述](~/tools/dotnet-embedding/errors.md)
