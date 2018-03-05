---
title: "環境"
ms.topic: article
ms.prod: xamarin
ms.assetid: 67BFD4E1-276C-4B9F-9BD8-A5218D2BD529
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: ef5cfa2a9eee0d5d01922e5b7b3f89a209396c56
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="environment"></a>環境

「執行環境」是一組環境變數，會影響程式的執行。 環境變數可在專案屬性中暫時設定，或透過將額外引數指定給 mtouch 封裝工具來永久設定。

## <a name="temporary-environment-variables"></a>暫存環境變數

暫存環境變數可在專案的 [屬性]/[選項] 視窗中的 [執行] > [一般] 區段中進行設定。 這些環境變數只會在應用程式使用 Visual Studio for Mac 啟動時才會生效。若應用程式是透過手動點選啟動的，則將不會設定這些環境變數。

## <a name="permanent-environment-variables"></a>永久環境變數

永久環境變數可藉由將額外的引數指定給 mtouch 封裝工具來設定。 這些環境變數會編譯到可執行檔中，即使應用程式不是從 Xamarin Studio 啟動的也會進行設定。

## <a name="example"></a>範例

```csharp
# log all exceptions to the device log
--setenv:MONO_TRACE=E:all

# to pass multipe environment variables, use --setenv multiple times
--setenv:MONO_TRACE=E:all --setenv:GC_DONT_GC=1
```

