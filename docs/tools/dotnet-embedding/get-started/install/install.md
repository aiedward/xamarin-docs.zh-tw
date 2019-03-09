---
title: 安裝.NET 內嵌
description: 本文件說明如何安裝.NET 內嵌。 它討論如何以手動方式，執行這項工具如何產生繫結如何自動執行，使用自訂的 MSBuild 目標和必要的建置後步驟。
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 04/18/2018
ms.openlocfilehash: 2a572748c21d2a640add3346d1162f4b6bdc8e99
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668435"
---
# <a name="installing-net-embedding"></a>安裝.NET 內嵌

## <a name="installing-net-embedding-from-nuget"></a>安裝來自 NuGet 的.NET 內嵌

選擇**新增 > 新增 NuGet 封裝...** 並安裝**Embeddinator 4000** NuGet 套件管理員：

![NuGet 封裝管理員](images/visualstudionuget.png)

這會安裝**Embeddinator 4000.exe**並**objcgen**成**封裝/Embeddinator 4000/工具**目錄。

一般情況下，應該選取 Embeddinator 4000 的最新版本下載。 您必須以 OBJECTIVE-C 支援 0.4 或更新版本。

## <a name="running-manually"></a>手動執行

既然已安裝 NuGet，您可以執行這項工具，以手動方式。

- 開啟終端機 (macOS) 或命令提示字元 (Windows)
- 將目錄變更為您方案的根目錄
- 這項工具會安裝在：
    - **。 / 封裝/Embeddinator-4000。[VERSION] / tools/objcgen** (Objective C)
    - **./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe** (Java/C)
- 在 macOS 上， **objcgen**可以直接執行。
- 在 Windows 中， **Embeddinator 4000.exe**可以直接執行。
- 在 macOS 上， **Embeddinator 4000.exe**必須與執行**mono**:
    - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

每個命令引動過程需要平台專屬的文件中所列的參數的數目。

## <a name="automatic-binding-generation"></a>自動繫結產生

有兩種方法，來自動執行.NET 內嵌在建置程序。

- 自訂的 MSBuild 目標
- 建置後步驟

雖然本文件將說明如何同時，使用自訂的 MSBuild 目標是慣用的。 從命令列建置時，不會需要執行後續建置步驟。

### <a name="custom-msbuild-targets"></a>自訂的 MSBuild 目標

若要自訂您的組建使用 MSbuild 目標，請先建立**Embeddinator 4000.targets**旁您如下所示的 csproj 檔案：

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

在這裡，命令的文字應該會填入其中一個平台專屬的文件中所列.NET 內嵌的引動過程。

若要使用此目標：

- 關閉您的專案在 Visual Studio 2017 或 Visual Studio for Mac
- 在文字編輯器中開啟文件庫 csproj
- 在底端，最後上方新增此行`</Project>`列：

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- 重新開啟專案

### <a name="post-build-steps"></a>建置後步驟

新增建置後步驟，來執行.NET 內嵌的步驟有所不同 IDE:

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

在 Visual Studio for Mac 中，移至**專案選項 > 建置 > 自訂命令**並加入**之後建置**步驟。

設定平台特定文件中所列的命令。

> [!NOTE]
> 請務必使用您從 NuGet 安裝的版本號碼

如果您打算要執行進行中的開發上C#專案中，您可以加入自訂的命令，以清除**輸出**之前執行.NET 內嵌目錄：

```shell
rm -Rf '${SolutionDir}/output/'
```

![自訂的建置動作](images/visualstudiocustombuild.png)

> [!NOTE]
> 產生的繫結將會放在所指定的目錄`--outdir`或`--out`參數。 產生的繫結名稱可能會有所不同，因為某些平台上的套件名稱會有限制。

#### <a name="visual-studio-2017"></a>Visual Studio 2017

我們基本上是將設定相同的作業，但 Visual Studio 2017 中的功能表會有些許不同。 Shell 命令也會稍有不同的。

移至**專案選項 > 建置事件**，然後輸入命令列在 [平台特定文件放置到**建置後事件命令列**] 方塊中。 例如: 

![內嵌在 Windows 上的.NET](images/visualstudiowindows.png)
