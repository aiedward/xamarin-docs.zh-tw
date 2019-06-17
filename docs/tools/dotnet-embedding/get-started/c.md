---
title: C 使用者入門
description: 本文件說明如何使用.NET 內嵌 C 應用程式中內嵌.NET 程式碼。 它討論如何使用.NET 內嵌在 Visual Studio 2019 和 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: lobrien
ms.author: laobri
ms.date: 04/19/2018
ms.openlocfilehash: 342ba2a6b51483983df7bd04034a4cef62fd57ff
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61213588"
---
# <a name="getting-started-with-c"></a>C 使用者入門

## <a name="requirements"></a>需求

若要使用.NET 內嵌 C，您將需要 Mac 或 Windows 的電腦執行：

### <a name="macos"></a>macOS

* macOS 10.12 (Sierra) 或更新版本
* Xcode 8.3.2 或更新版本
* [Mono](https://www.mono-project.com/download/)

### <a name="windows"></a>Windows

* Windows 7、 8、 10 或更新版本
* Visual Studio 2015 或更新版本

## <a name="installing-net-embedding-from-nuget"></a>安裝來自 NuGet 的.NET 內嵌

請遵循這些[指示](~/tools/dotnet-embedding/get-started/install/install.md)安裝及設定.NET 內嵌為您的專案。

（可能使用不同的版本號碼和路徑），您應該設定的命令引動過程看起來像：

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

由於`--compile`旗標傳遞至工具，.NET 內嵌應該也已編譯的輸出檔至共用的程式庫，您可以找到產生的檔案旁邊， **libmanaged.dylib**檔案在 macOS 和**managed.dll**在 Windows 上。

若要取用共用程式庫，您可以加入**managed.h** C 標頭檔，提供對應至個別的 C 宣告 managed 程式庫 Api，並使用先前所述的連結編譯的共用程式庫。

## <a name="further-reading"></a>進一步閱讀

* [.NET 內嵌限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [錯誤碼和描述](~/tools/dotnet-embedding/errors.md)
