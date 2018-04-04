---
title: C 使用者入門
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 8dff45de6de7c9492b199f323656778ac5c34d57
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-c"></a>C 使用者入門


## <a name="requirements"></a>需求

若要使用.NET 內嵌 c 中，您將需要 Mac 或 Windows 的電腦執行：

* macOS 10.12 （利也） 或更新版本
* Xcode 8.3.2 或更新版本

* Windows 7、 8、 10 或更新版本
* Visual Studio 2015 或更新版本

* [Mono](http://www.mono-project.com/download/)


## <a name="installation"></a>安裝

下一個步驟是下載並在電腦上安裝的.NET 內嵌的工具。

C 和 Java 的產生器二進位的組建仍然無法使用，但即將推出。

或者它是可以建置從我們的 git 儲存機制，請參閱[參與](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)文件的指示。


## <a name="generation"></a>產生

若要產生 C 程式碼，叫用.NET 內嵌工具傳遞為目標的 C 語言的正確旗標：

### <a name="windows"></a>Windows：

```csharp
$ build/lib/Debug/Embeddinator-4000.exe --gen=c --output=managed_c --platform=windows --compile managed.dll
```

請確定呼叫從 Visual Studio 命令殼層特定的 Visual Studio 版本是目標。

### <a name="macos"></a>macOS

```csharp
$ mono build/lib/Debug/Embeddinator-4000.exe --gen=c --output=managed_c --platform=macos --compile managed.dll
```

### <a name="output-files"></a>輸出檔

如果一切順利，將會看見下列輸出：

```csharp
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

因為`--compile`旗標傳遞至工具，.NET 內嵌應該也已編譯輸出檔至共用的程式庫，您可以尋找產生的檔案旁邊、 `libmanaged.dylib` macOS 上的檔案和`managed.dll`Windows 上。

若要使用共用的程式庫，您可以包含`managed.h`C 標頭檔，提供對應至個別的 C 宣告 managed 程式庫 Api 並與先前所述的連結編譯共用程式庫。

## <a name="further-reading"></a>進一步閱讀

* [Embeddinator 限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [錯誤碼與描述](~/tools/dotnet-embedding/errors.md)
