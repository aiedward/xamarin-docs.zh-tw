---
title: C 使用者入門
description: 本檔說明如何使用 .NET 內嵌，在 C 應用程式中內嵌 .NET 程式碼。 它討論如何在 Visual Studio 2019 和 Visual Studio for Mac 中使用 .NET 內嵌。
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: davidortinau
ms.author: daortin
ms.date: 04/19/2018
ms.openlocfilehash: 9660eccd3e14a6c4ecad901064650fe1e25458f8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029788"
---
# <a name="getting-started-with-c"></a>C 使用者入門

## <a name="requirements"></a>需求

若要搭配 C 使用 .NET 內嵌，您需要執行下列的 Mac 或 Windows 電腦：

### <a name="macos"></a>macOS

* macOS 10.12 （塞拉里昂）或更新版本
* Xcode 8.3.2 或更新版本
* [Mono](https://www.mono-project.com/download/)

### <a name="windows"></a>Windows

* Windows 7、8、10或更新版本
* Visual Studio 2015 或更新版本

## <a name="installing-net-embedding-from-nuget"></a>從 NuGet 安裝 .NET 內嵌

請遵循這些[指示](~/tools/dotnet-embedding/get-started/install/install.md)，為您的專案安裝和設定 .net 內嵌。

您應該設定的命令調用看起來像（可能有不同的版本號碼和路徑）：

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

```shell
mono {SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=macos --compile managed.dll
```

### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
$(SolutionDir)\packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=windows --compile managed.dll
```

## <a name="generation"></a>產生

### <a name="output-files"></a>輸出檔案

如果一切順利，您會看到下列輸出：

```shell
Parsing assemblies...
    Parsed 'managed.dll'
Processing assemblies...
Generating binding code...
    Generated: managed.h
    Generated: managed.c
    Generated: mscorlib.h
    Generated: mscorlib.c
    Generated: embeddinator.h
    Generated: glib.c
    Generated: glib.h
    Generated: mono-support.h
    Generated: mono_embeddinator.c
    Generated: mono_embeddinator.h
```

由於 `--compile` 旗標已傳遞至工具，因此 .NET 內嵌也應該將輸出檔案編譯成共用程式庫，您可以在產生的檔案、macOS 上的**dylib**檔案，以及 Windows 上的**managed .dll**中找到該檔案。

若要取用共用程式庫，您可以加入**managed .h** c 標頭檔，它會提供對應至個別 managed 程式庫 Api 的 C 宣告，並與先前所述的已編譯共用程式庫連結。

## <a name="further-reading"></a>進一步閱讀

* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [錯誤碼和描述](~/tools/dotnet-embedding/errors.md)
