---
title: 安裝 .NET 嵌入
description: 本檔說明如何安裝 .NET 內嵌。 它討論如何以手動方式執行工具、如何自動產生系結、如何使用自訂的 MSBuild 目標, 以及必要的後期建立步驟。
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 04/18/2018
ms.openlocfilehash: 7da163e85b04791c276f9cb14f5b21615b7909fb
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200164"
---
# <a name="installing-net-embedding"></a>安裝 .NET 嵌入

## <a name="installing-net-embedding-from-nuget"></a>從 NuGet 安裝 .NET 內嵌

選擇 [**新增] > [新增 NuGet 套件 ...** ], 然後從 NuGet 套件管理員安裝**Embeddinator-4000** :

![NuGet 封裝管理員](images/visualstudionuget.png)

這會將**Embeddinator-4000**和**objcgen**安裝到**封裝/Embeddinator-4000/tools**目錄中。

一般來說, 應該選取最新版本的 Embeddinator-4000, 以供下載。 目標-C 支援需要0.4 或更新版本。

## <a name="running-manually"></a>手動執行

現在已安裝 NuGet, 您可以手動執行此工具。

- 開啟終端機 (macOS) 或命令提示字元 (Windows)
- 將目錄變更為您的方案根目錄
- 工具會安裝在:
    - **./packages/Embeddinator-4000。[VERSION]/tools/objcgen** (目標-C)
    - **./packages/Embeddinator-4000。[VERSION]/tools/Embeddinator-4000.exe** (JAVA/C)
- 在 macOS 上, **objcgen**可以直接執行。
- 在 Windows 上, 可以直接執行**Embeddinator-4000** 。
- 在 macOS 上, **Embeddinator-4000**必須使用**mono**來執行:
    - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

每個命令調用都需要特定平臺檔中所列的幾個參數。

## <a name="automatic-binding-generation"></a>自動系結產生

有兩種方法可自動執行組建程式的 .NET 內嵌部分。

- 自訂 MSBuild 目標
- 建立後步驟

雖然這份檔將同時說明這兩種情況, 但使用自訂的 MSBuild 目標是慣用的。 從命令列建立時, 不一定會執行 Post 組建步驟。

### <a name="custom-msbuild-targets"></a>自訂 MSBuild 目標

若要使用 MSbuild 目標自訂群組建, 請先在您的 .csproj 旁建立**Embeddinator-4000 .targets**檔案, 如下所示:

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

在這裡, 命令的文字應該填入平臺特定檔中所列的其中一個 .NET 內嵌調用。

若要使用這個目標:

- 在 Visual Studio 2017 或 Visual Studio for Mac 中關閉您的專案
- 在文字編輯器中開啟文件庫 .csproj
- 在最後`</Project>`一行上方的底部新增這一行:

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- 重新開啟您的專案

### <a name="post-build-steps"></a>建立後步驟

新增建立後置步驟以執行 .NET 內嵌的步驟會根據 IDE 而有所不同:

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

在 Visual Studio for Mac 中, 移至 [**專案選項] > [組建 > 自訂命令**], 然後新增 [ **After build** ] 步驟。

設定平臺特定檔中所列的命令。

> [!NOTE]
> 請務必使用您從 NuGet 安裝的版本號碼

如果您要在C#專案上進行進行中的開發, 您也可以在執行 .net 內嵌之前, 新增自訂命令來清除**輸出**目錄:

```shell
rm -Rf '${SolutionDir}/output/'
```

![自訂群組建動作](images/visualstudiocustombuild.png)

> [!NOTE]
> 產生的系結將會放在`--outdir`或`--out`參數所指示的目錄中。 產生的系結名稱可能會因為某些平臺對套件名稱有所限制。

#### <a name="visual-studio-2017"></a>Visual Studio 2017

基本上, 我們會設定相同的東西, 但是 Visual Studio 2017 中的功能表有點不同。 Shell 命令也有些許不同。

移至 [**專案選項] > [建立事件**], 然後在 [建立**後事件命令列**] 方塊中輸入特定平臺檔中所列的命令。 例如：

![Windows 上的 .NET 內嵌](images/visualstudiowindows.png)
 