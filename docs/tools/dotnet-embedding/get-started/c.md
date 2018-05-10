---
title: C 使用者入門
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: topgenorth
ms.author: toopge
ms.date: 04/19/2018
ms.openlocfilehash: f3c238dc9805dafa922f8e32fb4b1935a3fa152c
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="getting-started-with-c"></a>C 使用者入門

## <a name="requirements"></a>需求

若要使用.NET 內嵌 C，您將需要 Mac 或 Windows 的電腦執行：

### <a name="macos"></a>macOS

* macOS 10.12 （利也） 或更新版本
* Xcode 8.3.2 或更新版本
* [Mono](http://www.mono-project.com/download/)

### <a name="windows"></a>Windows

* Windows 7、 8、 10 或更新版本
* Visual Studio 2015 或更新版本

## <a name="installing-net-embedding-from-nuget"></a>安裝.NET NuGet 內嵌

請遵循這些[指示](~/tools/dotnet-embedding/get-started/install/install.md)安裝及設定專案的.NET 內嵌。

（可能具有不同的版本號碼和路徑），您應該設定的命令引動過程看起來像：

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

```shell
mono {SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=macos --compile managed.dll
```

### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
$(SolutionDir)\packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=windows --compile managed.dll
```

## <a name="generation"></a>產生

### <a name="output-files"></a>輸出檔

如果一切順利，將會看見下列輸出：

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

因為`--compile`旗標傳遞至工具，.NET 內嵌應該也已編譯輸出檔至共用的程式庫，您可以尋找產生的檔案旁邊、 **libmanaged.dylib** macOS 和上的檔案**managed.dll** Windows 上。

若要使用共用媒體櫃，您可以包含**managed.h** C 標頭檔，提供對應至個別的 C 宣告 managed 程式庫 Api 並與先前所述的連結編譯共用程式庫。

## <a name="further-reading"></a>進一步閱讀

* [.NET 內嵌限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [錯誤碼與描述](~/tools/dotnet-embedding/errors.md)
