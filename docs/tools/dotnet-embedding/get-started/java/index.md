---
title: 開始使用 Java
ms.topic: article
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/28/2018
ms.openlocfilehash: 06df925525c7bc01963274e9e0d5edfe7bb70613
ms.sourcegitcommit: 17a9cf246a4d33cfa232016992b308df540c8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2018
---
# <a name="getting-started-with-java"></a>開始使用 Java


這是使用者入門的開始使用 頁面的 Java 中，其中涵蓋了所有支援平台的基本概念。

## <a name="requirements"></a>需求

若要使用.NET 內嵌 java，您將需要：

* Java 1.8 版或更新版本
* [Mono 5.0](http://www.mono-project.com/download/)

適用於 Mac:
* Xcode 8.3.2 或更新版本

若為 Windows:
* 使用 c + + 支援 visual Studio 2017
* Windows 10 SDK

針對 Android:
* [Xamarin.Android 7.5](https://www.visualstudio.com/xamarin/)或更新版本
* [Android Studio 3.x](https://developer.android.com/studio/index.html) java 1.8

您可以使用[Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/)來編輯和編譯 C# 程式碼。

> [!NOTE]
> 較早版本 Xcode、 Visual Studio、 Xamarin.Android、 Android Studio、 和 Mono_可能_運作，但是未經測試，並且不受支援。

## <a name="installation"></a>安裝

.NET 內嵌是上目前可用[NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```csharp
nuget install Embeddinator-4000
```
這將`Embeddinator-4000.exe`到`packages/Embeddinator-4000/tools`目錄。

此外，您可以建置 Embeddinator 來源，請參閱我們[git 儲存機制](https://github.com/mono/Embeddinator-4000/)和[參與](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)文件的指示。

## <a name="platforms"></a>平台

Java 目前為預覽狀態，如 macOS、 Windows 和 Android。

平台會選取傳遞`--platform=<platform>`embeddinator 命令列引數。 目前`macOS`， `Windows`，和`Android`支援。

### <a name="macos-and-windows"></a>macOS 和視窗

要進行開發，應該能夠使用任何支援 Java 1.8 的 Java IDE。 您甚至可以使用 Android Studio 這個如有需要，[此處](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects)。 任何標準的 Java jar 檔一樣，您可以使用輸出的 JAR 檔案。

### <a name="android"></a>Android

請確定您已設定好來開發 Android 應用程式，然後再嘗試建立一個使用 Embeddinator。 [指示](~/tools/dotnet-embedding/get-started/java/android.md)假設您有已經成功地建立和部署 Android 應用程式從您的電腦。

Android Studio 進行開發，建議使用，但是其他 Ide 中應該會運作，只要沒有支援[AAR 檔案格式](https://developer.android.com/studio/projects/android-library.html)。

## <a name="further-reading"></a>進一步閱讀

* [在 Android 上開始使用](~/tools/dotnet-embedding/get-started/java/android.md)
* [在 Android 上回呼](~/tools/dotnet-embedding/android/callbacks.md)
* [Android 的初步研究](~/tools/dotnet-embedding/android/index.md)
* [Embeddinator 限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [錯誤碼與描述](~/tools/dotnet-embedding/errors.md)
